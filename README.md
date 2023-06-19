# Docs - project documentation

### How it works

- Stage 1 - RPM spec files, regular repositories. They are provided with CI to test the changes automatically. Two types of CI are available: [build](https://github.com/clearfraction/ffmpeg/blob/master/.github/workflows/build.yml) and [build-core](https://github.com/clearfraction/yt-dlp/blob/main/.github/workflows/build.yml). `Build-core` doesn't include Clear Fraction RPM repository; for example, if we need to build a package with Clear ffmpeg headers. Also `build-core` will save some time for unpacking and installing CF repository.

- Stage 2 - [mass-rebuild](https://github.com/clearfraction/mass-rebuild) workflow featching the latest RPM spect and build packages by sheduler. If build failed - fix `spec` in package repository and hit "rebuild" button in Github Actions interface. New build can be triggered manually, one failed package can be rebuilded too.

- Stage 3 - [bundles](https://github.com/clearfraction/bundles) workflow will be **triggered automatically** and will fetch the last [mass-rebuild](https://github.com/clearfraction/mass-rebuild) artifacts and create new release. A release is *immutable*, unless it's totally broken like non-upgradable - do not touch, just fix the bugs and create new release. After successful build this workflow will hit a hosting service to deploy new release.

### How to build software using Clear Fraction libraries like x264, x265 or CUDA

- install GCC and third-party libraries:
  
```
# sudo swupd bundle-add c-basic
# sudo 3rd-party bundle-add codecs-cuda devpkg-codecs-cuda

# if you don't need CUDA:
# sudo 3rd-party bundle-add codecs devpkg-codecs
```

- add Clear Fraction paths:

  
```
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH":/opt/3rd-party/bundles/clearfraction/usr/lib64:/opt/3rd-party/bundles/clearfraction/usr/local/lib64
export PKG_CONFIG_PATH="$PKG_CONFIG_PATH":/opt/3rd-party/bundles/clearfraction/usr/include:/opt/3rd-party/bundles/clearfraction/usr/local/include
```

Now check if the library is available:

```
$ pkg-config --list-all | grep x265
...
x265          x265 - H.265/HEVC video encoder
...
```
