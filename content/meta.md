+++
title = "Meta"
description = "Meta information about this website."
+++

*This page contains meta information about this website.*

## Style

### Written

My writing style is a reflection of my true writing style; I always write what I
think and rarely edit anything, aside from minor grammatical errors.

### Visual

I tend to favor minimalistic designs with a heavy emphasis on utility. If
something does not serve a purpose, I likely do not want it on my website. Some
people love this, some hate it. Either way, it's what I prefer.

As a result, I tend to use fewer images now. Older articles will have more
images for descriptive purposes.

## Software

I edit all files with [nano](https://nano-editor.org) and use
[minify](https://github.com/tdewolff/minify/tree/master/cmd/minify) to minify
CSS like so:

```sh
minify -o static/styles.min.css static/styles.css
```

This website utilizes the [Zola](https://getzola.org) static site
generator, which requires the Rust language. Internally, Zola uses
[Tera](https://tera.netlify.app) for templating and implements written content
in [CommonMark](https://commonmark.org).

At the server level, this website runs uses the [Nginx](https://nginx.org) web
server on [Ubuntu](https://ubuntu.com).

## Hardware

The server running this website is a custom-built, rack-mounted server that you
can [read about here](/blog/server-build/).
