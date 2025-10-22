# Uniweb Framework

## A Modern Approach to Web Development Through Separation of Concerns

The Uniweb Framework transforms how websites are built by creating a clean separation between content and code. This separation enables content teams and developers to work independently, each focusing on their areas of expertise without blocking each other.

## Usage Methods

This library can be used in three ways:

1. As a CLI tool
2. As a Node.js module
3. As a webpack plugin

### The Uniweb CLI Tool

The Framework includes a powerful command-line interface (CLI) that simplifies many common tasks:

```bash
# Install the Uniweb CLI tool globally
npm install -g @uniwebcms/framework
```

Installing the CLI tool globally offers several advantages:

- Access Uniweb commands from any directory
- Manage content across multiple projects
- Automate common workflows
- Maintain consistency across languages and sites

The CLI tool automatically detects your Uniweb project context, making it aware of the current site, page, or component you're working with.

Start using Uniweb with these basic CLI commands:

#### Create a minimal project

```bash
uniweb init my-project
```

This creates a barebones project with just the minimum requirements. After creation, you can navigate to it with `cd my-project` and run additional commands to add sites or modules.

#### Create a content-focused project

```bash
uniweb init my-project --site /
```

This creates a project with a root-level site, which is the basic setup for a content-focused project with the structure described earlier in this guide.

#### Create a development-focused project

```bash
uniweb init my-project --module M1 --interface marketing --site test
```

This creates a project with:

- A purpose-built Foundation under `src/M1` (where developers will create reusable components)
- A starter template of components based on the `marketing` [library interface](https://github.com/uniwebcms/library-interfaces) (defaults to `marketing/latest/core`)
- A test site under `sites/test` initialized with an index page for testing components

### Node.js Module

Install the library as a regular dependency.

```bash
npm install @uniwebcms/framework
```

Then, use it in your code.

```javascript
import { collectSiteContent } from "@uniwebcms/framework";

async function processWebsite() {
  try {
    const content = await collectSiteContent("./website");
    console.log(content);
  } catch (err) {
    console.error("Processing error:", err);
  }
}
```

### Webpack Plugin

The webpack plugin integrates content collection into your build process:

```javascript
import { SiteContentPlugin } from "@uniwebcms/framework/webpack";

export default {
  plugins: [
    new SiteContentPlugin({
      injectToHtml: true, // Optional: inject into HTML (requires html-webpack-plugin)
      variableName: "__SITE_CONTENT__", // Optional: id/variable name when injecting
      filename: "site-content.json", // Optional: output filename
      injectFormat: "json", // Optional: injection format ('json' or 'script')
    }),
  ],
};
```

#### HTML Injection Formats

The plugin supports two formats for injecting content into HTML:

1. JSON format (default):

```html
<script type="application/json" id="__SITE_CONTENT__">
  {
    "pages": {
      /* content */
    }
  }
</script>
```

Access in your code:

```javascript
const content = JSON.parse(
  document.getElementById("__SITE_CONTENT__").textContent
);
```

2. Script format:

```html
<script>
  window.__SITE_CONTENT__ = {
    /* content */
  };
</script>
```

Access in your code:

```javascript
const content = window.__SITE_CONTENT__;
```

## Error Handling

The library handles several types of errors:

- Missing parent sections for subsections
- Malformed YAML, JSON, or Markdown content
- Invalid file structure or naming
- Missing required files

Errors are collected in the `errors` array of the output, allowing processing to continue even when some files fail.

## Requirements

- Node.js >=18.0.0

## License

GPL-3.0-or-later - see LICENSE for details
