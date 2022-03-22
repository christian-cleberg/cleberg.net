# cleberg.io

This is a Markdown-based website and blog, using [Zola](https://www.getzola.org) to generate static-site files.

## Building & Deployment

If you use this structure to create your site, simply clone the repo:

```bash
git clone https://github.com/user/repo
```

Then, enter the repo and build the static files:

```bash
cd repo
zola build
```

Finally, copy the new static files to your web server location:

```bash
sudo cp -r public/* /var/www/website_directory
```
