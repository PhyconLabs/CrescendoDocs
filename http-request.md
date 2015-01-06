# HTTP Requests

HTTP requests in Crescendo are represented by `\Crescendo\HTTP\Request` class.

You can get the current request instance with `\Crescendo\HTTP\Request::current()` method or you can also access it in your controller from `$this->request`.

## Getting Request Details

Request class has various properties that you can access and modify.

`$request->method` contains request method in all caps. You can use `\Crescendo\HTTP\Request::METHOD_GET`, `\Crescendo\HTTP\Request::METHOD_POST`, `\Crescendo\HTTP\Request::METHOD_PUT`, `\Crescendo\HTTP\Request::METHOD_DELETE` class constants to compare request method ( or just use method name as string but remember that method is in all caps ).

`$request->scheme` contains request scheme.

`$request->host` contains request host.

`$request->port` contains request port.

`$request->path` contains request path. Request path won't contain trailing slash if it exists ( except for root URL which will return `/` ).

`$request->query` contains request query string ( as string ).

`$request->url` contains the full request URL that contains `scheme`, `host`, `port`, `path` and `query`. If URL is modified all these parts will change respectively. The same works the other way as well - if you change any of the parts the URL will change accordingly.

`$request->parameters` contains request parameters as an array of arrays. Key is method name and value is another array of actual parameters. If method doesn't have any parameters it won't exist in this array or will contain an empty array. You can also use `$request->METHODparameters` where method is method name. E.g. `$request->getParameters` that will return an array of `GET` parameters.

`$request->headers` contains an array of headers where array key is header name and array value is header value.

`$request->cookies` contains an array of cookies where array key is cookie name and array value is cookie value.

`$request->isAjax` will be `true` if request is an AJAX request or `false` if it isn't.

`$request->userAgent` contains request user agent ( or `null` if request didn't have an user agent ).

`$request->ipAddress` contains request client IP address.