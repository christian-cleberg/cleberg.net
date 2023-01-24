+++
date = 2023-01-23
title = "Connecting to a Random Mullvad Wireguard Host"
description = "Read to see how I used a shell script to randomly connect to a Mullvad Wireguard host each time I run the script."
+++

## Mullvad Wireguard

If you're using an OS that doesn't support one of Mullvad's apps, you're likely
using the Wireguard configuration files instead.

If not, the first step is to visit Mullvad's
[Wireguard configuration
files](https://mullvad.net/en/account/#/wireguard-config) page and download a
ZIP of the configuration files you want to use.

Personally, I downloaded all configuration files across the world and choose my
connections using the script below.

Once the files are downloaded, unzip them and move them to your preferred
location:

```sh
cd Downloads
unzip mullvad_wireguard_linux_all_all.zip
mkdir ~/mullvad && mv ~/Downloads/*.conf ~/mullvad/
```

## Creating a Script to Connect to a Random Host

Once you have a folder of Wireguard configuration files from Mullvad, you can
create a script to randomly connect to any one of the locations.

Start by creating a shell script - mine is called `vpn.sh`.

```sh
nano ~/vpn.sh
```

Within this script, you can paste the following info. Note that I specify `us-*`
in my script, which means that it will only consider US-based VPN locations. You
can alter this or simply change it `*` to consider all locations.

```sh
#!/bin/sh

ls /home/$USER/mullvad/us-* |sort -R |tail -n 1 |while read file; do
    # Replace `doas` with `sudo` if your machine uses `sudo`,
    # or remove `doas` if users don't need to su to run wg-quick
    doas wg-quick up $file;
    printf "\nCreated Mullvad wireguard connection with file: $file";
    printf "\n\nPrinting new IP info:\n"
    curl https://zx2c4.com/ip
done
```

Once you've modified the script to your liking, add executable permissions
and run the script:

```sh
chmod +x ~/vpn.sh
~/vpn.sh
```

The output should look like the following:

```txt
doas (cmc@alpine) password:

# ... The script will process all of the iptables and wg commands here

Created Mullvad wireguard connection with file: /home/cmc/mullvad/us-nyc-wg-210.conf

Printing new IP info:
86.106.143.10

curl/7.87.0
```

That's all there is to it. You can see your new location and IP via the
`printf` and `curl` commands included in the script.

You can also go to the [Connection Check |
Mullvad](https://mullvad.net/en/check/) page to see if you are fully connected
to Mullvad and if any leaks exist.

![Mullvad Connection Check](https://img.cleberg.net/blog/20230123-random-mullvad-wireguard/mullvad_check.png "Mullvad Connection Check")
