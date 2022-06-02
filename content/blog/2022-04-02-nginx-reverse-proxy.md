+++
date = 2022-04-02
title = "Set-Up a Reverse Proxy with Nginx"
description = "Learn how to set-up a reverse proxy server with Nginx on Ubuntu."
draft = false
aliases = ["/blog/set-up-nginx-reverse-proxy"]
+++

## What is a Reverse Proxy?

A reverse proxy is a server that is placed between local servers or services and
clients/users (e.g., the internet). The reverse proxy intercepts all requests
from clients at the network edge and uses its configuration files to determine
where each request should be sent.

### A Brief Example

For example, let's say that I run three servers in my home:

-   Server_01 (`example.com`)
-   Server_02 (`service01.example.com`)
-   Server_03 (`service02.example.com`)

I also run a reverse proxy in my home that intercepts all public traffic:

-   Reverse Proxy

Assume that I have a domain name (`example.com`) that allows clients to request
websites or services from my home servers.

In this case, the reverse proxy will intercept all traffic from `example.com`
that enters my network and determine if the client is requesting valid data,
based on my configuration.

If the user is requesting `example.com` and my configuration files say that
Server_01 holds that data, Nginx will send the user to Server_01. If I were to
change the configuration so that `example.com` is routed to Server_02, that same
user would be sent to Server_02 instead.

```txt
┌──────┐                                              ┌───────────┐
│ User │─┐                                         ┌──► Server_01 │
└──────┘ │                                         │  └───────────┘
         │    ┌──────────┐   ┌───────────────┐     │  ┌───────────┐
         ├────► Internet ├───► Reverse Proxy ├─────├──► Server_02 │
         │    └──────────┘   └───────────────┘     │  └───────────┘
┌──────┐ │                                         │  ┌───────────┐
│ User │─┘                                         └──► Server_03 │
└──────┘                                              └───────────┘
```

## Reverse Proxy Options

There are a lot of options when it comes to reverse proxy servers, so I'm just
going to list a few of the options I've heard recommended over the last few
years:

-   [Nginx](https://nginx.com)
-   [Caddy](https://caddyserver.com)
-   [Traefik](https://traefik.io/)
-   [HAProxy](https://www.haproxy.org/)
-   [Squid](https://ubuntu.com/server/docs/proxy-servers-squid)

In this post, we will be using Nginx as our reverse proxy, running on Ubuntu
Server 20.04.4 LTS.

## Nginx Reverse Proxy Example

### Local Applications

You may be like me and have a lot of applications running on your local network
that you'd like to expose publicly with a domain.

In my case, I have services running in multiple Docker containers within a
single server and wanted a way to visit those services from anywhere with a URL.
For example, on my local network, [Dashy](https://dashy.to) runs through port
4000 (`localhost:4000`) and
[Uptime Kuma](https://github.com/louislam/uptime-kuma) runs through port 3001
(`localhost:3001`).

In order to expose these services to the public, I will need to do the
following:

1. Set up DNS records for a domain or subdomain (one per service) to point
   toward the IP address of the server.
2. Open up the server network's HTTP and HTTPS ports (80 & 443) so that the
   reverse proxy can accept traffic and determine where to send it.
3. Install the reverse proxy software.
4. Configure the reverse proxy to recognize which service should get traffic
   from any of the domains or subdomains.

### Step 1: DNS Configuration

To start, update your DNS configuration so that you have an `A` record for each
domain or subdomain.

The `A` records should point toward the public IP address of the server. If you
don't know the public IP address, log in to the server and run the following
command:

```bash
curl ifconfig.co
```

In the DNS example below, `xxx.xxx.xxx.xxx` is the public IP address of the
server.

```config
example.com             A        xxx.xxx.xxx.xxx
uptime.example.com      A        xxx.xxx.xxx.xxx
dashy.example.com       A        xxx.xxx.xxx.xxx
www                     CNAME    example.com
```

Finally, ensure the DNS has propogated correctly with
[DNS Checker](https://dnschecker.org) by entering your domains or subdomains in
the search box and ensuring the results are showing the correct IP address.

### Step 2: Open Network Ports

This step will be different depending on which router you have in your home. If
you're not sure, try to visit [192.168.1.1](http://192.168.1.1) in your browser.
Login credentials are usually written on a sticker somewhere on your
modem/router.

Once you're able to login to your router, find the Port Forwarding settings. You
will need to forward ports `80` and `443` to whichever machine is running the
reverse proxy.

In my case, the table below shows the port forwarding rules I've created. In
this table, `xxx.xxx.xxx.xxx` is the local device IP of the reverse proxy
server - it will probably be an IP between `192.168.1.1` and `192.168.1.255`.

| NAME  | FROM | PORT |  DEST PORT/IP   | ENABLED |
| :---: | :--: | :--: | :-------------: | :-----: |
| HTTP  |  \*  |  80  | xxx.xxx.xxx.xxx |  TRUE   |
| HTTPS |  \*  | 443  | xxx.xxx.xxx.xxx |  TRUE   |

Once configured, these rules will direct all web traffic to your reverse proxy.

### Step 3: Nginx Installation

To install Nginx, simply run the following command:

```bash
sudo apt install nginx
```

If you have a firewall enabled, open up ports `80` and `443` on your server so
that Nginx can accept web traffic from the router.

For example, if you want to use `ufw` for web traffic and SSH, run the following
commands:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw allow SSH
sudo ufw enable
```

### Step 4: Nginx Configuration

Now that we have domains pointing toward the server, the only step left is to
configure the reverse proxy to direct traffic from domains to local services.

To start, you'll need to create a configuration file for each domain in
`/etc/nginx/sites-available/`. They will look identical except for the
`server_name` variable and the `proxy_pass` port.

Dashy:

```bash
nano /etc/nginx/sites-available/dashy.example.com
```

```config
server {
    listen 80;
    server_name dashy.example.com;

    location / {
        proxy_pass http://localhost:4000;
    }
}
```

Uptime:

```bash
nano /etc/nginx/sites-available/uptime.example.com
```

```config
server {
    listen 80;
    server_name uptime.example.com;

    location / {
        proxy_pass http://localhost:3001;
    }
}
```

Once the configuration files are created, you will need to enable them with the
`symlink` command:

```bash
sudo ln -s /etc/nginx/sites-available/dashy.example.com /etc/nginx/sites-enabled/
```

Voilà! Your local services should now be available through their URLs.

## HTTPS with Certbot

If you've followed along, you'll notice that your services are only available
via HTTP (not HTTPS).

If you want to enable HTTPS for your new domains, you will need to generate
SSL/TLS certificates for them. The easiest way to generate certificates on Nginx
is [Certbot](https://certbot.eff.org):

```bash
sudo apt install snapd; sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```
