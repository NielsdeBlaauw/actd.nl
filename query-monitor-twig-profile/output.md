---
title: Query Monitor Twig Profile output
layout: default
order: 2
permalink: query-monitor-twig-profile/output.html
---
You can find the output of Query Monitor Twig Profile (QMTP) in the 'Twig Profile' tab of 'Query monitor', which is available through the admin bar.

If you are not seeing any output on pages where you might expect it, make sure the [integration](integration) is working correctly.

## The file tree
The file tree shows you which files are being rendered, and explain the hierarchy of your templates. 

The file tree starts with a `main` element, which is the complete Twig environment. It then includes a single child. This child is the root template being rendered by Twig.

Twig has two ways of rendering different templates. Either a template is [extends](https://twig.symfony.com/doc/3.x/tags/extends.html#extends) from a different template and it might [include](https://twig.symfony.com/doc/3.x/functions/include.html) child templates. Both are rendered as childs in the tree.

The file tree also shows [`blocks`](https://twig.symfony.com/doc/3.x/templates.html#template-inheritance). These are also shown as children. They are identified with `::block_name` behind a filename.

## Time measurements
Every entry in the file tree shows a time measurement in milliseconds. This time is considered 'Inclusive cost'. It is cumulative of it's own execution time, and the sum of its children. Going down the tree, the time spent in a template always gets smaller.

Typically rendering templates should be *fast*. Rendering a very template take less than a millisecond. Rendering the entire page (the root element) should not take more than a second.

## The hot path
You can see the time it took a (sub)-template to render in ms, as percentage of its parent, and as a percentage of the total time spend on rendering the entire page.

The percentage spent as part of its parent and the percentage of total time spent are color coded. Red means a big part of rendering the template is happing here, and it should get most of your attention. Orange parts are important, but less than red. Black and muted colors are probably not causes for any performance issues on the page.

| Color  | Percentage of parent | Percentage of total | Text properties |
|--------|----------------------|---------------------|-----------------|
| Red    | >33%                 | >33%                | Block, italic   |
| Orange | >20%                 | >20%                | Italic          |
| Black  | 10%-20%              | 10%-20%             | Regular         |
| Grey   | <10%                 | <10%                | Muted           |

## Common causes for slowdowns
If you are seeing slowdown in particulair templates, you can check them for the following:
1. Network calls, such as retrieving information from an API.
2. Calls to non-performant functions.
3. For loops over very large arrays.

## Next steps
You can now start fixing performance issues on your site. Remember to use the `saved profiles` feature to compare performance before and after your changes.