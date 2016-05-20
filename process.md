### Prerequisites
* Apache2:
  * allow .htacces files use

### Basic folders and files
* into site root make a folder named __private__
* make file __private/.htaccess__ and prevent direct access to contained files
```
# Order Deny,Allow
deny from all
```
* into site root make a folder named 'public' and its subfolder 'conf'
* configuration files: I split configuration into:
  * configuration likely to be needed by every application (i.e. domains definition, database account), for portability it's useful to name it 'application', it has a tree structure so a php or json file is needed (use json if it has to be red by different technologies like PHP and js).
  make __private/application-name/conf/application.php__ (values to be adapted)
  ```php
  <?php
return [
    'contacts' => [
        'tech' => 'tech-email-address'
    ],
    'domains' => [
        'dev.your-domain.tld' => [
            'environment' => 'development'
        ],
        'your-domain.tld' => [
            'environment' => 'production'
        ]
    ],
    'basePath' => '/',
    'languages' => ['it','en'],
    'database' => [
        'driver' => '_mysql_',
        'host' => '_localhost_',
        'username' => '_db-username_',
        'password' => '_db-password_',
        'database' => '_db-name_'
    ],
  ```
  * configuration information specific to the domain logic (use the most appropriate file type)
  make __private/application-name/conf/application-name.ini__
* make file public/index.php: store the name of the application (from now on 'application-name') and the relative path from __index.php__ to the top level of application. These informations are used from this point till the end of the script so they must be defined here
```php
<?php
define('APPLICATION','application-name');
define('PATH_TO_ROOT','../../');
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

