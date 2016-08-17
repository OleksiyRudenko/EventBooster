`[2016-08-17] - created`

`[2016-08-17] - latest major update`

# Structure

## Table of Contents
 1. [Web-site Directory Layout](#web-site-directory-layout)
 1. [Application Directory Layout](#application-directory-layout)

## Web-site Directory Layout

The application itself is supposed to be located in private web-server area.
Only management entry point, public page, compiled event files and public 
static files (like css, js helpers, imagery and other media) are located in
public web area.

Should you be required to place app files into public area, please, consider
using `.htaccess` feature and `mod_rewrite` (if served with Apache).

Here is an example of web-server directory layout from web-site admin point 
of view.
 
```
/                   // web-site hosting root
|-- www/
|   |-- yoursite.com/
|   |   |-- css/
|   |   |-- fonts/
|   |   |-- media/
|   |   |-- js/
|   |   |-- eventSpecificDirectory1/
|   |   |   |-- index.html
|   |   |   |-- css/
|   |   |   |-- fonts/
|   |   |   |-- media/
|   |   |   \-- js/
|   |   |-- eventSpecificDirectory2/
|   |   ...
|   |-- anothersite.com
|   |
|   ...
|-- EventBooster/

```

[**[back-to-top](#table-of-contents)**]

## Application Directory Structure

[**[back-to-top](#table-of-contents)**]

