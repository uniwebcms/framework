# Uniweb Plugin System

Plugins extend the Uniweb build process with asset optimization, content transformations, and custom build configurations. Unlike plugins in traditional CMS platforms, Uniweb plugins are strictly **build-time extensions**—they process content and assets during development and builds, but don't affect runtime behavior.

## Philosophy

**Build-time focus.** Plugins maintain the separation of concerns that defines Uniweb's architecture. They enhance the build process without affecting how sites and Foundations work at runtime. Runtime functionality comes from Foundation components, not plugins.

**Why this matters:**

- Clear boundary between build tools and runtime behavior
- Prevents plugin conflicts and complexity at runtime
- Keeps the runtime lean and focused
- Ensures plugins can't break the content/code separation

## Plugin Types

### Asset Optimization Plugins

Transform and optimize static assets during the build process.

**Common use cases:**

- Image compression and format conversion (PNG → WebP)
- CSS minification and optimization
- JavaScript minification and tree-shaking
- SVG optimization
- Font subsetting

**Example configuration:**

```js
// uniweb.config.js
export default {
  plugins: [
    "@uniweb/plugin-image-optimizer",
    {
      name: "@uniweb/plugin-css-optimizer",
      options: {
        minify: true,
        removeUnusedCSS: true,
      },
    },
  ],
};
```

### Content Transformation Plugins

Modify or enhance content during processing.

**Common use cases:**

- Markdown extensions (custom syntax, special blocks)
- Automatic content translation via AI
- Metadata extraction and enrichment
- Content indexing for search
- Syntax highlighting for code blocks
- Link checking and validation

**Example:**

```js
// Custom markdown extension plugin
export default {
  plugins: [
    {
      name: "custom-markdown-blocks",
      type: "content",
      transform: (markdown, context) => {
        // Transform markdown before parsing
        return markdown.replace(/::callout/g, '<div class="callout">');
      },
    },
  ],
};
```

### Build Customization Plugins

Extend or modify the build configuration itself.

**Common use cases:**

- Custom webpack configuration
- Additional Babel plugins
- PostCSS configuration
- Development server middleware
- Environment-specific builds

**Example:**

```js
// Add custom webpack configuration
export default {
  plugins: [
    {
      name: "custom-webpack",
      type: "build",
      webpack: (config, context) => {
        // Modify webpack config
        config.resolve.alias["@components"] = path.resolve(
          __dirname,
          "src/components"
        );
        return config;
      },
    },
  ],
};
```

### CLI Extension Plugins

Add new commands or enhance existing CLI functionality.

**Common use cases:**

- Custom deployment scripts
- Content generation utilities
- Analysis and reporting tools
- Integration with external services
- Workflow automation

**Example:**

```js
// Add a custom CLI command
export default {
  plugins: [
    {
      name: "custom-deploy",
      type: "cli",
      commands: {
        deploy: {
          description: "Deploy site to custom hosting",
          action: async (args, context) => {
            // Custom deployment logic
            console.log("Deploying to custom hosting...");
          },
        },
      },
    },
  ],
};
```

## Plugin Configuration

### Basic Configuration

Plugins are configured in `uniweb.config.js` at the project root:

```js
// uniweb.config.js
export default {
  plugins: [
    // Simple plugin - uses defaults
    "@uniweb/plugin-image-optimizer",

    // Plugin with options
    {
      name: "@uniweb/plugin-markdown-extended",
      options: {
        enableTables: true,
        enableFootnotes: true,
      },
    },

    // Local plugin file
    "./plugins/custom-plugin.js",
  ],
};
```

### Plugin Options

Most plugins accept configuration options:

```js
{
  name: '@uniweb/plugin-image-optimizer',
  options: {
    // Optimization settings
    quality: 80,
    formats: ['webp', 'jpg'],

    // Size variants
    sizes: [400, 800, 1200, 1600],

    // Performance settings
    concurrent: 4,
    cache: true
  }
}
```

### Conditional Plugins

Load plugins conditionally based on environment:

```js
export default {
  plugins: [
    // Always loaded
    "@uniweb/plugin-markdown-extended",

    // Development only
    process.env.NODE_ENV === "development" && {
      name: "@uniweb/plugin-dev-tools",
      options: {
        /* ... */
      },
    },

    // Production only
    process.env.NODE_ENV === "production" && {
      name: "@uniweb/plugin-bundle-analyzer",
      options: {
        /* ... */
      },
    },
  ].filter(Boolean), // Remove falsy values
};
```

## Creating Custom Plugins

### Plugin Structure

A basic plugin exports a function or object:

```js
// plugins/my-plugin.js

// Function-based plugin
export default function myPlugin(options = {}) {
  return {
    name: 'my-plugin',

    // Hook into build lifecycle
    async beforeBuild(context) {
      console.log('Starting build...');
    },

    async afterBuild(context) {
      console.log('Build complete!');
    },

    // Transform content
    async transformContent(content, file, context) {
      // Modify content
      return content.replace(/TODO/g, '⚠️ TODO');
    }
  };
}

// Object-based plugin
export default {
  name: 'my-plugin',
  version: '1.0.0',

  apply(context) {
    // Plugin initialization
  },

  async beforeBuild(context) {
    // Pre-build hooks
  }
};
```

### Plugin Lifecycle Hooks

Plugins can hook into various stages of the build process:

```js
export default {
  name: "lifecycle-example",

  // Initialization
  async init(context) {
    // Called when plugin is loaded
    // Set up plugin state, validate options
  },

  // Pre-build
  async beforeBuild(context) {
    // Called before build starts
    // Clean directories, validate environment
  },

  // Content processing
  async transformContent(content, file, context) {
    // Transform markdown content before parsing
    return modifiedContent;
  },

  async transformAsset(asset, context) {
    // Transform static assets
    return modifiedAsset;
  },

  // Build customization
  async modifyWebpackConfig(config, context) {
    // Modify webpack configuration
    return config;
  },

  // Post-build
  async afterBuild(context) {
    // Called after build completes
    // Generate reports, upload artifacts
  },

  // Cleanup
  async cleanup(context) {
    // Called when build process ends
    // Close connections, clean up resources
  },
};
```

### Plugin Context

The `context` object provides access to project information:

```js
async transformContent(content, file, context) {
  // Project paths
  const { projectRoot, outputDir, cacheDir } = context.paths;

  // Current site/module being processed
  const { site, module } = context;

  // Build configuration
  const { isDevelopment, isProduction } = context.env;

  // Plugin options
  const options = context.getPluginOptions('my-plugin');

  // Utilities
  const { logger, cache } = context.utils;

  logger.info('Processing:', file.path);

  return content;
}
```

### Example: Auto-Translation Plugin

```js
// plugins/auto-translate.js
import { translate } from "./translation-service";

export default function autoTranslatePlugin(options = {}) {
  const { languages = ["fr", "es"], apiKey } = options;

  return {
    name: "auto-translate",

    async afterBuild(context) {
      const { logger } = context.utils;
      const sitePath = context.paths.contentDir;

      logger.info("Starting auto-translation...");

      for (const lang of languages) {
        logger.info(`Translating to ${lang}...`);

        // Get all markdown files
        const files = await context.getContentFiles();

        for (const file of files) {
          // Check if translation exists
          const translationPath = `locales/${lang}/${file.relativePath}`;

          if (await context.fileExists(translationPath)) {
            continue; // Skip if translation exists
          }

          // Translate content
          const content = await context.readFile(file.path);
          const translated = await translate(content, lang, { apiKey });

          // Write translation
          await context.writeFile(translationPath, translated);
          logger.info(`Created: ${translationPath}`);
        }
      }

      logger.success("Auto-translation complete!");
    },
  };
}
```

Usage:

```js
// uniweb.config.js
export default {
  plugins: [
    {
      name: "./plugins/auto-translate.js",
      options: {
        languages: ["fr", "es", "de"],
        apiKey: process.env.TRANSLATION_API_KEY,
      },
    },
  ],
};
```

## Best Practices

### 1. Keep Plugins Focused

Each plugin should do one thing well:

```js
// Good - focused on one task
export default {
  name: 'image-optimizer',
  async transformAsset(asset, context) {
    if (asset.type === 'image') {
      return optimizeImage(asset);
    }
    return asset;
  }
};

// Avoid - too many responsibilities
export default {
  name: 'mega-plugin',
  async transformAsset(asset) {
    // Optimizes images
    // Minifies CSS
    // Bundles JavaScript
    // Uploads to CDN
    // ... (too much in one plugin)
  }
};
```

### 2. Use Caching for Expensive Operations

```js
export default {
  name: "expensive-transform",

  async transformContent(content, file, context) {
    const { cache } = context.utils;
    const cacheKey = `transform-${file.path}-${file.hash}`;

    // Check cache first
    const cached = await cache.get(cacheKey);
    if (cached) return cached;

    // Perform expensive operation
    const result = await expensiveTransform(content);

    // Cache the result
    await cache.set(cacheKey, result);

    return result;
  },
};
```

### 3. Provide Clear Logging

```js
export default {
  name: "my-plugin",

  async beforeBuild(context) {
    const { logger } = context.utils;

    logger.info("Starting my-plugin...");
    logger.debug("Options:", this.options);

    try {
      await doSomething();
      logger.success("Operation completed!");
    } catch (error) {
      logger.error("Operation failed:", error.message);
      throw error;
    }
  },
};
```

### 4. Handle Errors Gracefully

```js
export default {
  name: "resilient-plugin",

  async transformContent(content, file, context) {
    const { logger } = context.utils;

    try {
      return await transform(content);
    } catch (error) {
      logger.warn(`Failed to transform ${file.path}:`, error.message);

      // Decide whether to fail or continue
      if (this.options.strict) {
        throw error; // Fail the build
      } else {
        return content; // Return original content
      }
    }
  },
};
```

### 5. Document Plugin Options

```js
/**
 * Image Optimization Plugin
 *
 * @param {Object} options
 * @param {number} [options.quality=80] - Image quality (0-100)
 * @param {string[]} [options.formats=['webp','jpg']] - Output formats
 * @param {number[]} [options.sizes] - Size variants to generate
 * @param {boolean} [options.cache=true] - Enable caching
 *
 * @example
 * plugins: [{
 *   name: '@uniweb/plugin-image-optimizer',
 *   options: {
 *     quality: 85,
 *     formats: ['webp', 'avif'],
 *     sizes: [400, 800, 1200]
 *   }
 * }]
 */
export default function imageOptimizer(options = {}) {
  const config = {
    quality: 80,
    formats: ["webp", "jpg"],
    cache: true,
    ...options,
  };

  return {
    name: "image-optimizer",
    // ... plugin implementation
  };
}
```

## Plugin Development Resources

### Testing Plugins

```js
// __tests__/my-plugin.test.js
import { describe, it, expect } from "vitest";
import myPlugin from "../plugins/my-plugin";

describe("myPlugin", () => {
  it("should transform content correctly", async () => {
    const plugin = myPlugin({
      /* options */
    });
    const content = "# Test";
    const file = { path: "test.md" };
    const context = createMockContext();

    const result = await plugin.transformContent(content, file, context);

    expect(result).toBe("# Test ✓");
  });
});
```

### Debugging Plugins

Enable debug mode to see detailed plugin logs:

```bash
DEBUG=uniweb:plugins npx uniweb start
```

Or in your plugin:

```js
export default {
  name: "debug-example",

  async transformContent(content, file, context) {
    if (process.env.DEBUG) {
      console.log("Input:", content);
      console.log("File:", file);
      console.log("Context:", context);
    }

    return content;
  },
};
```

## Official Plugins

Uniweb provides several official plugins:

- **@uniweb/plugin-image-optimizer** - Image compression and format conversion
- **@uniweb/plugin-markdown-extended** - Extended markdown syntax
- **@uniweb/plugin-analytics** - Build-time analytics integration
- **@uniweb/plugin-sitemap** - Automatic sitemap generation
- **@uniweb/plugin-rss** - RSS feed generation

See the [Plugin Registry](https://plugins.framework.uniweb.app) for the complete list.

## Related Documentation

- [Understanding Uniweb](../understanding-uniweb.md) - Core architecture concepts
- [Developers Guide](../developers-guide.md) - Building Foundations
- [Terminology Reference](terminology.md) - Key terms and definitions

## Contributing Plugins

To contribute a plugin to the official registry:

1. Follow the plugin structure guidelines
2. Include comprehensive tests
3. Document all options and examples
4. Submit to the [plugin registry repository](https://github.com/uniwebcms/plugins)

For community plugins, publish to npm with the `uniweb-plugin` keyword.
