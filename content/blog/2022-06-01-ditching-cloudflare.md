+++
date = 2022-06-01
title = "Ditching Cloudflare for Njalla"
description = "After spending a few years jumping around to different DNS hosts, I'm finally ditching Cloudflare for good."
draft = false
aliases = ["/blog/ditching-cloudflare-for-njalla"]
+++

## Registrar

![Njalla's Registrar Dashboard](https://img.cleberg.io/blog/20220601-ditching-cloudflare-for-njalla/registrar.png)

After spending a year or so using Cloudflare for DNS only - no proxying or
applications - I spent the last few months using Cloudflare Tunnels and
Cloudflare Access to protect my self-hosted websites and applications via their
proxy traffic model.

However, I have never liked using Cloudflare due to their increasingly large
share of control over web traffic, as well as their business model of being a
MITM for all of your traffic.

So, as of today, I have switched over to [Njalla](https://njal.la) as my
registrar and DNS manager. I was able to easily transfer my domains over
rapidly, with only one domain taking more than 15-30 minutes to propagate.

I do still have two domains sitting at Cloudflare for the moment while I decide
if they're worth the higher rates (one domain is 30€ and the other is 45€).

## DNS

![Njalla's DNS Settings](https://img.cleberg.io/blog/20220601-ditching-cloudflare-for-njalla/dns.png)

As noted above, I'm using Njalla exclusively for DNS configurations on my
domains.

However, the transfer process was not ideal. As soon as the domains transferred
over, I switched the nameservers from Cloudflare to Njalla and lost most of the
associated DNS records. So, the majority of the time spent during the migration
was simply re-typing all of the DNS records back in one-by-one.

This would be much simpler if I were able to edit the plain-text format of the
DNS configuration. I was able to do that at a past registrar (perhaps it was
[Gandi.net](https://gandi.net/)?) and it made life a lot easier.

### Dynamic DNS Updates

I am working on a local server script to dynamically update my `A` records
whenever my public IP address changes at home - I will update this post with a
link whenever that becomes available. In the meantime, I do know that you can
create `Dynamic` DNS records with Njalla that
[work for updating dynamic subdomains](https://njal.la/docs/ddns/).

### Njalla's DNS Tool

One neat upside to Njalla is that they have a
[DNS lookup tool](https://check.njal.la/dns/) that provides a lot of great
information for those of you (AKA: me) who hate using the `dig` command.

This was very useful for monitoring a couple of my transferred domains to see
when the changes in nameservers, records, and DNSSEC went into effect.

![Njalla's DNS Tool](https://img.cleberg.io/blog/20220601-ditching-cloudflare-for-njalla/dns_tool.png)

## Tunnel

Cloudflare Tunnel is a service that acts as a reverse-proxy (hosted on
Cloudflare's servers) and allowed me to mask the private IP address of the
server hosting my various websites and apps.

However, as I was moving away from Cloudflare, I was not able to find a suitable
replacement that was both inexpensive and simple. So, I simply went back to
hosting [my own reverse proxy with Nginx](/blog/set-up-nginx-reverse-proxy/).
With the recent additions of Unifi hardware in my server/network rack, I am much
more protected against spam and malicious attacks at the network edge than I was
before I switched to Cloudflare.

## Access

Cloudflare Access, another app I used in combination with Cloudflare Tunnel,
provided an authentication screen that required you to enter valid credentials
before Cloudflare would forward you to the actual website or app (if the
website/app has their own authentication, you'd then have to authenticate a
second time).

I did not replace this service with anything since I only host a handful of
non-sensitive apps that don't require duplicate authentication.
