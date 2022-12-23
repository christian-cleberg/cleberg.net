+++
date = 2022-12-23
title = "Alpine Linux as a Desktop OS"
description = ""
draft = true
+++

## Isn't Alpine Linux for Servers?

<TODO>

## Installation

<TODO> - [my other alpine post](/blog/alpine-linux/)

```sh
setup-alpine
```

## Initial Setup

Login is as root intially or `su` to root once you login as your user. From here, you should start by updating and upgrading the system just in case the ISO was not fully up-to-date.

```sh
# Update and upgrade system
apk -U update && apk -U upgrade

# Add necessary packages
apk add linux-firmware iwd doas nano git curl wget brightnessctl

# Add yourself to the wheel group so you can use the doas command
apk add $USER wheel
```


### Window Manager (Desktop)

The [Sway installation guide](https://wiki.alpinelinux.org/wiki/Sway) has everything you need to get Sway working on Alpine.

```sh
apk add eudev
setup-devd udev
# Since I have Radeon graphics, I need the following packages
apk add mesa-dri-gallium mesa-va-gallium
adduser $USER input
adduser $USER video
apk add ttf-dejavu
apk add seatd
rc-update add seatd
rc-service seatd start
adduser $USER seat
apk add sway sway-doc
apk add                \ # Install optional dependencies:
    xwayland             \ # recommended for compatibility reasons
    foot                 \ # default terminal emulator
    bemenu               \ # wayland menu
    swaylock swaylockd   \ # lockscreen tool
    swaybg               \ # wallpaper daemon
    swayidle               # idle management (DPMS) daemon
```

```sh
nano ~/.profile
```

```sh
# ~/.profile
if test -z "${XDG_RUNTIME_DIR}"; then
  export XDG_RUNTIME_DIR=/tmp/$(id -u)-runtime-dir
  if ! test -d "${XDG_RUNTIME_DIR}"; then
    mkdir "${XDG_RUNTIME_DIR}"
    chmod 0700 "${XDG_RUNTIME_DIR}"
  fi
fi
```

```sh
dbus-run-session -- sway
```

### WiFi Issues

To solve the WiFi issues, I did two main things:

1. Upgrade to the `edge` version of Alpine as I assume the `linux-lts` kernel of 5.15 had issues with my device/drivers.
2. Enable the `NameResolvingService=resolvconf` in `iwd`.

#### Upgrade to Edge Repos

```sh
nano /etc/apk/repositories
```

```conf
#/media/sda/apks
#http://mirrors.gigenet.com/alpinelinux/v3.17/main
#http://mirrors.gigenet.com/alpinelinux/v3.17/community
http://mirrors.gigenet.com/alpinelinux/edge/main
http://mirrors.gigenet.com/alpinelinux/edge/community
#http://mirrors.gigenet.com/alpinelinux/edge/testing
```

```sh
apk -U update
apk add --upgrade apk-tools
apk upgrade --available
sync
reboot
```

#### Enable WiFi Resolving Service

```sh
doas nano /etc/iwd/main.conf
```

```conf
[Network]

NameResolvingService=resolvconf
```
