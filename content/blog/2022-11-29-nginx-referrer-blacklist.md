+++
date = 2022-11-29
title = "Creating a Referrer Blacklist on Nginx"
description = "A quick explanation detailing my own way of blacklisting referral domains on Nginx."
+++

## Creating the Blacklist

In order to blacklist referral domains or websites with Nginx, you need to 
create a blacklist file. The file below will accept regexes for different 
domains or websites you wish to block.

First, create the file in your nginx directory:

```sh
doas nano /etc/nginx/blacklist.conf
```

Next, paste the following contents in and fill out the regexes with whichever 
domains you're blocking.

```conf
# /etc/nginx/blacklist.conf

map $http_referer $bad_referer {
    hostnames;

    default                           0;

    # Put regexes for undesired referrers here
    "~news.ycombinator.com"           1;
}
```

## Configuring Nginx

In order for the blacklist to work, Nginx needs to know it exists and how to 
handle it. For this, edit the `nginx.conf` file.

```sh
doas nano /etc/nginx/nginx.conf
```

Within this file, find the `http` block and add your blacklist file location to 
the end of the block.

```conf
# /etc/nginx/nginx.conf

http {
  ...

  # Include blacklist
  include /etc/nginx/blacklist.conf;
}
```

## Enabling the Blacklist

Finally, we need to take action when a bad referral site is found. To do so, 
edit the configuration file for you website. For example, I have all website 
configuration files in the `http.d` directory. You may have them in the 
`sites-available` directory on some distributions.

```sh
doas nano /etc/nginx/http.d/example.com.conf
```

Within each website's configuration file, edit the `server` blocks that are 
listening on ports 80 and 443 and create a check for the `$bad_referrer` 
variable we created in the blacklist file.

If a matching site is found, you can return any [HTTP Status 
Code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) you want. Code 
403 (Forbidden) is logical in this case since you are preventing a client 
connection due to a blacklisted domain.

```conf
server {
  ...

  # If a referral site is blacklisted, return an error
  if ($bad_referer) {
    return 403;
  }
  
  ...
}
```

## Restart Nginx

Lastly, restart Nginx to enable all changes made.

```sh
doas rc-service nginx restart
```

## Testing the Results

In order to test the results, let's curl the contents of our site. To start, 
I'll curl the site normally:

```sh
curl https://cleberg.net
```

The HTML contents of the page come back successfully:

```html
<!doctype html>...</html>
```

Next, let's include a blacklisted referrer:

```sh
curl --referer https://news.ycombinator.com https://cleberg.net
```

This time, I'm met with a 403 Forbidden response page. That means we are 
successful and any clients being referred from a blacklisted domain will be met 
with this same response code.

```html
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx</center>
</body>
</html>
```
