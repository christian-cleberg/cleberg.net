+++
date = 2020-12-27
title = "Redesigning My Website: The 5 KB Result"
description = "Read about the status of this website, its minimal design, and my personal opinions on the state of web minimalism."
+++

## A Brief History

As a form of continuous learning and entertainment, I've been running a handful of websites since 2016 when I took my first programming courses in college. One of the websites I maintain is [cleberg.io](https://cleberg.io), the place I consider the official website to represent me. Under this site, I have a handful of sub-directories and sub-domains.

One of the parts I've enjoyed the most about web development is the aspect of designing an identity for a web page and working to find exciting ways to display the site's content. Inevitably, this means I've changed the designs for my websites more times than I could possibly count. Since I don't really host anything on my main webpage that's vital, it allows me the freedom to change things as inspiration strikes.

Historically, I've relied on core utilities for spacing, components, and layouts
from [Bootstrap](https://getbootstrap.com) and added custom CSS for fonts, accents, colors, and other items. I also tend to create sites with no border radius on items, visible borders, and content that takes up the entire screen (using whitespace inside components instead of whitespace around my components).

### The Old Design

![Old website design](https://img.cleberg.io/blog/20201227-minimal-website-redesign/old_design.png)

## The Redesign Process

About a week ago, I found myself wishing for a new design yet again. The prior design was largely inspired by IBM's [Carbon Design System](https://www.carbondesignsystem.com) and relied on jQuery, Bootstrap, along with some compressed [.png](https://en.wikipedia.org/wiki/WebP) images.

To anyone who knows my preferences toward web design - and even in my personal life - it should be no surprise that I immediately started looking for inspiration on minimalism. While there are some decent minimalistic designs on sites like [Dribbble](https://dribbble.com/search/shots/popular/web-design?q=minimalism), people seem to mostly discuss [brutalist web design](https://brutalist-web.design) when you ask about minimalism. While brutalist web design doesn't have to be minimal, it often is.

I suppose, in a way, I did create a brutalist website since my HTML is semantic and accessible, hyperlinks are colored and underlined, and all native browser functions like scrolling and the back button work as expected. However, I didn't think about brutalism while designing these sites.

The new design followed a simple design process. I walked through the screens on my blog and asked myself: "Is this element necessary for a user?" This allowed me to first start by removing all javascript, which had the sole purpose of allowing users to open a collapsed navbar on mobile. Replacing the collapsible navbar allowed me to remove both jQuery and Bootstrap's javascript.

Next, I removed things like author names (since I'm literally the only person who will ever write on this site), multiple ways to click on a blog post card, blog post descriptions, and the scroll-to-top button. It also helped to move all categories to a single page, rather than have each category on its own page.

The final big piece to finish the "[KonMari](https://en.wikipedia.org/wiki/Marie_Kondo#KonMari_method)"-like part of my process was to remove Bootstrap CSS in its entirety. However, this meant pulling out a few very useful classes, such as `.img-fluid` and the default font stacks to keep in my custom CSS.

After removing all the unnecessary pieces, I was finally able to reorganize my content and add a very small amount of custom CSS to make everything pretty. This took a very short amount of time, effectively just consisting of me converting `<div>` tags into things like `<ul>` lists and choosing accent colors.

## The Results

### The New Design

![New website design](https://img.cleberg.io/blog/20201227-minimal-website-redesign/new_design.png)

### Reflection

So, what did all of this reorganizing do to [cleberg.io](https://cleberg.io)
and [cleberg.io/blog/](https://cleberg.io/blog/)? Well first, my websites are now **ridiculously fast**. Since the prior designs were also minimal and didn't have many images, they measured up in Firefox's Network Monitor around 300 KB - 600KB. After making the changes, my main site is at 5 KB transferred (22 KB total), and my blog is at 6.5 KB transferred (13 KB total). **That means the redesigned pages are less than 2% the size of the old designs.**

Google Lighthouse ranks [cleberg.io](https://cleberg.io) as 100 in performance, accessibility, and best practices - withSEO at 92 since they think tap targets are not sized appropriately for mobile users. First contextual paints of the pages are under 0.8 seconds with 0 ms of blocking time. However, [cleberg.io/blog/](https://cleberg.io/blog/) ranks at 100 for all four categories! First contextual paints of the blog homepage are under 1.0 seconds with 0 ms of blocking time, due to the fact that the CSS for my blog is within a separate CSS file and the CSS for my main website is simply embedded in the HTML file.

Now that everything is complete, I can confidently say I'm happy with the result and proud to look at the fastest set of websites I've created so far.
