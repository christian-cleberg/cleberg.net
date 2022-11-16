+++
date = 2022-02-16
title = "Migrating to a New Web Server Setup with Debian, Nginx, and Agate"
description = "I've finally taken the leap of faith on two different technologies for the first time: trying out Debian as my main server OS and Nginx as my web server software."
draft = false
aliases = ["/blog/migrating-to-debian-and-nginx"]
+++

## Server OS: Debian

![Debian + neofetch](https://img.cleberg.net/blog/20220216-migrating-to-debian-and-nginx/neofetch.png)

I've used various Linux distributions throughout the years, but I've never used
anything except Ubuntu for my servers. Why? I really have no idea - mostly just
comfort around the commands and software availability.

However, I have always wanted to try Debian as a server OS after testing it out
in a VM a few years ago (side-note: I'd love to try Alpine too, but I always
struggle with compatibility). So, I decided to launch a new VPS and use
[Debian](https://www.debian.org) 11 as the OS. Spoiler alert: it feels identical
to Ubuntu for my purposes.

I did the normal things when first launching the VPS, such as adding a new user,
locking down SSH, etc. If you want to see that level of detail, read my other
post about
[How to Set Up a VPS Web Server](https://cleberg.net/blog/how-to-set-up-a-vps-web-server/).

All of this has been similar, apart from small things such as the location of
users' home folders. No complaints at all from me - Debian seems great.

## Web Server: Nginx

![Nginx status](https://img.cleberg.net/blog/20220216-migrating-to-debian-and-nginx/nginx.png)

Once I had the baseline server configuration set-up for Debian, I moved on to
trying out [Nginx](https://nginx.org) as my web server software. This required
me to install the `nginx` and `ufw` packages, as well as setting up the initial
UFW config:

```bash
sudo apt install nginx ufw
sudo ufw allow 'Nginx Full'
sudo ufw allow SSH
sudo ufw enable
sudo ufw status
sudo systemctl status nginx
```

Once I had the firewall set, I moved on to creating the directories and files
for my website. This is very easy and is basically the same as setting up an
Apache server, so no struggles here.

```bash
sudo mkdir -p /var/www/your_domain/html
sudo chown -R $USER:$USER /var/www/your_domain/html
sudo chmod -R 755 /var/www/your_domain
nano /var/www/your_domain/html/index.html
```

The next part, creating the Nginx configuration files, is quite a bit different
from Apache. First, you need to create the files in the `sites-available` folder
and symlink it the `sites-enabled` folder.

Creating the config file for your domain:

```bash
sudo nano /etc/nginx/sites-available/your_domain
```

Default content for an Nginx config file:

```bash
server {
        listen 80;
        listen [::]:80;

        root /var/www/your_domain/html;
        index index.html index.htm index.nginx-debian.html;

        server_name your_domain www.your_domain;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

Finally, symlink it together:

```bash
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```

This will make your site available to the public (as long as you have
`your_domain` DNS records pointed at the server's IP address)!

Next, I used [certbot](https://certbot.eff.org/) to issue an HTTPS certificate
for my domains using the following commands:

```bash
sudo apt install snapd; sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```

Now that certbot ran successfully and updated my Nginx config files to include a
`443` server block of code, I went back in and edited the config file to include
security HTTP headers. This part is optional, but is recommended for security
purposes - you can even test a website's HTTP header security at
[Security Headers](https://securityheaders.com/).

The configuration below shows a set-up where you only want your website to serve
content from its own domain - except for images and scripts, which may come from
`nullitics.com`. All other content would be blocked from loading in a browser.

```bash
sudo nano /etc/nginx/sites-available/your_domain
```

```bash
server {
    ...
        add_header Content-Security-Policy "default-src 'none'; img-src 'self' https://nullitics.com; script-src 'self' https://nullitics.com; style-src 'self'; font-src 'self'";
        add_header X-Content-Type-Options "nosniff";
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Frame-Options "DENY";
        add_header Strict-Transport-Security "max-age=63072000; includeSubDomains";
        add_header Referrer-Policy "no-referrer";
    ...
}
```

```bash
sudo systemctl restart nginx
```

### Nginx vs. Apache

As I stated at the beginning, my historical hesitation with trying Nginx was
that the differences in configuration formats scared me away from leaving
Apache. However, I actually prefer Nginx over Apache for a few reasons:

1. Nginx uses only one config file (`your_domain`) vs. Apache's two-file
   approach for HTTP vs. HTTPS (`your_domain.conf` and
   `your_domain-le-ssl.conf`).
2. Symlinking new configurations files and reloading Nginx is way easier than
   Apache's process of having to enable headers with `a2enmod mod_headers`,
   enable PHP with `a2enmod php` (plus any other mods you need), and then
   enabling sites with `a2ensite`, and THEN reloading Apache.
3. The contents of the Nginx config files seem more organized and logical with
   the curly-bracket approach. This is a small one, but everything just felt
   cleaner while I was installing my sites and that had a big quality of life
   impact on the installation for me.

They're both great software packages, but Nginx just seems more organized and
easier to use these days (in my situation - everyone is different). I will
certainly be exploring the Nginx docs to see what other fun things I can do with
all of this.

## Gemini Server: Agate

![Agate status](https://img.cleberg.net/blog/20220216-migrating-to-debian-and-nginx/agate.png)

Finally, I set up the Agate software on this server again to host my Gemini
server content - using Rust as I have before. You can read my other post for
more information on installing Agate:
[Hosting a Gemini Server](https://cleberg.net/blog/hosting-a-gemini-server/).

All in all, Debian + Nginx is very slick and I prefer it over my old combination
of Ubuntu + Apache (although it's really just Nginx > Apache for me, since
Debian seems mostly the same as Ubuntu so far).
