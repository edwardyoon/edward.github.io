---
title: "letsencrypt-with-apache-vhost-on-ubuntu"
date: 2018-04-13 10:26:28 -0400
categories: 
---

I sudo-su-d into my root account and enabled the ssl module: 
```
$ a2enmod ssl
$ service apache2 restart
```
I installed the letsencrypt client: 
```
$ git clone https://github.com/letsencrypt/letsencrypt
$ cd letsencrypt
```
I shut down my Apache – there is probably a better way, but on a low-traffic test server/domain, that’s entirely okay for me. I then requested a new certificate and started Apache up again. 
```
$ service apache2 stop
$ ./letsencrypt-auto certonly --standalone --email email@example.com  -d example.com
$ service apache2 start
```
Now I have a lovely certificate! Yay! Now I went into the config file of my virtual host and changed things around a bit. I am sure that it is frowned upon to put two VirtualHost directives with two different ports into one config file, but… ¯\_(ツ)_/¯ 
```
<VirtualHost *:80>
 ServerName example.com
 Redirect "/" "https://example.com/"
</VirtualHost>
 
<VirtualHost *:443>
 ServerName example.com
 DocumentRoot "/var/www/example/public"
 DirectoryIndex index.htm
 <Directory /var/www/example/public>
  Options FollowSymLinks
  AllowOverride All
 </Directory>
 SSLEngine on
 SSLCertificateFile /etc/letsencrypt/live/example.com/cert.pem
 SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
 SSLCertificateChainFile /etc/letsencrypt/live/example.com/fullchain.pem
</VirtualHost>
```
One more apache restart 
```$ service apache2 restart```


