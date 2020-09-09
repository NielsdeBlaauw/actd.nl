---
author: Niels de Blaauw
title: Optimisation workflow for WordPress
tags: [
    'WordPress', 
    'performance',
    'workflow',
    'engineering',
    'cache'
]
---

## Introduction
Is your website slow? Does it use a lot of resources to do what others seem to handle effortlessly? Don't know where to start fixing it?

Optimising a website is a creative exercise, but it also requires a process to identify issues. I will outline the steps I use to identify, analyse and fix performance issues.

The examples in this article will be geared towards WordPress and the specific stack I'm used to. The methods used will be applicable in any technology stack.

Remember: *[premature optimisation is the root of all evil](https://stackify.com/premature-optimization-evil/)*. Identification and analysis is an important step. You should not be 'fixing' any code before you are sure it's causing performance issues.

### Why
The performance and efficiency of a website is important. 

An optimized website improves conversion, lowers hosting costs, provides a great user experience and is good for the environment.

The opposite happens on a non-optimised website. Conversion rates go down, you are adding and scaling servers constantly and probably waste CPU time and energy on efficient calculations.

## The starting point
To get started with identifying performance issues you need the following:

1. **A way to trigger the performance issue.**  
  This might be easier said than done. So this is what we are going to explore.
2. **An environment where the performance issue occurs.**  
  The performance issue might not occur in all environments. Ideally you would always reproduce the situation locally, but this might not always be possible. 

### Triggers
A trigger can be a lot of things:
- Requesting a page URL.
- Saving a page.
- Running a cron.
- Making an API call.
- etc.

The first objective is to find a trigger that is as specific as possible.  
Like in regular debugging, we are limiting the scope of things we are looking at. Through [deductive reasoning](https://en.wikipedia.org/wiki/Deductive_reasoning) we narrow the trigger down. 

This might look something like the following:

1. *Someone notices trigger X on the site is slow.*  
    - Example Questions:
        - Is every trigger slow? or just some? 
        - Is it slow all the time, or just in peak hours/specific times?
    - Techniques:  
        - Timing information for pages from analytics.  
          In Google Analytics this can be found in
      Behaviour -> Site Speed -> Page Timings.
        - Timing information of the cron.  
          WP-CLI outputs this, and it should be logged somewhere.
        - Trial and error.  
          Simply try different triggers under different circumstances, to try, finding patterns when this happens and when it does not. Keep a list of good/bad triggers.
2. *You manage to bring it down to only certain triggers.*
    - Example questions:  
        - What are the shared elements between these triggers?
        - Does removing certain elements from a trigger speed it up?
    - Tools:
        - Commonality analysis  
          Items unique to certain triggers are eliminated as causes. You can put elements common to all triggers on a shortlist to explore later. 
          <div><a href="https://www.researchgate.net/figure/Illustrating-Commonality-Analysis_fig1_238605329"><img width="300" src="https://www.researchgate.net/profile/Robert_Capraro/publication/238605329/figure/fig1/AS:669410532548621@1536611316685/Illustrating-Commonality-Analysis.png" alt="Illustrating Commonality Analysis."/></a></div>
        - Trial and Error  
          Try modifying the trigger to exclude some elements. This might be editing a page to only include or exclude some Gutenberg blocks, or removing a certain parameter from a POST request in curl.
3. Keep asking yourself questions about the trigger until you narrow it down to be as small of a case you can get.  
  How do you know if your trigger is specific enough to continue? 

### Common vs special causes
Your trigger is 'perfect' when you can explain the slowness of a situation purely on the trigger and no external elements cause unexpected results.

In static process control, a distinction is made on common and special causes. 

Common causes explain the normal variation and should produce a repeatable distribution over time.

Special causes can always be assigned a source of the variation. An external influence is affecting some outcomes.

In determining the perfect trigger, you want to eliminate all special causes for delay.

Example: **Element Z is expected to cause the issue.**
- A trigger with element Z always results in a page load of ~8.0 seconds.
- A trigger with element X always results in a page load of ~0.5 seconds.
- A trigger with element Y always results in a page load of ~0.5 seconds.
- A trigger with elements XYZ always results in a page load of ~8.0 seconds
- A trigger with element XY **sometimes** results in a **page load of ~8.0 seconds**.

The above analysis shows that there is an unexplained factor in the XY combination elements.

### Everything is slow, all the time
The process is probably resource constrained. This causes everything to be queued. This is a very hard to debug situation.

Resource constraints appear in several forms:
- 100% CPU usage.
- Some processes take long, causing workers in a pool to fill up.

Use ['theory of constraints' analysis](https://www.leanproduction.com/theory-of-constraints.html) to identify which parts of a program are using the most of available resources. 

These problems often only occur in production environments.

### Server or client side
Anything happening outside of the browser is considered a server side issue. This includes crons, API calls, command line calls, etc.

In the browser, the issue might be a slow frontend (javascript slowing down the page load, or assets in the critical path that are loading slowly), or a slow server response.

To determine if the server is slow, open the network tab of your browser and check out the server response time. Anything >1.5 seconds is worth exploring further.

To identify frontend issues: use the [time to interactive](https://web.dev/interactive/) and [First contentful Paint](https://web.dev/first-contentful-paint/) indicators. Subtract the server response time. Any time remaining I would consider it to be caused by 'frontend'. If this is >1.5 seconds, consider exploring the frontend performance further.

## Digging deeper
We should now have a good idea where and when the performance issue is occuring. It's time to explore why the issue occurs. 

There are a lot of tools available to help identify bottlenecks. The type of issue we are investigating, and the environment we have available may limit the effective toolset.

### Backend issue identification

### Frontend issue identification

## Fixing
At this time we should have identified a probable performance issue. Let's start fixing it! This might be as easy as disabling/replacing a plugin, or rewriting entire data structures.

Just like regular coding, there is not one solution to fixing these issues. Instead there are some common patterns and best practices you can use as a starting point, and have to get creative from there. 

### Establishing a performance baseline

### Common techniques
