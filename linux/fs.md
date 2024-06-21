# FS

"Eject" / "Safely remove USB" (source: <https://unix.stackexchange.com/a/178648>):

I would recommend first to unmount all filesystems on that usb. This can be done also with `udisksctl`, so steps would be:

```console
udisksctl unmount -b /dev/sda1
udisksctl power-off -b /dev/sda
```

If you are not using `systemd` then old good `udisks` should work:

```console
udisks --unmount /dev/sda1
udisks --detach /dev/sda
```
