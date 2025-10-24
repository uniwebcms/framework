# Uniweb Framework

A web development framework built on clean separation of concerns. **Content** lives in sites (markdown files or managed via the Uniweb App), while **Foundations** provide the React components that render that content.

This architecture means content teams and developers work independently — content editors compose pages using intuitive components, developers build those components.

## Quick Start

Create a new Uniweb project:

```bash
npx @uniwebcms/framework create my-portfolio --site main --module marketing
```

Start the development server:

```bash
cd my-portfolio
npm install
npx uniweb start
```

Visit `http://localhost:3000/sites/main/` to see your site.

## Core Concepts

### Sites and Foundations

- A **site** is your content – pages, assets, and configuration
- A **Foundation** is a collection of React components designed to work together
- A **module** is how Foundations are packaged and delivered to sites

Each site links to one Foundation (local or remote) that provides all the components it needs.

### Content-Driven Rendering

Content is written in markdown with YAML frontmatter that specifies which component to use:

```markdown
---
component: HeroSection
layout: centered
background: dark
---

# Welcome to Our Platform

Discover innovative solutions for your business.
```

At runtime, the Framework connects your content with the appropriate component from your Foundation.

## Project Types

**Content-Only Project** (uses a remote Foundation):

```bash
npx @uniwebcms/framework create my-site \
  --site / \
  --module https://modules.uniweb.app/username/marketing
```

Ideal for content teams – no local code, just content management.

**Development Project** (build your own Foundation):

```bash
npx @uniwebcms/framework create my-project --site demo --module marketing
```

Creates a local Foundation at `src/marketing` for building custom components, plus a demo site for testing.

**Minimal Project** (add sites/modules as needed):

```bash
npx @uniwebcms/framework create my-project
```

## Key CLI Commands

```bash
# Inside a project
npx uniweb start              # Development server
npx uniweb page add about     # Create a new page
npx uniweb component add FAQ  # Add component to your Foundation

# Inline help
npx uniweb -h                 # Main help index
npx uniweb page add -h        # Help for 'page add' command

# Publishing (coming soon)
npx uniweb site publish       # Publish your site
npx uniweb module publish     # Publish your Foundation to the registry
```

## Installation Options

**Recommended:** Use `npx` (no installation needed):

```bash
npx @uniwebcms/framework create my-project
```

**Optional:** Install globally for frequent use:

```bash
npm install -g @uniwebcms/framework
uniweb create my-project
```

## Why Uniweb Framework?

**Build plug-and-play Foundations.** Create a Foundation and deploy it across multiple sites. When you update components, all connected sites benefit automatically (controlled by per-site versioning policy).

**Standard React workflow.** Use any packages, styles, or tools you prefer. The Framework scaffolds a normal React project – no vendor lock-in.

**Optional schemas unlock powerful features.** Add component schemas for your user-facing components:

- The local build process uses the component schemas to validate frontmatter options
- The Uniweb App uses a Foundation's schema to integrate its exposed components as native building blocks in its visual editor

### The Power of Specialization

Build one Foundation for a vertical (documentation, marketing, corporate, medical, legal, real estate) and deploy to dozens of client sites. Updates propagate automatically, controlled by per-site versioning policies.

**Developers maintain Foundations.** Build and refine components without managing individual client content.

**Content teams manage content.** Work with Git and Markdown, or use the [Uniweb App](https://uniweb.app) for a professional visual editing experience. Components are built by developers; content teams use them without touching code.

This separation eliminates bottlenecks and lets each team focus on their expertise.

### The Broader Ecosystem

The Uniweb Framework is open source (GPL-3.0) and free to use. The broader Uniweb ecosystem includes:

- **Uniweb App** - Professional visual editor and hosting platform (free for drafts, pay to publish)
- **Foundation Registry** - Publish and share Foundations with licensing options (coming soon)
- **Community** - Open interfaces, examples, and shared best practices

The Framework works standalone or integrates with the full ecosystem as your needs grow.

## Requirements

- Node.js ≥18.0.0

## Learn More

- **[Framework Website](https://framework.uniweb.app)** - Guides, blog, and comprehensive resources
- **[Documentation](https://docs.framework.uniweb.app)** - Complete API reference and tutorials
- **[Uniweb App](https://uniweb.app)** - Visual content editor and hosting platform
- **[Examples](https://github.com/uniwebcms/examples)** - Sample Foundations and components
- **[Community Interfaces](https://github.com/uniwebcms/interfaces)** - Standard component specifications

## License

GPL-3.0-or-later – see [LICENSE](LICENSE) for details
