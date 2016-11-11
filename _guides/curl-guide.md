---
# vim: tw=80
title: Testing with curl
blurb: A crash course on curl and how to use it to try out the API.
layout: guide
---

[curl](http://curl.haxx.se/) is a simple and popular command line tool that
allows you to perform various kinds of HTTP requests. It may already be
installed on your system - run `curl --version` in your shell to check. Once
you've confirmed that you have it installed, we can use it to test out the
Linode API from the comfort of your shell.

## Unauthenticated Requests

You can perform anonymous HTTP requests against various resources on the API.
You can tell which ones are anonymous from the <span class="text-muted">
<i class="fa fa-lock"></i> Authenticated</span> indicator in the [reference
documentation](/reference). For API endpoints where this indicator is missing,
you're able to use curl to test them without any additional steps. For example,
we could [list supported distributions](/reference/#ep-distributions):

    curl https://{{ site.api_root }}/{{ site.api_version }}/linode/distributions

This will give you a response like this:


{% highlight json %}
{
    "distributions": [
        {
            "created": "2014-10-24T15:48:04",
            "id": "linode/ubuntu14.10",
            "label": "Ubuntu 14.10",
            "minimum_storage_size": 650,
            "recommended": true,
            "vendor": "Ubuntu",
            "x64": true
        }
        /* and so on */
    ],
    "page": 1,
    "total_pages": 2,
    "total_results": 34
}
{% endhighlight %}

## Authenticated Requests

For many requests, you will have to authenticate as a particular user. For now,
we're going to use a personal access token to make things easier. If you'd like
to learn how to make a full blown OAuth client, read the
[authentication documentation](/reference#authentication).

To generate a personal access token, [go
here](https://login.alpha.linode.com/tokens). These tokens can be used to make
API requests authenticated with your Linode account and have full access to all
OAuth scopes. You'll only see the full OAuth token once, so be sure to write it
down somewhere. If you're in the shell, running something like this might work
well:

    token="that token"

### Authentication Header

Now you can make requests with curl using your access token by adding `-H
"Authorization: token $token"`. The <span class="text-muted"><i class="fa
fa-lock"></i> Authenticated</span> requests on the [reference page](/reference)
include this header in the curl examples. Try this, for example:

    curl -H "Authorization: token $token" \ 
        https://{{ site.api_root }}/{{ site.api_version }}/linode/instances

This will give you a response like this:


{% highlight json %}
{
   "linodes": [
      {
         "id": 2019697,
         "label": "prod-1",
         "ipv4": "97.107.143.73",
         "ipv6": "2600:3c03::f03c:91ff:fe0a:18c6/64",
         "datacenter": {
            "label": "Newark, NJ",
            "id": "newark",
            "country": "us"
         },
         "backups": {
            "last_backup": null,
            "snapshot": null,
            "schedule": {
               "day": null,
               "window": null
            },
            "enabled": false
         },
         "status": "running",
         "group": "",
         "hypervisor": "kvm",
         "created": "2016-11-10T19:38:00",
         "distribution": {
            "minimum_storage_size": 900,
            "id": "linode/debian8",
            "created": "2015-04-27T16:26:41",
            "recommended": true,
            "vendor": "Debian",
            "label": "Debian 8.1",
            "x64": true
         },
         "alerts": {
            "io": {
               "threshold": 10000,
               "enabled": true
            },
            "transfer_out": {
               "enabled": true,
               "threshold": 10
            },
            "transfer_in": {
               "enabled": true,
               "threshold": 10
            },
            "transfer_quota": {
               "enabled": true,
               "threshold": 80
            },
            "cpu": {
               "enabled": true,
               "threshold": 90
            }
         },
         "type": [
            {
               "backups_price": 250,
               "label": "Linode 2048",
               "storage": 24576,
               "transfer": 2000,
               "vcpus": 1,
               "id": "standard-1",
               "hourly_price": 1,
               "ram": 2048,
               "monthly_price": 1000,
               "mbits_out": 125,
               "class": "standard"
            }
         ],
         "total_transfer": 2000,
         "updated": "2016-11-10T19:39:36"
      }
      /* and so on */
   ],
   "page": 1,
   "total_pages": 1,
   "total_results": 1
}
{% endhighlight %}
