# WiFi

Archwiki: <https://wiki.archlinux.org/title/Network_configuration/Wireless>

Install drivers and regdb:

```console
sudo pacman -S iw wireless-regdb
```

List wifi devices: `iw dev`

Scan for networks (may require root): `iw dev wlan0 scan`
