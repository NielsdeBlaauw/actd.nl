---
title: Integrating Query Monitor Twig Profile
layout: default
order: 2
permalink: query-monitor-twig-profile/integration.html
---
To show performance information about your page load, Query Monitor Twig Profile (QMTP) needs to know what Twig is doing. For this you will have to supply a reference to the Twig Environment.

## Automatically
If you use [Timber](https://github.com/timber/timber) or [Clarkson Core](https://github.com/level-level/Clarkson-Core/) this happens automatically. Otherwise you need to [complete the integration manually](integration).

## Manually
You can manually integrate QMTP into your project.
1. Figure out how to get your [Twig Environment](https://twig.symfony.com/doc/3.x/api.html#basics) object.  
If you have a custom implementation you should know where this is. If you are using a framework, it might be available through a `filter` or `action`. Consult the documentation of your framework to find out more.
2. Give your `Twig Environment` object to QMTP.  
You can add this as a code snippet  to the `functions.php` file in your theme.  
**Example:**
```php
if ( function_exists( 'NdB\QM_Twig_Profile\collect' ) ) {
	$twig = \NdB\QM_Twig_Profile\collect( $twig );
}
```

After adding the correct code snippet, QMTP will show you the profile of your page in the Query Monitor admin bar tab.

## Next
You can experiment for yourself, or read more about [interpreting the results](output).