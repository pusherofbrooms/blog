---
title: "Letsencrypt"
date: 2020-05-01T09:35:00-04:00
draft: false
tags: ["sysadmin"]
---
I finally got around to setting up letsencrypt certs on this blog site. It's a lot easier than I expected. The letsencrypt website has great docs, and certbot seems to be pretty smart about detecting and adding the certs used by my apache webserver.

The letsencrypt ssl options overwrote my site specific options and enabled TLSv1 and TLSv1.1, which is easy enough to rectify in /etc/letsencrypt/options-ssl-apache.conf.

