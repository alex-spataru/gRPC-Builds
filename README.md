# gRPC Pre-built Binaries

[![Build gRPC](https://github.com/alex-spataru/gRPC-Builds/actions/workflows/build.yml/badge.svg)](https://github.com/alex-spataru/gRPC-Builds/actions/workflows/build.yml)

Pre-built static gRPC C++ libraries for Linux, macOS & Windows. Drop them into your CI pipeline and skip the 30+ minute build-from-source step.

## Platforms

| Archive | Platform | Arch | Compiler |
|---------|----------|------|----------|
| `grpc-{ver}-linux-x86_64.tar.gz` | Linux | x86_64 | GCC |
| `grpc-{ver}-linux-aarch64.tar.gz` | Linux | aarch64 | GCC |
| `grpc-{ver}-macos-universal.tar.gz` | macOS | arm64 + x86_64 | Apple Clang |
| `grpc-{ver}-windows-x86_64.zip` | Windows | x86_64 | MSVC 2022 (v193) |

All builds use C++20, static linking, Release mode.

## Usage

### Trigger a build

Go to **Actions → Build gRPC → Run workflow**, enter the desired gRPC version (e.g. `1.78.1`).

The workflow builds all 4 platforms in parallel and creates a GitHub release with the archives.

### Consume in CI

```yaml
- name: Download prebuilt gRPC
  run: |
    gh release download v1.78.1 \
      -R alex-spataru/gRPC-Builds \
      -p "grpc-1.78.1-linux-x86_64.tar.gz"
    mkdir -p grpc-prefix
    tar -xzf grpc-1.78.1-linux-x86_64.tar.gz -C grpc-prefix
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}

- name: Configure
  run: |
    cmake -B build \
      -DCMAKE_PREFIX_PATH=${{ github.workspace }}/grpc-prefix \
      -DENABLE_GRPC=ON \
      ...
```

## Build details

- Static libraries only (`BUILD_SHARED_LIBS=OFF`)
- Only the C++ plugin is built (no C#, Node, PHP, Python, Ruby plugins)
- macOS: universal binaries via `lipo`, deployment target 11.0
- Windows: static MSVC runtime (`/MT`) via `CMAKE_MSVC_RUNTIME_LIBRARY=MultiThreaded`
