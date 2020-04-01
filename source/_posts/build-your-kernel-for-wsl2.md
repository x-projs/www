---
title: Build Your Kernel For WSL2
date: 2020-03-14 08:16:26
tags: linux
---

Windows 10 2004 will be released to public very soon. It has been released in Windows Insider Program for a long time.
In this release, the biggest improvement to me is WSL2. In WSL2, windows actually hosted a virtual machine, and WSL
is actually running inside the virtual machine. That means you can run a real linux kernel and even you can build your
kernel and run it. It's very convenient for study Linux kernel. You can very easily switch kernels.

Build linux kernel is actually very easy, you just need a Linux environment. Yes, you can also build a kernel in side
WSL. In this article, I'll use Ubuntu which is the one of the most popular Linux distribution. You need install the
following packages:

    sudo apt install g++ make flex bison libssl-dev libelf-dev bc

Then you can download a copy of Linux kernel source code from https://www.kernel.org. Currently,
the official Microsoft WSL release uses 4.19.x. But I always like try new things, so let's download 5.5.9.

Use the following command to extract the kernel source code:

    tar xvf linux-5.5.9.tar.xz
    cd linux-5.5.9

Now, you need a configure file. The config file saves the kernel options. Lot all of the kernel features are supported
by Windows. And some kernel features are used for kernel developer debug only. So you need the config file to turn off
those features. I think the best way to get the config file is copying from Microsoft WSL repository. Microsoft WSL
team has turned off those options for you already. You can download from https://github.com/microsoft/WSL2-Linux-Kernel/blob/master/Microsoft/config-wsl.

Create a folder `Microsoft` under linux-5.5.9, and copy the put the config file in. Then you can build the kernel now:

    make KCONFIG_CONFIG=Microsoft/config-wsl

If you use linux 4.19.x, I believe you won't hit any issue. But if you use linux 5.5.9 like me, you will be ask for some
questions also. This is because the config file you download is for 4.19.x, in 5.5.9, there are a lot of new options added.
So you need answer "Yes" or "No" to enable or disable these new options. You can answer "No" for all questions, or you can
download the config file from my repository: https://github.com/xieyubo/WSL2-Linux-Kernel/blob/wsl-xyb-port-5.5.y/Microsoft/config-wsl.
I've answered those questions for you already.

After build finish, you can copy the kernel to a windows folder. The name of the kernel file under build folder is `bzImage`,
we rename it to `kernel` which is used in WSL2:

    cp arch/x86_64/boot/bzImage /mnt/c/temp/kernel

Now our kernel is ready to use. But before we using it, please make sure you are enable WSL2 and switch your existed WSL instances
from WSL1 to WSL2. You can follow the instructions on http://aka.ms/wsl2.

OK, let's open a Windows cmd window and shutdown WSL2 virtual machine:

    wsl --shutdown

Goto `C:\Windows\System32\lxss\tools`[^1] folder, use the new kernel file to replace the old one. Re-launch your WSL instance, type:

    uname -r

You should see the kernel version is `5.5.9` now.

![](/images/build-your-kernel-for-wsl2-1.png)

***Update on 2020-04-01***

Instead of replacing the original `kernel` file, you can also use global wsl2 configuration file.
You can create a `.wslconfig` file under `%UserProfile%`, and put the following content:

    [wsl2]
    kernel=c:\\tmp\\kernel

You must use absolute windows path and with escaped backslashes. So you can not use `\` in the path, must use `\\`. There are some
other configurations you can specify, please check https://github.com/MicrosoftDocs/WSL/blob/live/WSL/release-notes.md, search
`.wslconfig`.

Some people might be confused: why the original kernel file is around 50 MB, but the new one is just around 9 MB? Do you remember
the original name of the new kernel file is `bzImage`? `bzImage` is actually a kind of compressed file format. It compresses the
real kernel file (vmlinux which is around 50 MB), and adds code which can decompress the real kernel at runtime. The following
picture comes from https://en.wikipedia.org/wiki/Vmlinux explain the relation ship between `vmlinux` and `bzImage` very clear:

![](/images/Anatomy-of-bzimage.png)[^2]


[^2]: Original path of this picture: https://en.wikipedia.org/wiki/Vmlinux#/media/File:Anatomy-of-bzimage.png.
