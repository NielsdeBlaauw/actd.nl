---
author: Niels de Blaauw
title: Understanding the Psalm autoloader
tags: [
    'psalm', 
    'php',
    'static analysis',
    'autoloader',
    'composer'
]
---

[Psalm](https://psalm.dev) is a static analysis tool maintained by Vimeo that scans PHP files for potential problems. In order to correctly assert issues in your codebase it needs to find relations definitions and invocations of your program. Customising the Psalm autoloader allows you to assist Psalm in finding the correct files and definitions within your program, in case it can't find the right files on it's own. 

## A generic introduction to autoloading
Autoloading is a capability to automatically find and link the right parts of your PHP script. Instead of manually using `require_once` or `include`, autoloading uses the `spl_autoload_register` PHP function to define where to look for files based on their class name.

Some standards for autoloading are specified in [PSR-0 (deprecated)](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md) and [PSR-4](https://www.php-fig.org/psr/psr-4/). 

[Composer](https://getcomposer.org/) is a well known dependency manager for PHP. Beyond defining dependencies, it also handles autoloading for libraries and possibly your own program. This autoloading logic is bootstrapped by loading the `vendor/autoload.php` file, which subsequently registers all required autoloaders. These can be in the form of PSR-0, PSR-4, classmaps or individual files. 

## Psalm default autoloading behaviour
By default Psalm will look for `vendor/autoload.php` in the root of your project and folder below it. If it can not find the composer autoloader it will let you know with the following message.

```shell
$ vendor/bin/psalm
Could not find any composer autoloaders in ~/project/
Add a --root=[your/project/directory] flag to specify a particular project to run Psalm on.
```

When `resolveFromConfigFile="true"` is set, Psalm will look for the `vendor/autoload.php` file from the place where the config file is stored.

If you run Psalm with `--debug` parameter, you can see which autoload files Psalm is using:

```shell
Scanning files...
Registering autoloaded files
   ~/project/vendor/autoload.php
   ~/project/vendor/composer/autoload_real.php
   ~/project/vendor/composer/ClassLoader.php
   ~/project/vendor/composer/autoload_static.php
   ~/project/vendor/symfony/polyfill-ctype/bootstrap.php
   ~/project/vendor/symfony/polyfill-mbstring/bootstrap.php
   ~/project/vendor/symfony/polyfill-php80/bootstrap.php
   ~/project/vendor/amphp/amp/lib/functions.php
   ~/project/vendor/amphp/amp/lib/Internal/functions.php
   ~/project/vendor/symfony/polyfill-intl-grapheme/bootstrap.php
   ~/project/vendor/symfony/polyfill-intl-normalizer/bootstrap.php
   ~/project/vendor/symfony/polyfill-php73/bootstrap.php
   ~/project/vendor/symfony/string/Resources/functions.php
   ~/project/vendor/amphp/byte-stream/lib/functions.php
   ~/project/vendor/vimeo/psalm/src/functions.php
   ~/project/vendor/vimeo/psalm/src/spl_object_id.php
   ~/project/vendor/composer/package-versions-deprecated/src/PackageVersions/Versions.php
```

## Overwriting the Psalm autoloader
You can specify a php file with the `autoloader` attribute on the `psalm` tag in `psalm.xml`.  This file is then considered the autoloader and will be responsible for bootstrapping all files Psalm needs to analyse your code correctly.

File will look something like the following:

```xml
<?xml version="1.0"?>
<psalm
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="https://getpsalm.org/schema/config"
    xsi:schemaLocation="https://getpsalm.org/schema/config vendor/vimeo/psalm/config.xsd"
    ...
    autoloader="load.php"
>
    <projectFiles>
        ...
    </projectFiles>
</psalm>
```

When `resolveFromConfigFile="true"` is set, the path you specify in `autoloader=""` will be relative to the configuration file.

If the custom autoloader can not be found yo will receive the following message:

```shell
$ vendor/bin/psalm --debug
Problem parsing ~/project/psalm.xml:
  Cannot locate autoloader
```

If the autoload file is found, the output will look something like this.

```shell
$ vendor/bin/psalm --debug
Config change detected, clearing cache
Scanning files...
Registering autoloaded files
   ~/project/vendor/autoload.php
   ~/project/vendor/composer/autoload_real.php
   ~/project/vendor/composer/ClassLoader.php
   ~/project/vendor/composer/autoload_static.php
   ~/project/vendor/symfony/polyfill-ctype/bootstrap.php
   ~/project/vendor/symfony/polyfill-mbstring/bootstrap.php
   ~/project/vendor/symfony/polyfill-php80/bootstrap.php
   ~/project/vendor/amphp/amp/lib/functions.php
   ~/project/vendor/amphp/amp/lib/Internal/functions.php
   ~/project/vendor/symfony/polyfill-intl-grapheme/bootstrap.php
   ~/project/vendor/symfony/polyfill-intl-normalizer/bootstrap.php
   ~/project/vendor/symfony/polyfill-php73/bootstrap.php
   ~/project/vendor/symfony/string/Resources/functions.php
   ~/project/vendor/amphp/byte-stream/lib/functions.php
   ~/project/vendor/vimeo/psalm/src/functions.php
   ~/project/vendor/vimeo/psalm/src/spl_object_id.php
   ~/project/vendor/composer/package-versions-deprecated/src/PackageVersions/Versions.php
   ~/project/vendor/composer/autoload_files.php
   ~/project/load.php
```

Three things to note:

1. The composer autoloader is still processed, even when we add our own autoload attribute.
2. The file specified by the autoload attribute is processed last, after the default composer one. In your custom autoload file, you can use composer dependencies.
3. The autoload attribute path can not be absolute. It must be a relative path, either to the config or to the specified Psalm root.

## Why you might customise the Psalm autoloader

### Non-standard autoloading dependencies
Though Psalm will attempt to register the standardized autoloaders that Composer provides, it will not execute any code it is analysing. It will look for `require_once` and similar functions and parse these as well.

Psalm will actually figure out the following:

```php
// src/Foo.php
class Foo{
    public function doSomething():void{
        require_once '../Bar.php';
        new \Bar;
    }
}
```

```php
// Bar.php
class Bar{}
```

The result will be the following:

```shell
Analyzing files...
Getting ~/project/src/Foo.php
Analyzing ~/project/src/Foo.php
Parsing ~/project/src/Foo.php
  checking Bar.php
Parsing ~/project/Bar.php
Checking class references
```

However, in some cases you might not bootstrap your own environment. An example of this would be WordPress, with Woocommerce installed and running, and a custom plugin that we want to analyse with Psalm that depends on WooCommerce classes.

In this case, WordPress would bootstrap and load WooCommerce, which would in turn make its code available in the scope of the request.

Within our plugin we might extend `WC_Order_Refund`, but because we never explicitly require the file that creates contains this class, and it is not autoloaded through composer, Psalm has no way to find out what `WC_Order_Refund` is.

In turn it will alert: `ERROR: UndefinedClass - src/Foo.php:9:13 - Class or interface WC_Order_Refund does not exist (see https://psalm.dev/019)
`.

In the file we define in the `autoloader=""` attribute we can help Psalm in this example by either:

1. Bootstrapping WordPress completely. 
  The autoloader file would include something like:
  ```php
  <?php
 require_once(__DIR__. '/../wp-load.php');
 ```
2. Explicitly requiring the files we need.
  In this case the autoloader file would include something like:
  ```php
  <?php
 require_once(__DIR__. '/plugins/woocommerce/src/Autoloader.php');
 \Automattic\WooCommerce\Autoloader::init();
 ```

Note that explicitly requiring files is more work, but might result in a cleaner run.

### Declaring constants and functions
You can use the autoloader file to declare constants and functions you might need while running Psalm, which might normally be set by the environment the script is run in. 

An example file like the following:

```php
# src/Foo.php
class Foo{
    public function do_something():void{
        $example = BAR . '_test';
    }
}
```

Will trigger the following error:

```shell
ERROR: UndefinedConstant - src/Foo.php:4:20 - Const BAR is not defined (see https://psalm.dev/020)
        $example = BAR . '_test';
```

However, you can add `define('BAR', 'bar');` to your autoloading file and the `UndefinedConstant` error will no longer be triggered.

### Handling environments
You can also load a different bootstrap file in your development environment and in a CI pipeline with something like the following:

```php
# load.php
if ( file_exists( 'psalm-bootstrap.php' ) ) {
    require_once 'psalm-bootstrap.php';
} else {
    require_once 'psalm-bootstrap.dist.php';
}
```

In this example you would commit `psalm-bootstrap.dist.php` into your version control system and have the `psalm-bootstrap.php` in the ignored files. 

You can also add debugging output, notices or other information to the output which might help you. Any output generated in the autoloader file is shown at the start of the Psalm run.

An example to add the date/time of the run and some environment information:

```php
# load.php
printf( "Runs on: %s\n", (new DateTime())->format( 'r' ) );
phpinfo( INFO_GENERAL );
```

## Alternatives
Instead of loading the actual code that defines functions and classes, you might be able to use [stubs](https://psalm.dev/docs/running_psalm/configuration/#stubs). This might be faster, but the downside is that the stubs need to be kept in sync with the actual dependencies.

You might check out available [plugins](https://psalm.dev/plugins) that help autoloading some popular frameworks. It is also possible to write your own.