+++
title = "Privacy Policy"
+++

## Dates & Revisions

Please see the
[full Git history](https://github.com/christian-cleberg/cleberg.io/commits/main)
for all source code changes to this privacy policy and website. Otherwise, see
below for the creation and revision dates for this policy.

Created: `2022-02-11 23:05:00 UTC`  
Updated: `2022-03-26 23:00:00 UTC`

## Data Collection & Usage

This website collects only the bare essential information to provide a secure
and private experience to each and every visitor.

### Nginx

The web server software running this website, Nginx/1.18.0 (Debian), collects
two logs of information automatically. See below for examples of information
collected in each log:

**Error Log**

```config
2022/02/23 05:53:57 [error] 35218#35218: *1090 directory index of "/var/www/img.cleberg.io/" is forbidden, client: XXX.XXX.XXX.XXX, server: img.cleberg.io, request: "GET / HTTP/1.1", host: "XXX.XXX.XXX.XXX"
```

**Access Log**

```config
XXX.XXX.XXX.XXX - - [23/Feb/2022:15:05:15 +0000] "GET http://cleberg.io/phpmyadmin/scripts/setup.php HTTP/1.0" 404 153 "-" "-"
```

This information collected by the server is not sent to any third parties and
remains on the server for 14 days before deletion. Please see the following
bullet points for the purpose of each log:

-   **Error Log**: To provide information that may help the developer fix any
    website errors or issues.
-   **Access Log**: To enable the local [Fail2Ban](https://www.fail2ban.org)
    software to block malicious attacks against the server.

### Analytics (Deprecated)

For the period of 2022-02-02 - 2022-03-26, this website utilized
[Nullitics](https://nullitics.com) as a tool to learn what to improve on this
website. As of 2022-03-26, this tracking script was removed from the site and is
no longer in use.

This information was not personally identifiable - no user tracking, cookies, or
user-agent strings. Here's an example of everything Nullitics provided for a
user who visited this website:

```txt
Timeframe: 6:00 - 7:00 CT
Path: /privacy/
Referrer: duckduckgo.com
Country: US
Device: Mobile
Page Views: 1
```

For more information, see the
[Nullitics documentation](https://nullitics.com/docs/#/privacy).

## Questions?

Please don't hesitate to reach out if you have any questions at all. You can
always reach me at [hello@cleberg.io](mailto:hello@cleberg.io)
[[GPG key](https://keys.openpgp.org/vks/v1/by-fingerprint/3A8AA9FF8906334646ABE0CC7FA94F8154265655)].
