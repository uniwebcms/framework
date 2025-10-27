# Understanding Uniweb: Bridging Development and Content Creation

Uniweb operates at a higher level of abstraction than typical web frameworks. Rather than building websites directly, you build **Foundations**—collections of components designed to interface with content creators, not just developers.

## Why "Foundation" Instead of "Component Library"?

The term "component library" suggests code meant to interact with other code—packages you `npm install` and import into your code. Think React, NextJS, Material-UI, shadcn/ui. These are tools developers use to build websites.

**Foundations are fundamentally different.** They're designed to bridge from the developer world into the content creation world.

When you build a Foundation, you're creating an interface that content creators interact with—through markdown frontmatter, through visual editors, through declarative configuration. The components aren't code that developers import; they're building blocks that content teams compose with.

This cross-domain bridging is the key distinction:

- **Component library**: Developer imports → Developer uses in code → Developer deploys
- **Foundation**: Developer builds → Content creator references → Content creator composes pages

## Two Types of Interfaces

To understand Foundations, you need to see the difference between developer-facing and content-facing interfaces:

### Developer-Facing Interface (Traditional Component Libraries)

```jsx
import { HeroSection } from "@my/library";

function MyPage() {
  return <HeroSection layout="centered" background="dark" title="Welcome" />;
}
```

The consumer is a developer. They write code, manage imports, and deploy applications.

### Content-Facing Interface (Uniweb Foundations)

```markdown
---
component: HeroSection
layout: centered
background: dark
---

# Welcome to Our Platform
```

The consumer is a content creator. They write markdown, configure through frontmatter, and compose pages. No code. No imports. No deployment pipelines.

**This is the fundamental shift.** Foundations are designed for the second pattern—where components are referenced declaratively by people who don't write code.

## The Core Concept

Most web frameworks help you build _a website_. Uniweb helps you build _a system for content creators to build websites_.

When you create a Foundation, you're building a specialized framework for a particular domain—documentation sites, marketing sites, corporate sites, real estate listings, medical portals. That Foundation becomes the vocabulary and toolset that content teams use to create pages.

## Inside vs Outside the Boundary

A Foundation contains two types of components:

**Exposed components** sit at the boundary with content creators. These are defined by schemas and can be referenced in markdown frontmatter. This is where the Framework's architecture matters—these components receive preprocessed content, follow the content-facing interface conventions, and must be declared in schemas.

**Internal components** are pure React implementation details. They're imported and used by your exposed components using standard React patterns. Import any npm packages you want. Use any styling approach. Compose components however you prefer. This is just regular React development—the Framework doesn't prescribe anything here.

```jsx
// Internal component - standard React, no framework involvement
import { Button } from "some-ui-library";

function CallToAction({ text, href }) {
  return (
    <Button variant="primary" href={href}>
      {text}
    </Button>
  );
}

// Exposed component - this is the content-facing interface
export default function HeroSection({ block }) {
  const { title, subtitle, ctaText, ctaLink } = block.main.content;

  return (
    <section>
      <h1>{title}</h1>
      <p>{subtitle}</p>
      <CallToAction text={ctaText} href={ctaLink} />
    </section>
  );
}
```

Content creators only interact with `HeroSection` (because it's in a schema). The `CallToAction` component is invisible to them—it's internal implementation.

**This is why "Foundation" fits better than "component library."** You're not exposing every component to content creators. You're building a foundation with a carefully designed surface area—the exposed components—while the internal structure is entirely up to you and standard React practices.

## Three-Tier Architecture

Uniweb enforces a clean separation across three layers:

### 1. Content (Sites)

Markdown files with YAML frontmatter that specify _which_ component to use and _how_ to configure it. Content teams work here exclusively.

```markdown
---
component: HeroSection
layout: centered
background: dark
---

# Welcome to Our Platform

Discover innovative solutions for your business.
```

### 2. Components (Foundations)

React component libraries that provide the implementations. Developers work here, building the components that content teams will reference.

### 3. Runtime Connection (Module Federation)

Webpack's Module Federation technology dynamically loads Foundations at runtime, enabling dependency sharing and instant updates. When you improve your Foundation, connected sites receive the update on their next page load—no redeployment needed.

## The Content-Facing Interface

The key architectural insight is that **a Foundation's interface is designed for content creators, not developers**.

When you expose a component in your Foundation, you're not creating a JavaScript API for other developers to import. You're creating an option that content creators can reference in markdown frontmatter:

```markdown
---
component: HeroSection
layout: centered
background: dark
---
```

This is the contract boundary: content declares _what_ component to use and _how_ to configure it, while the Foundation provides the implementation. Content creators never touch JavaScript—they compose with components as if they were native features.

**Only exposed components appear in this interface.** Internal components—the React components you use to build your exposed components—remain invisible to content creators. They're standard React implementation details, composed using normal import patterns and npm packages. The Framework only cares about the boundary layer.

**Component schemas define this content-facing interface:**

- At minimum: the component's name (what content creators can reference)
- For parameterized components: available options content creators can configure
- For visual editor integration: the schema becomes the UI—parameters become controls, presets become templates

This schema-driven approach serves multiple audiences:

- **Build-time**: Validates that content references valid components and options
- **Content creators**: Defines what's possible through frontmatter configuration
- **Visual editors**: Translates components into native UI building blocks

The Foundation's interface isn't `import { HeroSection } from '@my/foundation'`—it's the set of component names and options that appear in markdown frontmatter. This is a content interface, not a code interface.

## Progressive Complexity

The framework supports the entire spectrum of use cases:

**Simple end**: Build hardcoded React components for a single site. Use minimal schemas (just component names). Work like any React framework.

**Complex end**: Create comprehensive design systems with rich schemas, parameterized components, theming, presets, and deep visual editor integration. Serve multiple sites with content creators who never touch code.

Start anywhere on this spectrum and grow as needs evolve. The architecture doesn't dictate your complexity—it accommodates it.

**The Framework is unopinionated about implementation.** How you build your exposed components internally—what npm packages you use, how you structure internal components, what styling approach you take—is entirely standard React development. The Framework only defines the boundary layer where components meet content. Inside that boundary, it's your choice.

## Single Source of Truth

A Foundation is the canonical implementation. When you update a component:

1. The improvement exists in one place (the Foundation)
2. All connected sites can receive the update (based on their version strategy)
3. No per-site redeployment is required (runtime loading)

This architectural decision eliminates the traditional problem of component libraries that require explicit upgrades and redeployment across consuming projects. The update propagation is automatic and controlled.

## Why This Abstraction Level?

Traditional web frameworks optimize for building individual websites. Uniweb optimizes for building **classes of websites**.

The framework shines when:

- You're building multiple related sites (different clients, brands, or products)
- You have separate teams for development and content
- You want component improvements to propagate automatically
- You need version control over breaking changes
- You're building a vertical specialization (a framework for X type of sites)

## The Broader Ecosystem

While the Framework is open source and works standalone, it integrates with a commercial ecosystem:

- **Uniweb App**: Professional visual editor and managed hosting
- **Foundation Registry**: Publish and share Foundations
- **Community**: Shared interfaces, specifications, and best practices

You can use the Framework entirely on your own infrastructure, or leverage the ecosystem as needs grow. The architecture avoids lock-in while providing a path to managed services.

## In Practice: Two Worlds, One System

**Developers** build Foundations—creating components with content-facing interfaces. You're not just writing React components; you're designing the vocabulary and building blocks that content creators will use.

**Content creators** are the primary consumers of your Foundation. They compose sites by referencing your components through markdown frontmatter or visual editors. To them, your `HeroSection` component is as native as a heading or paragraph—just a building block they can use and configure.

**Sites** control their update strategy—choosing between automatic updates, conservative policies (minor/patch only), or pinned versions.

This is fundamentally different from traditional component libraries:

- **Component library**: Developers consume the API in code
- **Foundation**: Content creators consume the interface through declarative markup

The separation eliminates bottlenecks: Developers aren't blocked by content changes. Content teams aren't waiting for developer availability to launch pages. Each role works in their domain—code for developers, content for creators—connected through the Foundation's interface.

## The Key Insight

Uniweb recognizes that **components need interfaces for both developers and content creators**.

Traditional component libraries only have developer-facing interfaces—JavaScript APIs, props, imports. Uniweb Foundations add a second interface layer: a content-facing interface defined through schemas and exposed via frontmatter options.

This dual-interface design is why "Foundation" is the right term. You're not just building reusable components for developers. You're building a foundation that content creators build upon—a system where your components become their vocabulary, your parameters become their configuration options, and your design decisions become their creative constraints.

**The Foundation bridges two worlds:**

- **Developer world**: React components, JavaScript, npm packages, build tools
- **Content world**: Markdown files, frontmatter, visual editors, declarative composition

Most frameworks optimize for the developer side. Uniweb optimizes for the bridge between both sides. That's the fundamental difference, and why the abstraction level makes sense: you're not building for developers to use in code—you're building for content creators to compose with declaratively.
