# cleberg.net

This is my personal Markdown-based website and blog, using
[Zola](https://www.getzola.org) to generate static-site files.

## Building & Deployment

If you want to use this structure to create your site, simply clone the repo and
rename it to something meaningful:

```bash
git clone https://github.com/christian-cleberg/cleberg.net
```

```bash
mv cleberg.net <your-website>
```

```bash
cd <your-website>
```

Make changes to the config, templates, static, and content files to make this
your own website. You can preview the files, with live updates:

```bash
zola serve
```

When you're finished with your updates, build the static files:

```bash
zola build
```

Finally, copy the new static files to your web server location:

```bash
sudo cp -r public/* /path/to/<your-website-directory>
```
