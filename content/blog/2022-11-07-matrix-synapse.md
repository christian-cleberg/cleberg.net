+++
date = 2022-11-07
title = "Self-Hosting Matrix Synapse on Alpine Linux"
description = "A short guide on how I was able to get Synapse working on Alpine Linux."
+++

## Synpase

If you're reading this, you likely know that Synapse is a popular Matrix 
homeserver software that allows users to run their own Matrix homeserver.

This post is a short guide describing how I was able to get Synapse working in a 
minimally-useable state on Alpine Linux.

## Installation Process

### Dependencies

First, since there is no Alpine-specific packge for Synapse, we need to ensure 
that Alpine has the required dependencies for the Python-based install.

```bash
doas apk -U update
doas apk add python3 py3-virtualenv
```

Next, we need to set-up the Python environment for Synapse to run inside a 
virtual environment:

```bash
mkdir -p ~/synapse && cd ~/synapse
virtualenv -p python3 ~/synapse/env
source ~/synapse/env/bin/activate
pip install --upgrade pip
pip install --upgrade setuptools
pip install matrix-synapse
```

### Running Synapse

Once installed, running Synapse is easy. Simple execute the following command, 
replacing `example.com` with the domain name that will be used with this 
homeserver.

```bash
python -m synapse.app.homeserver \
    --server-name example.com \
    --config-path homeserver.yaml \
    --generate-config \
    --report-stats=no
```

Once the configuration is generated, we can start-up the Synapse server:

```bash
synctl start
```

### Configuring Synapse

To make any change to Synapse, we need to edit the `YAML` configuration file:

```bash
nano ~/synapse/homeserver.yaml
```

For now, we just need to ensure the `server_name` is accurate. However, there 
are a lot of other configuration options found in the [Configuring 
Synapse](https://matrix-org.github.io/synapse/develop/usage/configuration/config_documentation.html) 
documentation that can be enabled/disabled at this point.

```yaml
server_name: "example.com"
```

Make sure to restart Synapse when you make changes to the configuration:

```bash
synctl restart
```

### Nginx Reverse-Proxy

To ensure that Synapse is reachable from the public, we need to connect our 
domain to the Synapse server. In my case, I use an Nginx reverse-proxy for this 
purpose.

To use Nginx, we need to create a reverse-proxy configuration file:

```bash
doas nano /etc/nginx/http.d/example.com.conf
```

If you already have TLS certificates for this domain (`example.com`), you can 
simply use the SSL configuration and point toward your TLS certificates.

```bash
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    # For the federation port
    listen 8448 ssl http2 default_server;
    listen [::]:8448 ssl http2 default_server;

    server_name matrix.cleberg.io;

    location ~ ^(/_matrix|/_synapse/client) {
        # note: do not add a path (even a single /) after the port in `proxy_pass`,
        # otherwise nginx will canonicalise the URI and cause signature verification
        # errors.
        proxy_pass http://localhost:8008;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $host;

        # Nginx by default only allows file uploads up to 1M in size
        # Increase client_max_body_size to match max_upload_size defined in homeserver.yaml
        client_max_body_size 50M;
    }

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    access_log /var/log/nginx/matrix.access.log;
}

server {
	if ($host = example.com) {
		return 301 https://$host$request_uri;
	}

  server_name example.com;
  listen 80;
	return 404;
}
```

If you need to generate TLS certificates (I recommend 
[Certbot](https://certbot.eff.org/)), you'll need a more minimal Nginx conf file 
before you can use the TLS-enabled example above. Instead, use this 
configuration file during the Certbot certificate generation process:

```conf
server {
  server_name example.com;
  location / {
      try_files $uri $uri/ =404;
  }
  listen 80;
}
```

Once you're done editing the Nginx conf file, restart Nginx:

```bash
doas rc-service nginx restart
```

If you still need to generate TLS certificates, run `certbot` now and obtain the 
certificates. This command will ask if you want to use a webroot or spin up a 
temporary web server - I highly recommend using the temporary web server due to 
the many issues with using a webroot. You will need to stop Nginx to do this:

```bash
# Stop Nginx so certbot can spin up a temp webserver for cert generation
doas rc-service nginx stop
doas certbot certonly -v
doas rc-service nginx start
```

### Open Firewall & Router Ports

If you use a firewall on the server, open the `8448` port for discovery and 
federation. If you want additional services, such as voice calls, you will need 
to read the Synapse documentation to see which ports need to be opened for those 
features.

```bash
# UFW port example
doas ufw allow 8448
```

Remember to forward any Synapse ports, such as `8448`, in your Router from the 
internet to your server.

### Adding Matrix Users

Finally, if you didn't enable public registration in the `homeserver.yaml` file, 
you can manually create users via the command-line:

```bash
cd ~/synapse
register_new_matrix_user -c homeserver.yaml
```

Remember that the format for federated Matrix usernames is 
`@username:example.com`! Once Synapse is running and you have a username, you 
are ready to login to a Matrix client and start sending messages, joining rooms, 
and utilizing your very own Matrix server.
