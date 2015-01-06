# HTTP Controllers

Your HTTP controllers should live in `application/src/Controllers/HTTP` directory and should have a namespace of `\AppNamespace\Controllers\HTTP\` ( you can replace `AppNamespace` with your own namespace ).

Typically you will want to create your own controller base class that extends `\Crescendo\Controller\HTTPController` class. This will allow you to easily extend any functionality you would want.

```
<?php

// application/src/Controllers/HTTP/Controller.php
class Controller extends \Crescendo\Controllers\HTTPController
{
    // functionality here
}

?>
```

Controller instances are created automatically for you from the matched route so you never have to create one yourself.

## Actions

Action method name must be prefixed with `action`. E.g. `actionLogin`.

Actions will receive path parameters as array as the only argument.

## Before And After Callbacks

`before($action)` method will be called before action is called. You can use this to set up any funcionality you might need later.

`after($action)` method will be called after action has finished executing. You can use this to do some common final things or anything else you need.

## Request

Current request can be accessed from `$this->request` property.

## Response

Current request response can be accessed from `$this->response` property.

## Layout

Layout is available through `$this->layout` property and is an instance of `\Crescendo\Templating\View` class.

Layout is initialized before your action or before callback is called. Layout is just a view file and views you render within your actions will become this views children.

Default layout is `layout` which will resolve to `application/views/layout.php`. There are several ways to override this depending on how much customisation you need.

If your controller will use a completely different layout you can just set `$defaultLayout` to any other layout path.

```
<?php

class UserController extends \Crescendo\Controllers\HTTPController
{
    // will resolve to 'application/views/userLayout.php'
    protected $defaultLayout = "userLayout";
}

?>
```

If you need more customisation for the view you can override `initLayout` method to setup your layout. In this method you can either set `$this->layout` property to `\Crescendo\Templating\View` class instance or call parent method and append more things.

```
<?php

class UserController extends \Crescendo\Controllers\HTTPController
{
    protected function initLayout()
    {
        // initialize layout as it would normally happen
        parent::initLayout();
        
        // add more things to the layout
        $this->layout->locals["isUserLoggedIn"] = User::isLoggedIn();
    }
}

?>
```

If a specific action needs custom behaviour for layout then you can just modify `$this->layout` directly.

```
<?php

class UserController extends \Crescendo\Controllers\HTTPController
{
    protected function actionShow(array $parameters)
    {
        $user = User::getById($parameters["id"]);
        
        $this->layout->locals["title"] = "User: {$user->name}";
    }
}

?>
```

## Helpful Methods

### Halting Action

`$this->haltAction()` will halt execution of current action. You can also use this in before or after callbacks ( if called in before callback then action itself and after callback won't be called ).

This is useful to use after forwarding to other action / controller or other circumstances.

### Forwarding To Another Controller / Action

`$this->forward($to, array $pathParameters = [], $makePrimary = false)` is used to internally forward request to other controller and action. `$to` must contain path to controller and action in the same way you define it in routes. `$pathParameters` must contain any path parameters this route might require. Set `$makePrimary` to `true` if forwarded controller and action should become the primary in this request.

This is useful in situations where you need a router that needs to execute some smart logic to fully decide where a specific request should go.

```
<?php

class HomeRouterController extends \Crescendo\Controllers\HTTPController
{
    protected function actionIndex(array $parameters)
    {
        if (User::isLoggedIn()) {
            $this->forward(
                [ "UserHome", "index" ],
                [ "id" => User::loggedIn()->id ],
                true
            );
        } else {
            $this->forward([ "GuestHome", "index" ], [], true);
        }
    }
}

?>
```

### Rendering

Instead of manually calling `$this->layout->nest(new \Crescendo\Templating\View("view"), $section)` it's recommended that you use `$this->render($view, array $locals = [])` and `$this->renderFor($section, $view, array $locals = [])` methods. This way you don't have to manually create a view instance making it shorter and easier to read.

You if need a view file for some instance you can use `$this->dispatchView($view, array $locals = [])` method.

### Redirecting

Although you can redirect with `$this->request->redirect($newUrl, $statusCode)` method it's recommended that you use `$this->redirect($newUrl, $statusCode)` method as it's shorter and will also call `$this->haltAction()` automatically as there shouldn't be any execution after redirect.