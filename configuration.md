# Configuration

Configuration is located in `application/config` directory. Firstly global configuration is loaded which is located in `application/config/global` directory, but you can override global configuration with your environment configuration which is located in `application/config/$environment` directory where `$environment` is your environment name.

These directories should contain `application.php` file that should return an array of configuration options.

```
<?php

return [
    "scheme" => "http",
    "host" => "localhost",
    "baseUrl" => "/"
];

?>
```

These options can then be retrieved from `\Crescendo\Config\Config` class instance which you can access via `\Crescendo\Config\Config::main()` method.

Returned array, first level keys become properties for this object and this is how you retrieve values;

```
<?php

$config = \Crescendo\Config\Config::main();

$scheme = $config->scheme;
$host = $config->host;
$baseUrl = $config->baseUrl;

?>
```

## Grouping Configuration Options

As mentioned above - your configuration lives in `application.php` file but it makes sense to split up configuration files by their areas. For example configuration for database would live in `database.php`. This makes it easier to manage configuration as you don't have large configuration files that contain everything.

To do this you just create a new configuration file for the group you like and you can access options for this group as a property for `\Crescendo\Config\Config` instance with the same name as the configuration file.

```
<?php

// application/config/global/database.php

return [
    "host" => "localhost",
    "user" => "root",
    "password" => ""
];

// anywhere else

$config = \Crescendo\Config\Config::main();

$dbHost = $config->database->host;
$dbUser = $config->database->user;
$dbPassword = $config->database->password;

?>
```

It's worth mentioning that these configuration files will only be loaded when you request them so if you don't need to retrieve options for a specific group for a given request then these configuration files won't even be loaded.