### Premise
This document should depict the process of building a website using the PHPCraft Manifest as a guide.

### Conventions
* __your-domain.tld__: application internet domain, to be substituted everywhere in code examples real application internet domain
* __application-name__: the name of the application currently developed, to be substituted everywhere in code examples and folder and file names with real application name
* __application-root__: the folder where the application resides, the folder of __your-domain.tld__ webspace where are created at least the folders _private_, _public_ and __vendor__ 
* __subject__: see the README doc, it can be thought as a section of the application and/or a db table, some concept the application is able to work upon and/or display data about; for example it could be 'products'
* __action__: see the README doc, a single operation upon a __subject__, like 'save', 'list', 'delete'

### Prerequisites
* Apache2:
  * allow .htacces files use
  * module mod_rewrite enabled

### Basic folders and files
* into __application-root__ make a folder named __private__ with the subfolders __global__ and __application-name__
* into __private/application-name__ folder make the subfolders __configurations__ and __procedures__
* make file __private/.htaccess__ and prevent direct access to contained files:
```
# Order Deny,Allow
deny from all
```
* make the main configuration file which contains informations likely to be needed by every application (i.e. domains definition, database account), for portability it's useful to name it 'application', it has a tree structure so a php or json file is needed (use json if it has to be red by different technologies like PHP and js).
So make __private/application-name/configurations/application.php__ (values to be adapted)
```php
<?php
return [
    'contacts' => [
        'tech' => 'tech-email-address' //used into friendly exceptions
    ],
    'domains' => [ //set environment based on requested domain (when possible)
        'dev.your-domain.tld' => [
            'environment' => 'development'
        ],
        'your-domain.tld' => [
            'environment' => 'production'
        ]
    ],
    'basePath' => '/',
    'languages' => ['it','en'], //implemented languages
    'database' => [ //database account (when present)
        'driver' => 'mysql',
        'host' => 'localhost',
        'username' => 'db-username',
        'password' => 'db-password',
        'database' => 'db-name'
    ]
];
```
* make the application bootstrap file __private/application-name/procedures/bootstrap.php__ with some test code (which will come in handy later):
```php
<?php
echo '<pre>';
print_r(apache_get_modules());
echo '</pre>';
```
* into __application-root__ make a folder named __public__ with subfolder __application-name__
* into __public/application-name__ make the subfolder __procedures__
* make file __public/application-name/procedures/index.php__:
  * just for the moment, as the very first thing, turn on PHP errors display at maximum level
  * store the name of the application and the relative path from __index.php__ to the __application-root__. These informations are used from this point till the end of the script so they must be defined here
  * include the bootstrap file
```php
<?php
ini_set("display_errors", 1);
error_reporting(E_ALL);
define('APPLICATION','application-name');
define('PATH_TO_ROOT','../../../');
//bootstrap
require PATH_TO_ROOT . 'private/' . APPLICATION . '/procedures/bootstrap.php';
```
  * test the url __http://your-domain.tld/public/application-name/procedures/index.php__, the duump of the `apache_get_modules` function should be outputted.

### Application-root .htaccess file
* make the file .htaccess into __application-root__
* to test that .htaccess file really are processed:
  * write some gibberish inside (like `xyz`)
  * test the url __http://your-domain.tld
  * you should get a 500 Internal Server Error
* to test that the Apache module mod\_rewrite is enabled look for 'mod\_rewrite' into index.php output
* clean the gibberish and put the above code which:
  * switches on apache rewrite engine (FollowSymlinks is required)
  * sets the default route ('default-application-subject' below) for bare domain request in the form of 'subject[/action]', action is not mandatory since it is reasonable to set a default (and possibly only) action for the subject; i.e. 'products' or 'products/browse'
  * routes every other request (except for existing files and directories, such as css or js files) to __index.php__
```
Options +FollowSymlinks
RewriteEngine on
#default domain page
RewriteRule ^$ default-application-subject [R,L]
#requests pointing to real files are not redirected
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ public/application-name/procedures/index.php [NC]
```
#### Application-root inside a webspace subfolder
If the __application root__ is NOT into the top level folder of the domain web space it is necessary to use the full URLs, so rows number 4 and 8 must be changed:
```
[...]
RewriteRule ^$ http://your-domain.tld/path-to-application-root/default-application-subject [R,L]
[...]
RewriteRule ^(.*)$ http://your-domain.tld/path-to-application-root/public/application-name/procedures/index.php [NC]
```


### bootstrap file

  * configuration information specific to the domain logic ar that can take many forms (i.e. address that can be split or compact), use the most appropriate file type, in this case .ini:
  make __private/application-name/conf/application-name.ini__
  ```ini
phone = '__1234.5678.90__'
a-certain-property = 'its-value'
  ```
  
### Environments
The match between visited domain and environment (development or production) at the moment has no conrete consequences since the problem of how to set a development environment is still open: there is the [idea](https://www.smashingmagazine.com/2015/07/development-to-deployment-workflow/) to use [Vagrant](https://www.vagrantup.com/) and a repository service or to host a GIT server upon server machine
