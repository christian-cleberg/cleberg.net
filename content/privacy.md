+++
title = "Privacy"
+++

## Dates & Revisions

Please see the
[full Git history](https://github.com/christian-cleberg/cleberg.io/commits/main)
for all source code changes to this privacy policy and website. Otherwise, see
below for the creation and revision dates for this policy.

Created: `2022-02-11 23:05:00 UTC`  
Updated: `2022-05-27 01:15:00 UTC`

## Data Collection & Usage

This website collects only the bare essential information to provide a secure
and private experience to each and every visitor.

### Nginx

The web server software running this website, Nginx, does not collect any logs.
The default access logs and error logs are disabled. More technically, see the following snippet for the `nginx.conf` logging settings:

```config
http {
    ...
    access_log /dev/null crit;
    error_log  /dev/null crit;
    ...
}
```

### Cloudflare

This website uses [Cloudflare](https://www.cloudflare.com) for DNS and tunneling
purposes. Per the Cloudflare privacy policy for end users, the following quoted
statement is provided in
[their privacy policy](https://www.cloudflare.com/privacypolicy/).

Please note that within Cloudlfare's privacy policy, you are the End User and I
am the Customer.

> **End Users**
>
> Cloudflare processes End Users’ interactions with Customer’s Internet
> Properties and the Services. This information is processed when End Users
> access or use our Customers’ domains, websites, APIs, applications, devices,
> end points, and networks that use one or more of our Services, and when End
> Users access or use Services, such as Cloudflare Zero Trust. The information
> processed may include but is not limited to IP addresses, traffic routing
> data, system configuration information, and other information about traffic to
> and from Customers’ websites, devices, applications, and/or networks.
>
> The End User data that we make available to our Customers via the Service
> dashboard or other online interface are specifically defined as “Customer
> Logs”.

## Questions?

Please don't hesitate to reach out if you have any questions at all. You can
always reach me at [hello@cleberg.io](mailto:hello@cleberg.io)
[[GPG key](https://cleberg.io/gpg_key.txt)].
