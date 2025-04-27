# Use Cases and Examples

This document provides practical examples and use cases for the CI Demo project.

## Table of Contents

- [Basic Build Pipeline](#basic-build-pipeline)
- [Multi-Platform Testing](#multi-platform-testing)
- [Security Scanning](#security-scanning)
- [Artifact Publishing](#artifact-publishing)
- [Kubernetes Integration](#kubernetes-integration)
- [Matrix Testing](#matrix-testing)

## Basic Build Pipeline

A simple build pipeline that compiles the project and runs tests:

```yaml
job: basic-build

kubernetes:
  cloud: swx-k8s

volumes:
  - {mountPath: /hpc/local, hostPath: /hpc/local}

steps:
  - name: Configure
    run: |
      ./autogen.sh
      ./configure

  - name: Build
    run: make -j$(nproc)

  - name: Test
    run: make check
```

## Multi-Platform Testing

Testing across different operating systems and architectures:

```yaml
job: multi-platform

kubernetes:
  cloud: swx-k8s

runs_on_dockers:
  - {file: '.ci/Dockerfile.rhel8-6', name: 'rhel8-6', tag: 'latest'}
  - {file: '.ci/Dockerfile.ubuntu24-04', name: 'ubuntu24-04', tag: 'latest'}

matrix:
  axes:
    os:
      - rhel8-6
      - ubuntu24-04
    arch:
      - x86_64
      - aarch64

steps:
  - name: Build and Test
    containerSelector: "{name: '${os}'}"
    run: |
      ./autogen.sh
      ./configure
      make -j$(nproc)
      make check
```

## Security Scanning

Integrating security scanning tools like Coverity and BlackDuck:

```yaml
job: security-scan

kubernetes:
  cloud: swx-k8s-spray

runs_on_dockers:
  - {name: 'coverity', url: 'harbor.mellanox.com/swx-storage/ci-demo/x86_64/centos7-7:latest', category: 'tool'}
  - {name: 'blackduck', url: 'harbor.mellanox.com/toolbox/ngci-centos:latest', category: 'tool'}

steps:
  - name: Coverity Scan
    containerSelector: "{name: 'coverity'}"
    shell: action
    module: dynamicAction
    run: coverity.sh
    args:
      - "--build_script 'make -j 3'"
      - "--ignore_files 'devx googletest tests'"
    archiveArtifacts: 'cov.log'

  - name: BlackDuck Scan
    containerSelector: "{name: 'blackduck'}"
    shell: action
    module: ngci
    run: NGCIBlackDuckScan
    args:
      projectName: "MyProject"
      projectVersion: "1.0"
      projectSrcPath: "${WORKSPACE}/src"
      scanMode: "source"
```

## Artifact Publishing

Building and publishing artifacts to a repository:

```yaml
job: publish-artifacts

kubernetes:
  cloud: swx-k8s

registry_host: harbor.mellanox.com
registry_path: /swx-storage/myproject
registry_auth: swx-storage

credentials:
  - {credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS'}

env:
  NEXUS_URL: http://nexus.example.com:8081/

steps:
  - name: Build Package
    run: .ci/build_package.sh

  - name: Publish RPM
    credentialsId: 'nexus-creds'
    resource: actions/nexus.py
    run: |
      .ci/actions/nexus.py yum -u $NEXUS_URL -U $NEXUS_USER -P $NEXUS_PASS \
        -a upload -n my_repo --file *.rpm --upload_path 7/x86_64/
```

## Kubernetes Integration

Running tests in Kubernetes pods:

```yaml
job: k8s-test

kubernetes:
  cloud: swx-k8s
  namespace: ci-demo
  serviceAccount: jenkins

steps:
  - name: Deploy Test Pod
    run: |
      kubectl apply -f .ci/test-pod.yaml
      kubectl wait --for=condition=Ready pod/test-pod
      kubectl logs test-pod
      kubectl delete pod test-pod
```

## Matrix Testing

Running tests with different combinations of parameters:

```yaml
job: matrix-test

kubernetes:
  cloud: swx-k8s

matrix:
  axes:
    cuda:
      - dev/cuda9.2
      - dev/cuda10.0
    arch:
      - x86_64
      - aarch64
    compiler:
      - gcc
      - clang

steps:
  - name: Build with CUDA
    run: |
      export CUDA_HOME=/usr/local/cuda-${cuda}
      make -j$(nproc) CC=${compiler}

  - name: Run Tests
    run: |
      make check
```

## Best Practices

1. **Container Selection**
   - Use specific container selectors for steps that require special environments
   - Group similar steps to run in the same container

2. **Resource Management**
   - Use matrix builds for testing multiple configurations
   - Archive artifacts only when necessary
   - Clean up resources after use

3. **Error Handling**
   - Use `failFast: false` for matrix builds to ensure all configurations are tested
   - Implement proper error handling in scripts
   - Use `always` steps for cleanup

4. **Security**
   - Store credentials securely using the credentials system
   - Use environment variables for sensitive information
   - Implement proper access controls for artifacts

5. **Performance**
   - Use parallel builds where possible
   - Cache dependencies between builds
   - Optimize container images for faster startup 