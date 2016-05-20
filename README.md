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
* area: a set of pages code into an application with at least one strong common trait  (i.e. behind login,  page layout)
* subject: a set of pages that performs operations about the same coherent portion of domain logic (i.e. products, customers)
* action: the set of operations performed by the page over the subject
 
An application contains at least one area, a area contains at least on subject, one subject contains at least one action

### Horizontal

#### Scope
* private: files protected (by .htaccess rules and/or login) from direct browser access
* public: files that can be requested by browser

#### Context
* global: all the code shared between different applications
* application level: all the code specific to an application

## Filesystem

## .htacces
