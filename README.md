# AKRI

[![App Status](https://argocd-internal.spirit-dev.net/api/badge?name=akri-turingpi&revision=true&showAppName=true)](https://argocd-internal.spirit-dev.net/applications/akri-turingpi)

## Table of content

[[_TOC_]]

## UDEV rules

> udev rules are meant for USB devices

run first the command `lsusb` to list all usb devices like follow

```shell
#> root@turing-node-4:~# lsusb
[...]
Bus 004 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 004 Device 004: ID 0764:0601 Cyber Power System, Inc. PR1500LCDRT2U UPS
[...]
```

note the Bus number and the Device id. Next run the folowing command to get more information about the device you want to add to your config combine them into `/dev/bus/usb/<bus>/<device>`, which in this example is: `/dev/bus/usb/004/004`.

```shell
#> udevadm info --attribute-walk --path=$(udevadm info --query=path /dev/bus/usb/<bus>/<device>

  looking at device '/devices/platform/fe180000.pcie/pci0001:30/0001:30:00.0/0001>
    KERNEL=="4-1.1"
    SUBSYSTEM=="usb"
    DRIVER=="usb"

  looking at device '/devices/platform/fe180000.pcie/pci0001:30/0001:30:00.0/0001>
    KERNEL=="4-1.1"
    SUBSYSTEM=="usb"
    DRIVER=="usb"
    [...]
    ATTR{devpath}=="1.1"
    ATTR{devspec}=="(null)"
    ATTR{idProduct}=="0601"
    ATTR{idVendor}=="0764"
    [...]
```

Based on this output, you can generate the `udevRules`

for example:

```yaml
udevRules:
  - ATTRS{idVendor}=="0764", ATTRS{idProduct}=="0601"
```

## UDEV commands

Here are some usefull udev commands:

```shell
# List usb devices attached
#> lsusb

# Detail a specific usb device
#> lsusb -vd PPPP:VVVV - detail by product and vendor id

# Reset a usb device like if pull out, pull back in the cord
#> usbreset PPPP:VVVV - reset by product and vendor id
```

## Resources

- [AKRI Documentation](https://docs.akri.sh/user-guide/customizing-an-akri-installation)

## Installation process

The installation is entirely managed by Argocd.

A `Makefile` is present here to ease the first and one-time deployment or in case of an issue.
The installation should be done in two steps:

```shell
#> make dry-run ENV=<ENV>
#> make install ENV=<ENV>
```
