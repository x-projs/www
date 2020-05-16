---
title: Make VS Code work well under WSL2 with customized kernel
date: 2020-05-16 06:25:11
tags: linux vscode
---

Microsoft WSL2 is amazing. But the kernel of the official release version is out of date.
Fortunately, [you can build a new customized kernel and use it](/2020/03/14/build-your-kernel-for-wsl2/).

Now, issue is comming. Under the new kernel, you will notice [VS Code](https://code.visualstudio.com/) can't
work well. If you type `code` under WSL, you will see some garbage outpus. This is because VS code uses a
naive way to detect the WSL kernel version. It requires the kernel version string follow this pattern:
`x.xx.xx-microsoft-standard`. At mean while, its version comparing algorithm is too naive to get the right
result. For example, if current kernel version is `4.19` and the new kernel version is `5.1`. VS code will
think the new kernel is older than the current kernel because `51` is smaller than `419`.

Wish VS Code can fix this issue in its newer release. But for now, we need do some hacking work. The easiest
way is generating the same kernel version string as official WSL.

Let's goto `/usr/bin` folder, rename `uname` to `uname.real`. Then create a new `uname` file with the following
content:

    #!/bin/bash
    echo $(/usr/bin/uname.real $@ | sed -E s/[0-9]+.[0-9]+.[0-9]+-microsoft-wsl-xyb-port\\+/4.19.84-microsoft-standard/)

`-microsoft-wsl-xyb-port` is the version pattern of [my customized kernel](https://github.com/xieyubo/WSL2-Linux-Kernel/releases/tag/wsl-xyb-port-5.6.y-latest).
You can replace it with the pattern used by your kernel.

Now, run `uname -r`, make sure you will see the following line:

    4.19.84-microsoft-standard

Now, you can reinstall vscode-server:

1. Under WSL, run `rm -rf ~/.vscode-server`
1. Under Windows, run `wsl --shutdown` and goto `%userprofile%\.vscode\extensions`, delete `ms-vscode-remote.remote-wsl-xxx` folder.

Re-launch WSL, go to any folder you want to open in VS Code, type `code .`. VS code should work as normal now.
