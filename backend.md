
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

```
make __private/application-name/procedures/backend.php__
```php
<?php
//authentication
require sprintf('%sprivate/%s/procedures/%s/authentication.php', PATH_TO_ROOT, APPLICATION, AREA);
```

Add to the 'require' section of composer.json (adapter of [nikic/FastRoute](https://github.com/nikic/FastRoute)):
```json
"phpcraft/router": "@dev"
```
update composer:
```bash
composer update
```
make __private/application-name/configurations/routes.php__:
