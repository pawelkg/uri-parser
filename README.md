URI string parser and builder
=======

[![Build Status](https://img.shields.io/travis/thephpleague/uri-parser/master.svg?style=flat-square)](https://travis-ci.org/thephpleague/uri-parser)
[![Latest Version](https://img.shields.io/github/release/thephpleague/uri-parser.svg?style=flat-square)](https://github.com/thephpleague/uri-parser/releases)

This package contains a userland PHP URI string parser and builder compliant with:

- [RFC 3986](http://tools.ietf.org/html/rfc3986).
- [RFC 3987](http://tools.ietf.org/html/rfc3987).
- [RFC 6874](https://tools.ietf.org/html/rfc6874).

```php
<?php

use League\Uri\UriString;

var_export(UriString::parse('http://www.example.com/'));
//returns the following array
//array(
//  'scheme' => 'http',
//  'user' => null,
//  'pass' => null,
//  'host' => 'www.example.com',
//  'port' => null,
//  'path' => '/',
//  'query' => null,
//  'fragment' => null,
//);

```

System Requirements
-------

You need **PHP >= 7.1.3** but the latest stable version of PHP is recommended.

**While the library no longer requires the `ext/intl` extension, it is strongly recommended to install this extension if you are dealing with URIs containing non-ASCII host. Without the extension, the parser will throw a `InvalidURI` exception when trying to parse such URI.**


Installation
--------

```bash
$ composer require league/uri-parser
```

Documentation
---------

### URI string parsing

```php
<?php

public static function League\Uri\UriString::parse($uri): array
```

The `UriString::parse` static method is a drop-in replacement to PHP's `parse_url` function, with the following differences:

#### The parser is RFC3986/RFC3987 compliant

```php
<?php

use League\Uri\UriString;

var_export(UriString::parse('http://foo.com?@bar.com/'));
//returns the following array
//array(
//  'scheme' => 'http',
//  'user' => null,
//  'pass' => null,
//  'host' => 'foo.com',
//  'port' => null,
//  'path' => '',
//  'query' => '@bar.com/',
//  'fragment' => null,
//);

var_export(parse_url('http://foo.com?@bar.com/'));
//returns the following array
//array(
//  'scheme' => 'http',
//  'host' => 'bar.com',
//  'user' => 'foo.com?',
//  'path' => '/',
//);
// Depending on the PHP version
```

#### The parser returns all URI components.

```php
<?php

use League\Uri\UriString;

var_export(UriString::parse('http://www.example.com/'));
//returns the following array
//array(
//  'scheme' => 'http',
//  'user' => null,
//  'pass' => null,
//  'host' => 'www.example.com',
//  'port' => null,
//  'path' => '/',
//  'query' => null,
//  'fragment' => null,
//);

var_export(parse_url('http://www.example.com/'));
//returns the following array
//array(
//  'scheme' => 'http',
//  'host' => 'www.example.com',
//  'path' => '/',
//);
```

#### The parser needs no extra parameters

```php
<?php

use League\Uri\UriString;

$uri = 'http://www.example.com/';

UriString::parse($uri)['query']; //returns null
parse_url($uri, PHP_URL_QUERY); //returns null
```

#### The parser treats empty and undefined components differently

A distinction is made between an unspecified component, which will be set to `null` and an empty component which will be equal to the empty string.

```php
<?php

use League\Uri\UriString;

$uri = 'http://www.example.com?';

UriString::parse($uri)['query'];  //returns ''
parse_url($uri, PHP_URL_QUERY); //returns null
```

#### The returned path component is always defined

Since a URI is made of at least a path component, this component is never equal to `null`

```php
<?php

use League\Uri\UriString;

$uri = 'http://www.example.com?';
UriString::parse($uri)['path'];  //returns ''
parse_url($uri, PHP_URL_PATH); //returns null
```

#### The parser throws exception instead of returning `false`.

```php
<?php

use League\Uri\UriString;

$uri = '//example.com:toto';
UriString::parse($uri);
//throw a League\Uri\Exception\InvalidURI

parse_url($uri); //returns false
```

#### The parser is not a validator

Just like `parse_url`, `UriString::parse` only parses and extracts components from the URI string.

<p class="message-info">You still need to validate them against its scheme specific rules.</p>

```php
<?php

use League\Uri\UriString;

$uri = 'http:www.example.com';
var_export(UriString::parse($uri));
//returns the following array
//array(
//  'scheme' => 'http',
//  'user' => null,
//  'pass' => null,
//  'host' => null,
//  'port' => null,
//  'path' => 'www.example.com',
//  'query' => null,
//  'fragment' => null,
//);
```

### URI string building

```php
<?php

public static function League\Uri\UriString::build(array $components): string
```

You can rebuild a URI from its hash representation returned by the `UriString::parse` method or PHP's `parse_url` function using the `UriString::build` public static method.  

**If you supply your own hash you are responsible for providing valid encoded components without their URI delimiters.**

```php
<?php

use League\Uri\UriString;

$base_uri = 'http://hello:world@foo.com?@bar.com/';
$components = UriString::parse($base_uri);
//returns the following array
//array(
//  'scheme' => 'http',
//  'user' => 'hello',
//  'pass' => 'world',
//  'host' => 'foo.com',
//  'port' => null,
//  'path' => '',
//  'query' => '@bar.com/',
//  'fragment' => null,
//);

$uri = UriString::build($components);

echo $uri; //displays http://hello@foo.com?@bar.com/
```

Testing
-------

The library has a :

- a [PHPUnit](https://phpunit.de) test suite
- a coding style compliance test suite using [PHP CS Fixer](http://cs.sensiolabs.org/).
- a code analysis compliance test suite using [PHPStan](https://github.com/phpstan/phpstan).

To run the tests, run the following command from the project folder.

``` bash
$ composer test
```

Contributing
-------

Contributions are welcome and will be fully credited. Please see [CONTRIBUTING](.github/CONTRIBUTING.md) and [CONDUCT](CONDUCT.md) for details.

Security
-------

If you discover any security related issues, please email nyamsprod@gmail.com instead of using the issue tracker.

Credits
-------

- [ignace nyamagana butera](https://github.com/nyamsprod)
- [All Contributors](https://github.com/thephpleague/uri-parser/contributors)

License
-------

The MIT License (MIT). Please see [License File](LICENSE) for more information.