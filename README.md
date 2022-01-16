# Android Emulator for Apple Silicon with x86 support

Experimental support for x86_64/i386 guests in android emulator on Macs with Apple M1 chips.

Official android emulator on M1 only supports ARM system images. Some system images provided by Google does not have arm64 versions, only providing intel support. That includes latest iterations of Android TV and Wear OS. 

Current release of emulator binary and patches is based on this commit from AOSP:

https://android.googlesource.com/platform/external/qemu/+/8e6fc056f813719c519032261c45daefe817a7ed

### How to use 

Go to Github [releases](https://github.com/fredyshox/android-emulator-m1-x86/releases) page, and download .tar.gz archive and extract it. Emulator executable is located in `CWD/emulator/emulator`.

Perfpormance is bad. Cold boot time is around 2-3 minutes on M1 Pro using WearOS 3 image. But then it's usable for light workflows. ARM64 with hardware virtualization remains supported in this version, unchanged from official distribution provided by Google.

Alternatively you can download emulator source code from [here](https://source.android.com/setup/build/downloading), apply provided patches and build it from source yourself.

### Patches 

Emulator source code is available on https://android.googlesource.com/platform/external/qemu, on branches with `emu-` prefix (e.g. `emu-master-dev`, `emu-31-release`).

Repo includes patches, which can be applied using following command from `{AOSP_ROOT}/external/qemu ` directory:

```sh
patch -p1 < x86-tcg-on-aarch64.patch 
```

Available patches:

* [x86-tcg-on-aarch64.patch](./x86-tcg-on-aarch64.patch) - x86 emulation support via TCG backend for aarch64 host.
* [x86-tcg-for-rosetta.patch](./x86-tcg-for-rosetta.patch) - experimental x86 emulation support via TCG backend for x86_64 hosts, meant to be used via Rosetta on M1. It supports MTTCG, but tests showed much lower performance than its native counterpart.

### What does not work?

* multiple cpu cores - only single core emulation for now, as multi-threaded TCG is not supported for x86 guests on arm hosts, due to differences in memory ordering
* process hangs when trying to quit via UI - use force quit or `kill -9` 

TBD

### Tips 

* Use host gpu acceleration, it speed things up by a lot - set `hw.gpu.enabled = yes`,`hw.gpu.mode = host` and `hw.gltransport = virtio-gpu-pipe` in `${ANDROID_AVD_HOME}/YourAVD.avd/config.ini`

