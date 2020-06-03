---
title: Logging & handling exceptions
---

## PHP & Monolog
Adding monolog as a dependency is easy with Composer:
```console
niels@actd.nl:~$ composer require monolog/monolog
```

Setting up a basic logging solution is not much harder. The following code adds a new custom logger that outputs any events to a file on your server.

```php
<?php
require_once('vendor/autoload.php');

use Monolog\Logger;
use Monolog\Handler\StreamHandler;

// create a log channel
$log = new Logger('name');
$log->pushHandler(new StreamHandler('path/to/your.log', Logger::WARNING));

// add records to the log
$log->info('Foo');
$log->error('Bar');
```

You can add different output channels in the form of handlers.

```php
$log->pushHandler(new \Monolog\Handler\SlackWebhookHandler('https://slack.com/SECRET'));
```

There is [a big list of available handlers](https://github.com/Seldaek/monolog/blob/master/doc/02-handlers-formatters-processors.md#handlers) that can output data on the Github repository.

To add metadata, like a URL, referrer, hostname, etc, you can add processors like the WebProcessor.

```php
$log->pushProcessor( new \Monolog\Processor\WebProcessor() );
```

There are [many processors](https://github.com/Seldaek/monolog/blob/master/doc/02-handlers-formatters-processors.md#formatters) available by default.

One additional handler that is particularly useful when working with WordPress is Wonologs [WPContextProcessor](https://github.com/inpsyde/Wonolog/blob/master/src/Processor/WpContextProcessor.php). It logs some additional WP specific data, such as the multisite ID, the currently logged-in user and if the request was a CRON or AJAX request.


You can also format the output of the event. If you wanted to e-mail the alert, you might use the HtmlFormatter.

```php
$handler->setFormatter( new \Monolog\Formatter\HtmlFormatter() );
```

It is possible to use handlers to control behaviour. The SamplingHandler allows you to only log every `n`-th message.

```php
$stream_handler = new StreamHandler( 'path/to/your.log', Logger::INFO );
$sampling_handler = new SamplingHandler( $stream_handler, 10 ); // Only 1 in 10 messages goes to the stream handler.
$log->pushHandler( $handler );
```

The FingersCrossedHandler captures all events, until one exceeds some treshold level and then forwards everything to the next handler.

```php
$stream_handler = new StreamHandler( 'path/to/your.log', Logger::INFO );
$handler = new FingersCrossedHandler( $stream_handler, Logger::ERROR );
$log->pushHandler( $handler );
```

To get a bit more background information you can watch the session 'Advanced logging in PHP' by Claes Gyllensvärd at DrupalCamp London.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/aZ_9PuHemBk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br/>
You might also want to check out this in-depth [article on monolog by stackify](https://stackify.com/php-monolog-tutorial/).

Plugins might provide their own logging and error handling. [WooCommerce](https://woocommerce.wordpress.com/2017/01/26/improved-logging-in-woocommerce-2-7/) does logging like this:

```php
wc_get_logger()->info('Foo');
wc_get_logger()->error('Bar');
```

While [GravityForms](https://docs.gravityforms.com/custom-logging-statements/) provides a similar functionality:

```php
GFCommon::log_debug('Foo');
GFCommon::log_error('Bar');
```


## Centralised logging

There are platforms like Sentry that allow you to collect logging information in a central hub.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/3yXtXnjoIpY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br />
## Javascript

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/DIzJC8wRp-s" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
