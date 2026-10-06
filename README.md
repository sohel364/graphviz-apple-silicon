# Graphviz - Graph Visualization Tools

[![build status](https://gitlab.com/graphviz/graphviz/badges/main/pipeline.svg)](https://gitlab.com/graphviz/graphviz/-/pipelines/)

from AT&amp;T Research and Lucent Bell Labs

* https://graphviz.org/

See https://graphviz.org/doc/build.html for prerequisites and detailed build notes.

## main GIT Repository

The main GIT Repository for graphviz can be found at:

* https://gitlab.com/graphviz/graphviz/

## Support
Graphviz is maintained by volunteers. Most work is aimed at improving
the overall quality of the code (readability, consistency, organization,
and portability), modernizing the build toolchain, and supporting the
external audience and ecosystem for graphviz. This effort is supported
by an extensive regression test suite. Occasionally, work can
address new features, running time bottlenecks or specific bugs.

Meaningful bug reports are appreciated, but because resources are limited,
many reports will not be addressed individually. After years, the maintainers
have reduced open issues from thousands to several hundred. The Graphviz core
was written in an experimental style and is known to be not very resilient
to intentional attacks. We strongly recommend not exposing graphviz in a
potential attack surface, and it is of little benefit to submit batches of 
issues generated through automated fuzzing and ASAN testing.

## Documentation

The Graphviz documents are hosted at https://graphviz.org/

## macOS build instructions

The following commands are intended for a macOS machine using Apple Silicon (arm64), such as an M2 Pro. The build uses the Apple Command Line Tools, Homebrew, and CMake. The resulting binaries are native macOS executables; they are not portable to Windows or Linux.

### Prerequisites

Install the Xcode Command Line Tools and Homebrew if they are not already installed:

```sh
xcode-select --install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Install the command-line build dependencies:

```sh
brew update
brew install cmake pkg-config bison flex libtool
```

Set the compiler environment for the current Apple Silicon machine:

```sh
export DEVELOPER_DIR=/Library/Developer/CommandLineTools
export CMAKE_OSX_ARCHITECTURES=arm64
```

### Console-only Graphviz build

Use this option when you only need command-line tools such as `dot`, `neato`, `sfdp`, and `gvpr`.

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

The executable is available at `install-console/bin/dot`. For example, render a DOT file with:

```sh
./install-console/bin/dot -Tsvg input.dot -o output.svg
```

### Graphical GVEdit build with Qt

GVEdit is the graphical Graphviz editor. This build was verified with Qt 5 on Apple Silicon. Qt 5 is recommended for the current source tree because the GUI CMake configuration explicitly searches for Qt 5 after Qt 6.

Install Qt 5 and configure the Homebrew paths:

```sh
brew install qt@5

QT5_PREFIX="$(brew --prefix qt@5)"
export PATH="$QT5_PREFIX/bin:$PATH"
export CPPFLAGS="-I$QT5_PREFIX/include${CPPFLAGS:+ $CPPFLAGS}"
export LDFLAGS="-L$QT5_PREFIX/lib${LDFLAGS:+ $LDFLAGS}"
export PKG_CONFIG_PATH="$QT5_PREFIX/lib/pkgconfig${PKG_CONFIG_PATH:+:$PKG_CONFIG_PATH}"
```

Configure and build the GUI in a separate build directory:

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

Run the application directly from the build tree:

```sh
./build-gui/cmd/gvedit/gvedit
```

The executable should report as a native Apple Silicon binary:

```sh
file ./build-gui/cmd/gvedit/gvedit
```

Expected output includes `Mach-O 64-bit executable arm64`.

If a different Homebrew prefix is used, replace `/opt/homebrew` in the Qt paths with the result of `brew --prefix`. The GUI build requires the Qt Core, PrintSupport, and Widgets components. If CMake reports that Qt is not found, confirm that `qt@5` is installed and that `CMAKE_PREFIX_PATH` points to its Homebrew prefix.

### Build notes

- The project supports a `WITH_GVEDIT=OFF` command-line build without Qt.
- `WITH_GVEDIT=ON` enables the GUI; CMake prefers Qt 6 when it is available, but Qt 5 is the verified macOS option for this build.
- `CMAKE_OSX_ARCHITECTURES=arm64` produces a native Apple Silicon binary. An Intel Mac should use `x86_64` instead.
- Do not hard-code the Qt package path in the source tree. Use `brew --prefix qt@5` or `CMAKE_PREFIX_PATH` so the build remains portable between machines.

## Graph Visualization ( https://graphviz.org/about/ )

Graph visualization is a way of representing structural information as diagrams of abstract graphs and networks. It has important applications in networking, bioinformatics, software engineering, database and web design, machine learning, and in visual interfaces for other technical domains.

Graphviz is open source graph visualization software. It has several main layout programs. See the gallery for sample layouts. It also has web and interactive graphical interfaces, and auxiliary tools, libraries, and language bindings. We're not able to put a lot of work into GUI editors but there are quite a few external projects and even commercial tools that incorporate Graphviz. You can find some of these in the Resources section.

The Graphviz layout programs take descriptions of graphs in a simple text language, and make diagrams in useful formats, such as images and SVG for web pages; PDF or Postscript for inclusion in other documents; or display in an interactive graph browser.

Graphviz has many useful features for concrete diagrams, such as options for colors, fonts, tabular node layouts, line styles, hyperlinks, and custom shapes.

In practice, graphs are usually generated from an external data sources, but they can also be created and edited manually, either as raw text files or within a graphical editor. (Graphviz was not intended to be a Visio replacement, so it is probably frustrating to try to use it that way.)

## Contacts

If you have a bug or believe something is not working as expected, please submit a [bug report](https://gitlab.com/graphviz/graphviz/issues).
If you do not want to sign up for Gitlab, you can email bug reports to the
recent top committer
(`git shortlog --email --numbered --summary origin/main~100.. | head -1`).

If you have a general question or are unsure how things work, these queries can be posted in the [Graphviz Forum](https://forum.graphviz.org/).
