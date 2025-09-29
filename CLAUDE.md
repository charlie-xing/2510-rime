# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Squirrel (鼠鬚管) is the Rime Input Method Engine frontend for macOS. It's a C++/Swift hybrid project that provides a native macOS input method interface powered by the librime engine.

## Build Commands

### Prerequisites
- Xcode 14.0+ (required for Universal app builds)
- cmake (install via `brew install cmake`)

### Main Build Commands
```bash
# Build dependencies first
make deps

# Build release version (default)
make

# Build debug version
make debug

# Build with specific architectures (Universal binary)
make ARCHS='arm64 x86_64' BUILD_UNIVERSAL=1

# Build and create installer package
make package

# Install directly to system (requires sudo, logout recommended)
make install
```

### Shortcut for Dependencies
```bash
# Download latest librime binary instead of building from source
bash ./action-install.sh
```

### Clean Commands
```bash
make clean              # Clean Squirrel artifacts only
make clean-deps         # Clean all dependencies (librime, plum, sparkle)
make clean-package      # Clean package artifacts
```

## Development Commands

### Linting
```bash
swiftlint                           # Run SwiftLint on Swift source files
periphery scan --relative-results --skip-build --index-store-path build/Index.noindex/DataStore  # Check for unused code
```

### Environment Variables
- `BOOST_ROOT`: Path to Boost libraries (required)
- `DEV_ID`: Apple Developer ID for code signing (optional)
- `BUILD_UNIVERSAL=1`: Build universal binary
- `PLUM_TAG=":preset"` or `":extra"`: Build with plum formulae
- `ARCHS`: Target architectures (e.g., 'arm64 x86_64')
- `MACOSX_DEPLOYMENT_TARGET`: Minimum macOS version (default: 13.0)

## Project Architecture

### Core Components
- **librime/**: Rime Input Method Engine (C++ submodule)
- **sources/**: Swift source files for macOS frontend
- **plum/**: Package manager for Rime schemas
- **Sparkle/**: Auto-update framework (submodule)
- **data/**: Input method data files (schemas, dictionaries)

### Key Swift Files
- `SquirrelApplicationDelegate.swift`: Main app delegate
- `SquirrelInputController.swift`: Core input handling logic
- `SquirrelPanel.swift`: Candidate selection UI
- `SquirrelView.swift`: Custom drawing and rendering
- `SquirrelTheme.swift`: Theme and appearance management
- `SquirrelConfig.swift`: Configuration management

### Build System
- Primary build system: Makefile + Xcode project
- Dependencies built via Make, app built via xcodebuild
- Complex dependency chain: Boost → librime → Squirrel
- Universal binary support for both Intel and Apple Silicon

### Data Flow
1. **librime** handles text processing, schema loading, and candidate generation
2. **SquirrelInputController** receives keyboard input and forwards to librime
3. **SquirrelPanel/View** displays candidates and handles user selection
4. **plum** manages schema installation and updates

### Configuration
- SwiftLint configured in `.swiftlint.yml` (line length: 200, strict mode off)
- Periphery configured in `.periphery.yml` for unused code detection
- Xcode project: `Squirrel.xcodeproj`
- Deployment target: macOS 13.0+

### CI/CD
- GitHub Actions workflows in `.github/workflows/`
- Automated building, linting, and packaging on commits
- SwiftLint and Periphery checks enforced in CI