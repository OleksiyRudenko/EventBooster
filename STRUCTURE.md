`[2016-08-17] - created`

`[2016-08-17] - latest major update`

# Structure

[**[Documentation Root](README.md)**]

## Table of Contents
 1. [Web-site Directory Layout](#web-site-directory-layout)
 1. [Repositories](#repositories)
 1. [Application Directory Layout](#application-directory-layout)

## Web-site Directory Layout

The application itself is supposed to be located in private web-server area.
Only management entry point, public page, compiled event files and public 
static files (like css, js helpers, imagery and other media) are located in
public web area.

Here is an example of web-server directory layout from web-site admin point 
of view.
 
```
/                                   // web-site hosting root
|-- www/                            // server web-sites storage
|   |-- yoursite.com/               // web-site specific folder -- web-site root
|   |   |-- .htaccess                   // access control
|   |   |-- config.php                  // web-site configuration
|   |   |-- index.html                  // compiled public entry-point
|   |   |-- manage.php                  // private entry-point - invokes the app
|   |   |-- build/                      // global components for build
|   |   |-- css/                        // global styles
|   |   |-- fonts/                      // global fonts
|   |   |-- media/                      // global media
|   |   |-- js/                         // global helper scripts
|   |   |-- event1/                 // event-specific folder
|   |   |   |-- index.html              // compiled event landing page
|   |   |   |-- build/                  // event-specific components for build
|   |   |   |-- css/                    // event-specific styles
|   |   |   |-- fonts/                  // event-specific fonts
|   |   |   |-- media/                  // event-specific media
|   |   |   \-- js/                     // event-specific helper scripts
|   |   |-- event2/                 // event-specific folder
|   |   ...
|   |
|   |-- anothersite.com             // another web-site root
|   |
|   ...
|
\-- EventBooster/                   // Application
    |-- ...
    ...
```

Should you be required to place app files into public area, please, consider
using `.htaccess` feature and `mod_rewrite` (if served with Apache) rules to
isolate application files.

[**[back-to-top](#table-of-contents)**]

## Repositories

* **EventBooster** (current repo) is a core application codeset.
* **EventBoosterFE** is a front-end part of application and should be deployed
  to the web-site root. And comprises:
  - `index.hml` - landing page compiled from components;
  - `manage.php` - script which invokes app located in non-public area;
  - `config.ini` - web-site specific config (app may support several sites)
  - `.htaccess` - prevents browsing file lists (`Options -Indexes`) and direct
    addressing to files like `config.php`
  - `component/` - components to build static pages from; used to build root public
    entry point, may be used to build event page;
  - `css/, fonts/, media/, js/` - configurable directories (see `config.ini`) to store
    styles, fonts, media files and scripts used by root page and possibly event specific
    pages;
* **_Event..._** - each event files should be stored in their repos and should be deployed
  to their directories under web-site root. Contents is similar to that 
  of **EventBoosterFE**.

[**[back-to-top](#table-of-contents)**]

## Application Directory Structure



[**[back-to-top](#table-of-contents)**]

