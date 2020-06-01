---
title: Install Ubuntu Desktop Under WSL2
date: 2020-05-31 17:37:21
tags: linux
---

Install Ubuntu desktop under wsl2 is easy, just type the following command is enough:

    sudo apt-get install ubuntu-desktop

When install is finished, shutdown the wsl service from windows cmd:

    wsl --shutdown

Then you can launch the wsl instance again, and start `dbus` service:

    sudo /etc/init.d/dbus start

Ubuntu desktop can be treated as a X11 client, you need run a x11 server in windows.
[VcXsrv](https://sourceforge.net/projects/vcxsrv/) is not a bad choice. It's free and
open source.

After install VcXsrv, it will create a `XLaunch` shortcut on the desktop:

![](/images/install-ubuntu-desktop-under-wsl2-1.png)

Double click it, you can select how to show the window:

![](/images/install-ubuntu-desktop-under-wsl2-2.png)

and how to start client:

![](/images/install-ubuntu-desktop-under-wsl2-3.png)

select `Disable access control` in `Extra settings` page so that Ubuntu desktop can
connect to:

![](/images/install-ubuntu-desktop-under-wsl2-4.png)

You will see a blank window opened:

![](/images/install-ubuntu-desktop-under-wsl2-5.png)

The title contains the x11 server address: `xybpc:3.0`. `xybpc` should be replaced
by the ip address which can be accessed from WSL. There are multiple ways to get the
ip address. You can type `cat /etc/resolv.conf` to get the ip address of the nameserver:

![](/images/install-ubuntu-desktop-under-wsl2-6.png)

Or type `ipconfig` in windows cmd to get the ip address of the WSL:

![](/images/install-ubuntu-desktop-under-wsl2-7.png)

Now, in WSL, type the following command to start GNome shell:

    gnome-shell -d 172.23.16.1:3.0 --x11

You should see the Ubuntu desktop now:

![](/images/install-ubuntu-desktop-under-wsl2-8.png)
