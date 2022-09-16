+++
date = 2022-09-12
title = "Serenity OS: Testing Out a Unique System"
description = "Walking through my exploration of Serenity OS, a unique 90s-esque system."
+++

## Overview

tbd

## Building

### Clone

```bash
git clone https://github.com/SerenityOS/serenity && cd serenity
```

### Build

Note that I followed the [Build 
Instructions](https://github.com/SerenityOS/serenity/blob/master/Documentation/BuildInstructions.md) 
in the SerenityOS repository as of commit ``.

Regardless, I want to repeat my steps here to illustrate any errors or differing 
commands I needed to run in order to build and run SerenityOS.

For Fedora, install these packages:

```bash
sudo dnf install texinfo binutils-devel curl cmake mpfr-devel libmpc-devel gmp-devel e2fsprogs ninja-build patch ccache rsync @"C Development Tools and Libraries" @Virtualization
```

Build the toolchain:

```bash
Meta/serenity.sh rebuild-toolchain
```

Build and run:

```bash
Meta/serenity.sh run
```

After this process is completed, the image should run automatically and launch.

## Screenshots

![](https://img.cleberg.io/blog/20220912-serenity-os/initial_launch.png)
![](https://img.cleberg.io/blog/20220912-serenity-os/basic_apps.png)
![](https://img.cleberg.io/blog/20220912-serenity-os/system_monitor.png)
![](https://img.cleberg.io/blog/20220912-serenity-os/themes.png)
