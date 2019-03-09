# Arch Linux Installation

## Create bootable USB disk for installation

```
$ lsblk
$ dd if=archlinux.iso of=/dev/sdX
```

## Boot into Arch Linux USB

### Configure network and set the time

```
# wifi-menu
# timedatectl set-ntp true
```

###  Partition the target disk
```
$ lsblk
$ fdisk /dev/sdX
```

Create the boot, root and swap drives.

Check if you have a computer that requires UEFI by looking for the existence of the following:

```
$ ls /sys/firmware/efi/efivars
```

If so, the steps for installation are a little different, mostly from the partitioning standpoint.

## Create the file systems on the partitions

```
# mkfs.ext4 /dev/sdXX
# mkswap /dev/sdXX
```

## Mount the partitions
```
# mount /dev/sdXX /mnt
# mkdir /mnt/boot && mnt /dev/sdXX /mnt/boot
# swapon /dev/sdXX

## Bootstrap the Arch Linux installation

```
# pacstrap /mnt base base-devel vim
# genfstab -U /mnt >> /mnt/etc/fstab
```

You may need to edit fstab to remove redundant swap partitions.

## Configure the network

```
# arch-chroot /mnt
# pacman -S networkmanager grub
# systemctl enable NetworkManager
```

## Install the bootloader

```
# grub-install --target=i386-pc /dev/sdX
# grub-mkconfig -o /boot/grub/grub.cfg
```

## Set the root password

```
# passwd
```

## Configure and generate the locale and timezone

```
# vim /etc/locale.gen
```
 
Uncomment the required locales and run the generator

```
# locale-gen
# echo "LANG=en-US.UTF-8" > /etc/locale.conf
```

Link /etc/localtime to /usr/share/zoneinfo

```
# ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```

## Set the hostname

```
echo "marmot" > /etc/hostname
```

# Wrap up

```
# exit
# umount -R /mnt
# reboot
```
