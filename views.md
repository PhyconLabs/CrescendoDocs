# Views

Views are handled through `\Crescendo\Templating\View` class.

Views in Crescendo are just PHP files with some helpers added.

You can get a new view instance easily: `$view = new \Crescendo\Templating\View($path, array $locals = [])`.

`$path` is path to view relative to `application/views` and without `.php` suffix. E.g. `users/register` will resolve to `application/views/users/register.php`.

`$locals` is an array of variables that will be available within the view. Array key must be the variable name and array value is variable value. These variables will be accessible directly within the view. E.g. if you pass `[ "userId" => 1, "query" => "example" ]` then in your view you will have access to `$userId` and `$query` variables.

You can access and modify view `$path` and `$locals` with `getView()`, `setView($path)`, `getLocals()` and `setLocals(array $locals)` methods.

## View Nesting

View nesting allows you to build up a view tree which allows you to have a very simple to use layout system.

You can nest child views using `$view->nest(\Crescendo\Templating\View $view, $section = "default")` method. `$view` is the child view and `$section` is section name where child view should live under. Default `$section` is just `default`.

There isn't a limit on how many section, child views or levels you can have.

Within your view file you can output child views using `$this->yieldSection($section = "default")` method. This will output all nested child views for given section ( defaults to `default` ) in the order they were added.

## View Rendering

To render a view just call `$view->render()` method and it will output rendered content. If instead you want to retrieve rendered output as string you can use `$view->getRenderedOutput()` method - this won't actually output it so you'll have to do it manually if you so wish.

## Checking If View File Exists

To make sure if view file exists you can use `$view->exists()` method that will return `true` if view file exists and `false` if it doesn't.

## View Helpers

Within view file you have access to several helper methods to make life easier. Since view file is rendered in the context of view class you can access all these methods via `$this`.

`$this->partial($path, array $locals = [])` will render another view at the place where this method is called. Arguments are the same as for creating a new view.

`$this->linkTo($urlName, $extra = "", array $parameters = [])` will return a link to given route. `$urlName` is route name. `$extra` will be appended to resolved link and `$parameters` is where you specify required parameters for the route. You can skip `$extra` and pass `$parameters` as second argument and it will still work correctly.

`$this->escape($text, $doubleEncode = false)` will escape given string making it XSS safe. `$text` is the string you want to escape. You can set `$doubleEncode` to true if wich to double encode the string.