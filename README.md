# PHPCraft-architecture

guide lines for PHPCraft `WORK IN PROGRESS`

## Premises
The following considerations apply primarily to a Linux + Apache + PHP environment

## Definitions
* __resource__: any file referent of an URI 
* __page__: a set of resources accessed through a route that displays and/or performs a piece of domain logic

## Principles
* __DRY__ (Do not Repeat Yourself): write every piece of information once
* __greedy__: do not load, include, define, instance any resource that is not going to be effectively used by the page

## Code organization

### Vertical Hierarchy (top down)
* __application__: the set of code that implements a certain domain logic
* __area__: a set of pages code into an application with at least one strong common trait  (i.e. behind login,  page layout)
* __subject__: a set of pages that performs operations about the same coherent portion of domain logic (i.e. products, customers)
* __action__: the set of operations performed by the page over the subject
 
An application contains at least one area, a area contains at least on subject, one subject contains at least one action

### Horizontal
#### Scope
* __private__: files protected (by .htaccess rules and/or login) from direct browser access
* __public__: files that can be requested by browser

#### Context
* __global__: all the code shared between different applications
* __application level__: all the code specific to an application

## Filesystem


## .htacces
