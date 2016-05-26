
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
define('LOGIN_PAGE', sprintf('/backend/login', PATH_TO_ROOT));
define('LOGOUT_PAGE', sprintf('/backend/logout', PATH_TO_ROOT));
define('DEFAULT_PAGE', sprintf('/backend/prodotti', PATH_TO_ROOT));
//r($http->cookies->get('loginRequestedUrl'));
switch(SUBJECT) {
    case 'login':
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
                    ->set('loginRequestedUrl', $http->request->getUri()->getOriginal())
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
update composer:
```bash
composer update
```
make __private/application-name/configurations/routes.php__:
