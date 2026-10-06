# macOS Graphviz Build Notes

## Purpose

This document preserves the macOS Apple Silicon build context so the project can be resumed immediately after a break.

## Project and repository

- Project: Graphviz
- Current version: `16.1.1~dev.20261004.1917`
- Platform: macOS on Apple Silicon / arm64
- Machine: Apple M2 Pro
- Compiler: Apple Clang from the Xcode Command Line Tools
- Build system: CMake with Unix Makefiles
- GUI: GVEdit with Qt
- Required Qt version: Qt 5.15.19 was used successfully for the GUI build
- GitHub repository: https://github.com/sohel364/graphviz-apple-silicon
- GitHub remote name: `github`
- Existing GitLab remote: `origin`

## Status

- The source repository was pushed to GitHub.
- The README build instructions were committed and pushed.
- Commit: `34683f403bcd59dd59fe26cb06b993932e03dc17`
- Commit message: `Add macOS Graphviz build instructions`
- The GitHub branch is `main`.
- The README is tracked and committed.
- The generated build directories are not tracked and must remain excluded from source control.
- The local `build-gvedit/` directory contains generated CMake files, object files, compiled binaries, and Qt-generated sources.
- The local `build/install/` directory contains generated installation outputs.
- Binaries were **not uploaded to GitHub**.

## Build results observed

The following native macOS builds were observed:

- `build/install/bin/dot`: Mach-O 64-bit executable arm64
- `build-gvedit/cmd/gvedit/gvedit`: Mach-O 64-bit executable arm64
- Graphviz version: `dot - graphviz version 16.1.1~dev.20261004.1917 (20261004.1917)`
- The build CMake configuration used `CMAKE_OSX_ARCHITECTURES=arm64`.
- Qt 5 was discovered through `/opt/homebrew/opt/qt@5/lib/cmake/Qt5`.
- `WITH_GVEDIT=ON` and `ENABLE_LTDL=ON` were enabled in the GUI build.

The build was successfully used to render an SVG with `dot -Tsvg` and to launch GVEdit. The observed executable architecture was arm64, not x86_64.

## Build prerequisites

Install the Xcode Command Line Tools and Homebrew if they are not already installed:

```sh
xcode-select --install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Install the core build dependencies:

```sh
brew update
brew install cmake pkg-config bison flex libtool
```

Set the compiler and architecture environment:

```sh
export DEVELOPER_DIR=/Library/Developer/CommandLineTools
export CMAKE_OSX_ARCHITECTURES=arm64
```

## Console-only build

Use this build for command-line Graphviz tools without the graphical editor.

```sh
cd /path/to/graphviz
rm -rf build-console install-console

cmake -S . -B build-console \
  -DCMAKE_OSX_ARCHITECTURES=arm64 \
  -DCMAKE_INSTALL_PREFIX="$PWD/install-console" \
  -DENABLE_LTDL=ON \
  -DWITH_GVEDIT=OFF

cmake --build build-console -j2
cmake --install build-console

./install-console/bin/dot -V
```

Example SVG render:

```sh
./install-console/bin/dot -Tsvg input.dot -o output.svg
```

## Qt GUI build

Use Qt 5 for the GUI build that was verified on this macOS system.

```sh
brew install qt@5

QT5_PREFIX="$(brew --prefix qt@5)"
export PATH="$QT5_PREFIX/bin:$PATH"
export CPPFLAGS="-I$QT5_PREFIX/include${CPPFLAGS:+ $CPPFLAGS}"
export LDFLAGS="-L$QT5_PREFIX/lib${LDFLAGS:+ $LDFLAGS}"
export PKG_CONFIG_PATH="$QT5_PREFIX/lib/pkgconfig${PKG_CONFIG_PATH:+:$PKG_CONFIG_PATH}"
```

Configure and build:

```sh
cd /path/to/graphviz
rm -rf build-gui install-gui

cmake -S . -B build-gui \
  -DCMAKE_OSX_ARCHITECTURES=arm64 \
  -DCMAKE_INSTALL_PREFIX="$PWD/install-gui" \
  -DCMAKE_PREFIX_PATH="$QT5_PREFIX" \
  -DENABLE_LTDL=ON \
  -DWITH_GVEDIT=ON

cmake --build build-gui -j2
cmake --install build-gui
```

Run GVEdit from the build tree:

```sh
./build-gui/cmd/gvedit/gvedit
```

Verify the binary architecture:

```sh
file ./build-gui/cmd/gvedit/gvedit
```

Expected result:

```text
Mach-O 64-bit executable arm64
```

## Repository files and source-control policy

Do not commit generated build outputs, CMake cache files, object files, generated Qt sources, installed binaries, or local executable files.

Recommended local exclusions:

- `build/`
- `build-gui/`
- `build-console/`
- `install/`
- `install-gui/`
- `install-console/`

The current repository only has the README documentation change committed. The local generated directories remain untracked and should not be pushed.

## Important decisions

1. The project is built natively for macOS arm64; it is not a cross-platform Windows/Linux binary.
2. Qt 5 was used because it was verified successfully with the repository's GVEdit configuration.
3. The console build and GUI build are separate CMake configurations.
4. The generated build artifacts were intentionally excluded from Git.
5. The README contains the reproducible instructions for another developer.
6. Binary artifacts were not uploaded to GitHub.

## Resume checklist

1. Open the repository on macOS with Apple Silicon.
2. Install the Xcode Command Line Tools and Homebrew.
3. Install CMake, pkg-config, Bison, Flex, libtool, and Qt 5.
4. Configure the Apple Silicon environment.
5. Run the console-only build if only command-line tools are needed.
6. Run the GUI build with `WITH_GVEDIT=ON` if GVEdit is needed.
7. Verify the expected executable architecture with `file`.
8. Run `dot -V` and launch `gvedit`.
9. Commit only source or documentation changes, never generated build directories.
10. Push source changes to the `github` remote; do not push binaries.

## Notes on the earlier build attempt

A build command was attempted after the working build was already created, but the tool did not return usable output. The earlier successful build evidence remains the architecture checks, version output, and GVEdit launch from the prior session. Before claiming a fresh build is successful, rebuild from a clean directory and capture the complete CMake and build output.
