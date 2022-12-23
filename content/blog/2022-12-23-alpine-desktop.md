+++
date = 2022-12-23
title = "Alpine Linux as a Desktop OS"
description = "Documenting my steps to a useable Alpine Linux desktop experience."
draft = true
+++

## Isn't Alpine Linux for Servers?

<TODO>

## Installation

<TODO> - [my other alpine post](/blog/alpine-linux/)

```sh
setup-alpine
```

Once you have gone through all of the options and installer finishes without 
errors, reboot.

```sh
reboot
```

## Initial Setup

Once Alpine is installed and the machine has rebooted, login is as root 
intially or `su` to root once you login as your user. From here, you should 
start by updating and upgrading the system in case the ISO was not fully 
up-to-date.

```sh
# Update and upgrade system
apk -U update && apk -U upgrade

# Add an editor so we can enable the community repository
apk add nano
```

You need to un-comment the `community` repository for your version of Alpine 
Linux. For v3.17, the repositories file should look like this:

```sh
nano /etc/apk/repositories
```

```conf
#/media/sda/apks
http://mirrors.gigenet.com/alpinelinux/v3.17/main
http://mirrors.gigenet.com/alpinelinux/v3.17/community
#http://mirrors.gigenet.com/alpinelinux/edge/main
#http://mirrors.gigenet.com/alpinelinux/edge/community
#http://mirrors.gigenet.com/alpinelinux/edge/testing
```

```sh
## Add the rest of the packages
apk add linux-firmware iwd doas git curl wget

# Add yourself to the wheel group so you can use the doas command
adduser $USER wheel
```

### Window Manager (Desktop)

The [Sway installation guide](https://wiki.alpinelinux.org/wiki/Sway) has 
everything you need to get Sway working on Alpine.

However, I'll include a brief list of the commands I ran and their purpose for 
posterity here.

```sh
# Add eudev and set it up
apk add eudev
setup-devd udev
# Since I have Radeon graphics, I need the following packages
apk add mesa-dri-gallium mesa-va-gallium
# Add user to applicable groups
adduser $USER input
adduser $USER video
# Add a font package
apk add ttf-dejavu
# Add the seatd daemon
apk add seatd
rc-update add seatd
rc-service seatd start
# Add user to seat group
adduser $USER seat
# Add elogind
apk add elogind polkit-elogind
rc-update add elogind
rc-service elogind start
# Finally, add sway and dependencies
apk add sway sway-doc
apk add                \ # Install optional dependencies:
    xwayland             \ # recommended for compatibility reasons
    foot                 \ # default terminal emulator
    bemenu               \ # wayland menu
    swaylock swaylockd   \ # lockscreen tool
    swaybg               \ # wallpaper daemon
    swayidle               # idle management (DPMS) daemon
```

To finish the `elogind` piece, I needed to enable suspend on lid close for my 
laptop:

```sh
nano /etc/elogind/logind.conf
```

```conf
[Login]
HandlePowerKey=suspend 
```

Once you have the packages installed and set-up, you need to export the 
`XDG_RUNTIME_DIR` upon login. To do this, edit your `.profile` file.

```sh
nano ~/.profile
```

Within the file, paste this:

```sh
if test -z "${XDG_RUNTIME_DIR}"; then
  export XDG_RUNTIME_DIR=/tmp/$(id -u)-runtime-dir
  if ! test -d "${XDG_RUNTIME_DIR}"; then
    mkdir "${XDG_RUNTIME_DIR}"
    chmod 0700 "${XDG_RUNTIME_DIR}"
  fi
fi
```

Once that's complete, you can launch Sway manually.

```sh
dbus-run-session -- sway
```

### Personal Touches

I also added the following packages, per my personal preferences and situation.

```sh
doas apk add brightnessctl   \ # Brightness controller
             zsh             \ # Shell
             firefox         \ # Browser
             syncthing       \ # File sync service
             wireguard-tools \ # Wireguard VPN
             gomuks          \ # CLI Matrix client
             neomutt         \ # CLI email client
             thunderbird     \ # GUI email client
             gnupg             # GPG key manager
```

From here, I use my Syncthing storage to pull all of the configuration files I 
stored from prior desktops, such as [my 
dotfiles](https://git.cleberg.net/cgit.cgi/dotfiles.git/tree/).

### WiFi Issues

I initially tried to set-up my WiFi the standard way with `iwd`, but it didn't 
work.

Here is what I initially tried (I did all of this as `root`):

```sh
apk add iwd
rc-service iwd start
iwctl station wlan0 connect <SSID> # This will prompt for the password
rc-update add iwd boot && rc-update add dbus boot
```

Then, I added the WiFi entry to the bottom of the networking interface file:

```sh
nano /etc/network/interfaces
```

```conf
auto wlan0
iface wlan0 inet dhcp
```

Finally, restart the networking service:

```sh
rc-service networking restart
```

My WiFi interface would receive an IP address from the router, but it could 
not ping anything in the network. To solve the WiFi issues, I did two main 
things:

1. Upgrade to the `edge` version of Alpine as I assume the `linux-lts` kernel of 
5.15 had issues with my device/drivers. At the time of writing, running the 
`edge` version has me on kernal version 6.1.1-0-lts.
2. Enable the `NameResolvingService=resolvconf` in `iwd`.

#### Upgrade to Edge Repos

Edit the repositories file again:

```sh
nano /etc/apk/repositories
```

Comment-out the v3.17 repositories and un-comment the edge repositories:

```conf
#/media/sda/apks
#http://mirrors.gigenet.com/alpinelinux/v3.17/main
#http://mirrors.gigenet.com/alpinelinux/v3.17/community
http://mirrors.gigenet.com/alpinelinux/edge/main
http://mirrors.gigenet.com/alpinelinux/edge/community
#http://mirrors.gigenet.com/alpinelinux/edge/testing
```

Run the updates and upgrades requires, then sync and reboot the machine.

```sh
apk -U update
apk add --upgrade apk-tools
apk upgrade --available
sync
reboot
```

#### Enable WiFi Resolving Service

In addition to upgradine to Edge above, I also needed to enable the network 
resolving service. This is as easy as editing the following file and 
un-commenting the lines below.

```sh
doas nano /etc/iwd/main.conf
```

```conf
[Network]

NameResolvingService=resolvconf
```

Once I did these things, my WiFi is working flawlessly.

### Sound Issues

Same as with the WiFi, I had no sound and could not control the mute/unmute or 
volume buttons on my laptop.

To resolve this, I installed 
[pipewire](https://wiki.alpinelinux.org/wiki/PipeWire).

```sh
# Add your user to the following groups
addgroup $USER audio
addgroup $USER video

# Install pipewire and other useful packages
apk add pipewire wireplumber pipewire-pulse pipewire-jack pipewire-alsa
```

finally, add `/usr/libexec/pipewire-launcher` to your `.xinitrc`:

```sh
nano ~/.xinitrc
```

```conf
export $(dbus-launch)
/usr/libexec/pipewire-launcher
```

Note that I did not enable bluetooth or screen sharing, so I won't cover those 
options in this post.
