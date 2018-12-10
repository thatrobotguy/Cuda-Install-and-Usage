# Cuda-Install-and-Usage
This repo explains how I got Cuda installed on my Ubuntu linux machines

## Before you Start
So there are apparently many different ways to install CUDA on a linux machine. There is the `apt-get install xxxx` methods, and then there are the download the file and install methods. The `apt` methods do not allow for the setting of the environment variables, while the more advanced method of downloading installers allows for more seamless integration to packages like `cmake`, and for ROS peeps, `catkin_make`. I find that the advanced method, is, well, more difficult, but in the end, is more user friendly for software development. If you do not develop software, the `apt` method is for you. I will be focusing on the more difficult method in this document. I also find this method more educational and rewarding. In order to test the installation, I will be demonstrating installing `tensorflow-gpu` and `keras` for hardware-accelerated neural network training.

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

You now have proprietary graphics installed! Now we can muck around elsewhere.
I have heard that installing this driver also fixes a lot of other issues with computers, like drivers not loading.

## Installing Cuda
#### Now for the fun part
You now need to create a developer account with NVidia before you can proceed. All of the downloads we need access to are locked until you create an account. 
