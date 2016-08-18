`[2016-07-20] - created`

`[2016-07-24] - latest major update`

# Installation

[**[Documentation Root](README.md)**]

## Installation under USBWebserver

Valid with USB WebServer v.8.6.

Installation under different set-up of Apache/PHP/MySQL
is similar.

 1. Install [USB Webserver](http://www.usbwebserver.net/en/)
 1. Set-up USB Webserver (see below)
 1. Check if USB Webserver runs properly
 1. Quit USB Webserver
 1. Find yourself in USB Webserver installation directory and
    run `git clone ` _`this-repo-url`_ to have this project installed under your web-server root
 1. Set-up server document root & database (see below)
 1. Launch USB Webserver and tap `Localhost` button

### Setting-up USBWebserver

#### settings/php.ini

Uncomment and set timezone. E.g. `date.timezone = Europe/Kiev`

#### settings/httpd.conf

Add as a last section:
```
<IfModule rewrite_module>
    RewriteEngine on
    RewriteCond %{SCRIPT_FILENAME} !^/(css/|font-awesome/|fonts/|images/|js/|phpmyadmin) [NC]
    RewriteRule ^(.+)$ /index.php/$1 [L]
</IfModule>
```

## Setting-up Database

Tap PHPMyAdmin.
 
Use `root`/`usbw` credentials to access MySQL.
Please, change these via DB admin panel and in `app/config.db.php`.

 1. Create database `dunning` with collation `utf8mb4_unicode_520_ci` (choose different,
    language specific collation should your textual content be in a single
    language and relevant collation provided by DB engine)
 1. Create tables according to specs as per
    `app/_Doc/DataModel-spec.xls`
