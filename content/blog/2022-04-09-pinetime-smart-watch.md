+++
date = 2022-04-09
title = "PineTime: An Open-Source SmartWatch"
description = "Exploring the PineTime, an open-source smartwatch created by PINE64."
draft = false
+++

## PineTime Product Information

### Overview

The [PineTime](https://www.pine64.org/pinetime/) is an open-source smartwatch,
created by [PINE64](https://www.pine64.org). Originally announced in September
2019, this ARM-based watch is a fantastic option for users who want the benefits
of a modern smartwatch with the backing of open-source components and software.

### Product Specifications

I won't dive into too many details that you can find on
[the product page](https://www.pine64.org/pinetime/), but I wanted to point out
the prices for each watch and the primary functions:

#### Price:

-   $26.99 (Sealed)
-   $24.99 (Dev Kit)
-   $51.98 (One Sealed + One Dev Kit)

#### Primary Functionality:

-   Clock (+ Smartphone Sync)
-   Pedometer
-   Heart Rate Monitor
-   Sleep Monitor
-   Calories burned
-   Messaging
-   Smartphone Notifications
-   Media Controls

## Unboxing

Now, my PineTime was ordered on 2022-02-17, shipped on 2022-02-22, and arrived
on 2022-03-23. With the current delays on shipping times around the world (and
the semiconductor shortage), a month for delivery from China seems reasonable to
me.

The packaging is simple and the watch comes with instructions, technical
information, the watch, and a charger (it does not include a USB wall adapter).

The watch itself was able to turn on immediately when I pulled it out of the
box, but the battery was depleted and required charging right away.

![PineTime Box Contents](https://img.cleberg.io/blog/20220409-pinetime-smart-watch/pinetime_box_contents.png)

### Physical Hardware

### Watch OS: InfiniTime

While turning on the watch for the first time, some of the main design choices
you can see in the watch OS,
[InfiniTime](https://wiki.pine64.org/wiki/InfiniTime), are:

-   A square bezel, not too thin against the sides of the watch.
-   A simple, rubber band.
-   Basic font and screen pixel design.
-   Swipe gestures to access other screens.

![PineTime Screens](https://img.cleberg.io/blog/20220409-pinetime-smart-watch/pinetime.png)

The OS itself is fantastic in terms of functionality for me. It does exactly
what a smart watch should do - track time, steps, heart rates, and connect to
other smart devices - without being overly burdensome on the user.

My only gripe so far is that it's _really_ difficult to swipe to different
screens, such as pulling down the notification tray. I'm not sure if this is an
OS or hardware issue, but it makes it quite hard to quickly move around the
screens.

However, my absolute favorite design choice is that the button the side turns
the screen on and off and tilting/waving my wrist doesn't accidentally turn on
the screen. With other watches, I absolutely hated not being able to turn off
the raise-to-wake or wave features (i.e., blinding myself while wearing a watch
at night because I moved my arm).

### iOS App: InfiniLink

Since I am using iOS as my primary mobile device OS, I am using the
[InfiniLink](https://github.com/xan-m/InfiniLink) app to connect my watch.

This app provides the following for PineTime owners:

-   Firmware updates
-   Steps
-   Charts
-   Notifications

I mashed up a few screenshots to show off the home page, menu, firmware update,
and notification test screens:

![InfiniLink Home](https://img.cleberg.io/blog/20220409-pinetime-smart-watch/infinilink_home.png)

Another big feature of InfiniLink is the ability to track pedometer steps in a
collection of beautiful graphs - with the option to change your step goal and
add in manual steps.

![InfiniLink Steps](https://img.cleberg.io/blog/20220409-pinetime-smart-watch/infinilink_steps.png)

Finally, there are charts to display the battery percentage and heart rates over
time. This area also comes with an option to clear data.

![InfiniLink Charts](https://img.cleberg.io/blog/20220409-pinetime-smart-watch/infinilink_charts.png)

## Final Thoughts

### Pros

After wearing my watch for a few weeks, I have mostly positive thoughts about
the watch so far. In the past, I have owned smart watches by FitBit, Fossil,
Apple, etc. - **but I prefer the PineTime over all of those watches**.

The PineTime strips out all of the unnecessary features and performs the
functions that it provides effectively and efficiently.

The battery life is amazing on this device. By default, the watch seems to last
anywhere from a few days to a week before dying.

And of course, it's open source and backed by some of the most dedicated
enthusiasts and developers I've seen. Watching the Matrix channel, forums, and
website have been exciting to see.

### Cons

If I had to complain about anything, it would simply be the small bugs in some
features that can be contributed to the companion apps more than the watch
itself.

A regular user would want native notification support out-of-the-box, which is
the biggest item not working for me at the moment.

My only other complaint is that the battery indicator on the watch doesn't seem
accurate when it's nearing depletion - it seems that there's a bit of battery
life left and then my watch is dead very suddenly after. This could just be me
misinterpreting the battery level icons, but it has fooled me a few times into
thinking I had more battery left than I actually did.

Other than those small items, I really do love this watch and am glad I replaced
my Apple watch with the PineTime.
