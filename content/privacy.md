+++
title = "Privacy"
description = "The official privacy policy of cleberg.io."
+++

## Dates & Revisions

Please see the
[full Git history](https://git.cleberg.net/cgit.cgi/cleberg.io.git/log/content/privacy.md)
for all source code changes to this privacy policy and website. Otherwise, see
below for the creation and revision dates for this policy.

Created: `2022-02-11 23:05:00 UTC`  
Updated: `2022-07-07 04:05:00 UTC`

## Data Collection & Usage

This website collects only the bare essential information to provide a secure
and private experience to each and every visitor.

### Nginx

The web server software running this website, Nginx, does not collect any logs.
The default access logs and error logs are disabled. More technically, see the following snippet for the `nginx.conf` logging settings:

```config
http {
    ...
    access_log /dev/null;
    error_log  /dev/null;
    ...
}
```

## Questions?

Please don't hesitate to reach out if you have any questions at all. You can
always reach me at [hello@cleberg.io](mailto:hello@cleberg.io)
[[PGP](https://cleberg.io/pgp.txt)].
