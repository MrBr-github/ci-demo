# Build System

This document describes the build system used in the CI Demo project.

## Table of Contents

- [Build Systems](#build-systems)
- [Build Scripts](#build-scripts)
- [Package Building](#package-building)
- [Dependencies](#dependencies)
- [Environment Setup](#environment-setup)

## Build Systems

The project supports multiple build systems:

### Autotools

The project uses GNU Autotools for building on traditional Unix-like systems.

Key files:
- `configure.ac`: Main configuration script
- `Makefile.am`: Automake input file
- `autogen.sh`: Script to generate configure script

### Meson

The project also supports Meson build system for modern builds.

Key files:
- `meson.build`: Main Meson build configuration

## Build Scripts

### Main Build Scripts

1. **build_package.sh**
   - Located in `.ci/build_package.sh`
   - Handles the complete build process
   - Supports both RPM and DEB package building
   - Includes distcheck and package verification

2. **buildrpm.sh**
   - Located in `.ci/buildrpm.sh`
   - Specifically for RPM package building
   - Handles spec file processing
   - Manages build dependencies

### Usage Examples

```bash
# Build using autotools
./autogen.sh
./configure
make

# Build using meson
meson setup builddir
meson compile -C builddir

# Build package
.ci/build_package.sh

# Build RPM
.ci/buildrpm.sh -s -t -b
```

## Package Building

### RPM Building

The project supports building RPM packages with the following features:

1. **Spec File Generation**
   - Automatic spec file creation
   - Version and release management
   - Dependency handling

2. **Build Process**
   - Source preparation
   - Build environment setup
   - Package creation
   - Verification

### DEB Building

For Debian-based systems:

1. **Package Creation**
   - Debian control file generation
   - Package metadata
   - Dependency resolution

2. **Build Process**
   - Source package creation
   - Binary package building
   - Installation verification

## Dependencies

### Build Dependencies

The project requires the following build dependencies:

1. **Autotools**
   - autoconf
   - automake
   - libtool
   - pkg-config

2. **Meson**
   - meson
   - ninja-build

3. **Package Building**
   - rpm-build (for RPM)
   - dpkg-dev (for DEB)

### Runtime Dependencies

Runtime dependencies are specified in:

1. **RPM**
   - Spec file requirements
   - Automatic dependency resolution

2. **DEB**
   - Debian control file
   - Package metadata

## Environment Setup

### Build Environment

The build environment can be set up using:

1. **Docker Containers**
   - RHEL 8.6 container
   - Ubuntu 24.04 container
   - Custom build environments

2. **Local Environment**
   - Development machine setup
   - CI/CD pipeline integration

### Environment Variables

Important environment variables:

```bash
# Build configuration
export AUTOMAKE_JOBS=$(nproc)
export CFLAGS="-O2 -g"
export CXXFLAGS="-O2 -g"

# Package building
export release_dir=/path/to/release
export do_release=1
```

## Best Practices

1. **Build Process**
   - Always run distcheck before releases
   - Verify package dependencies
   - Test installation process

2. **Environment**
   - Use clean build environments
   - Document build requirements
   - Version control build scripts

3. **Packaging**
   - Follow distribution guidelines
   - Maintain changelog
   - Test package installation

4. **CI/CD Integration**
   - Automate build process
   - Implement build verification
   - Archive build artifacts 