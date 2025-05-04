# Mathisen's Arch Linux Guide

> A beginner-friendly guide to installing Arch Linux, with step-by-step instructions and explanations.

## Overview: What to Expect

Arch Linux installation is different from most other Linux distributions. Instead of a graphical installer with a few clicks, you'll be building your system step by step from the command line. This process gives you complete control and understanding of your system.

The installation can be broken down into these main phases:

1. **Preparation**: Download the ISO, create a bootable USB, and gather information about your hardware
2. **Boot Setup**: Boot from the USB and verify your system's boot mode (UEFI or BIOS/Legacy)
3. **Disk Preparation**: Partition and format your disk for Arch Linux (with options for dual-booting)
4. **Base Installation**: Install the core system packages
5. **System Configuration**: Set up locale, timezone, networking, and user accounts
6. **Bootloader Installation**: Install and configure GRUB to boot your system
7. **Post-Installation**: Install desktop environments, drivers, and additional software

This guide covers all these steps with beginner-friendly explanations. The entire process typically takes 1-2 hours for a first-time installation.

> **Beginner Tip**: Read through the entire guide before starting to get a feel for the process. Don't worry if you don't understand everything yet—follow along step by step, and things will become clearer as you progress.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Creating a Bootable USB](#creating-a-bootable-usb)
- [Booting from USB](#booting-from-usb)
- [Verifying Boot Mode](#verifying-boot-mode)
- [Connecting to the Internet](#connecting-to-the-internet)
- [Update System Clock](#update-system-clock)
- [Partitioning and Formatting](#partitioning-and-formatting)
  - [UEFI Systems](#uefi-systems-partitioning)
  - [BIOS/Legacy Systems](#bioslegacy-systems-partitioning)
  - [Dual-Boot with Windows](#dual-boot-with-windows)
  - [Full Disk Encryption (Optional)](#full-disk-encryption-optional)
- [Installation](#installation)
- [System Configuration](#system-configuration)
- [Bootloader Installation](#bootloader-installation)
  - [For UEFI Systems](#bootloader-for-uefi-systems)
  - [For BIOS/Legacy Systems](#bootloader-for-bioslegacy-systems)
  - [Dual-Boot Configuration](#dual-boot-configuration)
- [Finishing Installation](#finishing-installation)
- [Post-Installation](#post-installation)
- [Package Management Basics](#package-management-basics)
- [Using the AUR (Arch User Repository)](#using-the-aur-arch-user-repository)
- [Gaming on Arch Linux](#gaming-on-arch-linux)
- [Further Resources](#further-resources)

## Prerequisites

Before you begin, you'll need:

- **Arch Linux ISO**: Download the latest ISO from the [Arch Linux Download page](https://archlinux.org/download/).
- **USB Drive**: At least 2GB, which will be erased during this process.
- **Internet Connection**: Required during installation. Ethernet (wired) is usually easiest and most reliable.
- **Another Device**: To view this guide or the Arch Wiki during installation.
- **Target Machine**: The computer where Arch will be installed. 

> **What is UEFI?** UEFI (Unified Extensible Firmware Interface) is the modern replacement for the traditional BIOS. Most computers made after 2012 use UEFI. Older computers typically use BIOS (also called Legacy boot).

> **Beginner Tip**: Both UEFI and BIOS/Legacy boot modes are supported in this guide. For older computers (pre-2012), you'll likely need to follow the BIOS/Legacy instructions.

> **Beginner Tip**: Back up any important data from your target machine before proceeding. This installation will erase existing data on the drive you select.

[📚 More information in the Arch Wiki: Installation guide](https://wiki.archlinux.org/title/Installation_guide)

## Creating a Bootable USB

This step creates a USB drive that can boot your computer into the Arch Linux installation environment.

### What You're Doing
You're copying the Arch Linux operating system onto a USB drive so you can boot your computer from it instead of from your regular hard drive.

### How to Create a Bootable USB

Use a tool like dd (Linux/macOS), Rufus (Windows), or Ventoy to write the ISO to the USB drive.

Example using dd (replace /dev/sdX with your USB drive identifier - be careful!):

```bash
sudo dd bs=4M if=/path/to/archlinux-*.iso of=/dev/sdX status=progress oflag=sync
```

> **Beginner Tip**: To find your USB drive identifier on Linux, insert the USB drive and run `lsblk` in a terminal. Look for the device that matches your USB drive's size. **Be extremely careful** to select the correct drive, as this process will erase all data on the selected drive.

> **Windows Users**: Download [Rufus](https://rufus.ie/) or [Balena Etcher](https://www.balena.io/etcher/) for an easy graphical interface to create your bootable USB.

[📚 More information in the Arch Wiki: USB flash installation medium](https://wiki.archlinux.org/title/USB_flash_installation_medium)

## Booting from USB

Now you'll start your computer from the USB drive to begin the installation process.

### What You're Doing
You're telling your computer to start up using the Arch Linux system on your USB drive instead of your normal operating system.

### Steps to Boot from USB

1. Insert the USB drive into the target machine.
2. Restart your computer.
3. As soon as your computer starts, press the key to enter BIOS/UEFI settings or boot menu. This is often:
   - F2, F10, F12, Delete, or Esc key
   - On laptops, you might need to press Fn + one of these keys
   - The key varies by manufacturer - look for a message during startup or check your computer's manual
4. Select your USB drive from the boot menu, or change the boot order in BIOS settings to prioritize USB.
5. Save and exit the BIOS/UEFI settings if necessary.
6. Your computer should now boot into the Arch Linux live environment (you'll see a command prompt).

> **Beginner Tip**: If you see a command prompt starting with `root@archiso ~ #`, you've successfully booted into the Arch Linux live environment.

[📚 More information in the Arch Wiki: Installation guide - Boot the live environment](https://wiki.archlinux.org/title/Installation_guide#Boot_the_live_environment)

## Verifying Boot Mode

Let's check if your system booted in UEFI mode or BIOS/Legacy mode.

### What You're Doing
Determining which boot mode your computer is using, which will affect how we partition the disk and install the bootloader.

### Check Boot Mode

Run this command to see if you're in UEFI mode:

```bash
ls /sys/firmware/efi/efivars
```

If the command shows a list of files, you're in UEFI mode - follow the UEFI-specific instructions in this guide.

If it shows "No such file or directory," you're in BIOS/Legacy mode - follow the BIOS/Legacy-specific instructions in this guide.

> **Beginner Tip**: Most modern computers (made after 2012) support UEFI. If your computer is older, you're likely using BIOS/Legacy mode, which is perfectly fine!

[📚 More information in the Arch Wiki: Installation guide - Verify boot mode](https://wiki.archlinux.org/title/Installation_guide#Verify_boot_mode)

## Connecting to the Internet

An internet connection is required to install Arch Linux.

### What You're Doing
Connecting your computer to the internet so you can download the Arch Linux packages during installation.

### Wired Connection (Recommended for Beginners)

Ethernet connections usually work automatically. Verify by pinging a website:

```bash
ping -c 3 archlinux.org
```

If you see replies, you're connected! If not, check your cable connection.

### Wireless Connection (Wi-Fi)

If you need to use Wi-Fi, follow these steps using the `iwctl` command:

```bash
# Enter the interactive prompt
iwctl

# List your wireless devices (usually wlan0 or similar)
device list

# Scan for available networks
station wlan0 scan     # Replace wlan0 with your device name

# List available networks
station wlan0 get-networks     # Replace wlan0 with your device name

# Connect to your network
station wlan0 connect "Your Network Name"     # Replace with your network name
# Enter your password when prompted

# Exit iwctl
exit

# Verify your connection
ping -c 3 archlinux.org
```

> **Beginner Tip**: If you're having trouble with Wi-Fi, a wired ethernet connection is much more reliable for installation.

[📚 More information in the Arch Wiki: Installation guide - Connect to the internet](https://wiki.archlinux.org/title/Installation_guide#Connect_to_the_internet)

## Update System Clock

This step synchronizes your system clock.

### What You're Doing
Setting your computer's clock to the correct time, which is important for certain system functions and security.

### Set the Clock

```bash
timedatectl set-ntp true
timedatectl status     # Verify the service is active
```

You should see that "NTP service" is "active".

> **Beginner Tip**: Accurate time is important for security certificates, package verification, and many system functions.

[📚 More information in the Arch Wiki: Installation guide - Update the system clock](https://wiki.archlinux.org/title/Installation_guide#Update_the_system_clock)

## Partitioning and Formatting

This is where you prepare your disk for Arch Linux installation.

### What You're Doing
Dividing your hard drive into sections (partitions) that will hold different parts of your operating system, then formatting those partitions so they can store data.

### Identifying Your Disk

First, identify your disk by running:

```bash
fdisk -l
```

Look for your disk (e.g., `/dev/sda` or `/dev/nvme0n1`). It will likely be the one that matches your computer's hard drive size.

### UEFI Systems Partitioning

If you're using a UEFI system (as verified earlier), follow these steps:

1. Start the partitioning tool:
   ```bash
   cfdisk /dev/sdX     # Replace sdX with your disk identifier (e.g., sda, nvme0n1)
   ```

2. Inside cfdisk:
   - Select `gpt` for the label type if asked.
   - Delete any existing partitions if you want a clean install (WARNING: This erases all data).
   - Create partitions:
     - EFI System Partition: Size: 512M, Type: EFI System
     - Swap Partition (optional): Size: Equal to your RAM amount (for hibernation) or half your RAM, Type: Linux swap
     - Root Partition: Use remaining space, Type: Linux filesystem
   - Select [ Write ], type "yes" to confirm, then select [ Quit ].

3. Format the partitions:
   ```bash
   # Format the EFI partition
   mkfs.fat -F32 /dev/sdX1     # Replace sdX1 with your EFI partition

   # If you created a swap partition
   mkswap /dev/sdX2     # Replace sdX2 with your swap partition
   swapon /dev/sdX2

   # Format the root partition
   mkfs.ext4 /dev/sdX3     # Replace sdX3 with your root partition
   ```

4. Mount the partitions:
   ```bash
   # Mount root partition
   mount /dev/sdX3 /mnt     # Replace sdX3 with your root partition

   # Create boot directory and mount EFI partition
   mkdir -p /mnt/boot
   mount /dev/sdX1 /mnt/boot     # Replace sdX1 with your EFI partition
   ```

### BIOS/Legacy Systems Partitioning

If you're using a BIOS/Legacy system, follow these steps:

1. Start the partitioning tool:
   ```bash
   cfdisk /dev/sdX     # Replace sdX with your disk identifier (e.g., sda)
   ```

2. Inside cfdisk:
   - Select `dos` (MBR) for the label type if asked.
   - Delete any existing partitions if you want a clean install (WARNING: This erases all data).
   - Create partitions:
     - Boot Partition: Size: 512M, Type: Linux (83), Make it bootable (toggle "Boot" flag)
     - Swap Partition (optional): Size: Equal to your RAM or half your RAM, Type: Linux swap (82)
     - Root Partition: Use remaining space, Type: Linux (83)
   - Select [ Write ], type "yes" to confirm, then select [ Quit ].

3. Format the partitions:
   ```bash
   # Format the boot partition
   mkfs.ext4 /dev/sdX1     # Replace sdX1 with your boot partition

   # If you created a swap partition
   mkswap /dev/sdX2     # Replace sdX2 with your swap partition
   swapon /dev/sdX2

   # Format the root partition
   mkfs.ext4 /dev/sdX3     # Replace sdX3 with your root partition
   ```

4. Mount the partitions:
   ```bash
   # Mount root partition
   mount /dev/sdX3 /mnt     # Replace sdX3 with your root partition

   # Create boot directory and mount boot partition
   mkdir -p /mnt/boot
   mount /dev/sdX1 /mnt/boot     # Replace sdX1 with your boot partition
   ```

### Dual-Boot with Windows

If you want to install Arch Linux alongside an existing Windows installation, you'll need to take special care during partitioning.

#### What You're Doing
Setting up your disk to maintain your existing Windows installation while adding Arch Linux in free space.

#### Prerequisites for Dual-Booting

Before proceeding, ensure you have:
1. Backed up all important Windows data
2. Defragmented your Windows partition
3. Disabled Fast Startup in Windows
   - Open Control Panel → Power Options → Choose what the power buttons do
   - Click "Change settings that are currently unavailable"
   - Uncheck "Turn on fast startup" and save changes

#### Creating Space for Arch Linux

There are two approaches:

**Option 1: Shrink Windows partition from within Windows (Safer)**
1. In Windows, right-click the Start Menu and select "Disk Management"
2. Right-click your Windows partition (usually C:) and select "Shrink Volume"
3. Enter the amount of space to shrink (in MB) and click Shrink
4. This will create unallocated space for Arch Linux

**Option 2: Shrink Windows partition during Arch installation**
1. Boot into the Arch installation environment
2. Install required tools:
   ```bash
   pacman -Sy ntfs-3g gparted
   ```
3. Use GParted to shrink the Windows partition:
   ```bash
   gparted
   ```
   - Right-click the Windows partition
   - Select "Resize/Move"
   - Adjust the size and click "Resize/Move"
   - Click the green checkmark to apply changes

#### Partitioning for Dual-Boot (UEFI Systems)

For UEFI systems, Windows already has an EFI System Partition (ESP) that you'll reuse:

1. Identify your disk and existing partitions:
   ```bash
   fdisk -l
   ```

2. Identify the EFI System Partition (usually the first small partition, ~100-500MB, type "EFI System")

3. Create new partitions in the free space:
   ```bash
   cfdisk /dev/sdX     # Replace sdX with your disk identifier
   ```
   - Create a swap partition (optional)
   - Create a root partition for Arch in the remaining free space
   - Write changes and exit

4. Format the new partitions (but NOT the existing EFI partition):
   ```bash
   # If you created a swap partition
   mkswap /dev/sdXY     # Replace sdXY with your swap partition
   swapon /dev/sdXY

   # Format the root partition
   mkfs.ext4 /dev/sdXZ     # Replace sdXZ with your root partition
   ```

5. Mount the partitions:
   ```bash
   # Mount root partition
   mount /dev/sdXZ /mnt     # Replace sdXZ with your root partition

   # Create boot directory and mount existing EFI partition
   mkdir -p /mnt/boot
   mount /dev/sdX1 /mnt/boot     # Replace sdX1 with the existing EFI partition
   ```

#### Partitioning for Dual-Boot (BIOS/Legacy Systems)

For BIOS systems:

1. Create new partitions in the free space:
   ```bash
   cfdisk /dev/sdX     # Replace sdX with your disk identifier
   ```
   - Create a boot partition (~512MB, type Linux, bootable flag)
   - Create a swap partition (optional)
   - Create a root partition in the remaining free space
   - Write changes and exit

2. Format the new partitions:
   ```bash
   # Format boot partition
   mkfs.ext4 /dev/sdXY     # Replace sdXY with your boot partition

   # If you created a swap partition
   mkswap /dev/sdXZ     # Replace sdXZ with your swap partition
   swapon /dev/sdXZ

   # Format the root partition
   mkfs.ext4 /dev/sdXA     # Replace sdXA with your root partition
   ```

3. Mount the partitions:
   ```bash
   # Mount root partition
   mount /dev/sdXA /mnt     # Replace sdXA with your root partition

   # Create boot directory and mount boot partition
   mkdir -p /mnt/boot
   mount /dev/sdXY /mnt/boot     # Replace sdXY with your boot partition
   ```

> **Beginner Tip**: When dual-booting, be extremely careful not to format or delete any Windows partitions. Always double-check partition numbers before formatting.

> **Beginner Tip**: It's normal to see multiple partitions for Windows: EFI System Partition, MSR (Microsoft Reserved), Windows (C:), and possibly a Recovery partition. Do not modify these.

[📚 More information in the Arch Wiki: Dual boot with Windows](https://wiki.archlinux.org/title/Dual_boot_with_Windows)

### Full Disk Encryption (Optional)

For users who need enhanced security, you can encrypt your entire Arch Linux installation. This provides strong protection in case your computer is lost or stolen.

#### What You're Doing
Setting up encryption that will require a password each time you boot your computer, protecting all data on your Arch Linux installation.

> **Warning**: Full disk encryption should be set up during installation. It can't be easily added later without reinstalling.

#### LUKS Encryption for UEFI Systems

1. Partition the disk as for a regular UEFI installation, but don't format the root partition yet:
   ```bash
   # Create partitions
   cfdisk /dev/sdX     # Create an EFI partition (~512MB) and a root partition
   
   # Format the EFI partition
   mkfs.fat -F32 /dev/sdX1
   ```

2. Set up encryption on the root partition:
   ```bash
   # Create the encrypted container
   cryptsetup luksFormat /dev/sdX2     # Replace sdX2 with your root partition
   
   # Open the encrypted container
   cryptsetup open /dev/sdX2 cryptroot
   ```

3. Format and mount the encrypted partition:
   ```bash
   # Format the encrypted partition
   mkfs.ext4 /dev/mapper/cryptroot
   
   # Mount the filesystems
   mount /dev/mapper/cryptroot /mnt
   mkdir -p /mnt/boot
   mount /dev/sdX1 /mnt/boot
   ```

4. Continue with the installation as normal until you reach the bootloader installation.

5. Before installing the bootloader, configure the system to unlock the encryption during boot:
   ```bash
   # Edit the mkinitcpio configuration
   nano /etc/mkinitcpio.conf
   ```
   
   Find the `HOOKS` line and add `encrypt` before `filesystems`:
   ```
   HOOKS=(base udev autodetect modconf block encrypt filesystems keyboard fsck)
   ```
   
   Regenerate the initramfs:
   ```bash
   mkinitcpio -P
   ```

6. Configure the bootloader (GRUB) to handle encryption:
   ```bash
   # Edit GRUB configuration
   nano /etc/default/grub
   ```
   
   Add to or modify the `GRUB_CMDLINE_LINUX` line:
   ```
   GRUB_CMDLINE_LINUX="cryptdevice=UUID=device-UUID:cryptroot root=/dev/mapper/cryptroot"
   ```
   
   Get your device UUID with:
   ```bash
   blkid -s UUID -o value /dev/sdX2     # Replace sdX2 with your encrypted root partition
   ```
   
   Then regenerate the GRUB configuration:
   ```bash
   grub-mkconfig -o /boot/grub/grub.cfg
   ```

> **Beginner Tip**: Encryption adds security but also a bit more complexity. Make sure to remember your encryption password - if you forget it, your data will be permanently inaccessible!

[📚 More information in the Arch Wiki: Dm-crypt/Encrypting an entire system](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system)

## Installation

Now you'll install Arch Linux onto your prepared disk.

### What You're Doing
Downloading and installing the basic Arch Linux system onto your prepared partitions.

### Select Mirrors (Optional but Recommended)

This step helps speed up your download by choosing faster mirrors near you:

```bash
# Install reflector
pacman -Sy reflector

# Select mirrors (change 'YourCountry' to your country)
reflector --country 'YourCountry' --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
# Example: reflector --country 'United States' --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

### Install Essential Packages

Now install the basic system:

```bash
pacstrap /mnt base linux linux-firmware nano     # Add networkmanager if you need Wi-Fi
```

What these packages are:
- `base`: Core system utilities
- `linux`: The Linux kernel (the core of the operating system)
- `linux-firmware`: Drivers for various hardware
- `nano`: A simple text editor (essential for editing configuration files)

> **Beginner Tip**: This step may take some time depending on your internet speed - be patient!

### Generate File System Table (fstab)

This file tells Arch Linux where to find all the partitions you created:

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

Verify the generated file looks correct:

```bash
cat /mnt/etc/fstab
```

You should see entries for all the partitions you created.

[📚 More information in the Arch Wiki: Installation guide - Install essential packages](https://wiki.archlinux.org/title/Installation_guide#Install_essential_packages)

## System Configuration

Now you'll set up your new Arch Linux system.

### What You're Doing
Configuring your newly installed system with basic settings like language, timezone, and networking.

### Enter Your New System

First, you need to switch from the installation environment to your newly installed system:

```bash
arch-chroot /mnt
```

Your prompt should change, indicating you're now working inside your new Arch Linux system.

### Set Time Zone

Tell your system where you're located geographically for correct time:

```bash
# Replace Region/City with your timezone
# Examples: America/New_York, Europe/London, Asia/Tokyo
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime

# Generate /etc/adjtime
hwclock --systohc
```

### Localization (Language Settings)

Set up your system's language:

```bash
# Edit the locale configuration file
nano /etc/locale.gen
```

Find and uncomment (remove the # from) your preferred locale, for example:
- `en_US.UTF-8 UTF-8` for US English
- `de_DE.UTF-8 UTF-8` for German
- `es_ES.UTF-8 UTF-8` for Spanish
- `fr_FR.UTF-8 UTF-8` for French

Press Ctrl+O then Enter to save, and Ctrl+X to exit nano.

Generate the locales:

```bash
locale-gen
```

Create and set your language in the locale configuration:

```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf     # Replace with your chosen locale
```

### Network Configuration

Set your computer's name (hostname):

```bash
echo "myhostname" > /etc/hostname     # Replace myhostname with a name for your computer
```

Configure the hosts file:

```bash
nano /etc/hosts
```

Add the following lines:
```
127.0.0.1   localhost
::1         localhost
127.0.1.1   myhostname.localdomain  myhostname     # Replace myhostname with your hostname
```

Press Ctrl+O then Enter to save, and Ctrl+X to exit nano.

Install and enable network management:

```bash
pacman -S networkmanager
systemctl enable NetworkManager     # This makes it start automatically after reboot
```

> **Beginner Tip**: NetworkManager will let you easily connect to Wi-Fi after installation using either the command line or a graphical interface once you install one.

### Set Root Password

Set a password for the root (administrator) account:

```bash
passwd
```

Enter a strong password when prompted. You won't see anything as you type - this is normal.

> **Beginner Tip**: This is your system's "master" password. Make it strong and don't forget it!

[📚 More information in the Arch Wiki: Installation guide - Configure the system](https://wiki.archlinux.org/title/Installation_guide#Configure_the_system)

## Bootloader Installation

A bootloader is software that starts your operating system when you turn on your computer.

### What You're Doing
Installing software that will allow your computer to properly start Arch Linux when you power it on.

### Bootloader for UEFI Systems

If you're using a UEFI system, follow these steps to install GRUB:

```bash
# Install necessary packages
pacman -S grub efibootmgr

# Install GRUB to the EFI partition
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=ARCH

# Generate GRUB configuration
grub-mkconfig -o /boot/grub/grub.cfg
```

### Bootloader for BIOS/Legacy Systems

If you're using a BIOS/Legacy system, follow these steps to install GRUB:

```bash
# Install necessary packages
pacman -S grub

# Install GRUB to the MBR
grub-install --target=i386-pc /dev/sdX     # Replace sdX with your disk (e.g., /dev/sda, NOT /dev/sda1)

# Generate GRUB configuration
grub-mkconfig -o /boot/grub/grub.cfg
```

### Dual-Boot Configuration

If you're dual-booting with Windows, you need additional configuration to detect and boot Windows.

#### What You're Doing
Setting up GRUB to detect Windows and present a boot menu that lets you choose between Arch Linux and Windows.

#### Installing Necessary Packages

Install needed packages for Windows detection and NTFS support:

```bash
pacman -S os-prober ntfs-3g
```

The `os-prober` utility detects other operating systems, and `ntfs-3g` provides support for Windows NTFS partitions.

#### Enable OS Prober in GRUB

GRUB disables OS detection by default for security. You need to enable it:

```bash
# Edit the GRUB configuration
nano /etc/default/grub
```

Add or uncomment this line:
```
GRUB_DISABLE_OS_PROBER=false
```

Save and exit (Ctrl+O, then Enter, Ctrl+X).

#### Regenerate GRUB Configuration

Now regenerate the GRUB configuration to detect Windows:

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

You should see output that includes:
```
Found Windows Boot Manager on /dev/sdXY
```

If Windows isn't detected, try:

```bash
# Make sure all partitions are mounted
mkdir -p /mnt/windows
mount /dev/sdXY /mnt/windows     # Replace sdXY with your Windows partition
os-prober
umount /mnt/windows
grub-mkconfig -o /boot/grub/grub.cfg
```

#### Fixing Time Differences

Windows and Linux handle the hardware clock differently, causing time to be incorrect when switching between systems:

```bash
# Configure Linux to use local time (like Windows)
timedatectl set-local-rtc 1
```

Alternatively, you can configure Windows to use UTC (like Linux) by creating a registry entry in Windows.

> **Beginner Tip**: After completing the installation, you'll see a GRUB menu at boot that lets you choose between Arch Linux and Windows.

> **Important Note**: Windows updates might occasionally overwrite the bootloader. If this happens, boot from your Arch USB and reinstall GRUB using the troubleshooting steps.

[📚 More information in the Arch Wiki: GRUB - Windows installed in UEFI mode](https://wiki.archlinux.org/title/GRUB#Windows_installed_in_UEFI_mode)
[📚 More information in the Arch Wiki: System time - UTC in Windows](https://wiki.archlinux.org/title/System_time#UTC_in_Windows)

## Finishing Installation

You've completed the main installation steps! Now it's time to exit and reboot into your new system.

### Exit and Reboot

```bash
# Exit the chroot environment
exit

# Unmount all partitions
umount -R /mnt

# If you activated swap, turn it off
swapoff /dev/sdX2     # Replace sdX2 with your swap partition

# Reboot your system
reboot
```

Don't forget to remove your USB drive when the computer restarts.

> **Beginner Tip**: After rebooting, you'll be presented with a login prompt. Log in with username "root" and the password you set earlier.

[📚 More information in the Arch Wiki: Installation guide - Reboot](https://wiki.archlinux.org/title/Installation_guide#Reboot)

## Post-Installation

Congratulations on installing Arch Linux! Here are the first steps you should take after installation.

### What You're Doing
Setting up a regular user account and essential software to make your system usable.

### First Login

You should now boot into your minimal Arch Linux command-line system. Log in as root with the password you set during installation.

### Creating a Regular User

It's not good practice to use the root account for everyday tasks. Let's create a regular user account:

```bash
# Create a new user and add to wheel group (for admin privileges)
useradd -m -G wheel yourusername     # Replace yourusername with your preferred username

# Set a password for your new user
passwd yourusername
```

### Install and Configure sudo

Sudo allows your regular user to perform administrative tasks when needed:

```bash
# Install sudo
pacman -S sudo

# Configure sudo to allow wheel group members
EDITOR=nano visudo
```

Find and uncomment (remove the # from) this line:
```
%wheel ALL=(ALL:ALL) ALL
```

Save and exit (Ctrl+O, then Enter, Ctrl+X).

Now log out and log back in as your new user:

```bash
exit
```

Log in with your new username and password.

### Gaming on Arch Linux

Arch Linux is an excellent platform for gaming, with strong support for both native Linux games and Windows games.

#### What You're Doing
Setting up your system for gaming, including support for Steam games and Windows games.

#### Enabling the Multilib Repository

Most games require 32-bit libraries even on 64-bit systems. You must enable the multilib repository first:

```bash
# Edit pacman.conf
sudo nano /etc/pacman.conf
```

Find these lines (around line 90-93) and uncomment them (remove the # at the beginning):
```
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

So they look like this:
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```

Save the file (Ctrl+O, then Enter) and exit (Ctrl+X).

Then update your package database:
```bash
sudo pacman -Sy
```

#### Installing Steam

Steam provides access to thousands of Linux-native games and Windows games (via Proton):

```bash
# Install Steam
sudo pacman -S steam
```

Launch Steam from your application menu and log in to your account.

#### Graphics Drivers for Gaming

Make sure you have the proper graphics drivers installed (as covered in the GPU drivers section). For gaming, the proprietary NVIDIA drivers or Mesa for AMD/Intel are recommended.

#### Gaming with Non-Steam Games using Bottles

Bottles is a modern, user-friendly tool for running Windows games and applications:

```bash
# Install Bottles
sudo pacman -S bottles
```

Using Bottles:
1. Launch Bottles from your application menu
2. Create a new bottle (Windows environment)
3. Choose a gaming-optimized configuration
4. Install your games through Bottles or point it to existing game installations

Bottles provides a convenient interface for managing different Windows environments and automatically configures Wine, DXVK, and other components for best performance.

#### Additional Gaming Tools

For a complete gaming setup, consider these additional packages:

```bash
# Game controller support
sudo pacman -S gamemode     # Optimizes system performance while gaming
sudo pacman -S steam-native-runtime     # Native runtime for better performance
sudo pacman -S mangohud     # In-game performance overlay
```

> **Beginner Tip**: For the best gaming experience, make sure to enable the multilib repository to access 32-bit libraries needed by many games.

[📚 More information in the Arch Wiki: Steam](https://wiki.archlinux.org/title/Steam)
[📚 More information in the Arch Wiki: Wine](https://wiki.archlinux.org/title/Wine)

## Package Management Basics

Learning how to manage packages is essential for maintaining your Arch Linux system.

#### What You're Doing
Learning the core commands for installing, updating, and managing software on your Arch Linux system.

#### Core Pacman Commands

Pacman is Arch Linux's package manager. Here are the most important commands:

```bash
# Update package database and upgrade all packages
sudo pacman -Syu

# Search for a package
sudo pacman -Ss package_name

# Install a package
sudo pacman -S package_name

# Remove a package
sudo pacman -R package_name

# Remove a package and its dependencies not needed by other packages
sudo pacman -Rs package_name

# Get information about a package
sudo pacman -Si package_name     # For remote packages
sudo pacman -Qi package_name     # For installed packages

# List installed packages
sudo pacman -Q

# Find which package owns a file
sudo pacman -Qo /path/to/file

# Clean the package cache to free disk space
sudo pacman -Sc     # Remove unused packages
sudo pacman -Scc    # Remove all cached packages (not recommended)
```

#### Package Groups

Pacman supports package groups for related software:

```bash
# List available groups
sudo pacman -Sg

# Install all packages in a group
sudo pacman -S gnome     # Installs all packages in the GNOME group
```

> **Beginner Tip**: Before running a system update with `pacman -Syu`, always check the [Arch News](https://archlinux.org/news/) for important announcements about potential issues.

[📚 More information in the Arch Wiki: Pacman](https://wiki.archlinux.org/title/Pacman)

## Using the AUR (Arch User Repository)

The Arch User Repository (AUR) contains user-contributed packages not found in the official repositories.

#### What You're Doing
Learning how to access a large repository of community-maintained packages that greatly expand the software available for your system.

#### Understanding the AUR

The AUR is not directly accessible through pacman. You'll need an AUR helper like `yay` or manually build packages.

> **IMPORTANT WARNING FOR BEGINNERS**: The AUR contains user-maintained packages, not officially supported software. Unlike the official repositories, AUR packages:
>
> - Can break your system if poorly maintained
> - Might contain security vulnerabilities or even malicious code
> - Can suddenly stop working if maintainers abandon them
> - Often require manual intervention during updates
>
> **Do not install excessive numbers of AUR packages**, especially as a beginner. Each AUR package adds maintenance overhead and increases the risk of system problems. Whenever possible, prefer packages from the official repositories.
>
> Always check the comments on AUR package pages and review the PKGBUILD files before installing. Never blindly install AUR packages recommended online without understanding what they do.

#### Installing an AUR Helper (yay)

`yay` is one of the most popular AUR helpers, providing an interface similar to pacman:

```bash
# Install git (needed to download yay)
sudo pacman -S git

# Clone the yay repository
git clone https://aur.archlinux.org/yay.git

# Build and install yay
cd yay
makepkg -si
cd ..
```

#### Using yay

Once installed, yay works similarly to pacman:

```bash
# Update all packages (including AUR)
yay -Syu

# Search for packages (including AUR)
yay -Ss package_name

# Install a package (including AUR)
yay -S package_name

# Remove a package and its dependencies
yay -Rs package_name
```

#### Manual AUR Package Installation

If you prefer not to use an AUR helper (recommended for better understanding):

```bash
# Download the package build files
git clone https://aur.archlinux.org/package_name.git

# Enter the directory
cd package_name

# Review the PKGBUILD (important for security!)
less PKGBUILD

# Build and install the package
makepkg -si
```

> **Beginner Tip**: For your first few months with Arch Linux, try to limit yourself to no more than 5-10 AUR packages total. This will help you learn proper system maintenance and avoid dependency nightmares.

[📚 More information in the Arch Wiki: Arch User Repository](https://wiki.archlinux.org/title/Arch_User_Repository)

## Further Resources

- [Arch Linux Wiki](https://wiki.archlinux.org/) - The most comprehensive source of Arch Linux information
- [Arch Linux Forums](https://bbs.archlinux.org/) - Get help from the Arch community
- [r/archlinux](https://www.reddit.com/r/archlinux/) - Reddit community for Arch Linux

---

*This guide is for reference only. Commands may need to be adjusted for your specific hardware and requirements.*