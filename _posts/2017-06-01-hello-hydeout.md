---
layout: post
title: Test Post
excerpt_separator:  <!--more-->
---

Adding my first Test Post from the original to save having to create a new file as I am being glared at by the wife.

### Customization

Hydeout replaces Hyde's class-based theming with the use
of the following SASS variables:

```scss
$sidebar-bg-color: #202020 !default;
$sidebar-sticky: true !default;
$layout-reverse: false !default;
$link-color: #268bd2 !default;
```

To override these variables, create your own `assets/css/main.scss` file.
Define your own variables, then import in Hydeout's SCSS, like so:

```
---
# Jekyll needs front matter for SCSS files
---

$sidebar-bg-color: #ac4142;
$link-color: #ac4142;
$sidebar-sticky: false;
@import "hydeout";
```

See the [_variables](_sass/hydeout/_variables.scss) file for other variables
you can override.

You can also insert custom head tags (e.g. to load your own stylesheets) by
defining your own `_includes/custom-head.html` or insert tags at the end
of the body (e.g. for custom JS) by defining your own
`_includes/custom-foot.html`.

### New Features

* Hydeout also adds a new tags page (accessible in the sidebar) and a new
  "category" layout for dedicated category pages.

* Category pages are automatically added to the sidebar. All other pages
  must have `sidebar_link: true` in their front matter to show up in
  the sidebar.

* A simple redirect-to-Google search is available. If you want to use
  Google Custom Search or Algolia or something with more involved,
  override the `search.html`.

* Disqus integration is ready out of the box. Just add the following to
  your config file:

  ```yaml
  disqus:
    shortname: my-disqus-shortname
  ```

  If you don't want Disqus or want to use something else, override
  `comments.html`.

* For Google Analytics support, define a `google_analytics` variable with
  your property ID in your config file.

There's also a bunch of minor tweaks and adjustments throughout the
theme. Hope this works for you!
