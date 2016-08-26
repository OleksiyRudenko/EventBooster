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

[**[Documentation Root](../README.md)**]

## Table of Contents

1. [Application Structure](#application-structure)

## Application Structure

[**[back-to-top](#table-of-contents)**]