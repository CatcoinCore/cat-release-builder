# cat-release-builder

A specialized Gitian builder script for creating reproducible Catcoin Core builds. This tool ensures binary consistency and security across different platforms through a controlled build environment.

## Host Requirements

You can run this builder on Windows, Linux, or MacOS hosts. Each platform has its own requirements:

### All Platforms
- Docker Desktop
- Git
- Python 3
- GPG (GnuPG) for signing
- ~50GB free disk space
- 8GB+ RAM recommended

### Platform-Specific Notes
- **Windows**: WSL2 required for Docker Desktop
- **Linux**: Additional build tools may be required
- **MacOS**: Command Line Tools package required

The specific installation steps for each component will vary by platform. Please refer to the official documentation for each tool for your specific operating system.

## Quick Start

```bash
# Clone the repository
git clone -b catcoin-changes https://github.com/CatcoinCore/cat-release-builder.git
cd cat-release-builder

# Initial setup with Docker
./build-release.py --docker --setup USERNAME VERSION --disable-apt-cacher
```

## Core Parameters

| Parameter | Description | Example |
|-----------|-------------|----------|
| `--docker` | Use Docker for builds (recommended) | `--docker` |
| `--build` | Build unsigned binaries | `--build` |
| `--sign` | Create signed binaries | `--sign` |
| `--buildsign` | Build and sign in one operation | `--buildsign` |
| `-o` | Target OS(es): l(Linux), w(Windows), m(MacOS) | `-o wm` |
| `-j` | Build threads | `-j 4` |
| `-m` | Memory allocation (MB) | `-m 8000` |
| `--no-commit` | Skip committing build results | `--no-commit` |
| `--disable-apt-cacher` | Disable apt-cacher (recommended) | `--disable-apt-cacher` |

## Common Build Commands

### Build for Single Platform
```bash
./build-release.py --docker --build USERNAME 2.0.0-beta15 -o w -j 4 -m 8000 --no-commit
```

### Build and Sign All Platforms
```bash
./build-release.py --docker --buildsign USERNAME 2.0.0-beta15 -j 4 -m 8000
```

## Build Process Overview

This is what the script automatically handles when you run it:

1. **Setup Phase** (automated)
   - Installs required dependencies
   - Clones necessary repositories
   - Downloads MacOS SDK (if building for MacOS)
   - Prepares build environment

2. **Build Phase** (automated)
   - Compiles binaries for selected platforms
   - Creates unsigned packages
   - Generates build signatures
   - Moves artifacts to `catcoin-binaries/VERSION/`

3. **Sign Phase** (automated, if enabled)
   - Signs Windows executables
   - Signs MacOS packages
   - Creates detached signatures
   - Updates signature repository

You only need to run the appropriate command (see "Common Build Commands" above) and the script handles all these steps automatically.

## GPG Key Requirements

You'll need a GPG key for signing builds. The setup process varies by platform:
- Windows: Use Gpg4win or Windows Subsystem for Linux
- Linux: Use your distribution's GPG package
- MacOS: Use GPG Suite or Homebrew's GPG

Key requirements:
- ECC (sign only) key
- Password protection enabled
- The key identifier should match the USERNAME you use in the build commands

## Output Directory Structure

After building, files are organized as follows:
```
catcoin-binaries/VERSION/
├── debug/           # Debug symbols
├── unsigned/        # Unsigned binaries
└── release/        # Final releases
    ├── linux/      # Linux binaries
    ├── osx/        # MacOS packages
    ├── win/        # Windows installers
    └── src/        # Source code archives
```

## Troubleshooting

Common issues to check:
- Docker is running and properly configured for your OS
- Sufficient disk space and memory
- GPG key is properly set up and accessible
- Correct permissions on build directories
- All prerequisites are installed and in PATH

## Support

- Report issues on GitHub
- Join Catcoin community channels for help
