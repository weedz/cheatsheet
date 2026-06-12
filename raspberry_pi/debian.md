# Install debian

## Flash image

Images: <https://cloud.debian.org/images/cloud/> (eg. <https://cloud.debian.org/images/cloud/trixie/20260601-2496/>)

Untar/zip/xz the archive and flash image (could probably be one command..):

```console
xz -d -k <image-name>.tar.xz
tar xf <image-name>.tar
sudo dd of=/dev/<device> if=disk.raw bs=64k oflag=dsync status=progress
```

## First startup

_!!Make sure to enter a root password!!_

1. Add user: `useradd -m <user>`
2. Add user to sudo: `usermod -aG sudo <user>`

For additional keymaps: `apt install console-data console-setup`

## Storage (might be optional)

The resulting partition might be only 3GB. To resize:
1. Install `cloud-utils` and `e2fsprogs` (needed for `growpart` and `resize2fs` commands)
2. Find your device and partition, run `lsblk`
3. Run `growpart /dev/<device> <partition number>`
4. Run `resize2fs /dev/<partition>`

Example:
```
$ sudo su
# lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
mmcblk1     179:0    0 59.5G  0 disk
|-mmcblk1p1 179:1    0  508M  0 part /boot/firmware
`-mmcblk1p2 179:2    0   59G  0 part /
# growpart mmcblk1 2
# resize2fs /dev/mmcblk1p2
```

## Network

NetworkManager is easy and "just works". Install with `apt install network-manager` (will probably auto-install stuff needed for wifi, wpa-supplicant etc.)
Setup networks with `nmtui`.
