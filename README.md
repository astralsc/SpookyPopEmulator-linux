# Spooky Pop Emulator

[![Android Build](https://github.com/VitalikObject/SpookyPopEmulator/actions/workflows/android.yml/badge.svg)](https://github.com/VitalikObject/SpookyPopEmulator/actions/workflows/android.yml)

An open-source emulator for the iOS version of "Spooky Pop" (a discontinued game by Supercell), allowing it to run natively on Android and Linux.

This emulator translates iOS instructions and shims iOS frameworks (like UIKit, Foundation, CoreGraphics, etc.) to allow the game to run on modern platforms. It uses [Dynarmic](https://github.com/lioncash/dynarmic.git) for dynamic binary translation.

## Features

* **Cross-Platform**: Designed to run on Android and Linux.
* **JIT Compilation**: Fast execution utilizing the Dynarmic JIT engine.
* **iOS Framework Shimming**: Emulates a subset of iOS APIs required by the game.

## Requirements

* CMake 3.21+
* C++20 compatible compiler
* Android NDK (for Android builds)
* Linux with build tools (for Linux builds)

## Game Assets

Before building or running the emulator, you must provide the original game files. Extract the decrypted iOS application and place the `Spooky Pop` executable and the `res/` folder directly into the `external/` directory (next to the `.pastehere` file).

## Build Instructions

### Linux

```bash
# Clone Dynarmic
rm -rf extern/dynarmic
git clone https://github.com/lioncash/dynarmic.git extern/dynarmic

# Download SQLite 3.50.4
rm -rf extern/sqlite3
mkdir -p extern/sqlite3
cd extern/sqlite3

curl -L https://www.sqlite.org/2025/sqlite-amalgamation-3500400.zip -o sqlite.zip
unzip sqlite.zip

mv sqlite-amalgamation-3500400/sqlite3.c .
mv sqlite-amalgamation-3500400/sqlite3.h .
mv sqlite-amalgamation-3500400/sqlite3ext.h .

rm -rf sqlite-amalgamation-3500400 sqlite.zip

cd ../..
rm -rf extern/stb
git clone https://github.com/nothings/stb.git extern/stb

# Build
mkdir build
cd build
cmake ..
cmake --build . -j$(nproc)
```

### Android

An automated build script is provided to generate the APK. The following
commands install the Android SDK command-line tools and required SDK
components, then build the Android ARM64 library and APK.

```bash
# Android SDK
mkdir -p ~/Android/Sdk/cmdline-tools
cd ~/Android/Sdk

curl -L https://dl.google.com/android/repository/commandlinetools-linux-13114758_latest.zip -o cmdline-tools.zip
unzip -o cmdline-tools.zip

mkdir -p cmdline-tools/latest
mv cmdline-tools/bin cmdline-tools/lib cmdline-tools/latest/ 2>/dev/null || true
mv cmdline-tools/NOTICE.txt cmdline-tools/source.properties cmdline-tools/latest/ 2>/dev/null || true
rm -f cmdline-tools.zip

# Android environment
export ANDROID_SDK_ROOT="$HOME/Android/Sdk"
export ANDROID_HOME="$ANDROID_SDK_ROOT"
export PATH="$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$ANDROID_SDK_ROOT/platform-tools:$PATH"

sdkmanager "ndk;27.2.12479018"

export ANDROID_NDK_HOME="$ANDROID_SDK_ROOT/ndk/27.2.12479018"

cd ~/SpookyPopEmulator-linux
./scripts/build_android_apk.sh
```

## License

This project is licensed under the [GNU General Public License v3.0](LICENSE).

## Disclaimer

This project is not affiliated with, endorsed, sponsored, or specifically approved by Supercell Oy. "Spooky Pop" and other related trademarks are the property of Supercell Oy. This is a non-commercial, educational fan project created for preservation purposes.
