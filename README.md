### Brett Miller - Blog
[https://millerb.co.uk](https://millerb.co.uk)

### Theme
I opted for the Hydeout theme, nice and clean and fairly customisable once you get to grips with Jekyll.

[Hydeout Theme](https://github.com/fongandrew/hydeout) by Andrew Fong.

### Customisations
#### Sidebar Links
Added additional links to [/_includes/sidebar-icon-links](/_includes/sidebar-icon-links.html) using the same format. There may have been an easier way to do this.

These needed corresponding .svg files in [/_includes/svg](/_includes/svg) which I got from [Simple Icons](https://simpleicons.org/)

### Config.yml
Variables in the Config.yml can be access via `{{ site.variable }}` so you can use the liquid tags throughout the other files.

e.g. `{{ site.github.repo }}` is used in the nav links to link to my github profile.

### Layout/Design
#### Variables
Alot of the design elements are stored in [/_sass/hydeout/_variables.scss](/_sass/hydeout/_variables.scss) and define colours of text and other elements.

#### Layout
By default the hydeout theme comes with a sticky sidebar setting so you can have the content of your sidebar at the bottom `$sidebar-sticky: true !default;` which is defined in the `_variables.scss` and used in the [/_sass/hydeout/_layout.scss](/_sass/hydeout/_layout.scss).

```scss
/* -----------------------------------------------------------
  Sticky sidebar

  Set $sidebar-stick variable to affix sidebar contents to the
  bottom of the sidebar in tablets and up.
----------------------------------------------------------- */

@if $sidebar-sticky {
  @media (min-width: $large-breakpoint) {
    body {
      align-items: flex-end;
    }
  }
}
```
I added another variable to the variables file `$sidebar-center: true !default;` and changed the css layout to align center

```scss
/* -----------------------------------------------------------
  Center sidebar

  Set $sidebar-center variable to center sidebar contents
  in tablets and up.
----------------------------------------------------------- */

@if $sidebar-center {
  @media (min-width: $large-breakpoint) {
    body {
      align-items: center;
    }
  }
}
```
This looked neater to me on desktop/laptop screens and didn't have much bearing on mobile layout.

#### Fonts
There is a section in [/_sass/hydeout/_layout.scss](/_sass/hydeout/_layout.scss) which defines the font used for the Site Title.

Changing the `.site-title` font-family to `montserrat, sans-serif`

```scss
#sidebar {
  flex: 0 0 auto;
  padding: $section-spacing;

  .site-title {
    font-family: "Montserrat", sans-serif;
    font-weight: normal;
    font-size: $large-font-size;
    text-align: center;
    color: rgba(255,255,255,.75);
    margin-top: 0;
    margin-bottom: $heading-spacing;

    a {
      color: inherit;
      &:hover { color: $sidebar-title-color; }
    }

    .back-arrow { margin-right: 0.5rem; }
  }
}
```
You also have to change the setting in [/_includes/font-includes.html](/_includes/font-includes.html)

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Montserrat" />
```
#### Sidebar
Aligning the site-title is in the same place as the font settings [/_sass/hydeout/_layout.scss](/_sass/hydeout/_layout.scss) then algining the rest of the content centrally had to be done for each element in the CSS

```scss
#sidebar {
    .site-title {
      font-size: 3.25rem;
      text-align: center;

      a { color: $sidebar-title-color; }
      .back-arrow { display: none; }
    }

    p.lead, header ~ * {
      display: block;
    }

    header ~ nav {
      display: flex;
    }
  }
```