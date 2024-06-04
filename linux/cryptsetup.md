# Cryptsetup

## Create encrypted image/file

1. Create the container:
   ```console
   $ fallocate -l 512M cryptfile.img
   ```
2. Setup LUKS and filesystem:
   ```console
   $ cryptsetup luksFormat cryptfile.img
   $ udisksctl loop-setup -f cryptfile.img
   Mapped file cryptfile2.img as /dev/loop0.
   $ udisksctl unlock -b /dev/loop0
   Unlocked /dev/loop0 as /dev/dm-0.
   $ sudo mkfs.fat /dev/dm-0
   mkfs.fat 4.2 (2021-01-31)
   ```
   (can't figure out how to `mkfs` without root..)

## Unlock and mount

```console
$ udisksctl loop-setup -f cryptfile.img
Mapped file cryptfile2.img as /dev/loop0.
$ udisksctl unlock -b /dev/loop0
Unlocked /dev/loop0 as /dev/dm-0.
$ udisksctl mount -b /dev/dm-0
Mounted /dev/dm-0 at /run/media/weedz/E47C-E412
```

## Unmount and lock

```console
$ udisksctl unmount -b /dev/dm-0
Unmounted /dev/dm-0.
$ udisksctl lock -b /dev/loop0
Locked /dev/loop0.
$ udisksctl loop-delete -b /dev/loop0
```
