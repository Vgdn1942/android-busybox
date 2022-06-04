# Native bionic (Android) port of Busybox

## Prepare a standalone Android Toolchain

- download Android NDK from [https://developer.android.com/ndk/downloads/index.html](https://developer.android.com/ndk/downloads/index.html)
- unpack android-ndk-rXX-linux-x86\_64.zip into a suitable directory (tested with r21e)
- cd in thejust unpacked directory launch for build arm\
  `./build/tools/make_standalone_toolchain.py --arch arm --api 21 --install-dir ~/arm-linux-androideabi` \
  or lunch for build aarch64\
  `./build/tools/make_standalone_toolchain.py --arch arm64 --api 21 --install-dir ~/aarch64-linux-android` \
  adding your preferred architecture to the command line like `--arch arm` for arm or `--arch arm64` for aarch64
  and if you want and your minimum api level
- open `~/arm-linux-androideabi/sysroot/usr/include/android/api-level.h` for arm \
  or `~/aarch64-linux-android/sysroot/usr/include/android/api-level.h` for aarch64 \
  and change:

  `#define __ANDROID_API__ __ANDROID_API_FUTURE__` \
  with \
  `#define __ANDROID_API__ 21`

  (the same number used on commandline or the default if not specified)

## Configuring busybox

- execute (or add to your bashrc): `export PATH=$PATH:$HOME/arm-linux-androideabi/bin` for arm,
  or `export PATH=$PATH:$HOME/aarch64-linux-android/bin` for aarch64
  adjust the path if needed
- verify that the compiler is in the path with `arm-linux-androideabi-gcc -v` for arm
  or `aarch64-linux-android-gcc -v` for aarch64
- in busybox source tree launch `make sherpya_android_defconfig` for build arm
  or lunch `make aarch64_android_defconfig` for build aarch64
- if you want to configure further, launch `make menuconfig`,
  if needed change `Cross Compiler prefix` in `Busybox Settings`
- launch make (add -jX if you want to use multiple jobs


/opt/arm-linux-androideabi/sysroot/usr/include/android/api-level.h

## Bugs & Limitations

- The current Android NDK has a fake resolver stub, so if you build a static busybox, the resulting
  executable will not be able to resolve hosts, e.g. `ping www.google.it` will fail,
  so just leave it dynamic
- Android versions before 4.1 (API 16) do not support PIE, you need to disable it
- Android versions after 5.0 (API 21) require PIE, you need to enabled it (default in my config)
- Running executables built with ancient API on newer Android versions may crash 
