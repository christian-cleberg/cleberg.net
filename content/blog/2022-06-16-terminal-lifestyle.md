+++
date = 2022-06-16
title = "A Terminal Lifestyle"
description = "A detailed description on how I'm living my digital life in the terminal."
+++

## Text-Based Simplicity

I've detailed my views on web-based minimalism and related topics, in other 
posts throughout the years, such as JavaScript/CSS bloat slowing down websites 
that are essentially a text document. However, I have never really expanded 
beyond talking about the web and describing how I focus on minimalizing 
distractions in other digital environments.

This post is going to set the baseline for how I *try* to live my digital life. 
It does not necessarily get into my physical life, which is often harder to 
control and contain all of the noise in our modern world.

While there are new things to do every day in our digital world, I find that 
keeping a core set of values and interests can ground you and keep you mindful 
of *why* you are participating in the digital world. For example, if - at your 
core - you have no interest in what strangers think about random topics, it 
would be unwise to start participating in social media. However, I am someone 
who has been dragged in by effective advertising to participate in communities 
that I realize I do not care for.

I won't dive much further into explaining the philosophy of all this, but I will 
link a few helpful articles that may pique your interest if you're in search of 
more meaningful experiences:

- [Mindfulness](https://en.wikipedia.org/wiki/Mindfulness)
- [Minimalism](https://en.wikipedia.org/wiki/Minimalism)
- [Stoicism](https://en.wikipedia.org/wiki/Stoicism)

## Living Life in the Terminal 

My personal approach to reducing digital distractions and increasing my focus on 
the task at hand is to use a terminal for as much as I possibly can.

Most days, this means that I have 3-5 tabs open in my terminal:

1. A web browser
2. A chat client
3. An email client
4. A local shell for navigating my computer's files
5. A remote shell for managing servers and other machines

Beyond this, I rarely open other tabs or GUI applications, unless absolutely 
necessary. If you look, you may be surprised what can be accomplished in the 
terminal.

For example, I have moved my music and entertainment downloads to the 
terminal, along with my device VPN connections. I am exploring options for 
moving my RSS subscriptions to something like [Newsboat](https://newsboat.org/), 
so that I can read my daily articles without all the fuss.

Now that we have some of the examples out of the way, let's dive into the 
specifics.

### Browsing the Web

I'm going to start off with a hard topic for those who prefer to live in the 
terminal: web browsing. This task is made hard mostly by websites and web apps 
that require JavaScript to run. The other difficult part is that if you're using 
a text-based browser, that means images won't load (hopefully that's obvious).

I am using [Lynx](https://lynx.invisible-island.net), a text-based browser that 
runs quickly and easily in the terminal. Lynx allows me to browser most websites 
by simply typing `g` and then typing in the URL I want.

![Lynx](https://img.cleberg.io/blog/20220616-terminal-lifestyle/lynx.png)

If you need a search engine while in Lynx, I recommend [DuckDuckGo 
(Lite)](https://lite.duckduckgo.com/lite/), which allows you to search the web 
using their text-only interface.

![DuckDuckGo 
!Lite](https://img.cleberg.io/blog/20220616-terminal-lifestyle/ddg.png)

Eventually, you will run into websites that don't work (or are just too ugly 
and messy) in a text-only mode and you'll be forced to switch over to a GUI 
browser to look at that site. Personally, I don't mind this as it doesn't happen 
as often as I thought it would.

The only time I need to do this is when I want to browse an image/video-focused 
webpage or if I need to login to a site and it doesn't support a text-only login 
page. For example, I am able to easily login to [Sourcehut](https://sr.ht) in 
lynx.

### Chatting with Friends

After web browsing activities, my main form of terminal communication is Matrix. 
I use the [gomuks](https://docs.mau.fi/gomuks/) client currently.

This was incredibly easy to install on macOS (but I will need to see if it'll 
be just as easy on Linux when my new laptop arrives):

```bash
brew install gomuks
```

Once you launch gomuks, it will sync and require your username and password to 
login. After doing so, the only problem I ran into was verifying my gomuks 
client so that I could participate in rooms with E2EE.

Finally, I was able to verify the session by opening the Element desktop app (I 
assume you can do this in the browser and mobile app too, but I'm not sure) and 
manually verifying myself with this process:

1. Open the Element desktop app
2. Open a room I was a member of
3. Open the `Room Info` pane
4. Open the `People` menu and search for myself
5. Click on my profile name
6. Click on the sessions link under the `Security` section and follow the 
prompts to manually verify the session

Overall, I like gomuks and am able to enjoy all of the features I was using in 
Element. The only hiccup I have occurred is manually downloading images to view 
them, which can be annoying.

![gomuks](https://img.cleberg.io/blog/20220616-terminal-lifestyle/gomuks.png)

### Email

Moving email to the terminal has been the hardest of the tasks for me. Unlike 
web browsing, where I can simply decide to not look at a website that does not 
work in the terminal, I cannot simply ignore emails sent to me.

Personally, I am experimenting with [neomutt](https://neomutt.org/) as a 
potential email client.

However, this requires a **TON** of configuration and tweaking to get right. 
Even when I was able to set-up neomutt, configure my email account, and 
customize a few personal preferences, a lot of emails still do not display 
correctly (mostly due to HTML and images).

I won't get into the details of configuring `neomutt`; I mostly followed this 
blog post: [Email in the Terminal: Configuring 
Neomutt](https://gideonwolfe.com/posts/workflow/neomutt/intro/).

Finally, I have yet to figure out how to connect my GPG keys to `neomutt`, but 
that's a problem for another day.

Regardless, here's a quick look at composing a message in `neomutt`, using 
`nano`:

![neomutt 
!compose](https://img.cleberg.io/blog/20220616-terminal-lifestyle/neomutt_compose.png)

And here is that same message after being delivered to my inbox (I moved it to 
my Junk folder for screenshot purposes):

![neomutt 
!inbox](https://img.cleberg.io/blog/20220616-terminal-lifestyle/neomutt_inbox.png)

### Writing & Programming

Unfortunately, the weak link in my terminal-based environment right now is my 
grasp of the possibilities of editing files within a shell.

I am used to the easy extensions found in VSCodium and Kate, so I am slowly 
learning how to mold the default editing tools to my needs. Currently, this 
means I am using `nano` with the following configuration:

```config
set breaklonglines
set autoindent
set linenumbers
set tabstospaces
set tabsize 2
set fill 80
```

This configuration allows nano to automatically hard-wrap lines at 80 
characters, autoindent the wrapped lines (if the previous line was indented), 
use 2 spaces per tab, and display line numbers within each file I open.

I am currently looking to see if `vim` or `emacs` would be more useful for my 
current needs, but I'm not in any rush so I don't expect to find an answer 
anytime soon.

With my current life demands, I am not programming at the moment and have not 
explored the best terminal set-up for programming. However, I have seen many 
peers find success configuring `vim` and `emacs`, so that's where I will start 
when I pick my projects back up.

![nano](https://img.cleberg.io/blog/20220616-terminal-lifestyle/nano.png)
