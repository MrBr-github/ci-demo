# Getting Started

This guide will help you get started with the CI Demo project.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Basic Usage](#basic-usage)
- [Development Setup](#development-setup)
- [Common Tasks](#common-tasks)
- [Troubleshooting](#troubleshooting)

## Prerequisites

### System Requirements

1. **Operating Systems**
   - RHEL 8.6 or later
   - Ubuntu 24.04 or later
   - Other Linux distributions with similar package support

2. **Hardware Requirements**
   - CPU: x86_64 or aarch64
   - RAM: 4GB minimum
   - Disk: 10GB free space

### Software Dependencies

1. **Build Tools**
   ```bash
   # RHEL/CentOS
   sudo yum install -y autoconf automake libtool pkg-config meson ninja-build

   # Ubuntu/Debian
   sudo apt-get install -y autoconf automake libtool pkg-config meson ninja-build
   ```

2. **Development Tools**
   ```bash
   # RHEL/CentOS
   sudo yum install -y gcc gcc-c++ make git

   # Ubuntu/Debian
   sudo apt-get install -y gcc g++ make git
   ```

3. **Package Building**
   ```bash
   # RHEL/CentOS
   sudo yum install -y rpm-build

   # Ubuntu/Debian
   sudo apt-get install -y dpkg-dev debhelper
   ```

## Installation

### Source Code

1. **Clone the Repository**
   ```bash
   git clone <repository-url>
   cd ci-demo
   ```

2. **Initialize Submodules**
   ```bash
   git submodule update --init --recursive
   ```

### Build Systems

1. **Autotools Build**
   ```bash
   ./autogen.sh
   ./configure
   make
   sudo make install
   ```

2. **Meson Build**
   ```bash
   meson setup builddir
   meson compile -C builddir
   sudo meson install -C builddir
   ```

## Basic Usage

### Building Packages

1. **RPM Package**
   ```bash
   .ci/buildrpm.sh -s -t -b
   ```

2. **DEB Package**
   ```bash
   dpkg-buildpackage -us -uc
   ```

### Running Tests

1. **Unit Tests**
   ```bash
   # Autotools
   make check

   # Meson
   meson test -C builddir
   ```

2. **Integration Tests**
   ```bash
   .ci/runtests.sh
   ```

## Development Setup

### IDE Configuration

1. **VS Code**
   - Install C/C++ extension
   - Configure build tasks
   - Set up debugging

2. **CLion**
   - Import project
   - Configure toolchains
   - Set up run configurations

### Development Workflow

1. **Create Feature Branch**
   ```bash
   git checkout -b feature/new-feature
   ```

2. **Make Changes**
   - Implement feature
   - Add tests
   - Update documentation

3. **Test Changes**
   ```bash
   make check
   .ci/runtests.sh
   ```

4. **Create Pull Request**
   - Push changes
   - Create PR
   - Address feedback

## Common Tasks

### Building the Project

1. **Clean Build**
   ```bash
   # Autotools
   make distclean
   ./autogen.sh
   ./configure
   make

   # Meson
   rm -rf builddir
   meson setup builddir
   meson compile -C builddir
   ```

2. **Debug Build**
   ```bash
   # Autotools
   ./configure CFLAGS="-g -O0"
   make

   # Meson
   meson setup builddir --buildtype=debug
   meson compile -C builddir
   ```

### Running Tests

1. **Specific Test**
   ```bash
   # Autotools
   make check TESTS="specific_test"

   # Meson
   meson test -C builddir specific_test
   ```

2. **Test with Valgrind**
   ```bash
   make check VALGRIND=valgrind
   ```

### Package Management

1. **Build Package**
   ```bash
   .ci/build_package.sh
   ```

2. **Install Package**
   ```bash
   # RPM
   sudo rpm -ivh *.rpm

   # DEB
   sudo dpkg -i *.deb
   ```

## Troubleshooting

### Common Issues

1. **Build Failures**
   - Check dependencies
   - Verify toolchain
   - Clean build directory

2. **Test Failures**
   - Check test environment
   - Verify dependencies
   - Review test logs

3. **Package Building**
   - Check spec file
   - Verify dependencies
   - Review build logs

### Debugging

1. **Build Debug**
   ```bash
   make V=1
   ```

2. **Test Debug**
   ```bash
   make check TEST_DEBUG=1
   ```

3. **Package Debug**
   ```bash
   .ci/build_package.sh -d
   ```

### Getting Help

1. **Documentation**
   - Read the docs
   - Check examples
   - Review best practices

2. **Community**
   - Join mailing list
   - Check issue tracker
   - Ask questions

3. **Support**
   - Contact maintainers
   - Report issues
   - Request features 