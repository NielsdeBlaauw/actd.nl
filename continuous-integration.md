---
title: Continuous Integration
---

There are many definitions of continuous integration to be found on the web. IF you are unfamilair with the practice, a good place to start is the [Wikipedia page on continuous integration](https://en.wikipedia.org/wiki/Continuous_integration) or this [Atlassian article on it](https://www.atlassian.com/continuous-delivery/continuous-integration).

Easier communication is one benefit I want to highlight. When a team consists of only one or two members, communication is relatively easy. They can talk directly to eachother and can coordinate changes quickly.

As the number of contributors on a team grows the amount of communication channels grows quicker.

<a title="Woody993 at English Wikipedia / CC0" href="https://commons.wikimedia.org/wiki/File:Metcalfe-Network-Effect.svg"><img width="256" alt="Metcalfe Network Effect" src="/assets/network-effect.svg"></a>

By centering communications around a hub such as the CI environment the number of channels are drastically reduced. This allows a team to scale more efficiently, improves quality of communication and greatly impacts consistency and collaboration within the team.
<table>
    <tr>
        <td>
            <a title="Originally User:Foobaz / Public domain" href="https://commons.wikimedia.org/wiki/File:NetworkTopology-FullyConnected.svg"><img width="256" alt="NetworkTopology-FullyConnected" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0f/NetworkTopology-FullyConnected.svg/256px-NetworkTopology-FullyConnected.svg.png"></a>
        </td>
        <td><a title="GW_Simulations / Public domain" href="https://commons.wikimedia.org/wiki/File:StarNetwork.svg"><img width="256" alt="StarNetwork" src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d0/StarNetwork.svg/256px-StarNetwork.svg.png"></a>
        </td>
    </tr>
</table>

The main drawback is that the CI platform becomes a single point of failure. So you must make sure it is reliable.

## Asynchronous communication 

Continuous integration can be thought of as a type of asynchronous communication. It allows you communicate a boatload of aspects of a project, including:

- standards,
- business rules,
- core functions,
- compatibility with external dependencies,
- configuration,
- and more...

### Example 1: 
Cou can have an internal standard that all text should have a contrast of 4.5:1, as defined in [WCAG 2.1](https://www.w3.org/TR/WCAG21/). However, not everyone in working on the project might be aware of this internal standard, or might lack the knowledge to implement it correctly. 

When standards are check in the continuous integration cycle, the commited code it tested and found to be non-compliant. The committer is now informed about the standard and is guided towards learning more.

### Example 2:
During the initial production phase of a project, a business rule has been implemented to apply free shipping to any order over $50.00 in a webshop. Years later, during the maintenance phase of the project, some dependencies are updated, including one that breaks the free shipping implementation.

Automated (functional) testing in the continuous integration workflow should identify this problem and communicate that the project no longer complies with a business rule.

WordPress core at the time of writing is executing 9073 tests (with 71743 assertions) every single time the code is edited. With the WordPress core receiving around 6-10 commits per day, that would amount to an insane amount of manual work. 

### Example 3:
The hosting provider has decided to update the project server environment to a new PHP version. Part of the team has received the memo and updated their development, testing and build environments aswel.

However, part of the team is still developing on the old PHP version, and has written some incompatible code. In the test/build phase of the continuous integration workflow this problem must be identified before the code reaches a production environment.

## Implementation

### Dependencies

In PHP, you can define and manage dependencies with Composer. Check out Micah Woods session 'Using Composer with WordPress' at WordCamp Atlanta 2016 to get a head start.

<iframe width="560" height="315" src="https://videopress.com/embed/Bl8kfVyj" frameborder="0" allowfullscreen></iframe>

<br />

Equivalent in the JavaScript space is NPM/Yarn.

### Standards
Coding standards and guidelines have [a positive effect on code quality, comprehensability and maintainability](https://www.multidots.com/importance-of-code-quality-and-coding-standard-in-software-development/). 

WordPress maintains it's [own coding standards for Core](https://make.wordpress.org/core/handbook/best-practices/coding-standards/), and has some required standards for plugins and themes published in the repository. 

#### WordPress coding standards
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

#### Other standards

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

### Testing

#### Static analysis

#### Unit tests

#### Integration tests

#### Functional tests

#### End-to-end tests

