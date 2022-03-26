+++
date = 2022-03-26
title = "Enable TOTP MFA for SSH"
description = "Harden your SSH target with user-enabled MFA options, such as TOTP."
draft = false
+++

## Why Do I Need MFA for SSH?

If you are a sysadmin of a server anywhere (that includes at home!), you may want an added layer of protection against intruders. This is not a replacement for other security measures, such as:

- Disable root SSH
- Disable SSH password authentication
- Allow only certain users to login via SSH
- Allow SSH only from certain IPs

However, MFA can be added as an additional security measure to ensure that your server is protected. This is especially important if you need to allow password authentication for SSH.

For more guidance on server security measures, see my other post: [Hardening a Public-Facing Home Server](/blog/hardening-a-public-facing-home-server/).

## Install MFA PAM Module

PAM, which stands for Pluggable Authentication Module, is an authentication infrastructure used on Linux systems to authenticate a user. In order to use this technology, let's install the `libpam-google-authenticator` package:

```bash
sudo apt-get update
```

```bash
sudo apt-get install libpam-google-authenticator
```

## Initialize the PAM Module

### Interactive Method

Once the package is installed, initialize it and following the interactive prompts to generate your OTP or TOTP:

```bash
google-authenticator
```

If you are not sure how to answer, read the prompts carefully and think about having to how each situation would affect your normal login attempts. If you are still not sure, use my default responses below.

```txt
OUTPUT

Do you want authentication tokens to be time-based (y/n) y
```

At this point, use an authenticator app somewhere one of your devices to scan the QR code. Any future login attempts after our upcoming configuration changes will require that TOTP.

```txt
OUTPUT

Do you want me to update your "/home/kaizoku/.google_authenticator" file? (y/n) y
```

```txt
OUTPUT

Do you want to disallow multiple uses of the same authentication
token? This restricts you to one login about every 30s, but it increases
your chances to notice or even prevent man-in-the-middle attacks (y/n) y
```

```txt
OUTPUT

By default, a new token is generated every 30 seconds by the mobile app.
In order to compensate for possible time-skew between the client and the server,
we allow an extra token before and after the current time. This allows for a
time skew of up to 30 seconds between authentication server and client. If you
experience problems with poor time synchronization, you can increase the window
from its default size of 3 permitted codes (one previous code, the current
code, the next code) to 17 permitted codes (the 8 previous codes, the current
code, and the 8 next codes). This will permit for a time skew of up to 4 minutes
between client and server.
Do you want to do so? (y/n) n
```

```txt
OUTPUT

If the computer that you are logging into isn't hardened against brute-force
login attempts, you can enable rate-limiting for the authentication module.
By default, this limits attackers to no more than 3 login attempts every 30s.
Do you want to enable rate-limiting? (y/n) y
```

### Non-Interactive Method

If you need to do this quickly, know your responses to the prompts, or are setting this up for numerous users, the non-interactive method can be much faster:

```bash
google-authenticator -t -d -f -r 3 -R 30 -w 3
```

The options referenced above are as follows:

```txt
google-authenticator [<options>]
 -h, --help                     Print this message
 -c, --counter-based            Set up counter-based (HOTP) verification
 -t, --time-based               Set up time-based (TOTP) verification
 -d, --disallow-reuse           Disallow reuse of previously used TOTP tokens
 -D, --allow-reuse              Allow reuse of previously used TOTP tokens
 -f, --force                    Write file without first confirming with user
 -l, --label=<label>            Override the default label in "otpauth://" URL
 -i, --issuer=<issuer>          Override the default issuer in "otpauth://" URL
 -q, --quiet                    Quiet mode
 -Q, --qr-mode={NONE,ANSI,UTF8} QRCode output mode
 -r, --rate-limit=N             Limit logins to N per every M seconds
 -R, --rate-time=M              Limit logins to N per every M seconds
 -u, --no-rate-limit            Disable rate-limiting
 -s, --secret=<file>            Specify a non-standard file location
 -S, --step-size=S              Set interval between token refreshes
 -w, --window-size=W            Set window of concurrently valid codes
 -W, --minimal-window           Disable window of concurrently valid codes
 -e, --emergency-codes=N        Number of emergency codes to generate
```

This fully configures the authenticator, saves it to a file, and then outputs the secret key, QR code, and recovery codes. (If you add the flag `-q`, then there won’t be any output). If you use this command in an automated fashion, make sure your script captures the secret key and/or recovery codes and makes them available to the user.

## PAM Configuration Settings

Once you've enabled the T/OTP and have it saved to an MFA app on your phone or other device, open the PAM `sshd` file:

```bash
sudo nano /etc/pam.d/sshd
```

You need to do two things in this file. First, add the following lines to the bottom of the file:

```config
auth required pam_google_authenticator.so nullok
auth required pam_permit.so
```

Second, comment-out the following line near the top of the file.

If you leave this line un-commented, every SSH login attempt will ask for the following three authentication factors:

1. Publickey
2. Password
3. T/OTP code

```config
#@include common-auth
```

## SSH Configuration Settings

Finally, edit the `sshd_config` file again:

```bash
sudo nano /etc/ssh/sshd_config
```

You'll need to change `ChallengeResponseAuthentication` to yes and add the `AuthenticationMethods` line to the bottom of the file.

```config
ChallengeResponseAuthentication yes
AuthenticationMethods publickey,password publickey,keyboard-interactive
```

Finally, restart the `ssh` service:

```bash
sudo systemctl restart sshd.service
```

The next time you login, you should an MFA verification code request!

![SSH MFA](https://img.cleberg.io/blog/20220326-enable-totp-mfa-for-ssh/ssh_mfa.png)
