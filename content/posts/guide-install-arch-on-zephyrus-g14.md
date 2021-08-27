---
title: "How to install Arch Linux on the Zephyrus G14 with full GPU support"
date: 2021-08-21T14:30:36+08:00
draft: false
---

In this blog, we will cover how to install Arch Linux on the G14 with full GPU support and getting everything working properly. Most importantly, the mic mute button. Later on, I will also show you how to get a QEMU/KVM Windows VM with GPU passthrough and most of the things working properly to be able to use Windows in your Linux daily driver setup, getting the best of both worlds.

Recently, I've been having a lot of problems with Windows in my daily life. Because I already use Arch on my Surface, I decided to try something new this time. I installed Pop!_OS instead of Arch. The first few days were perfectly fine, however, after a few weeks, I discovered that there were a few bugs in GNOME that really annoyed me and sometimes the Pop! Shop would just randomly freeze on me. Another issue was a glitch with the launchpad menu, which caused icons to cut-off somehow. Here's how it looks like:

![screenshot](/images/rxavniiyfii71.png)

That is why today, I will be sharing my experience and more as to how I installed Arch on my Zephyrus G14. Let's get started.

First, you will want to install Arch Linux with the base linux kernel and the standard kernel headers. As a disclaimer, this tutorial is not the most comprehensive guide to install Arch. For a better and more comprehensive guide, take a look at the [Arch Wiki](https://wiki.archlinux.org/title/installation_guide) instead.

Since the G14 has a UEFI BIOS system, the installation steps below will be only for BIOSes with UEFI support instead.

## Installation Steps

### Step 1: Download the Arch Linux ISO

Head over to https://www.archlinux.org/download/ and grab the latest ISO file. You will probably want to install one from a mirror that is closest to you.

### Step 2: Create a live USB of Arch Linux

You will have to create a live USB of Arch Linux from the ISO you just downloaded.

You may use [Etcher](https://www.balena.io/etcher/) to create the live USB.

### Step 3: Boot from the live USB

> Do note that in some cases, you may not be able to boot from live USB with secure boot enabled. If that’s the case with you, disable the secure boot first. You will have to go into your BIOS settings to do so.

To boot into the Arch Linux USB, power on the G14 and hold down the ESC key during boot. After the boot logo appears, you should see a menu that comes up telling you to choose the drive that Arch Linux is on. Just choose the correct drive.

After a while, you should see random words fly all over the screen and then a bash prompt. You have successfully booted into Arch now. Give yourself a pat on your back.

<details>
<summary>Not using US keyboard? Read this</summary>
The default keyboard layout in the live session is US. While most English language keyboards will work just fine, the same cannot be true for French, German and other keyboards.

If you face difficulties, you can list out all the supported keyboard layout:

```zsh
ls /usr/share/kbd/keymaps/**/*.map.gz
```

And then change the layout to the an appropriate one using loadkeys command. For example, if you want German keyboard, this is what you’ll use:

```zsh
loadkeys de-latin1
```
</details>

Next steps include partitioning disk, creating the filesystem and mounting it.

**_Again, read all the instructions properly and follow each steps carefully. You miss one step or ignore something and you’ll have a difficult time installing Arch._**

### Step 4: Partition the disks

For partitioning the disks, we’ll use the partition manager `fdisk`.

Use this command to list all the disk and partitions on your system:

```
fdisk -l
```

**Your hard disk should be labelled /dev/sda or /dev/nvme0n1. Please use the appropriate disk labeling for your system. I am using /dev/sda because that’s more common.**

First, select the disk you are going to format and partition:

```
fdisk /dev/sda
```

I suggest that you delete any existing partitions on the disk using command **d**. Once you have the entire disk space free, it’s time to create new partitions with command **n**.

#### Check if you have UEFI mode enabled

Some steps are different for UEFI and non-UEFI systems.You should verify if you have UEFI enabled system or not. Use this command:

```
ls /sys/firmware/efi/efivars
```

If this directory exists, you have a UEFI enabled system. You should follow the steps for UEFI system. The steps that differ are clearly mentioned.

#### Create an ESP partition (For UEFI systems only)

**If you have a UEFI system**, you **must** create an EFI partition at the beginning of your disk. Otherwise, skip this step.

When you enter n, it will ask you to choose a disk number, enter 1. Stay with the default block size, when it asks for the partition size, enter +512M.

![EfI System Partition](https://i2.wp.com/itsfoss.com/wp-content/uploads/2020/01/efi_system_partition-1.png?resize=709%2C140&ssl=1)

Creating EFI System Partition | Image Credit [Sacha](https://medium.com/@sacharin0/installing-a-bootloader-in-arch-f57e6fd5a9e7)

One important steps is to change the type of the EFI partition to EFI System (instead of Linux system).

Enter **t** to change type. Enter L to see all the partition types available and then enter its corresponding number to the EFI system.

![Efi System Partition](https://i2.wp.com/itsfoss.com/wp-content/uploads/2020/01/efi_system_partition.png?resize=501%2C75&ssl=1)

Change type of EFI System Partition | Image Credit [Sacha](https://medium.com/@sacharin0/installing-a-bootloader-in-arch-f57e6fd5a9e7)

#### Create root partition

You need to create root partition _**for both UEFI and legacy systems**_.

The common partitioning practice was/is to create root, swap and home partitions separately. You may just create a single root partition and [create a swapfile](https://itsfoss.com/create-swap-file-linux/) and home under the root directory itself.

So, in this approach, we’ll have a single root partition, no swap, no home.

While you are in the fdisk command, press n to create a new partition. It will automatically give it partition number 2. This time keep on pressing enter to allocate entire remaining disk space to the root partition.

![root partition](https://i0.wp.com/itsfoss.com/wp-content/uploads/2017/11/4-root-partition.png?resize=789%2C431&ssl=1)

Picture for representational purpose only

When you are done with the disk partitioning, enter **w** command to write the changes to the disk and exit out of fdisk command.

### Step 4: Create filesystem

Now that you have your disk partitions ready, it’s time to create filesystem on it. Follow the steps for your system

#### Creating filesystem for UEFI system

So, you have two disk partitions and the first one is EFI type. Create a [FAT32 file system](https://en.wikipedia.org/wiki/File_Allocation_Table) on it using the [mkfs command](https://linuxhandbook.com/mkfs-command/):

```
mkfs.fat -F32 /dev/nvme0n1p1
```

Now create an Ext4 filesystem on the root partition:

```
mkfs.ext4 /dev/nvme0n1p2
```

#### Creating filesystem for non-UEFI system

For non-UEFI system, you only have one single root partition. So just make it ext4:

```
mkfs.ext4 /dev/sda1
```

### Step 5: Connect to WiFi

You can connect to WiFi with a utility called `iwctl`. Enter:

```
iwctl
```

There should be an interactive prompt. Type `help` to see all available commands.

Generally, for the G14, I will do something like this:

```
[iwd]# station wlan0 scan
[iwd]# station wlan0 get-networks
[iwd]# station wlan0 connect _SSID_
[iwd]# quit
```

More information on `iwctl` can be found [here](https://wiki.archlinux.org/title/Iwd#iwctl).
 
Once you are connected, check if you can use the internet by using the ping command:

```
ping google.com
```

If you get bytes in reply, you are connected. Use Ctrl+C to stop the ping reply.

### Step 6: Select an appropriate mirror

This is a big problem with installing Arch Linux. If you just go on installing it, you might find that the downloads are way too slow. In some cases, it’s so slow that the download fails.

It’s because the mirrorlist (located in /etc/pacman.d/mirrorlist) has a huge number of mirrors but not in a good order. The top mirror is chosen automatically and it may not always be a good choice.

Thankfully, there is a fix for that. First sync the pacman repository so that you can download and install software:

```
pacman -Syy
```

Now, install reflector too that you can use to list the fresh and fast mirrors located in your country:

```
pacman -S reflector
```

Make a backup of mirror list (just in case):

```
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
```

Now, get the good mirror list with reflector and save it to mirrorlist. You should change the country from US to your own country.

```
reflector -c "US" -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist
```

All good to go now.

### Step 7: Install Arch Linux

Since you have all the things ready, it’s time to finally install the Arch Linux. You’ll be installing it on the root directory so mount it first.

**Do you remember the name of the root partition**? Use it to mount it:

```
mount /dev/nvme0n1p2 /mnt
```

With root mounted, it’s time to use the wonderful [pacstrap script](https://git.archlinux.org/arch-install-scripts.git/tree/pacstrap.in) to install all the necessary packages:

```
pacstrap /mnt base linux linux-firmware neovim amd-ucode
```

It will take some time to download and install these packages. If the downloads get interrupted, no need to panic. You can run the above command once again and it resumed the download.

I have added the NeoVim text editor to the list but if you have no idea what NeoVim and Vim is, you should use `nano` instead. It will make your life much easier. I added a text editor because you’ll need to edit some files post installation.

I also added `amd-ucode`. It's just a package that contains security and stability updates for the AMD processor. [More information about it](https://wiki.archlinux.org/title/microcode).

### Step 8: Configure the installed Arch system

Generate a [fstab file](https://en.wikipedia.org/wiki/Fstab) to define how disk partitions, block devices or remote file systems are mounted into the filesystem.

```
genfstab -U /mnt >> /mnt/etc/fstab
```

Now use [arch-chroot](https://wiki.archlinux.org/index.php/Chroot#Using_arch-chroot) and enter the mounted disk as root. Actually, now you are using the just installed Arch Linux system on the disk. You’ll have to do some configuration changes to the installed system so that you could run it properly when you boot from the disk.

```
arch-chroot /mnt
```

#### Setting Timezone

To [set up timezone on Linux](https://itsfoss.com/change-timezone-ubuntu/), you can use timedatectl command. First find your time zone:

```
timedatectl list-timezones
```

And then set it up like this (replace Europe/Paris with your desired time zone):

```
timedatectl set-timezone Europe/Paris
```

#### Setting up Locale

This is what sets the language, numbering, date and currency formats for your system.

The file _**/etc/locale.gen**_ contains all the local settings and system language in a commented format.

Open the file using Neovim and uncomment (remove the # from the start of the line) the language you prefer. I have used **en\_SG.UTF-8** (English Singapore).

Now generate the locale config in /etc directory file using the below commands one by one:

```
locale-gen
echo LANG=en_SG.UTF-8 > /etc/locale.conf
export LANG=en_SG.UTF-8
```

Both locale and timezone settings can be changed later on as well when you are using your Arch Linux system.

#### Network configuration

Create a _**/etc/hostname**_ file and add the hostname entry to this file. [Hostname](https://itsfoss.com/change-hostname-ubuntu/) is basically the name of your computer on the network.

In my case, I’ll set the hostname as **_g14_**. You can choose whatever you want:

```
echo g14 > /etc/hostname
```

The next part is to edit the /etc/hosts file with NeoVim to add the following lines to it:

```
127.0.0.1      localhost
::1            localhost
127.0.1.1      g14
```

#### Set up root passwd

You should also set the password for the root account using the passwd command:

```
passwd
```

#### Create user for yourself

You should also create a new user, because it isn't good to use Linux in superuser mode.

Replace _username_ with your own desired username.

```
useradd -m -G wheel -s /bin/zsh username
```

Then, set a password for the user:

```
passwd username
```

We will also want to use sudo, so install it:

```
pacman -S sudo
```

Then, set it up by running `EDITOR=nvim visudo`. On the line that says something like:

```
## Uncomment to allow members of group wheel to execute any command
# %wheel ALL=(ALL) ALL
```

Uncomment it by removing the hashtag (#) at the beginning of the line right before %wheel.

Save and exit the file.

### Step 9: (IMPORTANT) Install the Bootloader

This is one of the crucial steps and it differs for UEFI and non-UEFI systems. As mentioned at the top of this blog post, the instructions will only be for the ASUS Zephyrus G14, which is a UEFI-based system.

I will be installing systemd-boot instead of GRUB because it is my preferred bootloader of choice. You can choose whichever bootloader you want to install.

Make sure that you are still in the arch-chroot. We need to first mount the boot partition.

Create the directory where EFI partition will be mounted:

```
mkdir /boot
```

Now, mount the ESP (Boot) partition you had created

```
mount /dev/nvme0n1p1 /boot
```

Install systemd-boot like this:

```
bootctl install
```

For the next step, you need the UUID of the root partition. Write it to the file first:

```
blkid /dev/nvme0n1p1 >>/boot/loader/entries/arch.conf >>/boot/loader/entries/arch-fallback.conf
```

After that, add a boot entry by editing /boot/loader/entries/arch.conf and put this in the file:

```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /amd-ucode.img
initrd  /initramfs-linux.img
options root=UUID=uuid-of-the-partition rw
```

Replace `uuid-of-the-partition` with the part after `UUID=` from the line at the top of the file and remove the line. Don't include the quotes. An example of a UUID should be like this: `727cac18-044b-4504-87f1-a5aefa774bda` and you would get it from `/dev/sda1: UUID="727cac18-044b-4504-87f1-a5aefa774bda" TYPE="ext3"`

You will also want a fallback bootloader entry in case something goes wrong, so add a file for that too in /boot/loader/entries/arch-fallback.conf.

```
title   Arch Linux (fallback initramfs)
linux   /vmlinuz-linux
initrd  /amd-ucode.img
initrd  /initramfs-linux-fallback.img
options root=UUID=uuid-of-the-partition rw
```

Remember to replace uuid-of-the-partition with the part after `UUID=` as mentioned above.

After you are done, update systemd-boot with the following command:

```
bootctl update
```

### Step 10: Install a window manager and other programs (i3 in this case)

I will be installing i3-gaps, a fork of i3 which brings gaps and other small tweaks that make the whole experience much more customizable and better (in my opinion). You can choose whatever desktop environment or window manager you want though, this is just my setup.

```
pacman -S xorg i3-gaps networkmanager ly gnu-free-fonts emoji-font
```

In the above command, I installed the X11 display server, [i3-gaps](https://github.com/Airblader/i3) window manager, networkmanager for managing WiFi connections, and [ly](https://github.com/nullgemm/ly) display manager for logging in to the X11 session. The fonts are so that you don't get weird boxes when you boot because Arch is so lightweight that it doesn't have fonts pre-installed. 😲

After that, you want to enable NetworkManager.service so that you can get WiFi connectivity on boot. Also enable ly so that the display manager starts on boot.

```
systemctl enable NetworkManager.service
systemctl enable ly.service
```

Now exit from chroot using the exit command:

```
exit
```

And then shutdown your system

```
poweroff
```

After that, you want to unplug the USB installation drive and power on the machine. Congrats, you have successfully installed Arch Linux! 🥳

<sup>Note: the installations above were adapted from https://itsfoss.com/install-arch-linux/.</sup>

Once you are done, just login to your system and this is what I usually do after I install Arch~

## Post-installation stuff

This section describes a general overview of what I usually do after having installed Arch. Please note that you do not need to follow anything in this section. **You should set up your system the way you like it.** After you are done, skip to [Making everything work](#Making%20everything%20work)

1. Install the yay pacman helper
2. Install yadm, rofi, alacritty, polybar, i3-scrot, betterlockscreen, brave, visual-studio-code-bin, nodejs, poetry, libreoffice, syncthing, rust
3. Fetch my dotfiles from https://github.com/raphtlw/dotfiles.git with yadm
4. Reboot
5. Set up systemd-logind, acpid, and configure everything to have a [silent boot process](https://wiki.archlinux.org/title/Silent_boot).

## Making everything work

Now, after installing Arch, we need to install the Nvidia drivers, make the mic mute button work, and have the ability to toggle between the different power modes just like you can do with the armoury crate program on Windows. We will also set up the shortcuts on the keyboard so that we can make use of fan control key and AURA key as well. I will also link the instructions to get the AniMe Matrix working.

### NVIDIA Drivers

Nvidia drivers are pretty easy. Just install the drivers:

```
pacman -S nvidia nvidia-prime
```

Now, by default, you can use `prime-run` to run any program with the dedicated nvidia GPU.

If you want to completely switch to the nvidia card and stop using the AMD integrated graphics, you can do so later after installing asusctl in the next section.

### asusctl

asusd is a utility for Linux to control many aspects of various ASUS laptops. To install it, first get the repo by editing /etc/pacman.conf and adding this to the end of the file:

```
[g14]
SigLevel = DatabaseNever Optional TrustAll
Server = https://arch.asus-linux.org
```

Then just install asusctl with

```
pacman -Sy asusctl
```

<!-- > Since the 2021 version of the Zephyrus G14 has been out for quite a while, I don't need to install the custom kernel, but if you are using a newer one, say 2022 (if it comes out), then you will need them. To install it, do:
>
> ```
> pacman -Sy linux-g14 linux-g14-headers
> ```
>
> And instead of the regular `nvidia` package, you should install `nvidia-dkms` instead.
>
> **This article will be updated with clearer instructions when the newer G14s come out. In this case, there is no need to follow these instructions.** -->

After installing asusctl, enable the asus-notify service so that you get notifications when you make a change to the settings, like fan speed, keyboard backlight, etc.

```
systemctl --user enable --now asus-notify.service
```

Reboot.

For instructions on the AniMe Matrix, you should check out [this documentation](https://asus-linux.org/asusctl/#anime-control).

`asusctl` is a command line program which intends to be the main method of interacting with `asusd`.

For the AURA key, you will need to bind `asusctl led-mode -n` to the key (FN+F4) for it to work. For the fan control key, you will need to bind `asusctl profile -n` to the key (FN+F5).

Now, we will need to get the mic mute key to work properly. On the ASUS G14, the identification of the mic mute key doesn't correspond to the value that programs look for on Linux. To get it to work, you need to remap the key.

If you have a 2021 ROG Zephyrus G14, all you need to do is this:

1. Create a file at /etc/udev/hwdb.d/90-nkey.hwdb with:

  ```
  evdev:input:b0003v0B05p19B6*
    KEYBOARD_KEY_ff31007c=f20 # x11 mic-mute
  ```
  
2. Then, update the hwdb with:

  ```
  sudo systemd-hwdb update
  sudo udevadm trigger
  ```

This should immediately make the mic mute key usable.

Congrats, you have just completed a full Arch install on the Zephyrus G14 (2021). If this article helped you in any way, or if you have any feedback, please do let me know in the comments below.

I will be posting a guide to setting up a Windows 10 VM inside Arch Linux with GPU passthrough on the G14 as well, so stay tuned for that.

✌️
