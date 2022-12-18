+++
date = 2022-12-07
title = "Redirect Nginx Subdomains & Trailing Content with Regex"
description= "You can easily redirect Nginx traffic from subdomains and TLDs to a new domain while preserving trailing content."
+++

## Problem

I recently migrated from `cleberg.io` to `cleberg.net` and replaced the
`cleberg.io` homepage with a simple info page with instructions to users on how
to edit their bookmarks and URLs to get to the page they were seeking.

This was not ideal as it left the work up to the user and may have caused
friction for users who accessed my RSS feed.

## Solution

Instead, I finally found a solution that allows me to redirect both subdomains
AND trailing content. For example, both of these URLs now redirect properly
using the logic I'll explain below:

```text
# Example 1 - Simple base domain redirect with trailing content
https://cleberg.io/blog/alpine-linux/ -> https://cleberg.net/blog/alpine-linux/

# Example 2 - Complex redirect with both a subdomain and trailing content
https://libreddit.cleberg.io/r/history/comments/7z8cbg/new_discovery_mode_turns_video_game_assassins/
->
https://libreddit.cleberg.net/r/history/comments/7z8cbg/new_discovery_mode_turns_video_game_assassins/
```

Go ahead, try the URLs if you want to test them.

### Nginx Config

To make this possible. I needed to configure a proper redirect scheme in my
Nginx configuration.

```sh
doas nano /etc/nginx/http.d/cleberg.io.conf
```

Within this file, I had one block set to redirect HTTP requests to HTTPS for the
base domain and all subdomains.

```conf
server {
        listen [::]:80;
        listen 80;
        server_name cleberg.io *.cleberg.io;

        if ($host = cleberg.io) {
                return 301 https://$host$request_uri;
        }

        if ($host = *.cleberg.io) {
                return 301 https://$host$request_uri;
        }

        return 404;
}
```

For the base domain, I have another `server` block dedicated to redirecting all
base domain requests. You can see that the `rewrite` line is instructing Nginx
to gather all trailing content and append it to the new `cleberg.net` URL.

```conf
server {
        listen [::]:443 ssl http2;
        listen 443 ssl http2;

        server_name cleberg.io;

        rewrite ^/(.*)$ https://cleberg.net/$1 permanent;

        ssl_certificate     /etc/letsencrypt/live/cleberg.io/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/cleberg.io/privkey.pem;
}
```

Finally, the tricky part is figuring out how to tell Nginx to redirect while
keeping both a subdomain and trailing content intact. I found that the easiest
way to do this is to give it a `server` block of its own.

Within this block, we need to do some regex on the `server_name` line before we
can rewrite anything. This creates a variable called `subdomain`.

Once the server gets to the `rewrite` line, it pulls the `subdomain` variable
from above and uses it on the new `cleberg.net` domain before appending the
trailing content (`$request_uri`).

```conf
server {
        listen [::]:443 ssl http2;
        listen 443 ssl http2;

        server_name ~^(?<subdomain>\w+)\.cleberg\.io$;

        rewrite ^ https://$subdomain.cleberg.net$request_uri permanent;

        ssl_certificate     /etc/letsencrypt/live/cleberg.io/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/cleberg.io/privkey.pem;
}
```

That's all there is to it. With this, I simply restarted Nginx and watched the
redirections work in-action.

```sh
doas rc-service nginx restart
```


Looking back on it, I wish I had done this sooner. Who knows how many people
went looking for my sites or bookmarks and gave up when they saw the redirect
instructions page.

Oh well, it's done now. Live and learn.
