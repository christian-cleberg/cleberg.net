+++
date = 2020-03-25
title = "Session Private Messenger"
description = "Session Private Messenger is a private, cross-platform messaging app from the Loki Foundation."
draft = false
aliases = ["/blog/session-private-messenger"]
+++

## Privacy Warning

The company behind Session (Loki Foundation) is from Australia. If you didn't
know, Australia has introduced
[legislation](https://parlinfo.aph.gov.au/parlInfo/download/legislation/bills/r6195_aspassed/toc_pdf/18204b01.pdf)
mandating companies comply with government requests to build backdoor access
into applications. For more information,
[see my article on AES Encryption](/blog/aes-encryption/).

## About Session

[Session](https://getsession.org) is a private, cross-platform messaging app
from the [Loki Foundation](https://loki.foundation). As someone who has spent
years looking for quality alternatives to major messaging apps, I was excited
when I first heard about Session. Reading through
[Session's whitepaper](https://arxiv.org/pdf/2002.04609.pdf), you can learn the
technologies behind the Session app. Part of the security of Session comes from
the Signal protocol, which was forked as the origin of Session.

> Session is an end-to-end encrypted messenger that removes sensitive metadata
> collection, and is designed for people who want privacy and freedom from any
> forms of surveillance.

In general, this app promises security through end-to-end encryption,
decentralized onion routing, and private identities. The biggest change that the
Loki Foundation has made to the Signal protocol is removing the need for a phone
number. Instead, a random identification string is generated for any session you
create. This means you can create a new session for each device, if you want to,
or link new devices with your ID.

Since Session's website and whitepaper describe the details of Session's
security, I'm going to focus on using the app in this post.

## Features

Since most people are looking for an alternative to a popular chat app, I am
going to list out the features that Session has so that you are able to
determine if the app would suit your needs:

-   Multiple device linking (via QR code or ID)
-   App locking via device screen lock, password, or fingerprint
-   Screenshot blocking
-   Incognito keyboard
-   Read receipts and typing indicators
-   Mobile notification customization
-   Old message deletion and conversation limit
-   Backups
-   Recovery phrase
-   Account deletion, including ID, messages, sessions, and contacts

## Downloads

I have tested this app on Ubuntu 19.10, Android 10, macOS Monterey, and iOS 15.
All apps have worked well without many issues.

Below is a brief overview of the Session app on Linux. To get this app, you'll
need to [go to the Downloads page](https://getsession.org/download/) and click
to link to the operating system you're using.

For Linux, it will download an AppImage that you'll need to enable with the
following command:

```sh
sudo chmod u+x session-messenger-desktop-linux-x86_64-1.0.5.AppImage
```

![Session downloads page](https://img.cleberg.net/blog/20200325-session-private-messenger/session_downloads.png)  
_Fig. 1 - Session Downloads_

## Creating an Account

Once you've installed the app, simply run the app and create your unique Session
ID. It will look something like this:
`05af1835afdd63c947b47705867501d6373f486aa1ae05b1f2f3fcd24570eba608`

You'll need to set a display name and, optionally, a password. If you set a
password, you will need to enter it every time you open the app.

![Session login page](https://img.cleberg.net/blog/20200325-session-private-messenger/session_linux_login.png)

_Fig. 2 - Session Login (Linux)_

![Session login page](https://img.cleberg.net/blog/20200325-session-private-messenger/session_macos_login.png)

_Fig. 3 - Session Login (macOS)_

![Password authentication](https://img.cleberg.net/blog/20200325-session-private-messenger/session_password_authentication.png)

_Fig. 4 - Password Authentication_

## Start Messaging

Once you've created your account and set up your profile details, the next step
is to start messaging other people. To do so, you'll need to share your Session
ID with other people. From this point, it's fairly straightforward and acts like
any other messaging app, so I won't dive into much detail here.

### macOS

![macOS Conversations](https://img.cleberg.net/blog/20200325-session-private-messenger/session_macos_conversations.png)

_Fig. 5 - macOS Conversations_

One key feature to note is that the desktop application now provides a helpful
pop-up box explaining the process that Session uses to hide your IP address:

![IP Hiding](https://img.cleberg.net/blog/20200325-session-private-messenger/session_ip.png)

_Fig. 6 - IP Address Help Box_

### iOS

The mobile app is quite simple and effective, giving you all the standard mobile
messaging options you'd expect.

![iOS App](https://img.cleberg.net/blog/20200325-session-private-messenger/session_ios.png)

_Fig. 7 - iOS App_

## Potential Issues

I've discovered one annoying issue that would prevent from using this app
regularly. On a mobile device, there have been issues with receiving messages on
time. Even with battery optimization disabled and no network restrictions,
Session notifications sometimes do not display until I open the app or the
conversation itself and wait a few moments. This is actually one of the reasons
I stopped using Signal (although this seems fixed as of my updates in 2021/2022,
so I wouldn't worry about this issue anymore).

Looking for another messenger instead of Session? I recommend Signal, Matrix,
and IRC.
