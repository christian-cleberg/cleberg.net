+++
date = 2022-11-11
title = "Fixing Permission Errors in /var/lib/nginx"
description = "A quick fix for Nginx permission errors on the tmp directory."
+++

*This is a very short post so that I personally remember the solution as it has 
popped up a few times for me.*

## The Problem

After migrating to a new server OS, I started receiving quite a few permission 
errors like the one below. These popped up for various different websites I'm 
serving via Nginx on this server, but did not prevent the website from loading.

I found the errors in the standard log file:

```bash
cat /var/log/nginx/error.log
```

```bash
2022/11/11 11:30:34 [crit] 8970#8970: *10 open() "/var/lib/nginx/tmp/proxy/3/00/0000000003" failed (13: Permission denied) while reading upstream, client: 169.150.203.10, server: cyberchef.cleberg.io, request: "GET /assets/main.css HTTP/2.0", upstream: "http://127.0.0.1:8111/assets/main.css", host: "cyberchef.cleberg.io", referrer: "https://cyberchef.cleberg.io/"
```

## The Solution

In order to resolve the issue, I had to ensure the `/var/lib/nginx` 
directory is owned by Nginx. Mine was owned by the `www` user and Nginx was not 
able to read or write files within that directory. This prevented Nginx from 
caching temporary files.

```bash
# Alpine Linux
doas chown -R nginx:nginx /var/lib/nginx/

# Other Distros
sudo chown -R nginx:nginx /var/lib/nginx
```

You *may* also be able to change the `proxy_temp_path` in your Nginx config, but 
I did not try this. Here's a suggestion I found online that may work if the 
above solution does not:

```conf
# Set the proxy_temp_path to your preference, make sure it's owned by the 
# `nginx` user
proxy_temp_path /tmp;
```

Finally, restart Nginx and your server should be able to cache temporary files 
again.

```bash
# Alpine Linux (OpenRC)
doas rc-service nginx restart

# Other Distros (systemd)
sudo systemctl restart nginx
```
