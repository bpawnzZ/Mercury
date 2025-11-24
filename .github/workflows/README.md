# Mercury Build Workflows

This directory contains GitHub Actions workflows for building Mercury Browser for Arch Linux.

## Available Workflows

### 1. `build-arch-linux.yml`
- **Purpose**: Builds Mercury Browser from source for Arch Linux
- **Triggers**:
  - Push to `main` or `develop` branches
  - Tags starting with `v*`
  - Pull requests to `main` or `develop`
  - Manual trigger via workflow dispatch
  - Daily at 06:00 UTC (scheduled)
- **Output**: Mercury browser tarball (`.tar.bz2`)

### 2. `build-arch-package.yml`
- **Purpose**: Builds Arch Linux package (PKGBUILD) for easier installation
- **Triggers**:
  - Push to `main` or `develop` branches
  - Tags starting with `v*`
  - Pull requests to `main` or `develop`
  - Manual trigger via workflow dispatch
  - Weekly on Sunday at 00:00 UTC (scheduled)
- **Output**: Arch Linux package (`.pkg.tar.*`)

## Build Process

The workflows follow this build process:

1. **Environment Setup**: Uses Arch Linux Docker container with all build dependencies
2. **Source Bootstrap**: Downloads Mozilla source tree using `bootstrap.sh`
3. **Mercury Setup**: Copies Mercury source files over Mozilla tree using `setup.sh`
4. **Build**: Compiles Mercury using `build.sh`
5. **Package**: Creates distribution packages using `package.sh`

## Dependencies

Build dependencies installed in the workflow:

- **Base**: `base-devel`, `git`, `mercurial`
- **Languages**: `python`, `python-pip`, `rust`, `clang`, `llvm`, `lld`
- **Build Tools**: `cmake`, `ninja`, `autoconf2.13`, `bison`, `flex`, `gawk`
- **Libraries**: `gtk3`, `dbus-glib`, `nss`, `alsa-lib`, `pulseaudio`, `mesa`
- **Media**: `libjpeg-turbo`, `libpng`, `libwebp`, `libtiff`, `libvpx`
- **X11/Wayland**: Various X11 and Wayland libraries

## Usage

### Manual Build
1. Go to the **Actions** tab in your GitHub repository
2. Select the workflow you want to run
3. Click **Run workflow**
4. Choose branch and click **Run workflow**

### Automated Builds
- **Daily builds**: Run automatically at 06:00 UTC
- **Weekly package builds**: Run automatically on Sundays at 00:00 UTC
- **On every push**: Builds trigger automatically on push to main/develop
- **On tags**: Release builds trigger automatically on version tags

### Installing Built Packages

#### From Tarball:
```bash
tar -xf mercury-*.tar.bz2
cd mercury
./mercury
```

#### From Arch Package:
```bash
sudo pacman -U mercury-*.pkg.tar.*
```

## Configuration

### Environment Variables
- `MOZ_MAKE_FLAGS`: Controls parallel build jobs (default: `-j$(nproc)`)
- `HG_SRC_DIR`: Mozilla source directory (default: `$HOME/mozilla-unified`)

### Build Optimization
- Uses AVX2 optimizations when available
- Link Time Optimization (LTO) enabled
- Profile Guided Optimization (PGO) support
- Rust SIMD optimizations

## Troubleshooting

### Common Issues
1. **Build timeout**: Increase timeout in workflow if build takes longer than 120 minutes
2. **Missing dependencies**: Check if all required packages are listed in the workflow
3. **Memory issues**: Consider using larger GitHub runners for complex builds

### Performance Tips
- Use `workflow_dispatch` for manual testing
- Monitor build logs in GitHub Actions
- Check artifact sizes to ensure proper packaging