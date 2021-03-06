# Cuda-Install-and-Usage
This repo explains how I got Cuda installed on my Ubuntu linux machines

## Before you Start
So there are apparently many different ways to install CUDA on a linux machine. There is the `apt-get install xxxx` methods, and then there are the download the file and install methods. The `apt` methods do not allow for the setting of the environment variables, while the more advanced method of downloading installers allows for more seamless integration to packages like `cmake`, and for ROS peeps, `catkin_make`. I find that the advanced method, is, well, more difficult, but in the end, is more user friendly for software development. If you do not develop software, the `apt` method is for you. I will be focusing on the more difficult method in this document. I also find this method more educational and rewarding. In order to test the installation, I will be demonstrating installing `tensorflow-gpu` and `keras` for hardware-accelerated neural network training. __If you are installing software to program the Jetson, you should follow another guide, as this assumes you have not installed software for the Jetson TX2. This guide might mess up that installation. I am not sure at this time how big of a problem this is.__ I should also mention that this is not my software; I am merely providing to you the steps I used to get Cuda to work on _my_ machine, but to make it a hopefully more clear process.

## Prerequisites
Before you start, you need to know how to navigate the Ubuntu operating system. We will be using Ubuntu 1604, though many of these steps transfer to other distros and versions well. You will also need to verify you have a Cuda-capable GPU. You can find out if you have one by running `lspci | grep nvidia`. This will look for nvidia devices available on your machine and print them to the terminal. If the list is empty, chances are you are out of luck. If you think you are already running the proprietary driver, run `nvidia-smi`. This will output the stats of the gpu.

## Installing NVidia graphics drivers
In order to use hardware acceleration, you need to have the correct NVidia proprietary drivers. The driver that usually comes with your machine is `nouveau` which is the open-source implementation of the proprietary driver. This will not work for us. You will have to blacklist the `nouveau` driver and only use the NVidia driver. I will be showing you how to install driver version `384`. This version is the most stable and most recent prop driver that will work on older non-10 series cards like the `980m`. Here are the steps to follow modified from [this link](http://www.terriblesysadmin.com/?p=38):

1. Reboot your machine to grub
2. select `advanced options`
3. select the recovery mode that corresponds to the kernel version that you are currently operating with (This is usually kernel version 4.15)]
4. You will now be taken to a menu with a pink background. Select `Enable Networking` and then `Drop to root prompt`. You will now need to execute these commands here in this order:
```
sudo apt update
sudo apt-get remove --purge nvidia-*
sudo apt-get remove --purge xserver* 
sudo apt-get remove --purge libcheese7
sudo apt-get install xserver-xorg-lts-xenial'
sudo apt-get install nvidia-384 nvidia-settings nvidia-prime ubuntu-desktop
sudo reboot
```

After these steps, you can use `nvidia-smi` to check if the installation worked. Now you should have proprietary graphics installed! Now we can muck around elsewhere.
I have heard that installing this driver also fixes a lot of other issues with computers, like drivers not loading and mice and keyboards not working. __Off Topic__: I should also mention that `flashplugin` often breaks things for the same reason `nouveau` breaks things. Install the adobe official version if that is also a problem.

## Installing Cuda
#### Now for the fun part
You now need to create a developer account with NVidia before you can proceed. All of the downloads we need access to are locked until you create an account. 

Once you have created a developer account, we need to download Cuda. I will be downloading version 9.0 because it is the most table version for my applications, but I am sure that installing the newer versions is almost the exact same process. This is [the download link](https://developer.nvidia.com/cuda-90-download-archive) for Cuda 9.0. Go to this link, then click linux, then click your system architecture. If you are not sure which one is the correct one, you probably want the `x86_64` option. Then click Ubuntu and then 1604 and then the `deb local file`. This is the main installer file. Once the file is downloaded, use the Ubuntu gui to double click on it to install it. This process, actually, does not install it, but rather registers it as something that is installable, or some garbage like that. You should then follow the installation instructions right below the download link, which I have copied here:

```
sudo dpkg -i cuda-repo-ubuntu1604-9-0-local_9.0.176-1_amd64.deb
sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda
```

For the `sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub` line, I believe it should be `sudo apt-key add /var/cuda-repo-9.0/7fa2af80.pub`, but I will have to double check that. The deb file auto creates a directory there, so you should just `cd` there to figure out what it is. I will update this at some point to reflect this.

## Cuda installed properly - but does it run Crisis?
Just because you installed Cuda does not mean it actually works. Cuda relies on specific environment variables in order to find itself. This is the perk about installing this way: because we have to specify paths, this makes using `CMake` a simpler process, as we simply need to source the directories like any other library. Here are the variable that I set in my `~/.bashrc` file:
