---
author: Niels de Blaauw
title: Analysis of Data transfer savings of WordPress jQuery update
tags: [
    'WordPress', 
    'jQuery',
    'performance',
    'update'
]
---

WordPress 5.6 is going to [update jQuery](https://make.wordpress.org/core/2020/06/29/updating-jquery-version-shipped-with-wordpress/) from version 1.12 to 3.5.1. This move is going to save incredible amounts of data; improving user experiences and saving money. Let's do some back-of-the-napkin calculation what kind of savings we are talking about!

## What's happening?

As said in the intro: coming WordPress releases will update jQuery from an old version (1.12.4, released may 2016) to a newer version (3.5.1). In the process, they will also update jQuery UI and remove jQuery migrate.

![jQuery logo](/assets/jquery-logo.png)

The following roadmap was published in the announcement:

1. Remove jQuery Migrate 1.x. This is planned for WordPress 5.5.
2. Update to the latest version of jQuery and add the latest jQuery Migrate. This is tentatively planned for WordPress 5.6 depending on test results. Updating to the latest jQuery UI, version 1.12.1, is also planned for 5.6.
3. Remove jQuery Migrate. This is tentatively planned for WordPress 5.7 or later, depending on testing.

### Removing jQuery migrate
In the [list of warning messages](https://github.com/jquery/jquery-migrate/blob/1.x-stable/warnings.md) jQuery migrate can generate we find the following note:

> The use jQuery Migrate in production has performance impacts and can complicate debugging as it modifies the normal behavior of the version of jQuery being used.

![Oscar from Sesame street saying "I love it cause it's trash."](https://media.giphy.com/media/yRNSxsl1rJEwU/giphy.gif)

Removing jQuery migrate 1.4.1 immedietly saves downloading 10.1kb (4kb gzipped) on every first request to a domain. 

### Updating jQuery
Between jQuery 1.x and jQuery 2.x support for [Internet Explorer 6/7/8 was dropped](https://github.com/jquery/jquery-migrate/blob/1.x-stable/warnings.md). This resulted in about [12% smaller file](https://mathiasbynens.be/demo/jquery-size). Since then, the size of jQuery has crept back up. The 3.5.1 minified, gzipped production download is a 31.4kb download.

- minified
  - 96.9kb (jQuery 1.12.4)
  - 89.5kb (jQuery 3.5.1)
  - **7.4kb** saved
- minified, gzipped 
  - 34.7kb (jQuery 1.12.4)
  - 31.4kb (jQuery 3.5.1) 
  - **3.3kb** saved 

## The impact

Removing jQuery migrate, and updating jQuery itself, reduces the network payload by about 7.3kb and eliminates a request. This by itself might not seem as much, but WordPress has a very large reach.

As described by Danny van Kooten in his post ['CO2 emissions on the web'](https://dannyvankooten.com/website-carbon-emissions/):

> Shaving off a single kilobyte in a file that is being loaded on 2 million websites reduces CO2 emissions by an estimated 2950 kg per month.

Danny's plugin (Mailchimp for Wordpress) is actively used in 2 million+ sites. Amazing. But WordPress core is on a different level.

### The reach
There's a lot of numbers floating around on the internet on WordPress usage and visitors.

WordPress powers > 35% of the internet. Including some of the very large sites like Playstation, TechCrunch, (international eiditions of) Business Insider and the New York Post.

However, it's very hard to estimate the number of sites (and monthly unique visitors) these sites would have.

> Considering that the number of total active websites is estimated at over 1.3 billion according to a survey published by Netcraft, that means that around 455,000,000 websites are using WordPress right now, which means that around 20% of all self-hosted websites use WordPress, which is still huge.
[Analysis by 'Who is hosting this'.](https://www.whoishostingthis.com/compare/wordpress/stats/)

I think the 455 million active sites they reach in the analysis is a bit overestimated. And if the number is remotely right, a large part of these sites might not have any noteworthy traffic.

[WordPress.com](https://wordpress.com/activity/) gathers data of 409 million unique visitors per month on sites tracked by Jetpack. I know Jetpack is not installed on a lot of sites, so this number is greatly underestimating the number of visitors this change will reach. However, it's the most reliable number I have access to.

409.000.000 people. Not downloading jQuery migrate and downloading a smaller jQuery payload.

### Side notes

- **No CDN's**  
  I assume none of these sites are using a common CDN, so every unique visitor to a unique domain has to download the sites assets.
- **Cache cleared once per month**  
  Even though cache times of up-to one year are recommended for JavaScript files, in practice these times will be shorter, as user clear their cache, servers set shorter cache times, or assets use a cache busting technique (like `?ver=`) which downloads resources on update.

## Final result

As mentioned in ['CO2 emissions on the web'](https://dannyvankooten.com/website-carbon-emissions/) one GB of data transferred consumes around 0.5kWh of energy.

(0.5 kWh / 1.000.000KB) * 7,3KB  = 0,00000365 kWh per non-cached visit.

409.000.000 unique visits * 0,00000365 kwh = 

**1492,85 kwh per month.**

![Someone putting his thumb up in approval](https://media.giphy.com/media/dykJfX4dbM0Vy/giphy.gif)

This is the very lowest estimate I could make. In reality I estimate a multiple of this, as Jetpack runs on just a part of all sites using WordPress.

### Next steps

- [Test the jQuery update early](https://wordpress.org/plugins/wp-jquery-update-test/)
- [You might not need jQuery](http://youmightnotneedjquery.com/) at all.
