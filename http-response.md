# HTTP Response

HTTP response ir represented by `\Crescendo\HTTP\Response` class.

You can access this class from your controllers `$this->response` property.

Response class set various things that will returned to user as response.

`$response->setResponseCode($responseCode)` is used to set response code. By default response will have `200` response code, but you can set it to any valid response code.

`$response->setHeader($name, $value)` will set response header.

`$response->setCookie($name, $value, array $options = [])` will set response cookie. `$options` can contain one or more of:

 * `expire` - timestamp of when the cookie should expire. Defaults to end of users session
 * `path` - path where cookie will be available. Defaults to `baseUrl` configuration option.
 * `domain` - domain where cookie will be available. Defaults to `host` configuration option.
 * `secure` - whether cookie should only be available on HTTPS. Defaults to `true` if `scheme` configuration option is set to `https`.
 * `httpOnly` - whether cookie should be available to JS. Defaults to `false`.
 
`$response->removeCookie($name, array $options = [])` will remove response cookie. `$options` can contain the same options as `$response->setCookie()` method.

`$response->redirect($url, $responseCode)` will redirect user to specified `$url` with provided `$responseCode`.

`$response->setContentType($contentType)` will set response content type.