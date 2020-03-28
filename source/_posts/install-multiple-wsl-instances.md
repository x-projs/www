---
title: Install Multiple WSL Instances
date: 2020-01-25 11:32:36
tags: linux
---

First follow https://docs.microsoft.com/en-us/windows/wsl/install-win10 to install one WSL instance.
It will download the Linux distribution package and install the required windows services.

Then open your registry editor, navigate to `Computer\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lxss`.
There is a subkey which name is a guid:

![](/images/install-multiple-wsl-instances-1.png)

There is a package family name: `CanonicalGroupLimited.Ubuntu18.04onWindows_79rhkp1fndgsc`. Remove `_79rhkp1fndgsc` part,
you can get the package name: `CanonicalGroupLimited.Ubuntu18.04onWindows`. Now, open `Powershell` type the following command:

    Get-AppxPackage CanonicalGroupLimited.Ubuntu18.04onWindows

You will get the `InstallLocation` of this package:

![](/images/install-multiple-wsl-instances-2.png)

Open that folder, there are two important files:

![](/images/install-multiple-wsl-instances-3.png)

`install.tar.gz` is the distribution package of Linux. `ubuntu1804.exe` is the launcher. When you try to launch WSL, the luncher
will invoke WSL api with distribution name to check whether the reqiured distribution is installed or not. If it is not installed,
the luncher will invoke WSL api with distribution name and the path of the distribution package to install the WSL. Otherwise, it
will invoke the WSL api to active this distribution.

So in order to install multiple WSL instance, the key point is passing different distribution name to WSL api. Each WSL instance
should have unique distribution name. Unfortunately, the distribution name is [hard coded in the luncher](https://github.com/microsoft/WSL-DistroLauncher/blob/1f8551f7e2ea22bba2e6fb02f01e7a5f7fb757f3/DistroLauncher/DistributionInfo.h#L16). We need modify the launcher by ourselves to
different distribution name to WSL api.

I modified the luncher code in [my fork](https://github.com/xieyubo/WSL-DistroLauncher). It will use the folder name as the distribution
name. So you can use my luncher to create multiple WSL instances easily.

For example, let's create a folder to hold the first WSL instance:

    mkdir d:\WSLs\wsl-1

Copy `install.tar.gz` into that folder. And [download the modified luncher](https://github.com/xieyubo/WSL-DistroLauncher/releases/download/10b787c/ubuntu18.04.exe)
into that folder also. Run `ubuntu18.04.exe` from that folder. Because it is under `wsl-1` folder, so it will use `wsl-1` as the
distribution name. Due to we never install this distribution, the luncher will install it first, then active it.

> You can also directly download WSL distribution from https://aka.ms/wsl-ubuntu-1804. The file downloaded is actually
a zip file. You can use 7-zip to open it or change the file extention name to `.zip` and extract `install.targ.gz` directly.

Then create a new folder to hold the second WSL instance:

    mkdir d:\WSLs\wsl-2

Do the same steps and run `ubuntu18.04.exe` from `wsl-2` folder. It will use `wsl-2` as the distribution name. The same, it will
install the distribution first and active it.

Use this way, you can create as many WSL instances as you want.

***Update on 2020-03-28***

I just noticed that Ubuntu actually has a minimal rootfs released: [Ubunt Base](https://wiki.ubuntu.com/Base).
So we can use it to create WSL instance directly.

Let's download [Ubuntu Base 19.10](http://cdimage.ubuntu.com/ubuntu-base/releases/19.10/release/ubuntu-base-19.10-base-amd64.tar.gz.),
change its name to `install.tar.gz` and copy it with [our modified luncher](https://github.com/xieyubo/WSL-DistroLauncher/releases/download/10b787c/ubuntu18.04.exe)
into a same folder: `D:\ubuntu-19.10`. Double click the launcher. It will create a new WSL instance named as `ubuntu-19.10` for us.

There is no `sudo` in the minimal rootfs, so we need install it maunally. Open a cmd window from Windows and run the following command:

    wsl -d ubuntu-19.10 -u root apt update
    wsl -d ubuntu-19.10 -u root apt install sudo

Your new WSL instance is ready to use now.
