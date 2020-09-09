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

### Systemic vs 'accidental' issues

### Server or client side

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
