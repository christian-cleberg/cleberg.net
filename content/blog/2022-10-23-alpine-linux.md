+++
date = 2022-10-23
title = "Alpine Linux"
description = "In this post, I explain Alpine Linux's benefits as a server OS and explain my current setup."
draft = true
+++

## Alpine Linux

TODO: Brief overview/description of the distro, differences from Ubuntu/Fedora, 
etc.

### Installation

- https://wiki.alpinelinux.org/wiki/Installation
- login as `root`, then execute `setup-alpine`
- once rebooted, login as user, then `su`, `apk -U upgrade`

### Post-Installation

- sudo: `su`, `apk add doas` and `adduser <username> wheel`, `exit`
- add any needed packages: `doas apk add nano nginx docker docker-compose ufw`
- setup SSH and UFW
  - pubkey, ports
- hostname: `doas nano /etc/hostname` & `doas nano /etc/hosts`

## Nginx Web Server

```bash
doas adduser -D -g 'www' www
mkdir /www
doas mkdir /www
doas chown -R www:www /var/lib/nginx/
doas chown -R www:www /www
```

- conf files
- `doas rc-update add nginx default`
- `/www` files

## Docker Containers

- brief example

## Git Server

- reference to git article
- `lchsh` instead of `chsh`:
```bash
doas apk add libuser
doas touch /etc/login.defs
doas mkdir /etc/default
doas touch /etc/default/useradd
doas lchsh git
```
