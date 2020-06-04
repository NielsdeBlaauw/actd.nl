---
title: Testing & catching undefined behaviour early
layout: topic
order: 3
---

## Coding standards

Coding standards and guidelines have [a positive effect on code quality, comprehensability and maintainability](https://www.multidots.com/importance-of-code-quality-and-coding-standard-in-software-development/). 

WordPress maintains it's [own coding standards for Core](https://make.wordpress.org/core/handbook/best-practices/coding-standards/), and has some required standards for plugins and themes published in the repository. 

### WordPress coding standards
For PHP, the [WordPress coding standards](https://github.com/WordPress/WordPress-Coding-Standards) are built on a framework called 'PHP Code Sniffer'.

The purpose of WordPress coding standards is to standardise code style and prevent obviously incompatible code from becoming part of your project.

To install and run the coding standards on your local project, go to the root of your project folder and require it, and it's dependencies, through composer. Then run the coding standards tool by executing the executable in the `vendor/bin` folder, like shown below.

```console
me@actd.nl:~/project$ composer require --dev dealerdirect/phpcodesniffer-composer-installer wp-coding-standards/wpcs
me@actd.nl:~/project$ vendor/bin/phpcs --standard=WordPress ./
```

After installing, WPCS will notify you off issues in your code, such as the following example, which would break templates.

```php
ini_set('short_open_tag', 'Off'); // Error.
```

For more information I recommend watching Juliette Reinders Folmer: Leveraging the WordPress Coding Standards to review plugins and themes on WordPress TV.

<iframe width="560" height="315" src="https://videopress.com/embed/QwyAmXdy" frameborder="0" allowfullscreen></iframe>
<script src="https://videopress.com/videopress-iframe.js"></script>

<br />

For JavaScript coding standards there is a [WordPress ESLint package](https://www.npmjs.com/package/@wordpress/eslint-plugin).

For CSS coding standards there is an [Stylelint configuration](https://github.com/WordPress-Coding-Standards/stylelint-config-wordpress) available.

You can configure [HTMLHint](https://github.com/htmlhint/HTMLHint) to catch some non-standard HTML:

```json
{
  "tagname-lowercase": true,
  "attr-lowercase": true,
  "attr-value-double-quotes": true,
  "id-unique": true,
  "src-not-empty": true,
  "attr-no-duplication": true,
  "tag-self-close": true,
  "attr-value-not-empty": true
}
```

### Other standards

The WordPress Coding standards are not the only publicly maintained standards. You can augment the coding standards you want to maintain by adding rules/sniffs from other standards as well.

Some to check out:
- PSR1, PSR2, PSR12, included with [PHPCS](https://github.com/squizlabs/PHP_CodeSniffer).
- [WordPress VIP](https://github.com/Automattic/VIP-Coding-Standards).
- [Slevomat coding standards](https://github.com/slevomat/coding-standard).
- [PHP Compatibility](https://github.com/PHPCompatibility/PHPCompatibility).

An example custom PHPCS configuration might look something like this:

```xml
<?xml version="1.0"?>
<ruleset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" name="My Coding Standards" xsi:noNamespaceSchemaLocation="https://raw.githubusercontent.com/squizlabs/PHP_CodeSniffer/master/phpcs.xsd">
	<description>Basic custom coding standards.</description>

	<rule ref="PHPCompatibility"/>
	<config name="testVersion" value="7.1-"/>

	<rule ref="WordPress-Core">
		<exclude name="WordPress.PHP.YodaConditions.NotYoda" />
	</rule>

	<rule ref="WordPress-Extra"/>

	<rule ref="Squiz.PHP.CommentedOutCode"/>
	<rule ref="Generic.CodeAnalysis.UnusedFunctionParameter"/>
	<rule ref="PSR2.Files.ClosingTag"/>

	<rule ref="WordPressVIPMinimum.Hooks"/>
	
	<rule ref="SlevomatCodingStandard.Commenting.ForbiddenAnnotations">
		<properties>
			<property name="forbiddenAnnotations" type="array">
			    <element value="@created"/>
			</property>
		</properties>
	</rule>
</ruleset>
```

## Integration testing

> Integration tests determine if independently developed units of software work correctly when they are connected to each other.
- [Martin Fowler](https://martinfowler.com/bliki/IntegrationTest.html)

In the WordPress ecosystem, independently developed units of software are the norm. Think of WordPress core, all of your plugins, your theme(s), and any third party services you might rely on.

### Why no unit-tests?
For anyone who is wondering why I prefer integration tests over unit tests: unit tests have a very specific (and small) use case for business logic. Anything else quickly hooks into WordPress or it's plugins and requires a crazy amount of mocking to even get started. Even then you are mostly testing [glue code](https://en.wikipedia.org/wiki/Glue_code), instead of actual logic.


A great article on this topic is [Kent C. Dodds 'Write tests. Not too many. Mostly integration.'](https://kentcdodds.com/blog/write-tests).

### Implementing

[Codeception for WordPress](https://codeception.com/for/wordpress) is a more traditional testing solution. It has been around for many years and used by many.

[Wordhat](https://wordhat.info/) allow for creating tests that simulate critical scenario's through a domain specific language.

<iframe width="560" height="315" src="https://videopress.com/embed/iyvIZEdS" frameborder="0" allowfullscreen></iframe>
<script src="https://videopress.com/videopress-iframe.js"></script>

<br />
An example codeception test might look something like this (example from WP-Browser:

```php
<?php
// file tests/integration/SubmissionHandlingTest.php

class SubmissionHandlingTest extends \Codeception\TestCase\WPTestCase {
    public function test_good_request() {
        $request = new WP_Rest_Request();
        $request->set_body_params( [ 'name' => 'luca', 'email' => 'luca@theaveragedev.com' ] );
        $handler = new  Acme\Signup\SubmissionHandler();

        $response = $handler->handle( $request );

        $this->assertIntsanceOf( WP_REST_Response::class, $response );
        $this->assertEquals( 200, $response->get_status() );
        $this->assertInstanceOf( Acme\Signup\Submission_Good::class, $handler->last_submission() );
        $this->assertEquals( 'luca', $handler->last_submission()->name() );
        $this->assertEquals( 'luca@theaveragedev.com', $handler->last_submission()->email() );
    }
}
```

## Static analysis

Wikipedia describes [static analysis](https://en.wikipedia.org/wiki/Static_program_analysis) as:

> the analysis of computer software that is performed without actually executing programs, in contrast with dynamic analysis, which is analysis performed on programs while they are executing.
> [...]
> Analysis that takes into account interactions between unit programs to get a more holistic and semantic view of the overall program in order to find issues and avoid obvious false positives.

As with the rationale for Integration Tests, static analysis provides a lot of value in that it tests the interactions between different parts of your system.

Because the program is not actually executed, it is much faster than an integration test, which allows it to be much more in-depth.

WordPress also has a great habit of giving signatures to all core functions, which helps static analysis tools to determine what is happening. Odds are, you don't have to make many changes to get static analysis working and catching issues.

In PHP, there are two main tools for static analysis. Vimeo's [Psalm](https://github.com/vimeo/psalm) and [phpstan](https://phpstan.org/). 

### Example Psalm implementation

1. Install Psalm
    ```console
    niels@actd.nl:~$ composer require --dev vimeo/psalm
    ```

2. Psalm config.
    ```xml
    <?xml version="1.0"?>
    <psalm
        totallyTyped="false"
        autoloader="psalm-autoloader.php"
        resolveFromConfigFile="true"
        hideExternalErrors="true"
        allowStringToStandInForClass="true"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="https://getpsalm.org/schema/config"
        xsi:schemaLocation="https://getpsalm.org/schema/config vendor/vimeo/psalm/config.xsd"
        errorBaseline="psalm-baseline.xml.dist"
        errorLevel="2" 
    >
        <projectFiles>
            <directory name="src" />
            <ignoreFiles>
                <directory name="vendor" />
            </ignoreFiles>
        </projectFiles>
    </psalm>

    ```

3. Bootstrap WordPress.
    ```php
    <?php
    require_once(__DIR__. '/../wp-load.php');
    ```

4. Set up a baseline.
    ```console
    niels@actd.nl:~$ vendor/bin/psalm --set-baseline=your-baseline.xml
    ```
