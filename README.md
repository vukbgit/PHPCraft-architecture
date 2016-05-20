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
* __free__: avoid exclusive dependency form third party library, handle dependency thorugh [adapter pattern](https://en.wikipedia.org/wiki/Adapter_pattern) custom classes

## Code organization

### Vertical Hierarchy (top down)
* __application__: the set of code that implements a certain domain logic
* __area__: a set of pages code into an __application__ with at least one strong common trait  (i.e. behind login,  shared page layout)
* __subject__: a set of pages that performs operations about the same coherent portion of domain logic (i.e. products, customers)
* __action__: the set of operations performed by a page over a __subject__
 
An application contains at least one area, a area contains at least one subject, a subject contains at least one action

### Horizontal Criteria
#### Scope
* __private__: files protected (by .htaccess rules and/or login) from direct browser access
* __public__: files that can be requested by browser

#### Context
* __global__: all the code shared between different __applications__
* __application level__: all the code specific to an __application__

### Function
* __configuration__: file that store __application__ informations tipically in key-value pairs (i.e. ini format) or json
* __procedures__: files that contain a coherent portion of domain logic implemented through procedural code, criteria of allocation could be:
  * functional,  i.e. user authentication
  * hierarchical, i.e. __subject__ logic
* __lib__: libraries, (i.e. PHP or js) do not included in composer, both developed specifically for __application__ or by third part
* __templates__
* __locale__: translations
* __src__: files that are source for specific tasks but are not to be directly used into __application__, such as sass .scss files

### Internazionalization
In any context where files contain text to be exposed, specific folders and files should be always used langauge. Folders should be nominated by language code (i.e. 'en' or 'en_UK'). Language codes should follow a [convention](https://en.wikipedia.org/wiki/Language_code).

## Filesystem
Folders should be organized according to vertical and horizontal logic as more convenient. An example for 'Acme' application:
* private
  * global
    * procedures
      * environment.php _handles developement/production logic_
      * http.php _instantiate the http adapter class_
    * src
     * form.scss __sass file for standard forms__
  * acme
* public

### Special Folders
* __secret__: files used for application developement and mantainence; folder is http protected
* __vendor__: composer folder for storing libraries

## .htacces
