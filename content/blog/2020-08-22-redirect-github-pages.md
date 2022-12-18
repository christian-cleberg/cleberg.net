+++
date = 2020-08-22
title = "Redirect GitHub Pages from Subdomain to the Top-Level Domain"
description = "Learn how to redirect GitHub Pages from a subdomain to the TLD."
draft = false
aliases = ["/blog/github-pages-subdomain-to-tld"]
+++

## Short answer

### Step 1

Add a new file CNAME to your GitHub Pages repository containing only one line:
your top-level domain name. E.g.: `example.com`

### Step 2

[Optional] but highly recommended

2.1: Remove all other top-level records (prefixed with @) of type A from your
DNS configuration.

2.2: Remove a CNAME record for the second-level domain www if it is present.

### Step 3

Add these 5 entries to the very top of your DNS configuration:

```txt
@        A        185.199.108.153
@        A        185.199.109.153
@        A        185.199.110.153
@        A        185.199.111.153
www      CNAME    your_github_username.github.io.
```

Replace `your_github_username` with your actual GitHub username.

### Step 4

Wait for your DNS changes to propagate. DNS changes aren't effective
immediately. They can take up to a full day to propagate.

## Long answer

This issue has two sides. One is the DNS configuration itself. Another one is
the way GitHub Pages forwards HTTP requests.

We need to know a few things to understand what GitHub is trying to say in their
documentation.

### DNS Entry Types

There are two types of DNS records which interest us: CNAME and A.

`A` is also known as `Apex` or sometimes as `root entry`. It forwards requests
to a specified fixed IP address. `CNAME` entry forwards requests to a specified
URL (actual valid plain text URL, not an IP address).

### DNS Load balancing

GitHub has one central URL address which accepts all DNS requests for GitHub
Pages: `http://username.github.io`. That URL is resolved to different IP
addresses based on your geographical location. Website hosted on GitHub Pages is
a simple collection of `HTML`, `CSS` and `JS` files. GitHub distributes these
files to different servers across the globe. So that when your browser sends a
request from Europe it receives data from a server in Europe. The same is valid
for the requests from Asia and the USA.

### What GitHub is trying to say

Since `A` records in DNS must contain IP addresses, and they must be either
`185.199.108.153` or `185.199.109.153` or `185.199.110.153` or
`185.199.111.153`, there is no way to forward requests to a server located
somewhere in Europe or Asia. Your website hosted at GitHub Pages will be
downloaded from a central GitHub Pages server. There is a minor risk that if
GitHub Pages DNS servers (`x.x.x.153`) are down for some reason, all custom
domains which use fixed GitHub Pages IP addresses will not be accessible (their
DNS requests will not be resolvable).

That is why GitHub strongly suggests to either use a second-level domain for
your GitHub Pages (e.g. `blog.example.com`) or use a DNS service provider that
supports a record type `ALIAS` that acts as `A` record but forwards request to a
URL address (e.g. `username.github.io`) instead of a fixed IP address.

### How GitHub Pages treats HTTP requests

After a DNS request for `your_github_username.github.io`. is resolved into an IP
address, e.g. `185.199.108.153` your browser sends an HTTP request to that
server with an HTTP header `Host`. Below are `curl` examples that load the same
website (these examples might not work if you are behind a proxy server):

```sh
curl --header "Host: your_github_username.github.io" http://185.199.108.153/
curl --header "Host: www.example.com" http://185.199.108.153/
curl --header "Host: example.com" http://185.199.108.153/
```

This way GitHub Pages servers know which user website to serve.

> GitHub Pages server will automatically redirect HTTP requests to the top-level
> domain if your `CNAME` file contains `example.com` but `www.example.com` is
> requested.
>
> The same is valid if your `CNAME` file contains `www.example.com` but the
> header `Host` in the `HTTP` request contains `example.com`.

### Why can't I add a `CNAME` record entry that accepts a top-level request (`@`) to my DNS configuration?

Quote from the GitHub Pages documentation:

> Warning: Do not create a CNAME record for your custom apex domain! Doing so
> may cause issues with other services, such as email, on that domain.

## References:

\[1\]:
[Setting up a custom domain with GitHub Pages](https://docs.github.com/en/github/working-with-github-pages/configuring-a-custom-domain-for-your-github-pages-site)  
\[2\]:
[My custom domain isn't working](https://docs.github.com/en/github/working-with-github-pages/troubleshooting-custom-domains-and-github-pages)  
\[3\]:
[Cannot access my GitHub Pages website by IP Address](https://serverfault.com/questions/589370/cannot-access-my-github-pages-website-by-ip-address)  
\[4\]:
[How do I set up GitHub Pages to redirect DNS requests from a subdomain (e.g. www) to the top-level domain (TLD, Apex record)?](https://stackoverflow.com/questions/23375422/how-do-i-set-up-github-pages-to-redirect-dns-requests-from-a-subdomain-e-g-www)
