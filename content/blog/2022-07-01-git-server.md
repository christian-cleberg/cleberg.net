+++
date = 2022-07-01
title = "Self-Hosting a Personal Git Server"
description = "My retrospective on successfully setting up a personal git server at home."
+++

## My Approach to Self-Hosting Git

I have often tried to self-host my Git repositories, but have always fallen 
short when I tried to find a suitable web interface to show on the front-end.

After a few years, I have finally found a combination of methods that allow me 
to easily self-host my projects, view them on the web, and access them from 
anywhere.

Before I dive into the details, I want to state a high-level summary of my 
self-hosted Git approach:

- This method uses the `ssh://` and `git://` protocols for push and pull access.
  - For the `git://` protocol, I create a `git-daemon-export-ok` file in any 
  repository that I want to be cloneable by anyone.
  - The web interface I am using (`cgit`) allows dumb HTTP cloning by default. I 
  do not disable this setting as I want beginners to be able to clone a 
  repository even if they don't know the proper method.
- I am not enabling Smart HTTPS for any repositories. Updates to repositories 
must be pushed via SSH.
- Beyond the actual repository management, I am using `cgit` for the front-end 
web interface.
  - As far as I am aware, you can't "exclude" a repository from `cgit` if it's 
  stored within the directory that `cgit` reads. To host private repositories, 
  you'd need to set up another directory that `cgit` can't read.

### Assumptions

For the purposes of this walkthrough, I am assuming you have a URL 
(`git.example.com`) or IP address (`207.84.26.991`) addressed to the server that 
you will be using to host your git repositories.

## Adding a Git User

In order to use the SSH method associated with git, we will need to add a 
user named `git`. If you have used the SSH method for other git hosting sites, 
you are probably used to the following syntax:

```bash
git clone [user@]server:project.git
```

The syntax above is an `scp`-like syntax for using SSH on the `git` user on the 
server to access your repository.

Let's delete any remnants of an old `git` user, if any, and create the new user 
account:

```bash
sudo deluser --remove-home git
sudo adduser git
```

### Import Your SSH Keys to the Git User

Once the `git` user is created, you will need to copy your public SSH key on 
your local development machine to the `git` user on the server.

If you don't have an SSH key yet, create one with this command:

```bash
ssh-keygen
```

Once you create the key pair, the public should be saved to `~/.ssh/id_rsa.pub`.

If your server still has password-based authentication available, you can copy 
it over to your user's home directory like this:

```bash
scp ~/.ssh/id_rsa.pub your_user@your_server:
```

Once on the server, you will need to copy the contents into the `git` user's 
`authorized_keys` file:

```bash
cat id_rsa.pub > /home/git/.ssh/authorized_keys
```

### (Optional) Disable Password-Based SSH

If you want to lock-down your server and ensure that no one can authenticate in 
via SSH with a password, you will need to edit your SSH configuration.

```bash
sudo nano /etc/ssh/sshd_config
```

Within this file, find the following settings and set them to the values I am 
showing below:

```conf
PermitRootLogin no
PasswordAuthentication no
AuthenticationMethods publickey
```

You may have other Authentication Methods required in your personal set-up, so 
the key here is just to ensure that `AuthenticationMethods` does not allow 
passwords.

## Setting up the Base Directory

Now that we have set-up a `git` user to handle all of transport methods, we need 
to set-up the directory that we will be using as our base of all repositories.

In my case, I am using `/git` as my source folder. To create this folder and 
assign it to the user we created, execute the following commands:

```bash
sudo mkdir git
sudo chown -R git:git /git
```

## Creating a Test Repository

On your server, switch over to the `git` user in order to start managing git 
files.

```bash
su git
```

Once logged-in as the `git` user, go to your base directory and create a test 
repository.

```bash
cd /git
mkdir test.git && cd test.git
git init --bare
```

If you want to make this repo viewable/cloneable to the public via the `git://` 
protocol, you need to create a `git-daemon-export-ok` file inside the 
repository.

```bash
touch git-daemon-export-ok
```

## Opening the Firewall

Don't forget to open up ports on the device firewall and network firewall, if 
you want to access these repositories publicly. If you're using default ports, 
forward ports `22` (ssh) and `8169` (git) from your router to your server's IP 
address.

If your server also has a firewall, ensure that the firewall allows the same 
ports that are forwarded from the router. For example, if you use `ufw`:

```bash
sudo ufw allow 22
sudo ufw allow 8169
```

### Non-Standard SSH Ports

If you use a non-standard port for SSH, such as `9876`, you will need to create 
an SSH configuration file on your local development machine in order to connect 
to your server's git repositories.

To do this, you'll need to define your custom port on your client machine in 
your `~/.ssh/config` file:

```bash
nano ~/.ssh/config
```

```conf
Host git.example.com
  HostName git.example.com
  Port 9876
  User git
```

### Testing SSH

There are two main syntaxes you can use to manage git over SSH:

- `git clone [user@]server:project.git`
- `git clone ssh://[user@]server/project.git`

I prefer the first, which is an `scp`-like syntax. To test it, try to clone the 
example repository you set up on the server:

```bash
git clone git@git.example.com:/git/test.git
```

## Enabling Read-Only Access

If you want people to be able to clone any repository where you've placed a 
`git-daemon-export-ok` file, you will need to start the git daemon.

To do this on a system with `systemd`, create a service file:

```bash
sudo nano /etc/systemd/system/git-daemon.service
```

```conf
[Unit]
Description=Start Git Daemon

[Service]
ExecStart=/usr/bin/git daemon --reuseaddr --base-path=/git/ /git/

Restart=always
RestartSec=500ms

StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=git-daemon

User=git
Group=git

[Install]
WantedBy=multi-user.target
```

Once created, enable and start the service:

```bash
sudo systemctl enable git-daemon.service
sudo systemctl start git-daemon.service
```

To clone read-only via the `git://` protocol, you can use the following syntax:

```bash
git clone git://git.example.com/test.git
```

## Migrating Repositories

At this point, I have a working git server that works with both SSH and 
read-only access.

For each of the repositories I had hosted a different provider, I executed the 
following commands in order to place a copy on my server as my new source of 
truth:

Server:

```bash
su git
mkdir /git/<REPOSITORY_NAME>.git && cd /git/<REPOSITORY_NAME>.git
git init --bare
# If you want to make this repo viewable/cloneable to the public
touch git-daemon-export-ok
```

Client:

```bash
git clone git@<PREVIOUS_HOST>:<REPOSITORY_NAME>
git remote set-url origin git@git.EXAMPLE.COM:/git/<REPOSITORY_NAME>.git
git push
```

## Using `cgit`

If you want a web viewer for your repositories, you can use various tools, such 
as `gitweb`, `cgit`, or `klaus`. I chose `cgit` due to its simple interface and 
fairly easy set-up (compared to others).

### Docker

Installing via Docker is as simple as:

```bash
sudo docker run --name=cgit -d -p 8763:80 -v /git:/git invokr/cgit
```

Once it's finished installing, you can access the site at <SERVER_IP>:8763 or 
use a reverse-proxy service to forward `cgit` to a URL, such as 
`git.example.com`.

### Nginx Reverse Proxy

I am using Nginx as my reverse proxy so that the `cgit` Docker container can use 
`git.cleberg.net` as its URL. To do so, I simply created the following 
configuration file:

```bash
sudo nano /etc/nginx/sites-available/git.example.com
```

```conf
server {
        listen 80;
	      server_name git.example.com;

      	if ($host = git.example.com) {
		        return 301 https://$host$request_uri;
	      }

    	  return 404;
}

server {
        server_name git.example.com;
        listen 443 ssl http2;

        location / {
                # The final `/` is important.
		            proxy_pass http://localhost:8763/;
                add_header X-Frame-Options SAMEORIGIN;
                add_header X-XSS-Protection "1; mode=block";
                proxy_redirect off;
                proxy_buffering off;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_set_header X-Forwarded-Port $server_port;
        }

        # INCLUDE ANY SSL CERTS HERE
        include /etc/letsencrypt/options-ssl-nginx.conf;
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}

```

Once created, symlink it and restart the server.

```bash
sudo ln -s /etc/nginx/sites-available/git.example.com /etc/nginx/sites-enabled/
sudo systemctl restart nginx.service
```

As we can see below, my site at [git.cleberg.net](https://git.cleberg.net) is 
available and running:

![cgit example](https://img.cleberg.io/blog/20220701-git-server/cgit.png)

### Settings Up Git Details

Once you have `cgit` running, you can add some small details, such as repository 
owners and descriptions by editing the following files.

The `description` file within the repository on your server will display the 
description online.

```bash
cd /git/example.git
nano description
```

You can add a `[gitweb]` block to the `config` file in order to display the 
owner of the repository.

```bash
cd /git/example.git
nano config
```

```conf
[gitweb]
	owner = "YourName"
```

### Editing `cgit`

If you want to edit other items, such as the actual site name or description of 
`cgit`, you will need to open the Docker container and edit the `cgitrc` file 
inside:

```bash
sudo docker exec -it cgit bash
vi /etc/cgitrc
```

Below is an example configuration for `cgitrc`. You can find all of the 
configuration options within the 
[configuration manual](https://git.zx2c4.com/cgit/plain/cgitrc.5.txt).

```conf
css=/cgit.css
logo=/cgit.png
robots=noindex, nofollow

enable-index-links=1
enable-commit-graph=1
enable-blame=1
enable-log-filecount=1
enable-log-linecount=1
enable-git-config=1

# COMMENT-OUT THIS LINE OR ELSE YOUR FILE PREVIEWS MAY NOT WORK
# source-filter=/opt/highlight.sh
section-from-path=2
scan-path=/git

clone-url=git://git.example.com/$CGIT_REPO_URL ssh://git@git.example.com:/git/$CGIT_REPO_URL

root-title=<YOUR_WEBSITE_NAME>
root-desc=<YOUR_WEBSITE_DESCRIPTION>

# Highlight source code with python pygments-based highlighter
source-filter=/var/www/cgit/filters/syntax-highlighting.py

# Format markdown, restructuredtext, manpages, text files, and html files
# through the right converters
about-filter=/var/www/cgit/filters/about-formatting.sh

##
## List of common mimetypes
##
mimetype.gif=image/gif
mimetype.html=text/html
mimetype.jpg=image/jpeg
mimetype.jpeg=image/jpeg
mimetype.pdf=application/pdf
mimetype.png=image/png
mimetype.svg=image/svg+xml

##
## Search for these files in the root of the default branch of repositories
## for coming up with the about page:
##
readme=:README.md
readme=:readme.md
readme=:README.mkd
readme=:readme.mkd
readme=:README.rst
readme=:readme.rst
readme=:README.html
readme=:readme.html
readme=:README.htm
readme=:readme.htm
readme=:README.txt
readme=:readme.txt
readme=:README
readme=:readme
readme=:INSTALL.md
readme=:install.md
readme=:INSTALL.mkd
readme=:install.mkd
readme=:INSTALL.rst
readme=:install.rst
readme=:INSTALL.html
readme=:install.html
readme=:INSTALL.htm
readme=:install.htm
readme=:INSTALL.txt
readme=:install.txt
readme=:INSTALL
readme=:install
```

## Backups

The last thing to note is that running services on your own equipment means that 
you're assuming a level of risk that exists regarding data loss, catastrophes, 
etc. In order to reduce the impact of any such occurrence, I suggest backing up 
your data regularly.

Backups can be automated via `cron`, by hooking your base directory up to a 
cloud provider, or even setting up hooks to push all repository info to git 
mirrors on other git hosts. Whatever the method, make sure that your data 
doesn't vanish in the event that your drives or servers fail.
