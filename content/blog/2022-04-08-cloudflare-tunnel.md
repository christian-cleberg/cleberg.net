+++
date = 2022-04-08
title = "Securing Self-Hosted Apps with Cloudflare Tunnel"
description = "Discover how Cloudflare Tunnels and the Zero Trust dashboard can help secure your self-hosted applications."
draft = false
+++

## Cloudflare Zero Trust

### Overview

Cloudflare has slowly released a set of tools across the past few years that are
aimed at replacing legacy security perimeters with their own edge servers. These
tools have culminated in the
[Cloudflare Zero Trust](https://developers.cloudflare.com/cloudflare-one/)
product.

For personal users of Cloudflare (e.g., me) who use Cloudflare as a buffer or
DMZ between their homes and the public internet, some of these tools are
incredibly useful for fighting spam and malicious attacks.

One of the biggest benefits of using Cloudflare Tunnel is that you can close
ports on your home network and prevent common spam and abuse that people and
bots try to attack. I found that my home network went from ~100 attacks per day
to zero after setting up Cloudflare Tunnel and closing ports `80` and `443`!

In this post, I will be walking through the process of setting up a tunnel
between Cloudflare and my home network (using the GUI method, not in a
terminal), connecting local services to domain names, and protecting them all
with an application authentication.

### Getting Started

Once you're ready to set-up a security configuration, launch the
[Cloudflare Zero Trust Dashboard](https://dash.teams.cloudflare.com) and log in
to your account.

Once you're in, you should see your personal dashboard. In my case, the
dashboard shows how many users are connected, top logins by application, and
links to various pages.

![Cloudflare Zero Trust Dashboard](https://img.cleberg.net/blog/20220408-cloudflare-tunnel/cloudflare_zero_trust.png)

## Cloudflare Tunnel Set-Up

### Initial Config

To create your first tunnel, open the `Access` section on the left-hand side of
the Zero Trust Dashboard and then open the `Tunnels` link.

On the Tunnels page, use the `Create a Tunnel` button to open the initial
configuration page. These settings will ask you to name your tunnel first:

![Tunnel Name](https://img.cleberg.net/blog/20220408-cloudflare-tunnel/tunnel_name.png)

Next, you will need to install the connector for your system and the guide page
will provide the proper commands to run on your system. For example, here's a
test tunnel I've created on a macOS system:

![Tunnel Connector](https://img.cleberg.net/blog/20220408-cloudflare-tunnel/tunnel_connector.png)

Finally, the last step is to connect a local service on your connected machine
to the tunnel. In the example below, I'm telling Cloudflare that I have an
application running on port 3000 of the local host machine.

Note that if you currently have an `A` record for the domain you're using to
connect this app, you will need to delete it so that the tunnel can create a
`CNAME` instead.

![Tunnel Routing](https://img.cleberg.net/blog/20220408-cloudflare-tunnel/tunnel_routing.png)

Once set-up, your application traffic is now routed through a Cloudflare tunnel
and will not expose your IP address or local network information to public
visitors.

### Adding Additional Apps

If you would like to add additional applications to the tunnel, simply go back
to the tunnel page, click `Configure`, open the `Public Hostname` tab, and add
your new public host names.

## Cloudflare Application Access

### Overview

Finally, you can add an additional layer of security to your applications with
Cloudflare's application authentication. So far, the tunnel protects your local
network information but still tunnels public traffic straight to your local
ports.

If you would also like to restrict access to yourself or a group of people, keep
reading to find out how to set-up the application authentication.

### Set-Up Process

To get started, click the `Access` menu item in the left-hand side of the
dashboard and open the `Applications` page.

Next, click the `Add an Application` button and choose the appropriate type of
application that you are adding. When in doubt, choose the `Self-hosted` option.

![App Types](https://img.cleberg.net/blog/20220408-cloudflare-tunnel/app_types.png)

Next, set-up the app with a name, point it toward the proper domain name, and
customize the app's appearance and authentication methods.

![App Config](https://img.cleberg.net/blog/20220408-cloudflare-tunnel/app_config.png)

Finally, you will need to create a policy that defines who can and cannot access
this application. In the example below, I've told Cloudflare that
`user01@example.com` and `user02@example.com` are allowed to access this app and
any users in Antarctica are not allowed to access it.

![Access Policy](https://img.cleberg.net/blog/20220408-cloudflare-tunnel/access_policy.png)

We are all done now. When you attempt to access your application in the browser,
you will be greeted with a Cloudflare access authentication page.

Following the policy above, I'd need to enter `user01@example.com` or
`user02@example.com` as my email and then enter the authentication code into the
access page to be able to login to the application.

Note that if your local service has its own authentication page, you will need
to login to that local service once you pass the Cloudflare authentication page.

![Cloudflare Application Authentication](https://img.cleberg.net/blog/20220408-cloudflare-tunnel/cloudflare_app_authentication.png)

While there are different ways to protect local applications, I've found that
Cloudflare's tunnel and authentication products allow for a much higher sense of
security for self-hosted applications in my network.
