# HTTP Routing

Your application HTTP routes are defined in `application/routes/http` directory. Firstly global routes from `application/routes/http/global` are loaded but you can append more routes for your specific environment from `application/routes/http/$environment`.

All route files will have access to `$router` variable which is used to define new routes.

By default only `index.php` file is loaded but you can separate out your routes into groups and load them via `$router->load($groupName)` method.

```
<?php

// in application/routes/http/global/index.php

$router->load("users"); // will load 'application/routes/http/global/users.php'

?>
```

These loaded files will also have access to this `$router` variable.

## Note On Configuration

Before you can fully use routing you should define some configuration options for your application.

## Defining Routes

Routes are defined via `$router->on($path, array $options = [])` method where `$path` is the URL path that route should match and `$options` is an optional array of additional checks that should be performed before route is matched.

### Route Path

`$path` is just a string that will be matched against current request path. It can contain parameters for things like IDs and slugs. You can define path parameters like so: `users/<userId>`. Path parameter will match everything except slash. These parameters will then be passed to route handler as an array where key is the parameter name and value is parameter value.

`$path` can also contain a wildcard `*` that will match everything. Useful for defining catch-all routes.

### Route Options

`$options` can contain various options that request will be checked against before route is matched.

#### Request Method

By default `$request->on()` method will only match GET requests but you can change that by using `$options["method"]`.

The easier approach is to use `$router->onMETHOD()` methods where you replace `METHOD` with the request method.

```
<?php

// these two are equivalent

$router->onGet("/");

$router->on("/", [ "method" => "get" ]);

?>
```

You can also respond to any request method by using `ANY` as method name.

```
<?php

$router->onAny("/");

?>
```

#### Headers

In `$options["headers"]` you define what headers need to match. It needs to be defined as an array where array key is header name and array value is header value that it needs to match. You can also set array value to `true` which means that header just needs to exist but doesn't have to be set to any specific value.

```
<?php

$router->onGet("api.json", [
    "headers" => [
        "Content-Type" => "application/json"
    ]
]);

?>
```

#### Cookies

In `$options["cookies"]` you define what cookies need to match the same way how you define headers.

```
<?php

$router->onGet("me", [
    "cookies" => [
        "SID" => true
    ]
]);

?>
```

#### Request Parameters

You can match against request parameters in `$options["METHODparameters"]` where `METHOD` is one of: `get`, `post`, `put`, `delete`, `json`. Requirements are defined in the same way as for headers and cookies.

```
<?php

$router->onGet("search", [
    "getParameters" => [
        "q" => true
    ]
]);

?>
```

#### AJAX Requests

By default router won't care if request is an AJAX request or not but you can enable this check by setting `$options["isAjax"]` to `true` if you want request to be AJAX or to `false` if you don't want that.

```
<?php

$router->onGet("status", [
    "isAjax" => true
]);

?>
```

#### Named Routes

Named routes allows you to name routes so that you can easily generate links to these routes using `\Crescendo\HTTP\URL` class or `\Crescendo\Templating\View::linkTo()` method.

You can set route name with `$options["name"]`. Afterwards you can get a link to this route using `\Crescendo\HTTP\URL::urlName($extra = "", array $parameters = [])`. You can replace `urlName` with the name of your route. `$extra` is an optional string that will be appended to the link. `$parameters` must contain path parameters that your URL has. If you don't have `$extra` you can just pass `$parameters` as the first argument and everything will still work correctly.

```
<?php

// registers route with name
$router->onGet("/users/<id>", [ "name" => "showUser" ]);

// retrieve URL for user with ID 1
$url = \Crescendo\HTTP\URL::showUser([ "id" => 1 ]);

?>
```

Within your views you can also use `$this->linkTo($name, $extra = "", array $parameters = [])` method to achieve the same thing.

```
<?php

// registers route with name
$router->onGet("/users/<id>", [ "name" => "showUser" ]);

// retrieve URL for user with ID 1 within your view file
$url = $this->linkTo("showUser", [ "id" => 1 ]);

?>
```

## Route Handlers

Defining route requirements is one half of routing - the other half is telling it what to do when route matches.

`$router->on()` methods will return a new route object on which you then need to use `->dispatch($handler)` method on to tell where to go if it matches.

`$handler` can either be a anonymous function or a controller and action pair. Using controllers and actions is recommended.

### Anonymous Function Handler

```
<?php

$router->onGet("/")->dispatch(function(array $parameters, \Crescendo\HTTP\Request $request, \Crescendo\HTTP\Response $response) {
    // handle request
});

?>
```

`$parameters` contain path parameters. `$request` is current request that triggered this handler. `$response` is current response that will be sent to the browser.

### Controller And Action Handler

You can define controller and action is two ways. Either as a string with two parts separated by `::` character where first part is controller and second is action or as array where first element is controller and second is action.

Controller can just be defined as its base name and it will automatically be expanded to fully quallified class name. For example setting it to `Home` will automatically expand it to `\App\Controllers\HTTP\HomeController` assuming your application namespace is `App`. You can also provide a fully qualified class name if you like.

Action can be defined as action name without `action` prefix. For example setting it to `index` will expand it to `actionIndex`.

```
<?php

// all version will resolve to \App\Controllers\HTTP\HomeController::indexAction()

$router->onGet("/", "Home::index");

$router->onGet("/", [ "Home", "index" ]);

$router->onGet("/", [ "\App\Controllers\HTTP\HomeController", "index" ]);

?>
```

Action method will receive `array $parameters = []` as an argument which will contain request path parameters.

## Route Grouping

Routes with common requirements can be grouped using `$router->group(array $options, Closure $callback)` method. This allows you to define requirements once and they will automatically be merged with specific route requirements. This will also work with unlimited depth ( you can have groups within groups ) and with `$router->load()` calls within these groups.

`$options` can contain the same things as `$options` for `$router->on()` methods. The only addition is `baseUrl` which you can use to define URL path namespace for your routes within this group.

`$callback` is where you actually register your routes - this function will receive `$router` as its only argument.

```
<?php

// these two are equivalent

$router->group([ "baseUrl" => "api", "headers" => [ "Content-Type" => "application/json" ] ], function($router) {
    // will respond to GET /api/users where Content-Type is application/json
    $router->onGet("users")->dispatch([ "Api\User", "index" ]);
});

?>
```