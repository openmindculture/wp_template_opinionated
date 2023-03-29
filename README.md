# WordPress Template (opinionated, pure PostCSS Version)

⚠️ **INCOMPATIBLE WITH WORDPRESS CODE STYLE!**

`wp_template_opionated` is a classic theme starter boilerplate for a standalone theme focused on modern coding standards, performance, and accessibility, using future CSS instead of SCSS.

⚠️ This template is NOT suitable for developing themes or plugins that pass the official WordPress review process!

### TODO / Work in Progress

Currently, this is still a Twenty-Twenty-Three child theme wihout many of the features and changes already mentioned in the documentation.

## Development Setup

[wp_template_opionated](https://github.com/openmindculture/wp_template_opinionated)
 is a simple local host WordPress setup using Docker, docker-compose, wp-cli, and PostCSS to install, develop, and test themes and plugins. This template can help you build a classic (child) theme from scratch (without exporting from the block editor). We can still support and provide custom blocks or block templates in our theme.

The template markup has been inspired by [GeneratePress](https://generatepress.com/), [Fasto](https://wowlayers.com/wordpress-theme-fasto/), and [Twenty Twenty-Three ](https://wordpress.org/themes/twentytwentythree/).

You can use this template to write and emit modern, minimalist, and maintainable code following official core language standards and recommendations as closely as possible, striving to emit an accessible, semantic, quick, and cacheable frontend, avoiding controversial full-site-editing and theme engine features without completely giving up the increased versatility and preview features beneficial to non-technical site owners.

Technically speaking, his theme provides its own templates and prefers server-side PHP to client-side JavaScript to reduce the negative effects of Gutenberg block editor, trying to prevent inline style attributes and unnecessary client-side rendering, to emit complete documents that can be cached using reverse proxy servers and/or caching / performance plugins.

This repository has been forked from [wp_template](https://github.com/openmindculture/wp_template) which was forked from [wp_cli_docker](https://github.com/openmindculture/wp_cli_docker).

## Usage

- `npm install` initialize and start the development environment
- `npm start` should start up WordPress on http://localhost:8765/
- `npm run build` rebuilds and exports the theme's CSS and PHP files
- `npm run watch` adds file watcher to automatically rebuild the theme.
- `npm stop` stops the server without destroying data and configuration.
- `npm run destroy` removes the installation and its data.

## Requirements

- npm
- Docker
- docker-compose

## Configuration

### Set Theme name and Target Directory

- Create a subdirectory below `themes` matching your theme name.
- Open `package.json` and edit the build targets accordingly to make sure all necessary files will be exported.
- Edit your `style.css` and `theme.json` to match name, author, version etc.

### Modify localhost Port and Domain

The server listens on port 8765 by default, so you can enter http://localhost:8765/ to open WordPress in your browser. Modifying the port not only allows you to run multiple local projects at the same time, but it can also prevent browser caching issues between different local WordPress servers and ensure that cross-domain resources, paid plugins or fonts etc. will be displayed correctly.

You can also set a local domain name that matches your future production server, by adding the domain as a localhost alias in `/etc/hosts` and configuring WordPress to use that domain. A port set to `80` can be omitted in the url, so http://example.com:80/ is equivalent to http://example.com/ and might facilitate testing even more.

Edit the port mapping for the `wordpress:` service in `docker-compose.yml` and change the first value to whatever port you want to use in your browser.

Edit the `installCore` function in `install-local-environment.js` and change `--url=http://localhost:8765"` to the appropriate URL.

Edit `/etc/hosts` and add the local domain alias:

```
127.0.0.1	localhost example.com www.example.com
```

Add an offloader, load balancer, or reverse proxy to add advanced server functionality like https.

### Configure pre-installed Themes and Plugins

Modify [install-local-environment.js](./install-local-environment.js) to select which themes and plugins will be installed automatically using `wp-cli`. You must specify the technical names (text domains), not the current titles! The technical names are the same as the directory names in the plugin paths.

```js
/* specify the technical names (text domain) of plugins to be installed */
'wp plugin install incompatibility-status updraftplus --activate';
```

Some commercial / paid plugins cannot be installed automatically. They have to be uploaded or installed manually later.

### Configure WordPress Core, Web Server, PHP Version used

Modify [docker/WordPress.Dockerfile](./docker/WordPress.Dockerfile) to choose one of various predefined configurations using different PHP versions like 7.4, 8.0, 8.1 etc. and popular web servers like Apache or nginx to copy our customer's web hosting provider's technical setup as good as possible.

See https://hub.docker.com/_/wordpress/ for available docker tags, or keep `wordpress:latest` for the newest (latest) stable release.

```Dockerfile
FROM wordpress:latest
# use other tags in docker/wordpress.Dockerfile to test specific versions, see
# https://hub.docker.com/_/wordpress/
# FROM wordpress:6.1.1-php8.0-apache
```

## Custom Content, Block Patterns, and Optional Must-Use-Plugin

- The theme in a subdirectory of `/themes` will be rebuilt when `/src` files have changed.
- Use CSS and write verbose code in `/src`.
	- You can use some non-standard / future CSS syntax using [PostCSS](https://postcss.org)) and its plugins for extends, import, and nesting, etc.
	- (TODO: add PHP configuration to remove unnecessary bloat and cargo code like wp-emoji or jQuery)
- Add or configure other build tools, like minifying or transpiling if needed.
- Create a zip archive of that subdirectory to export the theme to another WordPress installation.

If we need to define custom data structures, we can use the popular [ACF (Advanced Custom Fields) plugin](https://wordpress.org/plugins/advanced-custom-fields/) to provide custom fields, and use our own minimal plugin to define custom post types. This should not be part of the theme, to prevent data loss when a theme is changed or deactivated. Using a must-use plugin ensures that the code will always be active. Custom fields defined using the ACF user interface in WordPress admin can be exported to JSON or PHP, so we can add it to our custom fields plugin as well.

To persist other important content like example posts or pages, use the default WordPress exporter to save an XML export as `content.xml`, which will automatically be imported when setting up the local environment using `npm install`. The data might have to be installed manually when setting up a production environment.

For the sake of simplicity, our example setup uses a single-file plugin and puts all styles directly into a single `style.css` file without using further `theme.css` or `theme.json` files, which we might want to use depending on the requirements for customizability. 

### PostCSS Configuration and optional Babel Setup

#### Future CSS and Inofficial Extension: Nesting, Variables, Extends, and Imports

PostCSS and Stylelint have been configured to use the latest official CSS features in our source code, plus

- Custom Properties (CSS Variables — `@var`) will be replaced with static values wherever possible.
- `@import` content will be inlined into the importing file.
- Nesting can be used according to the 2023 [CSS Nesting Module Working Draft](https://www.w3.org/TR/css-nesting-1/).
- `@extend` at-rules can be used according to the [speculative CSS extend rules specification](https://jonneal.dev/specs/css-extend-rule/), but limited to simple use-cases in its [current PostCSS implementation](https://github.com/csstools/postcss-extend-rule).

#### TypeScript / (Future) ECMAScript

TypeScript / ECMAScript transpilation can also be set up (**TODO**). In the current state of this template, any JavaScript code will be exportet exactly as it has been written, as the original example was focused on CSS development.

## Inspection

To facilitate debugging, `plugins` is mounted as a local directory, so you can search files and view error messages and annotations, to collect details for filing issues or for creating patches yourself.

You can enter the Docker container and use it much like a remote server.

Type

`docker exec -it wordpress_setup_test_wordpress_1 bash`

Replace `wp_cli_docker_wordpress_1` with the appropriate name found in `docker ps`, if necessary.

Inside the docker container, you will find WordPress in `/var/www/html`.

## Stop, Clean Up, Uninstall

- stop the current server by exiting the process (by pressing `Ctrl C` or your operating system's break key), or type
- `npm stop`

After stopping the server, the data will still be ready for another session and for inspection and backup of plugin files.

Use

`npm run destroy`

to remove the installation.

## Development

In JetBrains IDEA (PhpStorm, WebStorm, etc.) enable WordPress support and set `wp_data` as WordPress path, so that the local code inspections like SonarLint can recognize the built-in functions. You can still mark the directory as excluded to avoid unnecessary indexing and search results.

As we operate in a global namespace shared with other plugins, it is considered best practice to use a unique prefix for identifiers used for `function`, `class`, and `define`.

We might want to use BEM style underscores, which had been disallowed by the classic WordPress style guide, but seem to be used by the Gutenberg team, as discussed here: https://github.com/WordPress/gutenberg/issues/28616#issuecomment-770596122 , which also allow descending specificity as the rule seems to produce too many false positives in modular projects.

CSS class names don't follow a concise system due to the different existing class names used by WordPress core, base blocks, and the GeneratePress parent theme.

Some linter rules have been disabled to fit my personal, opinionated, code style preferences.

If in any doubt, please follow the latest official syntax recommendations for HTML, CSS, ECMAScript, and PHP!

### Editor Styles Specificity

To apply our (child) theme styles in the (Gutenberg) block editor, we have to `add_theme_support` for `editor-styles` and add  our `theme.css` using `add_editor_style` as [described in the block editor handbook](https://developer.wordpress.org/block-editor/how-to-guides/themes/theme-support/). This will wrap our frontend styles in an `.editor-styles-wrapper` which is the modern equivant of the class editor's iframe body.

It is still a good idea to ensure that our child theme styles override parent styles using CSS specificity, as [suggested by GeneratePress support](https://generatepress.com/forums/topic/child-theme-css-precedence/). This makes our theme more robust as it will not have to rely on the loading order.

Note that a `body` selector will be automatically replaced by `.editor-styles-wrapper` which would have been added anyway, so no higher specificity in the backend, and that the semantic landmark elements `main`, `article` and `header` are missing inside the block editor preview, and there is no `.site`  There is an `.editor-styles-wrapper .wp-block-post-content`, but we would not want to add backend-specific selectors to our frontend styles. Using a `body div` wrapper is not the most elegant workaround either.

In case that we only need the markup generated by the parent theme, we could dequeue the parent the style and omit any specificity wrappers altogether.

In future themes, we might want to support the new WordPress style engine and use `theme.json`, but we don't necessarily need to. See [Managing CSS Styles in a WordPress Block Theme by Ganesh Dahal on CSS-Tricks](https://css-tricks.com/managing-css-styles-in-a-wordpress-block-theme/) for more details.

## Block Library

We can provide blocks of our own, that can be composed, reused, and modified by content creators.

## Troubleshooting

- make sure that there is no other local service listening on port 8765 or configure another port.
