# CI/CD Pipeline

This document describes the CI/CD pipeline implementation in the CI Demo project.

## Table of Contents

- [Pipeline Overview](#pipeline-overview)
- [Pipeline Configuration](#pipeline-configuration)
- [Job Types](#job-types)
- [Pipeline Stages](#pipeline-stages)
- [Artifact Management](#artifact-management)
- [Pipeline Triggers](#pipeline-triggers)

## Pipeline Overview

The CI Demo project implements a comprehensive CI/CD pipeline using Jenkins and Kubernetes. The pipeline supports:

1. **Build Automation**
   - Multiple build systems (Autotools, Meson)
   - Package building (RPM, DEB)
   - Dependency management

2. **Testing**
   - Unit tests
   - Integration tests
   - Matrix testing
   - Security scanning

3. **Deployment**
   - Artifact publishing
   - Container image building
   - Kubernetes deployment

## Pipeline Configuration

### Jenkins Configuration

The pipeline is configured using:

1. **Jenkinsfile**
   - Main pipeline definition
   - Stage definitions
   - Post-build actions

2. **Jenkinsfile.shlib**
   - Shared library functions
   - Common pipeline utilities
   - Helper functions

### Kubernetes Integration

The pipeline integrates with Kubernetes through:

1. **Pod Templates**
   - Container definitions
   - Resource limits
   - Volume mounts

2. **Service Accounts**
   - Access control
   - Resource permissions
   - Security policies

## Job Types

### Build Jobs

1. **Standard Build**
   ```yaml
   job: build
   steps:
     - name: Build
       run: .ci/build_package.sh
   ```

2. **Matrix Build**
   ```yaml
   job: matrix-build
   matrix:
     axes:
       os: [rhel8-6, ubuntu24-04]
       arch: [x86_64, aarch64]
   steps:
     - name: Build
       run: .ci/build_package.sh
   ```

### Test Jobs

1. **Unit Tests**
   ```yaml
   job: unit-tests
   steps:
     - name: Run Tests
       run: make check
   ```

2. **Integration Tests**
   ```yaml
   job: integration-tests
   steps:
     - name: Deploy
       run: kubectl apply -f k8s/
     - name: Test
       run: .ci/runtests.sh
   ```

### Security Jobs

1. **Coverity Scan**
   ```yaml
   job: coverity-scan
   steps:
     - name: Coverity
       containerSelector: "{name: 'coverity'}"
       run: .ci/cov.sh
   ```

2. **BlackDuck Scan**
   ```yaml
   job: blackduck-scan
   steps:
     - name: BlackDuck
       containerSelector: "{name: 'blackduck'}"
       shell: action
       module: ngci
       run: NGCIBlackDuckScan
   ```

## Pipeline Stages

### Standard Stages

1. **Build Stage**
   - Source code checkout
   - Dependency installation
   - Package building

2. **Test Stage**
   - Unit tests
   - Integration tests
   - Performance tests

3. **Security Stage**
   - Static analysis
   - Dependency scanning
   - Vulnerability assessment

4. **Deploy Stage**
   - Artifact publishing
   - Container image building
   - Kubernetes deployment

### Example Pipeline

```yaml
job: full-pipeline
steps:
  - name: Build
    run: .ci/build_package.sh

  - name: Test
    run: .ci/runtests.sh

  - name: Security Scan
    containerSelector: "{name: 'security'}"
    run: .ci/security_scan.sh

  - name: Deploy
    run: .ci/deploy.sh
```

## Artifact Management

### Artifact Types

1. **Build Artifacts**
   - RPM/DEB packages
   - Source archives
   - Build logs

2. **Test Artifacts**
   - Test reports
   - Coverage data
   - Performance metrics

3. **Security Artifacts**
   - Scan reports
   - Vulnerability data
   - Compliance reports

### Artifact Storage

```yaml
steps:
  - name: Build
    run: make
    archiveArtifacts: 'dist/*.rpm'

  - name: Test
    run: make check
    archiveArtifacts: 'test-results/*.xml'
```

## Pipeline Triggers

### Trigger Types

1. **Git Triggers**
   - Push events
   - Pull request events
   - Tag events

2. **Schedule Triggers**
   - Periodic builds
   - Nightly builds
   - Release builds

3. **Manual Triggers**
   - User-initiated builds
   - Parameterized builds
   - Release deployments

### Example Triggers

```yaml
triggers:
  - git:
      branches:
        - main
        - develop
  - schedule:
      cron: '0 0 * * *'
  - manual:
      parameters:
        - name: RELEASE
          type: boolean
```

## Best Practices

1. **Pipeline Design**
   - Keep stages independent
   - Implement proper error handling
   - Use matrix builds for testing

2. **Resource Management**
   - Set resource limits
   - Clean up resources
   - Monitor pipeline performance

3. **Security**
   - Secure credential storage
   - Implement access control
   - Regular security scanning

4. **Maintenance**
   - Regular pipeline updates
   - Performance optimization
   - Documentation updates 