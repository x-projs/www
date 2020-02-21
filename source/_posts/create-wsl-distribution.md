---
title: Create WSL Distribution
date: 2020-02-21 13:27:22
tags: linux
---

WSL is a great product of Microsoft. You can have a linux develop environment on your windows machine via WSL.
But the official WSL distribution is a little old. For example, currently, the latest official WSL distribution
of Ubuntu is 18.04 and the official Ubuntu release is 19.10. Ubuntu 20.04 is coming even.

I want to try the latest Ubuntu 20.04 on windows, so I have to build a WSL distribution by myself. In order to
archive this goal, I need find a Ubuntu 20.04 distribution as a basement. I notice there is an [Ubuntu 20.04
docker image](https://hub.docker.com/_/ubuntu/). Docker image is a workable minimal system. It is a good basement.

Run the following command to pull an Ubuntu 20.04 docker image:

    docker pull ubuntu:20.04

Then launch the image:

    docker run -t -i ubuntu:20.04

You should see the prompt in your command window, it likes:

![](/images/create-wsl-distribution-1.png)

`root` is the current username logged in the docker image, `e09e3....` is the current docker container ID. It is
generated randomly by docker runner. This ID is useful, we will use it later.

Docker image doesn't have `sudo` installed, let's install it:

    apt update && apt install sudo

Now, let's create the install package for WSL:

    cd /
    tar czf install.tar.gz *

OK, the package is ready, let's exit the docker container and copy the package to local (remember replace `e09e3....` with your container ID):

    exit
    docker cp e09e39085a63:/install.tar.gz .

Create a folder to host the new WSL distribution, copy the package into the folder:

    md ubuntu-20.04
    copy install.tar.gz ubuntu-20.04

Download the launcher from https://github.com/xieyubo/WSL-DistroLauncher/releases/download/10b787c/ubuntu18.04.exe to that folder. You can also
change the launcher name to `ubuntu20.04.exe` if you like. Now, the folder should be looks like:

![](/images/create-wsl-distribution-2.png)

Double click `ubuntu28.04.exe`, that's all!
