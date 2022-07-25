+++
date = 2022-07-25
title = "CurseRadio: Listening to the Radio on the Command Line"
description = "A quick and easy installation for CurseRadio on Fedora Linux."
+++

## Overview

While exploring some interesting Linux applications, I stumbled across 
[curseradio](https://github.com/chronitis/curseradio), a command-line radio 
player based on Python.

This application is fantastic and incredibly easy to install, so I wanted to 
dedicate a post today to this app. Let's look at the features within the app and 
then walk through the installation process I took to get `curseradio` working.

## Features

![curseradio](https://img.cleberg.io/blog/20220725-curseradio/curseradio.png "curseradio")

The radio player itself is quite minimal. As you can see in the screenshot 
above, it contains a simple plaintext list of all available categories, which 
can be broken down further and further. In addition, radio shows are available 
for listening, alongside regular radio stations.

For example, the `Sports` > `Pro Basketball` > `Shows` category contains a 
number of specific shows related to Professional Basketball.

Aside from being able to play any of the listed stations/shows, you can favorite 
a channel by pressing `f`. It will now show up at the top of the radio player in 
the `Favourites` category.

### Commands/Shortcuts

|   Key(s)   |             Command             |
|:----------:|:-------------------------------:|
| ↑, ↓       | navigate                        |
| PgUp, PgDn | navigate quickly                |
| Home, End  | to top/bottom                   |
| Enter      | open/close folders, play stream |
| k          | stop playing stream             |
| q          | quit                            |
| f          | toggle favourite                |

## Installation

### Dependencies

Before installing `curseradio`, a handful of system and Python packages are 
required. To get started, install `python3`, `pip3`, and `mpv` on your system. 
In this example, I'm using Fedora Linux, which uses the `dnf` package manager. 
You may need to adjust this if you're using a different system.

```bash
sudo dnf install python3 pip3 mpv
```

Next, use `pip3` to install `requests`, `xdg`, and `lxml`:

```bash
pip3 install requests xdg lxml
```

### Repository Source Installation

Once all the dependencies are installed, we can clone the source code and enter 
that directory:

```bash
git clone https://github.com/chronitis/curseradio && cd curseradio
```

Once you're within the `curseradio` directory, you can install the application 
with the provided `setup.py` script.

```bash
sudo python3 setup.py install
```

In my case, I ran into a few errors and needed to create the folders that 
curseradio wanted to use for its installation. If you don't get any errors, you 
can ski pthis and run the app.

```bash
sudo mkdir /usr/local/lib/python3.10/
sudo mkdir /usr/local/lib/python3.10/site-packages/
```

```bash
sudo chown -R kaizoku:kaizoku /usr/local/lib/python3.10/
```

## Run the Application

Once fully installed without errors, you can run the application!

```bash
python3 /usr/local/bin/curseradio
```
