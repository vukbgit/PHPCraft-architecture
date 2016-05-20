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
* __free__: when a third part library is used, avoid tight dependency thorugh [adapter pattern](https://en.wikipedia.org/wiki/Adapter_pattern) custom classes

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
* __conf__: file that store __application__ informations tipically in key-value pairs (i.e. ini format) or json
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
      * http.php _instantiate the http class_
      * router.php _instantiate the router class and handles routing logic_
    * src
      * form.scss _sass file for standard forms style_
      * list.scss _sass file for standard lists style_
  * acme
    * conf
      * acme.ini _application settings_
      * routes.php _routes definition_
    * lib
      * Subject1.php _library to handle subject 1 logic_
      * Subject2.php _library to handle subject 2 logic_
    * locale
      * it
        * subject1.ini _translations for subject 1_
        * subject2.ini _translations for subject 2_
      * en
        * subject1.ini _translations for subject 1_
        * subject2.ini _translations for subject 2_
    * templates
      * backend _templates for backend area_
        * subject1 _templates for subject 1_
          * saveForm.html
          * list.html
        * subject2 _templates for subject 2_
          * saveForm.html
          * list.html
        * form.html _parent template of all fomrs_
        * list.html _parent template of all lists_ 
      * frontend _templates for frontend area_
        * home.html
        * subject1.html
        * subject2.html
      * header.html _header template common to whole application_

* public

### Special Folders
* __secret__: files used for application developement and mantainence; folder is http protected
* __vendor__: composer folder for storing libraries

## .htacces
