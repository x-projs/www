---
title: Cross Build FFmpeg & FFplay for Windows
date: 2020-01-25 08:55:26
tags: ffmpeg
---

Note: The following example is building for 32 bits. If you want to build for 64 bits, please use 
`x86_64-w64-mingw32` instead of `i686-w64-mingw32`.

### Build FFmpeg

Install the following packages:

    sudo apt-get update
    sudo apt-get make nasm gcc mingw-w64

Build:

    ./configure --arch=x86 --target-os=mingw32 --cross-prefix=i686-w64-mingw32- --prefix=/mnt/d/src/ffmpeg/build.d/win/x86

### Build FFplay

Build SDL2 first:

    cd /tmp
    wget https://www.libsdl.org/release/SDL2-2.0.10.tar.gz
    tar xvzf SDL2-2.0.10.tar.gz
    cd SDL2-2.0.10
    ./configure --prefix=/usr/sdl2-win32 --host=i686-w64-mingw32
    make -j`nproc` -s
    sudo make install

Make a symbol link to i686-w64-mingw32-sdl2-config:

    sudo ln -s /usr/sdl2-win32/bin/sdl2-config /usr/bin/i686-w64-mingw32-sdl2-config

Now reconfig FFmpeg, it will enable ffplay automatically:

    ./configure --arch=x86 --target-os=mingw32 --cross-prefix=i686-w64-mingw32- --prefix=/mnt/d/src/ffmpeg/build.d/win/x86
    make -j`nproc` -s
    sudo make install

### Copy dependencies and run

Copy required DLLs before running:

    cp /usr/sdl2-win32/bin/SDL2.dll /mnt/d/src/ffmpeg/build.d/win/x86/bin/
    cp /usr/lib/gcc/i686-w64-mingw32/7.3-win32/libgcc_s_sjlj-1.dll /mnt/d/src/ffmpeg/build.d/win/x86/bin/

You can run ffmpeg & ffplay from windows now.
