### Prerequisites
* Apache2:
  * allow .htacces files use

### first basic files
* into site root make a folder named __private__
* make file __private/.htaccess__ and prevetn direct access to contained files
```
# Order Deny,Allow
deny from all
```
* into site root make a folder named 'public' and its subfolder 'conf'
* make file public/index.php:
```
<?php
define('APPLICATION','application-name');
define('PATH_TO_ROOT','../../');
```
`APPLICATION` stores the name of the application (from now on 'application-name'), `PATH_TO_ROOT` the relative path form index.php to the top level of application. Thes informations are used from now on so they must be defined here.
It is very likely that there will be at least one piece of information that has value through the whole application, so make: 
* __private/application-name/conf/application-name.ini__:
```
// local configuration
$configuration = include(PATH_TO_ROOT .'private/' . APPLICATION . '/include/configuration.php');
//bootstrap
require PATH_TO_ROOT . 'private/' . APPLICATION . '/' . $configuration['domains'][$_SERVER['HTTP_HOST']]['environment'] . '.bootstrap.php';
```
### __.htaccess__
```
Options +FollowSymlinks
RewriteEngine on
#default domain page
RewriteRule ^$ http://your-domain.tld/default-application-subject [R,L]
#requests pointing to real files are not redirected
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ public/application-name/index.php [NC]
```

