# Arch-Linux-Installation-Documentation
**DISCLAIMER:** All of the documentation below led to a failed boot. After following through all of the steps, the UTM VM did not boot properly. I used the x86 emulation over the ARM version, which did not work.
## Setting Up the VM
- Accessed the Docker issues for Arch (download the very top one)
	- [Link](https://gitlab.archlinux.org/archlinux/archlinux-docker/-/issues)
- Visited the Docker hub for Arch
	- [Link](https://hub.docker.com/_/archlinux/)
- Visited [this GitHub repository](https://github.com/jellydn/arch-linux-on-m2/tree/main?tab=readme-ov-file) as a workaround for using Mac
- Selected generic Other Linux distro
- Set memory to 2048 MB
## 1.1 Acquiring an Installation Image
- Visited https://mirror.clarkson.edu/archlinux/iso/2025.11.01/
	- Downloaded [archlinux-2025.11.01-x86_64.iso](https://mirror.clarkson.edu/archlinux/iso/2025.11.01/archlinux-2025.11.01-x86_64.iso)``
- Created an emulator VM on UTM using the official Arch Linux ISO image
## Boot Setup
- 
## 1.6 Verifying the boot mode
- Typed the command `cat /sys/firmware/efi/fw_platform_size` to verify the boot mode
- Received 64, verifying that the boot mode is UEFI and has a 64-bit x64 UEFI
## 1.7 Connecting to the Internet
- Typed in the command `ip link` 
- Typed in the command `ping ping.archlinux.org`, verifying connection to the internet
## 1.8 Updating System Clock
- Typed in the command `timedatectl` to sync the time upon connecting to the internet
## 1.9 Partitioning the Disk
- Typed in the command `fdisk /dev/sda`
- Created a primary sda partition with 489.7 MiB called "sda1" (2048 - 1,005,000)
- Changed sda1 type to GPT using the alias `uefi`
- Created a second primary partition with 31.5 GB (1,00,5001 - 67,108,863)
- Changed the first partition type to uefi (hex code "EF")

```shell
fdisk /dev/sda
n
p
1
[enter]
+500M

n
p
2
[enter][enter][enter]

t
1
EF

w
```
## 1.10 Formatting the Partition
- Typed in the command mkfs.ext4 /dev/sda
- Typed in the command mkfs.fat -F 32 /dev/sda
```shell
# Format primary linux partition
mkfs.ext4 /dev/sda2

# Format EFI partition
mkfs.fat -F 32 /dev/sda1
```
## 1.11 Mounting the File Systems
- Ran the following command to mount the file systems
```
mount /dev/sda2 /mnt
```
- Used the UEFI mounting command:
  ```
  mount --mkdir /dev/sda1 /mnt/boot
  ```
## 2.1 Installation
- Selected mirror https://mirror.archlinuxarm.org
## 2.2 Install Essential Packages
- Installed Linux firmware using the command
  ```
  pacstrap -K /mnt base linux linux-firmware
  ```
## 3.1 Fstab
- Ran the following command to find the file mountpoints
```
findmnt -A
```
- Used the following command to generate an fstab file
```
genfstab -U /mnt >> /mnt/etc/fstab
```
## 3.2 Chroot
- Installed bash using the command
  ```
  pacstrap /mnt bash
  ```
- Changed root into the new system using this command:
  ```
  arch-chroot /mnt
  ```
## 3.5 Install (More) Essential Packages
```
pacman -Syu networkmanager
```
- Generated a nano file using the command
  ```
  nano /etc/hostname
  ```
- Set password \[REDACTED]
## 3.8 Boot Loader
- Used command `pacman -Syu grub`
- Used command `pacman -Syu efibootmgr`
- Used command 
  ```
  grub-install -target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
  ```
  - Used command to generate GRUB config file
    ```
	grub-mkconfig -o /boot/grub/grub.cfg
    ```
## Rebooting into ISO and troubleshooting (potentially re-partition drives and label the boot drive as BIOS)
*Set root partition type as GPT*
```shell
# Remount
mount /dev/sda2 /mnt
mount --mkdir /dev/sda1 /mnt/boot

# Enter chroot environment
arch-chroot /mnt
```
# ATTEMPT 2: VMware Fusion Arm Boot
Here, I went through Arch Boot and followed the above documentation once again. However, this time, I only went through a series of dialogue boxes to install Arch using the ARM version.
- Ran the command to install zsh
  ```
  pacman -S zsh
  ```
  However, after running echo $SHELL, I only saw bash as the default shell.
- Ran the command to install SSH
  ```
  sudo pacman -Sy openssh
  ```
- Added alias
  ```
  alias la="ls -a"
  ```
