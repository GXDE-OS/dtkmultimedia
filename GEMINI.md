# GEMINI.md — dtkmultimedia

## Project Overview

**dtkmultimedia** is a C++/Qt multimedia toolkit library for the Deepin desktop environment. It provides high-level APIs for audio/video playback, camera capture, screen recording, OCR, and table recognition. The project is developed by UnionTech and licensed under LGPL-3.0-or-later.

### Modules

The project is organized into four libraries under `src/`:

| Module | Directory | Description |
|--------|-----------|-------------|
| `dtkmultimedia` | `src/multimedia/` | Audio I/O, recording, media player, media capture, format handling |
| `dtkmultimediawidgets` | `src/multimediawidgets/` | Video widget, screenshot, engine backends (GStreamer/FFmpeg/MPV) |
| `dtkocr` | `src/ocr/` | OCR abstraction layer with plugin interface |
| `dtktablerecognizer` | `src/tablerecognizer/` | Table structure detection and recognition (requires ONNX Runtime; Qt6 only) |

Public headers are in `include/` mirroring the same subdirectory structure.

### Technology Stack

- **Language:** C++17
- **Qt:** Qt 6.8+ (with Qt5 fallback support)
- **Build System:** CMake (minimum 3.13)
- **Multimedia Backends:** GStreamer, FFmpeg (libav*), MPV
- **OCR:** ncnn + OpenCV Mobile
- **Table Recognition:** ONNX Runtime (amd64/arm64/loong64, Qt6 only)
- **Packaging:** Debian (debhelper-compat 12)

## Build Commands

### Full Build

```bash
mkdir build && cd build
cmake ..
make
sudo make install
```

### Debug Build (enables tests + examples)

```bash
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Debug
make
```

### Run Tests

Tests are only built in Debug mode. Two test targets:

```bash
# Main test suite (multimedia + multimediawidgets + ocr)
cd build && ctest -R ut-dtkmultimedia

# Table recognizer tests (only if onnxruntime available)
cd build && ctest -R ut-tablerecognizer
```

### CMake Options

| Option | Default | Description |
|--------|---------|-------------|
| `BUILD_DOCS` | ON | Generate Doxygen documentation |
| `BUILD_EXAMPLES` | ON | Build example applications |
| `DTK_ENABLE_TABLERECOGNIZER` | ON | Build tablerecognizer (requires onnxruntime) |

## Development Conventions

### Code Style

- **clang-format** is configured (`.clang-format`): Cpp language, 4-space indent, 130 column limit, Allman-style braces for classes/functions/namespaces/structs/unions, right-aligned pointers.
- Apply with: `clang-format -i <file>`

### Naming & Structure

- Public headers use `d` prefix (e.g., `DAudioInput`, `DMediaPlayer`, `DScreenRecorder`)
- Unit test files follow the pattern `ut_<module>.cpp`
- Examples live in `examples/<name>/` with their own CMakeLists.txt
- Documentation uses Doxygen `.dox` files in `docs/`

### Qt Version Handling

The project supports both Qt5 and Qt6. CMake auto-detects the Qt version:
- Qt6: sets `DTK_VERSION_MAJOR=6`, adds `-DBUILD_QT6` define
- Qt5: `DTK_VERSION_MAJOR` is empty string
- Libraries are named accordingly: `libdtk6multimedia` vs `libdtkmultimedia`
- The `tablerecognizer` module is Qt6-only

### Testing

- Uses **Google Test** framework
- Tests link against `gcov` for coverage; `test-recoverage.sh` is available at project root
- Test stubs use `cpp-stub` and `stub-ext` (in `tests/3rdparty/`)
- Table recognizer tests are gated on the `dtk*tablerecognizer` target existence

### Debian Packaging

- `debian/control.in` is the Qt5 template; `debian/control` is used directly for Qt6
- `tablerecognizer` is only built for amd64/arm64/loong64 architectures
- Package names include version prefix for Qt6 (e.g., `libdtk6multimedia`)

## Repository Structure

```
├── CMakeLists.txt          # Root build config
├── src/                    # Library sources (4 modules)
├── include/                # Public headers
├── tests/                  # Unit tests (Google Test)
├── examples/               # Example applications
├── docs/                   # Doxygen documentation
├── debian/                 # Debian packaging
├── misc/                   # pkg-config/cmake config templates
└── .github/workflows/      # CI pipelines
```
