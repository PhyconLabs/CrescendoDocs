# Environments

Environments are very simple in Crescendo. You define your environment in `ENV.php` file that should return a string value of the environment name.

```
<?php

return "development"; // sets environment to 'development'

?>
```

The default environment is `default` ( which is what you'll have if you don't have a `ENV.php` file ). It's recommended that you define your environment though.