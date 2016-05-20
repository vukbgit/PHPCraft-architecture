# PHPCraft-architecture

guide lines for PHPCraft `WORK IN PROGRESS`

## Premises
The following considerations apply primarily to a Linux + Apache + PHP environment

## Definitions
* resource: any file referent of an URI 
* page: a set of resources accessed through a route that displays and/or performs a piece of domain logic

## Principles
* DRY (Do not Repeat Yourself): write every piece of information once
* greedy: do not load, include, define, instance any resource that is not going to be effectively used by the page

## Code organization

### Vertical Hierarchy (top down)
* application: the set of code that implements a certain domain logic
* area: the set of code into an application that implements a  logic (i.e. account login) and/or visual layout (at least 1 section)
* section: groups operations and/or views about the same subject (at least 1 action)
* action: a piece of business logic and/or a view to display data called by an URL

### Horizontal

#### Scope
* private: files protected (by .htaccess rules and/or login) from direct browser access
* public: files that can be requested by browser

#### Context
* global: all the code shared between different applications
* application level: all the code specific to an application

## Filesystem

## .htacces
