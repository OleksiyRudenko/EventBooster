`[2016-08-26] - created`

`[2016-08-26] - latest major update`

# Event SPA

Every event is exposed via its dedicated SPA, which is: 
* compiled from and using:
  - event-specific components
  - global components
  - event-specific localized constants
  - event-specific variables supplied by core app
  - logic contained in components
* accessible via dedicated url.

Content and behaviour of Event SPA depends on current event phase and status and 
implements the process.

Event SPA gets recompiled every time event phase (Survey, Sales Campaign, D-Day etc.)
or properties (vacant seats, location etc.) get changed.

To design a template the designer should have minimal proficiency in `PHP`. 

[**[Documentation Root](../README.md)**]

## Table of Contents

1. [Application Structure](#application-structure)
1. [Activities Supported](#activities-supported)
1. [SPA Compilation](#spa-compilation)
   1. [Localized Constants](#localized-constants)
   1. [Variables](#variables)
   1. [Core API](#core-api)

## Application Structure

Event SPA is accessed via `http(s)://domain.com/event-url-id/`

**Directory layout**

```
event1/                 // event-specific folder
|-- index.*.html            // compiled locale event landing pages
|-- build/                  // event-specific components for build
|   |-- index.php               // page builder entry point
|   |-- config.php              // page settings
|   |-- c.*.php                 // localized constants
|   \-- *.php                   // custom page components
|-- css/                    // event-specific styles
|-- fonts/                  // event-specific fonts
|-- media/                  // event-specific media
\-- js/                     // event-specific helper scripts
```

Localized `index.*.html` based on HTTP language negotiation is served to client.

Localized `index.*.html` are created by means of compilation. See 
[SPA Compilation](#spa-compilation) section below.
 
Directory `build` contains page building script, set of scripts containing constants for
each of supported languages and components the SPA is composed of.
This directory should be hidden from web-clients by means of the web-server.

Other directories contain files (styles, fonts, scripts, media) used specifically by SPA.

[**[back-to-top](#table-of-contents)**]

## Activities Supported

Event SPA is expected to support certain user actions.
Some actions are responded with SPA itself (Front-End logic), while others
are processed by Core App components (Back-End logic). Wherever it is possible
BE requests processed via AJAX.

Typical user actions:
 * Native login
 * OAuth login/authentication

[**[back-to-top](#table-of-contents)**]

## SPA Compilation

The reason for compilation is to save resources as there is no need to react on event 
status changes in real time.

Core App event SPA compilation module executes `index.php` iteratively for each language
supported using setings from `config.php` supplying the script with:
 * appropriate `c.*.php` where `*` == ISO denomination of current language (`en`, `uk`, `ru` etc.)
 * variables reflecting current event status (phase and variable properties)
 
Configuration, basically, contains names of event resources containing directories 
(`css`, `js`, `media` etc.), which are used by core API methods.
 
Script (`index.php` and includes therefrom) implements logic based 
on variables values incorporating other components as appropriate.

The output of the script gets buffered and then written down to relevant `index.*.html`.

Please, refer to subsections below and example SPA for further details.

[**[back-to-top](#table-of-contents)**]

### Localized Constants

Localized constants are stored in appropriate `c.*.php` file, 
which defines `$c` variable of `Array` type.

The structure of an array, i.e. how array structure reflects SPA structure 
(if does at all) is absoultely up to the designer. This variable is then 
expected to be referred to by the script in `index.php` and includes.

#### Examples of `c.*.php` and constants use.

**Plain `$c` structure**

```
c.*.php:
    $c = [
        'heading1' => 'Welcome!',
        'heading2' => 'Author',
        'heading3' => 'Schedule',
        'buttonRegister'    =>  'Register NOW!',
        'buttonRegisterAction'    =>  'register',
        'buttonMore'        =>  'Learn more',
    ];
    
template:
    <BUTTON VALUE="<?=$c['buttonRegisterAction']?>"><?=$c['buttonRegister']?></BUTTON> 
```

**Well developed `$c` structure**

```
c.*.php:
    $c = [
        'global' => [ // these are used in any section
            'button' => [
                'register'  =>  'Register NOW!',
                'subscribe' =>  'Subscribe NOW!',
                'more'      =>  'Learn more',
            ],
            'navmenu' => [
                'welcome'   => 'Welcome',
                'author'    => 'Author',
                'register'  => 'Registration',
            ],
        ],
        'welcome' => [ // Welcome section
            'heading'   =>  'Welcome!',
            'title'     =>  'Boost Your Event workshop',
        ],
        'author' => [ // Author section
            'name'      =>  'Oleksiy Rudenko',
            'email'     =>  'oleksiy.rudenko@gmailx.com',
        ],
    ];
    
template:
    <BUTTON VALUE="register"><?=$c['global']['button']['register']?></BUTTON> 
```

[**[back-to-top](#table-of-contents)**]

### Variables

Event specific variable properties are available to the template script 
with `$v` variable. Values can be displayed directly or used in logical statements.

Example:
```
template:
    <BUTTON VALUE="<?=$v['phase']=='Survey'?'subscribe':'register'?>">
        <?=$v['phase']=='Survey'?
            ?$c['global']['button']['subscribe']
            :$c['global']['button']['register']?>
    </BUTTON>
    // another example
    <BUTTON VALUE="<?=$c['phaseBased']['action'][$v['phase']]?>">
            <?=$c['phaseBased']['actionButtonText'][$v['phase']]?>
    </BUTTON>
```

For `$v` structure please refer to [`Core App`](APP.md).

[**[back-to-top](#table-of-contents)**]

### Core API

Core API methods to be used in templates:

| Method | Description |
| --- | --- |
| Compilator::includeLocal | Include local component |
| Compilator::includeGlobal | Include global component |
| Compilator::resourceLocal | Path to local http resource |
| Compilator::resourceGlobal | Path to global http resource |


[**[back-to-top](#table-of-contents)**]
