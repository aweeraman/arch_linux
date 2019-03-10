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

## A simple script that can be used to perform a full system backup

```
#!/bin/bash
echo "Starting backup: `date`" >> ~/backup.log
cryptsetup open /dev/sdc4 backup
mount /dev/mapper/backup /backup
rsync -aAXv --delete --exclude=/dev/*        \
                     --exclude=/proc/*       \
                     --exclude=/sys/*        \
                     --exclude=/tmp/*        \
                     --exclude=/run/*        \
                     --exclude=/mnt/*        \
                     --exclude=/backup/*     \
                     --exclude=/lost+found/* \
                     --exclude="Downloads"   \
                     --exclude=.cache        \
                     --exclude=/shared/*     / /backup >> ~/backup.log 2>&1
umount /backup
cryptsetup close backup
echo "Ending backup: `date`" >> ~/backup.log
```

## To restore

```
# rsync -aAXv --dry-run --delete --exclude="lost+found" --exclude="boot" --exclude="var" /backup/ /arch/
```
