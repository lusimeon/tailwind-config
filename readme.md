# Tailwind config

[![NPM Version](https://img.shields.io/npm/v/@studiometa/tailwind-config.svg?style=flat-square)](https://www.npmjs.com/package/@studiometa/tailwind-config)
[![Dependency Status](https://img.shields.io/david/studiometa/tailwind-config.svg?label=deps&style=flat-square)](https://david-dm.org/studiometa/tailwind-config)
[![devDependency Status](https://img.shields.io/david/dev/studiometa/tailwind-config.svg?label=devDeps&style=flat-square)](https://david-dm.org/studiometa/tailwind-config?type=dev)

> A custom Tailwind CSS configuration.

## Installation

Install the package with NPM or your package manager of choice:

```bash
npm install @studiometa/tailwind-config --save-dev
```

## Usage

Use the package in your project's `tailwind.config.js` file. 

```js
// As is with no custom configuration
module.exports = require('@studiometa/tailwind-config');

// By merging the package's configuration with you own custom one
const config = require('@studiometa/tailwind-config');
const merge = require('lodash.merge');

module.exports = merge(config, {
  theme: {
    // ...
  },
});
```

## How to and best practices

### Responsive

Tailwind uses a mobile first breakpoint system. It is recommended to use the utility classes unprefixed to target mobile and override them at larger breakpoints.

```html
<div class="text-center sm:text-left md:text-right"></div>
```

For behaviors related to a **specific breakpoint** it is recommended to undo this behavior by using a utility class that reapplies the basic behavior on a larger breakpoint.

```html
<div class="bg-teal md:bg-red lg:bg-teal"></div>
```

### Custom utility classes

#### First method: Add it in the SCSS files

To manage the import of custom utility classes on Tailwind using a preprocessor you should place them in separate files and import them after Tailwind's utilities imports to avoid specificity issues.

```scss
@tailwind base;
@tailwind components;
@tailwind utilities;
@import "./custom-utilities";
```

To create custom utility classes that can be used with the breakpoints defined in our configuration you should declare these utility classes in a `@responsive` directive.

```scss
@tailwind base;
@tailwind components;
@tailwind utilities;

@responsive {
  @variants hover, focus {
    .rotate-0 {
      transform: rotate(0deg);
    }
    .rotate-90 {
      transform: rotate(90deg);
    }
    .rotate-180 {
      transform: rotate(180deg);
    }
    .rotate-270 {
      transform: rotate(270deg);
    }
  }
}
```

The responsive versions of these utility classes will be automatically generated by Tailwind using the breakpoints entered in the configuration file.

```html
<div class="rotate-0 hover:rotate-90"></div>
```

You can read the documentation on [variants management](https://tailwindcss.com/docs/pseudo-class-variants/) for more detailed informations.

#### Second method: By creating a plugin

We can add custom utilities with plugin. The following example generates the same classes as in the first method:

```
// tailwind.config.js
module.exports = {
  plugins: [
    function({ addUtilities }) {
      const newUtilities = {
        '.rotate-0': {
          transform: 'rotate(0deg)',
        },
        '.rotate-90': {
          transform: 'rotate(90deg)',
        },
        '.rotate-180': {
          transform: 'rotate(180deg)',
        },
        '.rotate-270': {
          transform: 'rotate(270deg)',
        },
      }

      addUtilities(newUtilities, ['responsive', 'hover'])
    }
  ]
}
```

Read the [documentation](https://tailwindcss.com/docs/plugins/#adding-utilities) for more informations on plugins.

### Directives and Functions

#### [@apply](https://tailwindcss.com/docs/functions-and-directives#apply)

Apply is useful for [component extraction](https://tailwindcss.com/docs/extracting-components/) when a pattern repeats itself. For buttons for example, it allows to centralize the properties of a component.

These declarations can be used alongside classic CSS declarations.

```scss
.btn {
  @apply font-bold py-2 px-4 rounded;
}
.btn-blue {
  @apply bg-blue-500 text-white;
}
.btn-blue:hover {
  @apply bg-blue-700;
  transform: translateY(-1px);
}
```

It is necessary to use interpolation to add `!important` to styles applied via the directive `@apply`:

```scss
.btn {
  @apply font-bold py-2 px-4 rounded #{!important};
}
```

The `@apply` directive cannot be used for statements about elements states or elemenrs responsiveness. Example of **what you can't do:**

```scss
.btn {
  @apply hover:bg-blue-500;
  @apply md:inline-block;
}
```

#### [@variants](https://tailwindcss.com/docs/functions-and-directives/#variants)

It is possible to generate states utilities for our own utility classes by wrapping them in an `@variants` directive.

```scss
@variants focus, hover {
  .rotate-0 {
    transform: rotate(0deg);
  }
  .rotate-90 {
    transform: rotate(90deg);
  }
}
```

This will generate the following CSS: 

```css
.rotate-0 {
  transform: rotate(0deg);
}
.rotate-90 {
  transform: rotate(90deg);
}

.focus\:rotate-0:focus {
  transform: rotate(0deg);
}
.focus\:rotate-90:focus {
  transform: rotate(90deg);
}

.hover\:rotate-0:hover {
  transform: rotate(0deg);
}
.hover\:rotate-90:hover {
  transform: rotate(90deg);
}
```

**It is important to keep in mind that state variations will be generated in the order in which they are declared.**

Finally, it is possible to generate [customized variations](https://tailwindcss.com/docs/plugins/#adding-variants) to meet more specific needs.

#### [@responsive](https://tailwindcss.com/docs/functions-and-directives#responsive)

It is possible to generate utility classes using the breakpoints defined in the configuration file with the `@responsive` directive.

```scss
@responsive {
  .bg-gradient-brand {
    background-image: linear-gradient(blue, green);
  }
}
```

This will generate the following CSS: 

```css
.bg-gradient-brand {
  background-image: linear-gradient(blue, green);
}

/* ... */

@media (min-width: 640px) {
  .sm\:bg-gradient-brand {
    background-image: linear-gradient(blue, green);
  }
  /* ... */
}

@media  (min-width: 768px) {
  .md\:bg-gradient-brand {
    background-image: linear-gradient(blue, green);
  }
  /* ... */
}

/*... */
```

#### [@screen](https://tailwindcss.com/docs/functions-and-directives/#screen)

Instead of writing media queries with pixel values it is possible to use the `@screen` directive and refer to a breakpoint by its name. If we have a breakpoint **sm** at **640px** it's possible to use it in the following way:

```scss
@screen sm {
  /* ... */
}
```

#### [theme()](https://tailwindcss.com/docs/functions-and-directives/#theme)

The `theme()` function allows us to access the values entered in our configuration file.

```scss
.content-area {
  height: calc(100vh - theme('spacing.12'));
}

.btn-blue {
  background-color: theme('colors.blue.500');
}
```
