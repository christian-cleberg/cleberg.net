+++
date = 2022-11-27
title = "Building a Custom Rack-Mount Server"
description = "I finally built my own custom rack-mounted server after years of indecision."
+++

## The Dilemma

For years, I have been using desktops and a Raspberry Pi as the backbone of my 
homelab. I have always wanted to move toward a single dedicated server that 
could handle all of my tasks, but was often put off by the complexity of the 
choices (and financial roadbloacks at some times).

However, after purchasing a small server rack this past year, I have been 
researching to see what kind of rack-mounted server I could buy. I initially 
bought a Dell R720XD loaded up with everything I could ever want in a server - 
but I did not care for it. It was far too loud and the age of the CPU/RAM was 
something I wanted to improve upon.

After returning the R720XD, I decided that I wanted to build my own server with 
modern, consumer-grade PC components. This time, I am very happy with the 
results of my server.

## Components

I'll start by listing all the components I used for this server build:

- **Case**: [Rosewill RSV-R4100U 4U Server Chassis Rackmount 
Case](https://www.rosewill.com/rosewill-rsv-r4100u-black/p/9SIA072GJ92825)
- **Motherboard**: [NZXT B550](https://nzxt.com/product/n7-b550)
- **CPU**: AMD Ryzen 7 5700G with Radeon Graphics
- **GPU**: N/A - I specifically chose one of the few AMD CPUs that support 
onboard graphics.
- **RAM**: 64GB RAM (2x32GB) _Max of 128GB RAM on this motherboard_
- **Boot Drive**: Western Digital 500GB M.2 NVME SSD
- **HDD Bay**:
  - 10TB WD White _(shucked, previously owned)_
  - 8TB WD White _(shucked, previously owned)_
  - 2 x 8TB WD Red Plus _(Black Friday lined up perfectly with this build so I 
  grabbed two of these)_
- **PSU**: Corsair RM850 PSU
- **Extras**:
  - Corsair TM3Q Thermal Paste
  - Noctua 120mm fan _(replacement for front case fan)_
  - 2 x Noctua 80mm fans _(replacement for rear case fans)_
  - CableMatters 6Gbps SATA Cables

## Building the Server

This took quite a while for me to build (in my opinion of time), totaling 
around 3 hours from start to finish. The case has some peculiar construction, so 
you have to completely remove the ODD & HDD cages to install the motherboard and 
other components first.

Now, I've never built a computer of any kind before, so I was quite 
nervous. Personally, the only challenging part was getting the CPU cooler to 
screw into the mothboard without sliding the thermal paste around too much 
underneath. I'm still not entirely sure I did a great job of it, but nothing's 
broken yet.

The main components were all fine and simple - however, installing the hard 
drives is slightly tedious as I need to power off the server and completely 
unscrew the HDD cage to install or remove any drives. Additionally, the drives 
are screwed directly into the metal cage with small screws, which is quite a bit 
different from the HDD trays I'm used to in other machines.

Seeing that the cases with hot-swap bays were 3-4x the price, I'm okay dealing 
with the tedium of removing the cage to install new drives.

## Software

I'm not going to dive into the software as I have done so in other recent posts. 
However, I wanted to note that I am using Alpine Linux on this server and 
hosting most services inside Docker. No virtual machines (VMs) and very few 
bare-metal services.

## The Results

How did my build turn out? Well, after migrating my other servers and their 
services over, I found that my server is blazing fast. The heaviest of my 
applications, Plex, is handled with ease. Even 4k streaming seems to be 
effortless.

I am very happy with the results and will likely continue to improve on this 
server as the years go by rather than buying another used server online.
