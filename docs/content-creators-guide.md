# Uniweb for Content Creators: A Practical Guide

This guide helps content creators build sophisticated websites without writing code. You'll learn how to create, organize, and configure content effectively within the Uniweb framework.

**Related documentation:**

- [Understanding Uniweb](understanding-uniweb.md) - Conceptual overview of Uniweb's architecture
- [Uniweb for Developers](developers-guide.md) - Technical guide for building Foundations
- [Terminology Reference](terminology.md) - Key terms and definitions

## Introduction

Uniweb's approach is unique because it completely separates content from code. This means:

- You can focus entirely on content without worrying about technical implementation
- You have the power to update websites without developer involvement
- You can reuse the same content structure across different designs
- Your work won't be blocked by technical changes happening behind the scenes

As a content creator, you'll work with markdown files, configure components through simple YAML settings, and organize pages—all without touching code.

## Getting Started

### Your Workspace at a Glance

```
project-root/
├── pages/                 # Where your content lives
│   ├── index/             # Home page
│   │   ├── hero.md        # Individual content sections
│   │   ├── features.md
│   │   └── page.yml       # Page metadata and section order
│   └── about/             # About page
│       └── ...
└── public/                # Where your images and files live
    └── images/
        └── ...
```

As a content creator, you'll primarily work with:

- **Markdown files (.md)** - Individual sections of content
- **Page files (page.yml)** - Control page metadata and section order
- **Images and assets** - Stored in the public folder

### Basic Content Creation

Creating content in Uniweb is as simple as writing markdown files. Here's a typical section:

```markdown
---
component: HeroSection
layout: centered
background: dark
---

# Welcome to Our Platform

Discover how our innovative solutions can transform your business.

![Hero Image](/images/hero.jpg)

[Get Started](#getting-started){button-primary}
[Learn More](#about){button-secondary}
```

Let's break this down:

1. **Front matter** (between `---` lines) - This tells Uniweb which component to use and how to configure it. Think of it as selecting a template and choosing some options.

2. **Content** - Everything after the front matter is just regular content:
   - `#` creates headings
   - Regular text creates paragraphs
   - `![Text](/images/file.jpg)` adds images
   - `[Text](link)` creates links
   - Adding `{button-primary}` to a link turns it into a styled button

You don't need to understand how components work internally—you just need to know which ones are available and what options they accept.

## Organizing Your Content

### Pages and Sections

In Uniweb, content is organized into pages and sections:

- **Pages** are complete web pages that visitors see (like "Home" or "About Us")
- **Sections** are individual content blocks on those pages (like a hero banner, feature list, or testimonial section)

```
pages/
├── index/                # Home page
│   ├── hero.md           # Hero section
│   ├── features.md       # Features section
│   └── page.yml          # Page metadata and section structure
└── about/                # About page
    ├── team.md           # Team section
    ├── mission.md        # Mission section
    └── page.yml          # Page metadata and section structure
```

The name of each folder becomes the URL path:

- `pages/index/` → Home page (`/`)
- `pages/about/` → About page (`/about`)
- `pages/products/services/` → Services page (`/products/services`)

### Organizing Sections with page.yml

The `sections` property in `page.yml` controls the order and hierarchy of sections:

```yaml
# pages/index/page.yml
title: Home Page
description: Welcome to our site
# Other page metadata...

# Section structure
sections:
  - hero # First section (references hero.md)
  - features # Second section (references features.md)
  - testimonials # Third section (references testimonials.md)
```

To create parent-child relationships (like tabbed content), use indentation:

```yaml
# pages/about/page.yml
title: About Us
description: Learn about our company

# Section structure with hierarchy
sections:
  - hero
  - team
    - member1  # Child of team
    - member2  # Child of team
  - history
```

If you add a new section file, remember to add it to the `sections` list in `page.yml` to make it appear on the page.

## Working with Components

Components are the building blocks that determine how your content is presented. Think of them as templates or layouts that give your content structure and style. In Uniweb, these components come from **Foundations**—purpose-built design systems created by developers specifically for your content needs.

> **What's a Foundation?** A Foundation is a collection of React components designed to work together as a cohesive design system. Your site uses one Foundation that provides all the components you can reference in your content. See [Understanding Uniweb](understanding-uniweb.md) for more details.

### Discovering Available Components

To see which components are available in your project:

```bash
# List all components in the current Foundation
npx uniweb component list
```

This command shows:

- Component names (what you'll use in your front matter)
- Descriptions (what each component does)
- Available parameters (options you can configure)

### Using Components in Your Content

To use a component, specify it in the front matter of your markdown file:

```yaml
---
component: TeamGrid
---
```

### Configuring Components

Most components have options you can configure:

```yaml
---
component: TeamGrid
layout: cards # How team members are displayed
columns: 3 # Number of columns in the grid
showDepartment: true # Whether to show department labels
---
```

Don't worry about memorizing all possible options. You can always check:

```bash
# Get detailed information about a specific component
npx uniweb component info TeamGrid
```

### Using Component Presets

Many components offer presets—pre-configured sets of options:

```yaml
---
component: FeatureShowcase
preset: compact # Uses a predefined configuration
---
```

To see available presets for a component:

```bash
npx uniweb component presets FeatureShowcase
```

Presets are a great way to use professionally designed configurations without having to specify all the individual settings.

## Creating Effective Content

### Structuring Your Content

Uniweb components expect content to be structured in a certain way. The most common pattern is:

```markdown
# Main Title

Introduction paragraph goes here.

## First Group Item

Content for the first item.

## Second Group Item

Content for the second item.
```

This creates:

- A main title (H1) with an introduction paragraph
- Multiple content groups (H2) that components can display as cards, tabs, accordion items, etc.

Here's how this structure looks when rendered by different components:

- **FeatureShowcase** - Displays each H2 group as a feature card
- **TabContainer** - Turns each H2 group into a separate tab
- **AccordionList** - Makes each H2 group into a collapsible accordion item

The same content structure can look completely different depending on which component you choose!

### Working with Images

Images are added using standard markdown syntax:

```markdown
![Team photo](/images/team.jpg)
```

The path should start with `/` and reference files in your `public` folder. For example, `/images/team.jpg` refers to `public/images/team.jpg` in your project.

You can add special attributes to images:

```markdown
![Background image](/images/background.jpg){background}
![Profile photo](/images/profile.jpg){circle}
```

These attributes tell components how to treat the image (as a background, circular crop, etc.).

### Creating Links and Buttons

Standard links use markdown syntax:

```markdown
[Learn more](/about)
```

To turn links into buttons, add an attribute:

```markdown
[Get Started](/signup){button-primary}
[Learn More](/about){button-secondary}
```

### Lists and Structured Content

Create bulleted and numbered lists using markdown:

```markdown
# Our Services

Here are the services we offer:

- Web Design
- App Development
- Content Creation

## Service Packages

1. Basic Package
2. Premium Package
3. Enterprise Solution
```

Components will automatically format these lists according to their design.

## Translating Your Content

Uniweb makes it easy to create multilingual websites without duplicating your entire site structure.

### How Translations Work

Translations are stored in the `locales` folder, mirroring your main content structure:

```
locales/
└── fr/                  # French translations
    └── pages/
        ├── index/
        │   ├── hero.md  # French version of hero section
        │   └── features.md
        └── about/
            └── team.md
```

The key benefit: You only need to translate the content itself, not the configuration or structure.

### Creating Translations

When creating a translation file, you only need to include the content—not the front matter:

**Original (pages/about/team.md):**

```markdown
---
component: TeamShowcase
layout: grid
columns: 3
---

# Our Team

Our dedicated professionals are committed to excellence.

## Alice Smith

Chief Executive Officer
```

**Translation (locales/fr/pages/about/team.md):**

```markdown
# Notre Équipe

Nos professionnels dévoués sont engagés dans l'excellence.

## Alice Smith

Directrice Générale
```

Notice that the translation file:

- Doesn't include the front matter (component, layout, columns)
- Only contains the translated content
- Keeps the same structure (headings, paragraphs)

### Using the CLI for Translations

The Uniweb CLI makes managing translations easy:

```bash
# Create a French translation for the team section
npx uniweb page section set about/team --locale fr --content "# Notre Équipe\n\nNos professionnels..."
```

This automatically creates the translation file in the correct location and format.

## Using the Uniweb CLI

The Uniweb Command Line Interface (CLI) is a powerful tool that simplifies content management. While you can edit files directly, the CLI helps ensure everything is in the right place and format.

### Common Content Tasks

#### Creating Content

```bash
# Add a new page
npx uniweb page add products

# Add a new section to a page
npx uniweb page section add about/mission

# Set section content
npx uniweb page section set about/mission --content "# Our Mission\n\nWe aim to..."
```

#### Managing Content

```bash
# Update existing section content
npx uniweb page section set about/team --content "# Updated Team Content"

# View current content of a section
npx uniweb page section get about/team

# Copy a section to another page
npx uniweb page section copy about/team contact/leadership
```

#### Working with Translations

```bash
# Add a French translation
npx uniweb page section set about/team --locale fr --content "# Notre Équipe"

# List available translations
npx uniweb locale list
```

### Getting Help

```bash
# See all available commands
npx uniweb help

# Get help for a specific command
npx uniweb help page section add
```

## Practical Examples

Let's look at some common scenarios:

### Creating a New Page with Sections

```bash
# Create the page
npx uniweb page add services

# Add sections
npx uniweb page section add services/overview
npx uniweb page section add services/offerings
npx uniweb page section add services/pricing

# Add content to sections
npx uniweb page section set services/overview --content "# Our Services\n\nWe provide..."
npx uniweb page section set services/offerings --content "# What We Offer\n\n## Service One\n\nDetails..."
npx uniweb page section set services/pricing --content "# Pricing Options\n\n## Basic\n\n$99/month..."
```

Then edit `pages/services/page.yml` to define the page metadata and section order:

```yaml
title: Our Services
description: Services we offer

sections:
  - overview
  - offerings
  - pricing
```

### Updating an Existing Section

```bash
# View current content
npx uniweb page section get about/team

# Update the content
npx uniweb page section set about/team --content "# Our Team\n\nMeet our expanded team of experts..."
```

## Best Practices

1. **Structure content logically** - Use headings to create a clear hierarchy
2. **Be consistent across sections** - Use similar patterns for similar content
3. **Think in terms of content, not presentation** - Focus on what you're saying, not exactly how it will look
4. **Use the appropriate component** - Different components are designed for different content types
5. **Preview changes frequently** - Use the development server to see how your content renders
6. **Leverage presets** - Use component presets for professionally designed layouts
7. **Use the CLI for complex operations** - The CLI helps maintain consistency, especially for translations

## Getting Help

If you're ever unsure about how a component works or what options are available:

```bash
# Get information about a component
npx uniweb component info HeroSection

# List all available components
npx uniweb component list

# List presets for a component
npx uniweb component presets FeatureShowcase
```

These commands provide the information you need without having to search through documentation.

## Next Steps

Now that you understand the basics of content creation in Uniweb, you're ready to start building!

**Continue learning:**

- [Understanding Uniweb](understanding-uniweb.md) - Deeper dive into the architecture
- [Terminology Reference](terminology.md) - Key terms and definitions
- [Deployment Guide](deployment-guide.md) - Publishing your site

**For developers:**

- [Uniweb for Developers](developers-guide.md) - Learn how Foundations are created

**Get support:**

- [Framework Website](https://framework.uniweb.app) - Guides and resources
- [Documentation](https://docs.framework.uniweb.app) - Complete API reference
- [Examples](https://github.com/uniwebcms/examples) - Sample content patterns
