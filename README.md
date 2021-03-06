# Slim-Http

Slim PSR-7 Object Decorators

[![Build Status](https://travis-ci.org/slimphp/Slim-Http.svg?branch=master)](https://travis-ci.org/slimphp/Slim-Http)
[![Coverage Status](https://coveralls.io/repos/slimphp/Slim-Http/badge.svg?branch=master&service=github)](https://coveralls.io/github/slimphp/Slim-Http?branch=master)
[![Total Downloads](https://poser.pugx.org/slim/http/downloads)](https://packagist.org/packages/slim/http)
[![License](https://poser.pugx.org/slim/http/license)](https://packagist.org/packages/slim/http)

## Installation

It's recommended that you use [Composer](https://getcomposer.org/) to install 
this library.

```bash
$ composer require slim/http "^0.5"
```

This will install the `slim/http` component and all required dependencies.
PHP 7.1, or newer, is required.

## Tests

To execute the test suite, you'll need to install all development dependencies.

```bash
$ git clone https://github.com/slimphp/Slim-Http
$ composer install
$ composer test
```


## Usage 

The Decoration Repo Provides 3 Factories which instantiate the Decorators. They respectively return PSR-7 Compatible Interfaces.
- `DecoratedResponseFactory`
- `DecoratedServerRequestFactory`
- `DecoratedUriFactory`

## Example for Instantiating a Decorated Nyholm/Psr7 Response
```php
<?php

use Nyholm\Psr7\Factory\Psr17Factory;
use Slim\Http\Factory\DecoratedResponseFactory;

$nyholmFactory = new Psr17Factory();

/**
 * DecoratedResponseFactory takes 2 parameters
 * @param \Psr\Http\Message\ResponseFactoryInterface which should be a ResponseFactory originating from the PSR-7 Implementation of your choice
 * @param \Psr\Http\Message\StreamFactoryInterface which should be a StreamFactory originating from the PSR-7 Implementation of your choice
 * Note: Nyholm/Psr17 has one factory which implements Both ResponseFactoryInterface and StreamFactoryInterface see https://github.com/Nyholm/psr7/blob/master/src/Factory/Psr17Factory.php
 */
$decoratedResponseFactory = new DecoratedResponseFactory($nyholmFactory, $nyholmFactory);

/**
 * @var \Slim\Http\Decorators\ResponseDecorator $response
 * The returned variable is a ResponseDecorator which has methods like withJson()
 */
$response = $decoratedResponseFactory->createResponse(200, 'OK');
$response = $response->withJson(['data' => [1, 2, 3]]);

```


## Example for Instantiating a Decorated Zend Diactoros Response
```php
<?php

use Zend\Diactoros\ResponseFactory;
use Zend\Diactoros\StreamFactory;
use Slim\Http\Factory\DecoratedResponseFactory;

$responseFactory = new ResponseFactory();
$streamFactory = new StreamFactory();

/**
 * DecoratedResponseFactory takes 2 parameters
 * @param \Psr\Http\Message\ResponseFactoryInterface which should be a ResponseFactory originating from the PSR-7 Implementation of your choice
 * @param \Psr\Http\Message\StreamFactoryInterface which should be a StreamFactory originating from the PSR-7 Implementation of your choice
 */
$decoratedResponseFactory = new DecoratedResponseFactory($responseFactory, $streamFactory);

/**
 * @var \Slim\Http\Decorators\ResponseDecorator $response
 * The returned variable is a ResponseDecorator which has methods like withJson()
 */
$response = $decoratedResponseFactory->createResponse(200, 'OK');
$response = $response->withJson(['data' => [1, 2, 3]]);

```


## Decoratored Response Object Methods
The decorated `ResponseInterface` provides the following additional methods:

#### `ResponseDecorator::withJson($data, $status, $options, $depth)` ####
| Parameter   | Type    | Description             |
|-------------|---------|-------------------------|
| **$data**   | `mixed` | The data to encode      |
| **$status** | `int`   | The HTTP Status Code    |
| **$depth**  | `int`   | JSON encoding max depth |

#### `ResponseDecorator::withRedirect($url, $status)` ####
| Parameter   | Type     | Description                  |
|-------------|----------|------------------------------|
| **$url**    | `string` | The redirect destination url |
| **$status** | `int`    | The HTTP Status Code         |

#### `ResponseDecorator::write($data)` ####
| Parameter | Type     | Description                              |
|-----------|----------|------------------------------------------|
| **$url**  | `string` | The data to write to the `Response` body |

#### `ResponseDecorator::isClientError()` ####
Assert the underlying response's status code is between **400** and **500**.

#### `ResponseDecorator::isEmpty()` ####
Assert the underlying response's status code is **204, 205** or **304**.

#### `ResponseDecorator::isForbidden()` ####
Assert the underlying response's status code is **403**.

#### `ResponseDecorator::isInformational()` ####
Assert the underlying response's status code is between **100** and **200**.

#### `ResponseDecorator::isOk()` ####
Assert the underlying response's status code is **200**.

#### `ResponseDecorator::isNotFound()` ####
Assert the underlying response's status code is **404**.

#### `ResponseDecorator::isRedirection()` ####
Assert the underlying response's status code is between **300** and **400**.

#### `ResponseDecorator::isServerError()` ####
Assert the underlying response's status code is between **500** and **600**.

#### `ResponseDecorator::isSuccessful()` ####
Assert the underlying response's status code is between **200** and **300**.

#### `ResponseDecorator::__toString()` ####
Will return a string formatted representation of the underlying response object.
```
HTTP/1.1 200 OK
Content-Type: application/json;charset=utf-8

{"Hello": "World"}
```


## Decoratored ServerRequest Object Methods
The decorated `ServerRequestInterface` provides the following additional methods:

#### `ServerRequestDecorator::withAttributes($attributes)` ####
| Parameter       | Type      | Description                              |
|-----------------|-----------|------------------------------------------|
| **$attributes** | `array`   | Attributes to be appended to the request |

#### `ServerRequestDecorator::getContentCharset()` ####
Returns the detected charset from the `Content-Type` header of the underlying server request object. Returns `null` if no value is present.

#### `ServerRequestDecorator::getContentType()` ####
Returns the value from the `Content-Type` header of the underlying server request object. Returns `null` if no value is present.

#### `ServerRequestDecorator::getContentLength()` ####
Returns the value from the `Content-Length` header of the underlying server request object. Returns `null` if no value is present.

#### `ServerRequestDecorator::getCookieParam($key, $default)` ####
| Parameter     | Type     | Description                                            |
|---------------|----------|--------------------------------------------------------|
| **$key**      | `string` | The attribute name                                     |
| **$default**  | `mixed`  | Default value to return if the attribute does not exist |

#### `ServerRequestDecorator::getMediaType()` ####
Returns the first detected value from the `Content-Type` header of the underlying server request object. Returns `null` if no value is present.

#### `ServerRequestDecorator::getMediaTypeParams()` ####
Returns an array of detected values from the `Content-Type` header of the underlying server request object. Returns an empty array if no values are present.

#### `ServerRequestDecorator::getParam($key, $default)` ####
Returns the value from key in `$_POST` or `$_GET`

| Parameter    | Type     | Description                                             |
|--------------|----------|---------------------------------------------------------|
| **$key**     | `string` | The attribute name                                      |
| **$default** | `mixed`  | Default value to return if the attribute does not exist |

#### `ServerRequestDecorator::getParams()` ####
Returns a merged associative array of the `$_POST` and `$_GET` parameters.

#### `ServerRequestDecorator::getParsedBody()` ####
Returns the parsed body from the underlying server request object if it already has been parsed by the underlying PSR-7 implementation. If the parsed body is empty, our decorator attempts to detect the content type and parse the body using one of the registered media type parsers.

The default media type parsers support:
- JSON
- XML

You can register your own media type parser using the `ServerRequestDecorator::registerMediaTypeParser()` method.


#### `ServerRequestDecorator::getParsedBodyParam($key, $default)` ####
Returns the value from key in the parsed body of the underlying server request object.

| Parameter    | Type     | Description                                             |
|--------------|----------|---------------------------------------------------------|
| **$key**     | `string` | The attribute name                                      |
| **$default** | `mixed`  | Default value to return if the attribute does not exist |

#### `ServerRequestDecorator::getQueryParam($key, $default)` ####
Returns the value from key in the parsed `ServerRequest` query string

| Parameter     | Type     | Description                                             |
|---------------|----------|---------------------------------------------------------|
| **$key**      | `string` | The attribute name                                      |
| **$default**  | `mixed`  | Default value to return if the attribute does not exist |

#### `ServerRequestDecorator::getServerParam($key, $default)` ####
Returns the value from key in parsed server parameters from the underlying underlying server request object.

| Parameter    | Type     | Description                                              |
|--------------|----------|----------------------------------------------------------|
| **$key**     | `string` | The attribute name                                       |
| **$default** | `mixed`  | Default value to return if the attribute does not exist  |

#### `ServerRequestDecorator::registerMediaTypeParser($key, $default)` ####
Returns the value from key in parsed server parameters from the underlying server request object.

| Parameter      | Type       | Description                                            |
|----------------|------------|--------------------------------------------------------|
| **$mediaType** | `string`   | A HTTP media type (excluding content-type params)      |
| **$callable**  | `callable` | A callable that returns parsed contents for media type |

#### `ServerRequestDecorator::isMethod($method)` ####
| Parameter   | Type     | Description     |
|-------------|----------|-----------------|
| **$method** | `string` | The method name |

#### `ServerRequestDecorator::isDelete()` ####
Asserts that the underlying server request's method is `DELETE`

#### `ServerRequestDecorator::isGet()` ####
Asserts that the underlying server request's method is `GET`

#### `ServerRequestDecorator::isHead()` ####
Asserts that the underlying server request's method is `HEAD`

#### `ServerRequestDecorator::isOptions()` ####
Asserts that the underlying server request's method is `OPTIONS`

#### `ServerRequestDecorator::isPatch()` ####
Asserts that the underlying server request's method is `PATCH`

#### `ServerRequestDecorator::isPost()` ####
Asserts that the underlying server request's method is `POST`

#### `ServerRequestDecorator::isPut()` ####
Asserts that the underlying server request's method is `PUT`

#### `ServerRequestDecorator::isXhr()` ####
Asserts that the header `X-Requested-With` from the underlying server request is `XMLHttpRequest`

## Decorated Uri Object Methods
The decorated `UriInterface` provides the following additional methods:

#### `UriDecorator::getBaseUrl()` ####
Returns the fully qualified base URL of the underlying uri object.

## Contributing
Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security
If you discover security related issues, please email security@slimframework.com 
instead of using the issue tracker.

## Credits
- [Josh Lockhart](https://github.com/codeguy)
- [Andrew Smith](https://github.com/silentworks)
- [Rob Allen](https://github.com/akrabat)
- [Pierre Bérubé](https://github.com/l0gicgate)
- [All Contributors](../../contributors)

## License

This component is licensed under the MIT license. See [License File](LICENSE) 
for more information.
