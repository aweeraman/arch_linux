# Backing up the Arch Linux Installation

## Create a partition on removable storage

```
# gdisk /dev/sdc
```

## Encrypt the backup disk

```
# cryptsetup -v --type luks --cipher aes-xts-plain64 --key-size 256 --hash sha256 --iter-time 2000 --use-urandom --verify-passphrase luksFormat /dev/sdc4
# cryptsetup open /dev/sdc4 backup
# mkfs.ext4 /dev/mapper/backup
# mount -t ext4 /dev/mapper/backup /mnt
```

## Unmount the disk

```
# umount /mnt
# cryptsetup close backup
```
