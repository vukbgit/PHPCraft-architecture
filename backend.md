
add area definition to __private/application-name/configurations/application.php__:
```php
'backend' => [
            'configuration' => false, // whether to load private/application-name/configurations/area-name.php
            'procedure' => true // whether to load private/application-name/procedures/area-name.php
        ]
```
add aura/auth library
```
composer require aura/auth
```
Add to the 'require' section of composer.json:
```json
"eonasdan/bootstrap-datetimepicker": "^4.17",
"phpcraft/container": "@dev",
"phpcraft/cookie": "@dev",
"phpcraft/router": "@dev",
"phpcraft/database": "@dev",
"phpcraft/template": "@dev",
"phpcraft/message": "@dev",
"phpcraft/csv": "@dev",
"phpcraft/subject": "@dev"
```
eonasdan/bootstrap-datetimepicker is a javascript library and composer installs it (along with its dependencies, such as jquery) into a 'components' folder into top directory unless instructed differently, so add a new section into composer.json:
```json
"config": {
        "component-dir": "public/global/components"
    }
```
update composer:
```bash
composer update
```
make __private/application-name/procedures/backend/authentication.php__:
```php
<?php
//session
@session_start();
//instances
$authFactory = $container->make('Aura\Auth\AuthFactory', [
    ':cookie' => $_COOKIE,
]);
$auth = $authFactory->newInstance();
//check current authentication status
$logStatus = $auth->getStatus();
define('LOGIN_PAGE', sprintf('/backend/logging/in', PATH_TO_ROOT));
define('LOGOUT_PAGE', sprintf('/backend/logging/out', PATH_TO_ROOT));
define('DEFAULT_PAGE', sprintf('/backend/prodotti', PATH_TO_ROOT));
//r($http->cookies->get('authenticationRequestedUrl'));
switch(SUBJECT) {
    case 'authentication':
        switch($logStatus) {
            case 'VALID':
                $http->response = $http->response->withHeader('Location', DEFAULT_PAGE);
            break;
            default:
                //check if authentication has been requested assuming that fields names are 'username' and 'password'
            break;
        }
    break;
    default:
        switch($logStatus) {
            case 'VALID':
                //pass
            break;
            default:
                $http->response = $http->cookies
                    ->set('authenticationRequestedUrl', $http->request->getUri()->getOriginal())
                    ->withHeader('Location', LOGIN_PAGE);
            break;
        }
    break;
}
```
make __private/application-name/procedures/backend.php__
```php
<?php
//authentication
require sprintf('%sprivate/%s/procedures/%s/authentication.php', PATH_TO_ROOT, APPLICATION, AREA);
```
add default backend page to .htaccess:
```htaccess
#default areas page
RewriteRule ^backend/?$ /backend/logging/in [R,L]
```
make __private/application-name/applications/Logging.php__:
