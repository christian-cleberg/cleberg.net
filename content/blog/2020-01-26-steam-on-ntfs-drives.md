+++
date = 2020-01-26
title = "Linux Gaming Tweak: Steam on NTFS Drives"
description = "Fix issues with NTFS-formatted hard drives with Linux and Steam."
+++

## Auto-Mount Steam Drives

![Steam example](https://img.cleberg.io/blog/007-the-best-linux-software/steam.png "Steam example")

If you want to see how to install Steam on Linux, see my other
post: [The Best Linux Software](the-best-linux-software/).

Are you having trouble launching games, even though they've installed correctly? This may happen if you're storing your
games on an NTFS-formatted drive. This shouldn't be an issue if you're storing your games on the same drive that Steam
is on, but some gamers prefer to put Steam on their main drive and game files on another SSD or HDD.

To fix this problem, you'll need to try a few things. First, you'll need to install the `ntfs-3g` package, which is
meant for better interoperability with Linux.

```bash
sudo apt install ntfs-3g
```

Next, you should set up the `/etc/fstab` file to automatically mount your drives on boot. To automatically mount your
drives when the computer boots up, you'll have to create the folders you want to mount your drive to first. I store mine
in the `/mnt` folder using names that I'll recognize, but you can create your folders wherever you want.

```bash
mkdir /path/to/folder
```

For example:

```bash
mkdir /mnt/SteamDrive
```

Next, open the `/etc/fstab` file:

```bash
sudo nano /etc/fstab
```

Each drive you want to mount on boot should have its own line in the `/etc/fstab` file that looks similar to this:

```text
UUID=B64E53824E5339F7 /path/to/folder ntfs-3g uid=1000,gid=1000 0 0
```

The UUID is the identification number connected to whichever drive you're using to store Steam games. To find your UUID,
run this command:

```bash
sudo blkid | grep UUID=
```

Drives are usually labeled similar to `/dev/nvme0n1p1` or `/dev/sda1`, so you'll need to find the line in the output
that correlates to your drive and copy the UUID over to the `/etc/fstab` file. Finally, make sure you've added
your `uid` and `gid` to the end of the configuration line in the `/etc/fstab` file. To find these, run the following
command:

```bash
id -u && id -g
```

Now all you need to do is unmount your drive and re-mount it. You can unmount the drive by doing this (be sure to use
the correct drive name here):

```bash
sudo umount /dev/sdxX
```

You can re-mount all your drives by executing the following:

```bash
sudo mount -a
```

If you don't know what your drive name is, or you're nervous about un-mounting and re-mounting, simply reboot your
computer, and it will be done for you automatically.
