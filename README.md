# PHPCraft-manifest `A WORK IN PROGRESS`

## Goal
Expose considerations and solutions about writing websites without using a framework.

## Premises
The following considerations apply primarily to a Linux + Apache + PHP environment even if, at least part of them, could be extended to other technologies and platforms.

## Definitions
* __resource__: any file referent of an URI 
* __page__: a set of resources accessed through a route that displays and/or performs a piece of domain logic

## Principles
1. __be organized__: choose and define rules and follow them for files organization and code writing
2. __be clear__: code organization and name conventions must be understandable
3. __be free__: it must be possible to develop every page without restrictions
4. __be tidy__: every piece of information must be written once and in the appropriate place
5. __be frugal__: just load, include, define, instance only resources that are really going to be used by the page
6. __be indipendent__: when a third part library is used, avoid tight dependency thorugh [adapter pattern](https://en.wikipedia.org/wiki/Adapter_pattern) custom classes
7. __enjoy__: write code that is comfortable for YOU to use
8. __recycle__: use code you ha written for past applications and write code that you can used in future applications

Follow these rules in order: apply each rule as long as it does not conflict with preceding rules.

## Code organization

### Vertical Hierarchy (top down)
* __application__: the set of code that implements a certain domain logic
* __area__: a set of pages into an __application__ with at least one strong common trait  (i.e. behind login,  shared page layout)
* __subject__: a set of pages into an __area__ that performs operations about the same coherent portion of domain logic (i.e. products, customers)
* __action__: the set of operations performed by a page over a __subject__
 
An application contains at least one area, an area contains at least one subject, a subject contains at least one action

### Horizontal Criteria
#### Scope
* __private__: files protected (by .htaccess rules) from direct browser access
* __public__: files that can be requested by browser

#### Context
* __global__: all the code shared between different __applications__
* __application level__: all the code specific to an __application__

#### Function
* __configurations__: file that store __application__ informations tipically in simple key-value pairs (i.e. ini format) or json and php files for more complex structures (depending on the technologies that are going to consume the files, there is [discussion](http://stackoverflow.com/questions/17666728/load-website-configuration-from-json-or-php-file) about which one is better...) 
* __procedures__: files that contain a coherent portion of domain logic implemented through procedural code, criteria of allocation could be:
  * functional,  i.e. user authentication
  * hierarchical, i.e. __subject__ logic
* __libraries__: libraries, (i.e. PHP or js) do not included in composer, both developed specifically for __application__ or by third part
* __templates__
* __locales__: translations
* __sources__: files that are source for specific tasks but are not to be directly used into __application__, such as sass .scss files

#### Internazionalization
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
  * acme
    * lib
      * acme.js
      * third-part-js-library-folder _not included through composer_

### Special Folders
* __secret__: files used for application developement and mantainence; folder is http protected
* __vendor__: composer folder for storing libraries

## .htacces
