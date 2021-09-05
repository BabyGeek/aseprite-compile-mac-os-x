# Compile Aseprite on Mac OS X

## Requirements :
1. Xcode v12.1 [download here](https://download.developer.apple.com/Developer_Tools/Xcode_12.1/Xcode_12.1.xip)
2. Xcode v12.1 command line tools [download here](https://download.developer.apple.com/Developer_Tools/Command_Line_Tools_for_Xcode_12.1_GM_seed/Command_Line_Tools_for_Xcode_12.1_GM_seed.dmg)
3. Homebrew installed [instructions can be found here](https://brew.sh/index_fr)

I used XCode v12.1 because I am running Catalina and this is the latest version for this os, if you run higher version of Mac OS X you can use higher version of XCode from App Store, the links I refered for XCode downloads are from [Apple "More - Downlaods" website](https://developer.apple.com/download/all/)

---

## 1 - Download Aseprite source code

```console
$ git clone --recursive https://github.com/aseprite/aseprite.git
```

---
## 2 - Install Ninja dependency

```console
$ brew install ninja
```
---
## 3 - Install Cmake dependency

```console
$ brew install cmake
```
---
## 4 - Build Skia
These steps will create a deps folder in your home directory with a couple of subdirectories needed to build Skia (you can change the __$HOME/deps__ with other directory). Some of these commands will take several minutes to finish:

```console
$ mkdir $HOME/deps
$ cd $HOME/deps
$ git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
$ git clone -b aseprite-m81 https://github.com/aseprite/skia.git
$ export PATH="${PWD}/depot_tools:${PATH}"
$ cd skia
$ python tools/git-sync-deps
$ gn gen out/Release-x64 --args="is_debug=false is_official_build=true skia_use_system_expat=false skia_use_system_icu=false skia_use_system_libjpeg_turbo=false skia_use_system_libpng=false skia_use_system_libwebp=false skia_use_system_zlib=false skia_use_sfntly=false skia_use_freetype=true skia_use_harfbuzz=true skia_pdf_subset_harfbuzz=true skia_use_system_freetype2=false skia_use_system_harfbuzz=false target_cpu=\"x64\" extra_cflags=[\"-stdlib=libc++\", \"-mmacosx-version-min=10.9\"] extra_cflags_cc=[\"-frtti\"]"
$ ninja -C out/Release-x64 skia modules
```
---

## 5 - Compile Aseprite

If you have Retina Display see the issue link on Step 5 source.

In this case, __$HOME/deps/skia__ is the directory where Skia was compiled or downloaded. Make sure that __CMAKE_OSX_SYSROOT__ is pointing to the correct SDK directory (in this case __/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.15.sdk__), but it could be different in your Mac.

```console
$ cd aseprite
$ mkdir build
$ cd build
$ cmake \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_OSX_ARCHITECTURES=x86_64 \
  -DCMAKE_OSX_DEPLOYMENT_TARGET=10.9 \
  -DCMAKE_OSX_SYSROOT=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.15.sdk \
  -DLAF_BACKEND=skia \
  -DSKIA_DIR=$HOME/deps/skia \
  -DSKIA_LIBRARY_DIR=$HOME/deps/skia/out/Release-x64 \
  -DSKIA_LIBRARY=$HOME/deps/skia/out/Release-x64/libskia.a \
  -G Ninja \
  ..
$ ninja aseprite
```
---
During the process you may see some failed states or warnings but until you didn't get error let it continu, if no errors happen, now you could run aseprite by ./aseprite.

---

Sources :

1. [Step 1, 2, 3](https://needoneapp.medium.com/create-an-auto-build-script-for-aseprite-on-mac-os-af8288b34f05)
2. [Step 4](https://github.com/aseprite/skia#skia-on-macos)
3. [Step 5](https://github.com/aseprite/aseprite/blob/main/INSTALL.md#macos-details)