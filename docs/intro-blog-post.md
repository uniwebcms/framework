# Introducing Uniweb Framework

We're excited to share something we've been working on for a while: the Uniweb Framework. It's a different way to think about building websites. You're not building sites with the framework. You're building frameworks that content creators use to build sites.

We call them Foundations—component collections that work like frameworks. You build and publish a Foundation. Content creators select it and build sites with your components. When you improve a component, every site using that Foundation gets the update on next page load. No coordination, no redeployment.

It's component development as infrastructure. And it's open source.

## What We Made

The simplest way to explain it: you build component collections for content creators, and content creators use those components to build sites. Like component libraries but usable directly by everyone, not just developers.

Sites are pure content and Foundations are pure code, hosted independently and interacting through a structured interface at runtime. Foundations can be hosted on any static CDN, or use Uniweb's hosting infrastructure.

A Foundation is React components. Build them however you want. A Site is content—markdown, structured data, dynamic data, configuration, assets. When someone visits a page, the site loads its Foundation dynamically and each content block renders using the component it specifies.

Update a Foundation with a non-breaking improvement, and every site using that Foundation gets it on their next page load. Foundations use semantic versioning so sites can control their update strategy for stability.

## Why This Approach

We wanted something that works well for both developers and content creators—without compromise.

Developers should build real, dynamic sites. Full React capabilities. Any patterns. Complex interactions, API calls, whatever the project needs. The rich, evolving web—not toy sites or constrained templates.

Content creators should compose pages, add content, and publish—without waiting for developer time or touching code.

Most frameworks force a choice or create friction. We built the separation into the architecture itself. Foundations and Sites are independent. Developers maintain components. Content creators compose with them. The schema defines what's possible—you control how much flexibility to expose.

## What You're Actually Working With

Here's what development looks like:

**Preprocessed content.** You don't parse markdown. The runtime engine does that before your component sees anything. You receive a structured content object—headings organized into sections, paragraphs as arrays, images with metadata, lists with structure. You render it.

**Mostly standard React.** Only components exposed to content creators need special interfaces and schemas. Everything else is regular React—internal components, utilities, whatever you need. Import any packages. Use any patterns. The Framework defines the boundary where components meet content. Inside that boundary, it's your code.

**Professional utilities included.** The `@uniwebcms/basic` package provides Section, Image, Link, Form, and more. These handle edge cases, accessibility, and performance. You don't start from scratch.

**Zero-config tooling.** Webpack, Babel, TypeScript support, Tailwind CSS, PostCSS—pre-configured. Hot module replacement, source maps, production optimization. Just build.

**Structured and dynamic data.** Markdown's natural structure handles most content. Need explicit structure like team members or products? Use JSON blocks with schema validation in the markdown. Need dynamic content? The framework's runtime engine handles data fetching from APIs and databases. Your component receives data ready to render.

**Semantic parameters.** Configuration options are semantic—`theme: "dark"` not `backgroundColor: "#333"`. Content creators understand their options. You can change implementation without breaking content.

## The Workflow

You build a Foundation for whatever you're building—marketing site, documentation, product catalog. Your components. Your design system. Write schemas defining each component's interface (at minimum just the name, add parameters for configurability). Publish it.

Someone creates a site and selects your Foundation. Technical users work in markdown and Git. Non-technical users work in the Uniweb App—a visual editor that reads your schemas and presents your components as native building blocks.

They compose pages: Hero at the top, feature grid below, FAQ at the bottom. Configure components through frontmatter or visual controls. Write content. Add pages. Publish.

You improve the Hero component—better responsive behavior, new feature, bug fix. You publish.

Their site gets it on the next page load. No redeployment from them. No coordination with you. The update propagated.

They add three pages and reorganize navigation. You never see those changes. Don't need to.

That's the independence.

## What Works Well

**Visual editor integration.** When a site uses the Uniweb App, your schemas tell the editor how to present your components—what controls to show, what presets to offer, what's valid. Your design system becomes the interface.

**Natural scaling.** Start with one site and one Foundation—better organized React development. Need to serve multiple sites later? The architecture already supports it. Don't need it? You've got cleaner separation regardless.

**True content independence.** Content creators work in their space, you work in yours. Neither blocks the other. Content changes don't require developer review. Component improvements don't require content coordination.

**Full dynamic capabilities.** Not limited to static content. API calls, databases, complex state, forms, authentication—build what your site needs.

**No lock-in.** Use any packages, styling, or tools. The Framework is unopinionated about implementation. Standard React development with better content organization.

## For Client Work

The framework makes client handoffs straightforward. Build a Foundation for your clients' needs—your design system as a framework they build on:

1. Go to [uniweb.app](https://uniweb.app)
2. Click **Create → Website**
3. Select your Foundation
4. Build initial content with the visual editor
5. Click **Publish**
6. Transfer ownership to your client

The Foundation license automatically transfers with the site. Your client owns their site and can continue building content independently. You maintain the Foundation—improving components, adding features, fixing bugs. Your improvements propagate to their site automatically.

Terms and compensation are between you and your client. You can offer Foundation maintenance as an ongoing service, or structure it however works for your business. The architecture just makes the technical workflow clean.

## Try It

```bash
npx @uniwebcms/framework@latest create my-project --template marketing
cd my-project
npm install
npx uniweb start
```

You'll get a Foundation with example components and a demo site. See how it works. Modify components. Check the docs at framework.uniweb.app.

## What's Next

This is the beginning of community engagement. The Framework is open source (GPL-3.0)—Foundations and sites you build are yours to license however you want. Build and deploy everything yourself, or use the Uniweb App and hosting as optional services.

We're building out the broader ecosystem too: better tooling, more examples, and the Uniweb App—a visual editor that integrates with Foundations to give content creators a complete UI for building and managing sites.

Right now, we're just happy to share what we made and see what people build with it.

If you've wanted to build component frameworks that others can build sites with, or clean separation between component code and content, or updates that propagate instantly across multiple sites—try it.

We'd love to hear what you think. Docs at docs.framework.uniweb.app, code on GitHub. We're early, so you're early. Build something. Tell us what's confusing or doesn't work. Help us figure out what this becomes.
