### Premise
This document should depict the process of building a website using the PHPCraft Manifest as a guide.

### Conventions
* __your-domain.tld__: application internet domain, to be substituted everywhere in code examples real application internet domain
* __application-name__: the name of the application currently developed, to be substituted everywhere in code examples and folder and file names with real application name
* __application-root__: the folder where the application resides, the folder of __your-domain.tld__ webspace where are created at least the folders _private_, _public_ and _vendor_ 
* __vertical-hierarchy__: the levels application is dived into, top-down: application, area, subject, action
* __subject__: see the README doc, it can be thought as a section of the application and/or a db table, some concept the application is able to work upon and/or display data about; for example it could be 'products'
* __action__: see the README doc, a single operation upon a __subject__, like 'save', 'list', 'delete'

### Prerequisites
* Apache2:
  * allow .htacces files use
  * module mod_rewrite enabled

Optional
* GIT (installed on server)

### Basic folders and files
* into __application-root__ make a folder named __private__ with the subfolders __global__ and __application-name__
* into __private/global__ folder make the subfolder __procedures__
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
  * test the url __http://your-domain.tld/public/application-name/procedures/index.php__, the dump of the `apache_get_modules` function should be outputted.

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
If the __application-root__ is NOT into the top level folder of the domain web space it is necessary to use the full URLs, so rows number 4 and 8 must be changed:
```
[...]
RewriteRule ^$ http://your-domain.tld/path-to-application-root/default-application-subject [R,L]
[...]
RewriteRule ^(.*)$ http://your-domain.tld/path-to-application-root/public/application-name/procedures/index.php [NC]
```

### Third part libraries &amp; composer
Soon it will be necessary to include PHP third part and custom libraries:
* [Composer](https://getcomposer.org/) creates a 'vendor' top levele library that will be placed directly into __application-root__
* other libraries will be placed into __private/global/libraries__ or __private/application-name/libraries__ depending on the consideration that are going to be used into ather applications or not

#### Composer installation

* if you have SSH access to server do:
```
php -r "readfile('https://getcomposer.org/installer');" > composer-setup.php
php -r "if (hash_file('SHA384', 'composer-setup.php') === '92102166af5abdb03f49ce52a40591073a7b859a86e8ff13338cf7db58a19f7844fbc0bb79b2773bf30791e935dbd938') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"

```
Beware that the hash verification key may change (due to version upgrade I guess), in case of any problem refer to that [Composer download page](https://getcomposer.org/download/) for updated instructions.
It is possible to make composer available system wide but it requires super user privileges; this allows to call composer by running `composer` instead of `php composer.phar`:
```
sudo mv composer.phar /usr/local/bin/composer
```
To verify composer.json syntax in any moment:
```
composer validate composer.json
```
From now on it will be supposed that Composer is globally installed on tha server, otherwise it mus be installed locally and _vendor_ folder, _composer.json_ and _composer.lock_ must be manually kept in sync on the server

#### Application definition
Make __composer.json__ with the properties describing tha application (the require property should already be auto-compiled):
* name is in the form _vendor/name_
* license can be one of [these](https://spdx.org/licenses/) or "proprietary"
```json
{
    "type": "project",
    "name": "vendor/application-name",
    "description": "application description",
    "license": "application-license"
}
```

#### Basic libraries

[Whoops](https://github.com/filp/whoops) to print useful informations about PHP errors and [REF](https://github.com/digitalnature/php-ref) to dump variable informations, other libraries are described later on:
```
composer require filp/whoops
composer require digitalnature/php-ref
composer require asika/http
```
To keep the application independent from third part libraries these will be used through adapter classes, hosted on [github](https://github.com) and managed through [packagist](https://packagist.org). These libraries must be added manually to composer.json because thay are still in development state:
```json
{
 ...
 "require": {
   ...
   "phpcraft/container": "@dev",
 }
}

```
and update composer:
```
composer update
```

### Application Bootstrap file
It is responsible to:
* load resources that provide application wide functionalities
* launch the routing process that process the following levels of the __vertical-hierarchy__
Include application configuration file into __private/application-name/procedures/bootstrap.php__:
```
// local configuration
$configuration = include(PATH_TO_ROOT .'private/' . APPLICATION . '/configurations/application.php');
```
Test __http://your-domain.tld__, a blank page should appear.
To manage in a smart way the loading of page resources, functionalities procedural code is broken into different files that are included at different levels of the __vertical-hierarchy__ as needed by the application. 



#### Environment
make __private/global/procedures/environment.php__:
```php
<?php
// composer autoload
require PATH_TO_ROOT . 'vendor/autoload.php';
// environment dependent settings
$whoops = new \Whoops\Run;
define('ENVIRONMENT',$configuration['domains'][$_SERVER['HTTP_HOST']]['environment']);
switch(ENVIRONMENT){
    case 'development':
        //error reporting
        ini_set("display_errors", 1);
        error_reporting(E_ALL);
        //exception handler
        $whoops->pushHandler(new \Whoops\Handler\PrettyPageHandler);
    break;
    case 'production':
        //error reporting
        ini_set("display_errors", 0);
         //exception handler
        $whoops->pushHandler(function($e){
            global $configuration;
            echo '
            <style type="text/css">
            body{font-family:Arial,sans-serif;}
            h1{color:#f00;}
            div{border-radius:5px;background-color:#ddd;padding:10px;}
            </style>
            <h1>Oh No! :-(</h1>
            <p>Sorry for the inconvenience, please report the following error to <a href="mailto:' . $configuration['contacts']['tech'] . '">' . $configuration['contacts']['tech'] . '</a></p>
            <div>' . $e->getMessage() .'
            <br>(' . $e->getFile() . ':' . $e->getLine() . ')
            </div>
            ';
        });
    break;
}
$whoops->register();
/** 
* REF wrapper so that debug messages are displayed in development environment only
*/
function rr($v){
    if(ENVIRONMENT == 'development') r($v);
}
```
* Main composer autoload file is loaded
* Environment is based on the domain (as defined into __private/application-name/configurations/application.php__), of course it could be also set on some other criteria (_GET or _SESSION parameter, hard coded...)
* Errors handlers are set:
  * into development whoops with all of its inforamtions; 
  * into production a polite message with technical email contact
* REF dump function `r()` is wrapped into `rr()` so that it is displayed only into development environment, to prevent accidentally forgotten calls not to clutter production pages (it does happen...)
 
Require the file into __private/application-name/procedures/bootstrap.php__ (overwrite the three test rows):
```
// global environment
require PATH_TO_ROOT . 'private/global/procedures/environment.php';
```
Test __http://your-domain.tld__, a blank page should appear.
Into _public/application-name/procedures/index.php_ delete the first two lines of code (with `ini_set` and `error_reporting`) since no longer needed.
  
##### a note
The match between visited domain and environment (development or production) at the moment has no concrete consequences since the problem of how to set a development environment is still open: there is the [idea](https://www.smashingmagazine.com/2015/07/development-to-deployment-workflow/) to use [Vagrant](https://www.vagrantup.com/) and a repository service or to host a GIT server upon server machine

#### Dependency Injection Container
make __private/global/procedures/dependency_injection.php__:
```php
$baseContainer = new \Auryn\Injector;
$container = new \PHPCraft\Container\ContainerRdlowreyAurynAdapter($baseContainer);
return $container;
```
include it into __private/application-name/procedures/bootstrap.php__:
```php
// dependency injection
$container = require PATH_TO_ROOT . 'private/global/procedures/dependency_injection.php';
```

#### HTTP
Using a [PSR7 HTTP message interfaces](http://www.php-fig.org/psr/psr-7/) derived class like [asika/http](https://github.com/asika32764/http) there is no need to define interface and adapter class:
```
composer require asika/http
```
make __private/global/procedures/http.php__:
```php
<?php
// container definition
$container->implementationToInterface('Psr\Http\Message\RequestInterface', 'Asika\Http\Request', true);
$container->implementationToInterface('Psr\Http\Message\ResponseInterface', 'Asika\Http\Response', true);
// http only object
$http = new stdClass;
// request
$http->request = $container->make('Psr\Http\Message\RequestInterface');
// response
$http->response = $container->make('Psr\Http\Message\ResponseInterface');
//return http object
return $http;
```
include it into __private/application-name/procedures/bootstrap.php__:
```php
// http
$http = require PATH_TO_ROOT . 'private/global/procedures/http.php';
```
#### Cookies
If the application makes needs cookie use a library like this one (an adapter of [dflydev-fig-cookies
](https://github.com/dflydev/dflydev-fig-cookies)) and add to the 'require' section of composer.json:
```json
   "phpcraft/cookie": "@dev"
```
make __private/global/procedures/cookies.php__:
```php
<?php
// container definition
$container->implementationToInterface('PHPCraft\Cookie\CookieInterface', 'PHPCraft\Cookie\CookieDflydevFigCookiesAdapter', true);
// request
$cookies = $container->make('PHPCraft\Cookie\CookieInterface');
return $cookies;
```
include it into __private/application-name/procedures/bootstrap.php__:
```php
// cookies
$http->cookies = require PATH_TO_ROOT . 'private/global/procedures/http.php';
```
