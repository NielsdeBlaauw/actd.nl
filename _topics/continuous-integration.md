---
title: Continuous Integration
layout: topic
order: 1
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
