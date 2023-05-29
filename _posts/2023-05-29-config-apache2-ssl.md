---
title: Apache2 Config with SSL
description: Notes deploying a website with apache2 and SSL
date: May 29, 2023
categories: [web]
layout: post
toc: false
comments: true
hide: false
search_exclude: false
---

## Install apache2 modules

```bash
# as root
a2enmod proxy_http
a2enmod proxy
a2enmod ssl
a2enmod md
```

## Config apache2.conf

```apacheconf
# in /etc/apache2/apache2.conf
ServerAdmin ssl-certificate@<your-domain.com>
MDCertificateAgreement https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf
```

## Config site apache

```apacheconf
# in /etc/apache2/sites-available/your-domain.com.conf
MDomain <your-domain.com>

<VirtualHost *:443>
  ServerName <your-domain.com>

  Protocols h2 http/1.1
  SSLEngine On
  ProxyRequests Off
  ProxyVia On
  ProxyPreserveHost On

  ErrorLog  ${APACHE_LOG_DIR}/<domain>-error.log
  TransferLog  ${APACHE_LOG_DIR}/<domain>-access.log

  # If necessary
  ProxyPass /api http://localhost:<exposed_port>/api retry=0
  ProxyPassReverse /api http://localhost:<exposed_port>/api retry=0

</VirtualHost>
```

## Activer les sites

```bash
# as root
cd /etc/apache2/sites-available
a2ensite <domain>.conf
```

## Restart

```bash
# as root
sudo apache2ctl configtest # checks if config is ok
sudo systemctl restart apache2
```

# Logs

```bash
# as root
# your domain logs
tail -f /var/log/apache2/<domain>-error.log
tail -f /var/log/apache2/<domain>-access.log
# apache 2 logs
tail -f /var/log/apache2/error.log
tail -f /var/log/apache2/access.log
```
