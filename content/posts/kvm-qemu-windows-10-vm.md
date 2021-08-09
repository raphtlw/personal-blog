---
title: "How to set up a KVM / QEMU Windows 10 VM"
date: 2021-08-09T12:39:38+08:00
draft: false
---

Over the weekend, I wiped Windows on my Zephyrus G14 and installed Pop!\_OS on it. I've been trying to get a Windows 10 Virtual Machine as I need it for school. It's also great to have a setup like this because I can destroy and create the VM anytime I want because Windows likes to be funny and break itself over time. With this, I can also have Windows 11, a macOS VM and more. It just gives me the freedom to have whatever operating system I want.

In this post, I describe how I setup Windows 10 on a Kernel-based virtual machine (KVM) using QEMU. In this approach I use the Virtual Machine Manager (virt-manager) but everything I describe here can be done with the terminal as well (see `virsh`).

## Resources

Here are all the files that you need to download for this:

[Windows 10 ISO](https://www.microsoft.com/en-us/software-download/windows10ISO)

[VirtIO Drivers ISO](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso)

### Installation

We should first install QEMU and KVM + the Virtual Machine Manager and their dependencies. To install it is as follows:

```shell
sudo apt-get install qemu-kvm libvirt-bin bridge-utils virt-manager qemu virt-viewer spice-vdagent
```

Before you continue, remember to enable virtualization (e.g. VT-x / AMD-V) in your BIOS / UEFI settings.

### Initial setup

Open up the Virtual Machine Manager and click on the upper left button to open the _New VM_ window.

![](/images/screenshot_2021-08-09_12-59-25.png)

The first thing you have to do is to select how you would like to install the operating system. In this case, we are using a Windows 10 ISO image. Click forward and choose the Windows 10 ISO you downloaded.

![](/images/screenshot_2021-08-09_13-26-59.png)

Then, click forward again and choose how much RAM and CPUs you want to have. Don't worry, this can be changed later. Personally, I would go with half of my system resources for a Windows VM, so that's 8 GB and 8 cores. Make sure to use multiples of 1024 when you convert it to MB. For the CPU it doesn't really matter what you put here because we will be making further changes to the configuration later on.

![](/images/screenshot_2021-08-09_13-39-09.png)

In the storage screen, choose _Select or create custom storage_ and choose _Manage_.

![](/images/screenshot_2021-08-09_13-41-10.png)

In the _Locate or create storage volume_ window, press the plus button and add the new storage volume. You will be prompted to put a name for it. Just leave it as default and change the format to raw. You can also change the capacity to however much you like. Once you are done, click _Finish_.

![](/images/screenshot_2021-08-09_13-45-29.png)

You should check the minimum requirements for the operating system you are going to install. For best performance choose the *raw* format. The *qcow2* format offers some advanced features such as copy on write and live snapshots (Source: [proxmox](https://pve.proxmox.com/wiki/Windows_10_guest_best_practices)). I don’t need this features for my use case, so I choose performance over features. In addition you need to decide the capacity as well as how much of this capacity should be already allocated at the hosts system. At the end give the storage a name, this name will be used as a filename. So in my case I would have a *win10.img* in my kvm_storage.

When you are done, you should tick _Customize configuration before install_, because we need to tweak a few things before we can begin the installation process.

![](/images/screenshot_2021-08-09_13-49-44.png)

### Configurations before installation

Sometimes the Windows image you’ve selected in the initial setup isn’t correctly connected. Make sure, that it is connected in IDECDROM 1. If not, connect it by clicking *connect* and set the image location to the according Windows 10 .ISO file.

Now, we will need to mount the VirtIO Drivers ISO. It contains the drivers and other guest tools that Windows will need later on to enable compatibility with the VM. You can find the ISO at the top of the page under [Resources](#resources). After downloading the ISO click on *Add Hardware* -> *Storage* -> *Select or create custom storage* -> *Manage*. Set the device type to CDROM device and click on manage. Choose the VirtIO Drivers ISO and add it.

![](/images/screenshot_2021-08-09_13-57-53.png)

Set the NIC. For best performance I set the network interface controller (NIC) to VirtIO by selecting the corresponding entry for the *Device model*.

![](/images/screenshot_2021-08-09_14-00-36.png)

To get the best performance on the VM the storage disks bus must be set to VirtIO. This can be done by selecting *VirtIO* under *IDE Disk 1* -> *Advanced options* -> *Disk bus*. Additionally the Cache mode should be set to _writeback_ for best performance or *None* for best stability. With this cache mode you may loose data on power outage. More about cache modes is nicely described at [proxmox](https://pve.proxmox.com/wiki/Performance_Tweaks).

![](/images/screenshot_2021-08-09_14-03-02.png)

For the final step, you'll need to set the 2 disc CDROM drives to be booted as well, so go to the _Boot Options_ section and tick the rest of the boot devices to be enabled.

![](/images/screenshot_2021-08-09_14-05-21.png)

After you’ve finished the customization steps above click *Begin Installation* in the top left corner of the customization window. This will launch the VM and should automatically boot the Windows installer. When you come to the point where you need to select where you want to install windows you’ll notice, that no volumes are found. This is because the storage driver is missing. To load the storage and the NIC drivers click on *Load driver* (bottom left of the second row).

![](/images/screenshot_2021-08-09_14-08-25.png)

Then, click on _Browse_ and expand the _CD Drive (E:)_ letter, expand _viostor_, _w10_ and your own CPU architecture. In my case it would be _amd64_. Once you are done click _Next_ and it should install the driver. At this step I would also recommend installing the network drivers and the display drivers as well. You can do so by repeating the same process but select the _NetKVM_ folder and _qxldod_ respectfully.

![](/images/screenshot_2021-08-09_14-11-11.png)

![](/images/screenshot_2021-08-09_14-14-30.png)

Now you can just proceed with the Windows installation process as usual.

### Install VirtIO guest tools

After you've installed Windows, to get the most optimal experience you should install the guest tools. On the [SPICE website](https://www.spice-space.org/download.html) It is described as follows:

> This installer contains some optional drivers and services that can be installed in Windows guest to improve SPICE performance and integration. This includes the qxl video driver and the SPICE guest agent (for copy and paste, automatic resolution switching, …)

The most important parts here are the video driver as well as the SPICE guest agent which enables e.g. to copy and paste between host and guest.

In Windows, open up the virtio E: drive in the file explorer and double click on the virtio-win-guest-tools application. It should look something like this:

![Win10_virtio_guest_tool_installer](/images/Win10_virtio_guest_tool_installer.png)

Congrats, you now have a fully functional KVM-based virtual machine. You can further explore this if you would like better graphics performance or CPU performance through a PCI passthrough.

