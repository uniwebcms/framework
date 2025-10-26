# Deployment Guide

This guide covers deploying Foundations and sites in the Uniweb ecosystem.

---

## Key Concepts

Understanding these terms helps clarify the workflows in this guide:

**Foundation Creator/Owner:** The person or team who develops and publishes a Foundation—the React components and schemas that define a design system.

**Site Owner:** The person or organization who owns and publishes a site. May be the same as the Foundation creator, or a different person/organization (such as a client).

**Uniweb Framework:** Open-source CLI and build tools for local development (`npx uniweb` commands).

**Uniweb App:** Web application at [uniweb.app](https://uniweb.app) providing visual editing, site management, and hosting.

**Foundation Registry:** Central repository at `modules.uniweb.app` where published Foundations are stored and distributed to sites at runtime.

**The Platform:** The complete ecosystem including Framework, Uniweb App, Registry, visual editor, JavaScript infrastructure, and hosting services.

**Module Federation:** Webpack technology that enables Foundations to load dynamically at runtime, allowing updates to propagate without site redeployment.

---

## Understanding the Platform

The Uniweb Framework separates concerns between foundation creators and site owners:

**Foundation creators build:**

- React components
- Component schemas (define parameters and options)
- Design system and layouts

**The platform provides:**

- Visual editor that interprets component schemas
- JavaScript infrastructure (forms, analytics, localization, data fetching)
- Foundation registry and version management
- Hosting infrastructure (CDN, SSL, deployment)
- Module Federation runtime

**Foundation creators maintain components. The platform maintains infrastructure.**

This separation means foundation creators can focus on design systems while site owners focus on content—each team working independently with their own expertise.

---

## Deployment Paths

You have two distinct approaches for deploying foundations and sites:

### Path 1: Uniweb Infrastructure (Recommended)

**Foundation** → Registry via CLI (`npx uniweb module publish`)  
**Site** → Uniweb App (visual editor and hosting)

**Benefits:**

- Visual editor for content management
- Built-in access control (grant specific sites)
- Managed hosting and CDN
- Automated version management
- Analytics and support

**When to choose:**

- Building foundations for client work
- Need access control and licensing
- Want visual editor for site owners
- Prefer managed infrastructure

**Cost:** See [pricing page](https://www.uniweb.app/pricing) for published sites

---

### Path 2: Self-Hosted (Full Control)

**Foundation** → Host anywhere (GitHub Pages, Netlify, Vercel, your CDN, etc.)  
**Site** → Host anywhere (GitHub Pages, Netlify, Vercel, your CDN, etc.)

**Benefits:**

- Complete control over infrastructure
- Zero cost to Uniweb
- Use existing deployment workflows
- Open source friendly

**Trade-offs:**

- No visual editor (CLI workflow only)
- No built-in access control
- Manual hosting management
- No Uniweb analytics

**When to choose:**

- Personal projects or open source
- Want full infrastructure control
- Already have hosting infrastructure
- Prefer zero platform costs

**Cost:** $0 to Uniweb (your hosting costs only)

---

**Note:** These are distinct paths. If you self-host your site, you self-host your foundation. The registry is specifically designed for Uniweb App hosted sites where access control and managed infrastructure matter.

---

## Path 1: Publishing to Registry and Uniweb App

This section covers the recommended managed infrastructure path.

### Publishing a Foundation to Registry

Foundation publishing is free—payment is required only when sites are published.

#### Prerequisites

- Completed Foundation (local module with components and schemas)
- Uniweb account at [uniweb.app](https://uniweb.app)
- Node.js ≥18.0.0

#### Step 1: Authenticate

```bash
npx uniweb login
```

This opens your browser to authenticate. Credentials are stored locally for future use.

**For CI/CD environments:** Generate an API key in Uniweb App settings:

```bash
npx uniweb login --key uwb_your_api_key_here
```

#### Step 2: Prepare Your Foundation

Ensure your Foundation is ready:

```bash
cd src/my-foundation

# Test the build
npm run build

# Validate components and schemas
npx uniweb module validate
```

Validation checks:

- All exported components have corresponding schema files
- Schemas are valid JSON
- Required metadata exists in `package.json`
- Webpack builds successfully

#### Step 3: Version Your Foundation

Update `package.json` with semantic versioning:

```json
{
  "name": "my-foundation",
  "version": "1.0.0",
  "description": "Custom design system"
}
```

**Semantic versioning:**

- **Patch** (1.0.1) — Bug fixes, no API changes
- **Minor** (1.1.0) — New features, backward compatible
- **Major** (2.0.0) — Breaking changes

Sites control which updates they receive through version strategies.

#### Step 4: Publish to Registry

For projects with a single foundation:

```bash
npx uniweb module publish
```

For projects with multiple foundations, specify which one:

```bash
npx uniweb module publish my-foundation
```

**What happens:**

1. Foundation builds (Webpack + Module Federation bundle)
2. Component schemas collected
3. Metadata generated from `package.json`
4. Bundle uploaded to registry
5. Foundation becomes available at its URL

**Example output:**

```
Building Foundation...
✓ Bundle created
✓ Schemas collected (12 components)
✓ Metadata validated

Uploading to Uniweb Registry...
✓ Published successfully

Foundation URL: https://modules.uniweb.app/yourusername/my-foundation
Version: 1.0.0

Your Foundation is now available in the registry.
```

#### Step 5: Create Sites with Your Foundation

**The simple workflow:**

1. Go to [Uniweb App](https://uniweb.app)
2. Create a new site
3. Select your Foundation from the list
4. Build and design your site

**Access is granted automatically** when you create a site with your own Foundation. No additional licensing steps needed.

This workflow enables you to:

- Set up initial content and pages
- Configure components and layouts
- Demonstrate the Foundation's capabilities
- Transfer a ready-to-publish site to clients

When you transfer the site, **access persists**—the site retains permanent permission to use your Foundation.

---

#### Advanced: Manual Access Grants

For special cases where you need to grant access to a site you didn't create:

**In Uniweb App:**  
Foundation Settings → Access Control → Grant Site Access → Enter site ID

**Via CLI:**

```bash
npx uniweb module grant site-abc-123
```

**Note:** This is rarely needed. The automatic grant when creating a site handles most workflows.

---

### Creating and Publishing a Site on Uniweb

Sites are published directly through uniweb.app with integrated hosting.

#### Understanding Site Pricing

Sites are priced based on Foundation choice and platform features. See [uniweb.app/pricing](https://www.uniweb.app/pricing) for current rates.

**Standard Foundations** (maintained by Uniweb):

- Essential — Free Foundation + hosting
- Advanced — Premium Foundation + hosting
- Signature — Enterprise Foundation + hosting

**Custom Foundations:**

- Your Foundation + full platform integration

**Why is there a cost for using your own Foundation?**

When a site uses a custom Foundation, the platform provides:

- **Visual editor integration** — Runtime interpretation of component schemas, turning parameters into native editor controls
- **JavaScript infrastructure** — Forms, analytics, localization, data fetching, uploads—handled automatically
- **Foundation registry** — Version management, access control, update distribution
- **Hosting infrastructure** — CDN, SSL certificates, deployment pipeline
- **Module Federation runtime** — Dynamic loading, dependency management, automatic updates

The platform work is the same regardless of who created the Foundation. Site owners are paying for the sophisticated capability of integrating custom component contracts into a professional visual editing experience.

This is fundamentally different from a static site generator—your components gain professional content management tools automatically, powered by your schemas. Building equivalent infrastructure independently would require substantial development time and ongoing maintenance.

#### Drafts Are Free

All sites are free in draft mode:

- Unlimited editing and previewing
- Test with any Foundation
- Share draft links with team
- No time limits

Payment is required only when a site is published for production use.

#### Publishing Steps

1. Create and edit your site in the Uniweb App
2. Preview with the visual editor
3. Click **Publish** when ready
4. Configure domain:
   - Use Uniweb subdomain: `yoursite.uniweb.[bio, pro, ...]`
   - Or connect custom domain: `www.yoursite.com`
5. Confirm pricing based on your Foundation choice
6. Site goes live immediately

**What site owners get:**

- Visual editor for ongoing content management
- Dynamic content storage and delivery
- Automatic SSL certificates
- Global CDN distribution for static assets
- Foundation updates based on version strategy
- Integrated analytics
- Email support

**No deployment configuration needed.** The platform handles everything.

---

## Path 2: Self-Hosting Foundations and Sites

This section covers complete self-hosting for full control and zero platform costs.

### Overview

In self-hosted mode, both your foundation and site are static files you host on any service:

- GitHub Pages
- Netlify
- Vercel
- Cloudflare Pages
- AWS S3 + CloudFront
- Your own server or CDN
- Any static file hosting

Foundation files and site files can be:

- On the same service
- On the same domain (foundation as subdirectory of site)
- On completely separate infrastructure

The only requirement: your site's configuration must point to where the foundation is hosted.

### Self-Hosting a Foundation

#### Build Your Foundation

```bash
cd src/my-foundation
npm run build
```

This creates a `dist` folder with your Module Federation bundle and schemas.

#### Deploy Foundation Files

Deploy the `dist` folder contents to any static hosting service.

**Example with Netlify:**

```bash
cd dist
netlify deploy --prod
```

**Example with GitHub Pages:**

```bash
# Push dist contents to gh-pages branch
git subtree push --prefix dist origin gh-pages
```

**Example with Vercel:**

```bash
vercel --prod dist
```

Your foundation is now accessible at a URL like:

- `https://username.github.io/my-foundation`
- `https://my-foundation.netlify.app`
- `https://my-foundation.vercel.app`
- `https://cdn.yourcompany.com/foundations/my-foundation`

#### Automated Deployment with GitHub Actions

For foundations hosted on GitHub, you can automate builds and deployments when you update your version:

```bash
npx uniweb foundation setup-ci
```

This creates `.github/workflows/deploy-foundation.yml` that:

1. Watches for changes to `package.json` version on main/master branch
2. Builds the foundation automatically
3. Deploys to GitHub Pages
4. Makes it accessible at your GitHub Pages URL

**Workflow:**

1. Update version in `package.json`: `"version": "1.2.0"`
2. Commit and push to main/master
3. GitHub Actions builds and deploys automatically
4. Foundation available at new version

---

### Self-Hosting a Site

#### Build Your Site

```bash
cd my-site
npx uniweb build
```

This generates a `dist` folder with your complete static site:

- Optimized HTML for all pages
- Compressed assets (images, CSS, JavaScript)
- Module Federation configuration

#### Configure Foundation URL

Before building, ensure your site configuration points to your foundation's URL.

**In `site.yml`:**

```yaml
foundation:
  url: https://username.github.io/my-foundation
  strategy: minor
```

The URL should point to where your foundation is hosted.

#### Deploy Site Files

Deploy the `dist` folder to any static hosting service (same as foundation deployment).

**Example with Netlify:**

```bash
cd dist
netlify deploy --prod
```

**Example with Vercel:**

```bash
vercel --prod dist
```

**Example with Cloudflare Pages:**
Connect your repository and configure:

- Build command: `npx uniweb build`
- Output directory: `dist`

#### Foundation Loading at Runtime

When your site loads, it fetches the foundation from the URL specified in `site.yml`. This happens at runtime via Module Federation, so:

- Foundation and site can be on different hosting services
- Foundation updates can be deployed independently
- Sites receive updates based on their version strategy

### Complete Self-Hosted Workflow

**For a fully self-hosted setup with zero Uniweb costs:**

1. **Develop foundation locally**

   ```bash
   npx uniweb create my-project --site demo --module my-foundation
   cd my-project
   npx uniweb start
   ```

2. **Build and deploy foundation**

   ```bash
   cd src/my-foundation
   npm run build
   # Deploy dist to GitHub Pages, Netlify, etc.
   ```

3. **Configure site to use foundation URL**

   ```yaml
   # site.yml
   foundation:
     url: https://your-foundation-url.com
   ```

4. **Build and deploy site**

   ```bash
   cd my-site
   npx uniweb build
   # Deploy dist to GitHub Pages, Netlify, etc.
   ```

5. **Updates:**
   - Deploy new foundation version → sites load it based on strategy
   - Update site content → rebuild and redeploy site

**Limitations:**

- No visual editor (CLI-only workflow)
- No built-in access control
- Manual deployment required for updates
- No Uniweb analytics or managed infrastructure

**Benefits:**

- Complete control over hosting
- Zero platform costs
- Use existing deployment infrastructure
- Good for open source projects

---

## Foundation Access Validation

When a site is published through the Uniweb App, the platform validates Foundation access:

**Validation checks:**

1. **Foundation availability** — Is the Foundation accessible in the registry?
2. **Access grant** — Does this site have permission to use the Foundation?
3. **Version availability** — Is the requested version published?

**If validation fails:**

```
⚠️ Cannot publish: Foundation access required

This site uses "Marketing Foundation" by Agency XYZ
but does not have access.

Contact the Foundation creator to request access.
```

**For sites created by Foundation creators:** Access is granted automatically, so this error won't appear.

**For sites owned by others:** The Foundation creator must grant access before the site can publish.

**For self-hosted sites:** No validation occurs. The site simply loads the foundation from the URL in `site.yml`.

---

## Foundation Version Strategies

Site owners control how they receive Foundation updates through version strategies.

**Configure in Uniweb App:**  
Site Settings → Foundation → Version Strategy

**Or in `site.yml`:**

```yaml
foundation:
  url: https://modules.uniweb.app/username/foundation-name
  strategy: minor
```

### Available Strategies

**`latest`** — Always use newest version

```yaml
strategy: latest
```

- Automatically receive all updates (patches, minor, major)
- Best for: Development sites, actively maintained sites
- Risk: Breaking changes may require content updates

**`minor`** — Accept minor and patch updates (Recommended)

```yaml
strategy: minor
```

- Receive new features and bug fixes automatically
- Never receive breaking changes (major versions)
- Best for: Production sites, managed by content teams
- Balance of stability and improvements

**`patch`** — Bug fixes only

```yaml
strategy: patch
```

- Only receive bug fix updates
- Skip new features until manually updated
- Best for: Critical production sites, conservative updates
- Maximum stability

**`pinned`** — Lock to specific version

```yaml
strategy: pinned
version: 1.2.3
```

- Never update automatically
- Manual version changes only
- Best for: Debugging, temporary lock during testing
- Complete control

### How Version Strategies Work

Version strategies are evaluated **at runtime** when the Foundation loads:

```
Foundation creator publishes v1.3.0

Site A (strategy: "latest") → Loads v1.3.0 immediately
Site B (strategy: "minor") → Loads v1.3.0 (minor update from v1.2.x)
Site C (strategy: "patch") → Stays on v1.2.5 (waits for v1.2.6)
Site D (strategy: "pinned" to "1.2.3") → Stays on v1.2.3
```

**For Uniweb App sites:** No redeployment needed. Sites receive updates on their next page load.

**For self-hosted sites:** Updates load at runtime based on version strategy and CDN caching. For immediate updates, you may need to clear CDN cache or wait for cache expiration.

### Changing Version Strategies

Site owners can change version strategies at any time:

**In Uniweb App:**

- Site Settings → Foundation → Version Strategy
- Select new strategy
- Changes apply immediately

**In site configuration:**

- Update `site.yml`
- Rebuild and redeploy site (for self-hosted)
- Or sync via Uniweb App

---

## Common Workflows

### Workflow 1: Foundation Creator Testing (Managed Path)

**Goal:** Build and test a Foundation before client work

```bash
# 1. Create Foundation locally
npx uniweb create my-project --site demo --module my-foundation

# 2. Develop components
cd src/my-foundation
# ... build components, add schemas ...

# 3. Test locally
cd ../..
npx uniweb start
# → Preview at http://localhost:3000

# 4. Publish Foundation to registry
cd src/my-foundation
npx uniweb module publish
# → https://modules.uniweb.app/you/my-foundation

# 5. Create production site in Uniweb App
# → Go to uniweb.app
# → Create Site → Select your Foundation
# → Design and preview (free in draft mode)

# 6. Publish site when ready
# → Click "Publish" in Uniweb App
# → Site goes live
```

**Cost during development:** $0 (drafts are free)  
**Cost when published:** See [pricing page](https://www.uniweb.app/pricing)

---

### Workflow 2: Agency Building for Client (Managed Path)

**Goal:** Create custom site for client, then transfer ownership

```bash
# 1. Publish Foundation to registry (one-time)
cd agency-foundation
npx uniweb module publish
# → https://modules.uniweb.app/agency/client-brand

# 2. Create site in Uniweb App
# → Select your Foundation
# → Access granted automatically
# → Build site for client

# 3. Set up initial content
# → Add pages and sample content
# → Configure components
# → Demonstrate capabilities
# → Client reviews draft (free)

# 4. Transfer ownership to client
# → Site Settings → Transfer Ownership
# → Enter client email
# → Client accepts transfer
# → Access to Foundation persists with the site

# 5. Client publishes site
# → Client clicks "Publish"
# → Confirms pricing
# → Site goes live
```

**Foundation creator's work:** Time developing Foundation (hours/days/weeks)  
**Site owner's payment to Uniweb:** See [pricing page](https://www.uniweb.app/pricing)  
**Foundation creator's invoice to client:** Separate agreement for development and maintenance

**Access persists through transfer.** The site retains permanent license to use the Foundation.

---

### Workflow 3: Open Source Project (Self-Hosted Path)

**Goal:** Build and share an open source foundation and demo site with zero costs

```bash
# 1. Create Foundation locally
npx uniweb create my-oss-project --site demo --module oss-foundation

# 2. Develop components
cd src/oss-foundation
# ... build components, add schemas ...

# 3. Set up GitHub Actions for automatic deployment
npx uniweb foundation setup-ci
# → Creates .github/workflows/deploy-foundation.yml

# 4. Push to GitHub
git add .
git commit -m "Initial foundation"
git push origin main
# → GitHub Actions builds and deploys to GitHub Pages

# 5. Configure demo site to use GitHub Pages URL
# site.yml:
# foundation:
#   url: https://username.github.io/oss-foundation
#   strategy: latest

# 6. Build and deploy demo site
cd demo-site
npx uniweb build
# Deploy dist to Netlify, Vercel, or GitHub Pages

# 7. Share with community
# → Foundation URL: https://username.github.io/oss-foundation
# → Demo site URL: https://demo.netlify.app
# → Others can use foundation URL in their own sites
```

**Cost:** $0 to Uniweb (only your hosting costs)  
**Access:** Open to anyone who knows the URL  
**Updates:** Push to main branch → GitHub Actions deploys automatically

---

### Workflow 4: Foundation Updates (Managed Path)

**Goal:** Improve Foundation and distribute updates to all sites

```bash
# 1. Make improvements
cd my-foundation
# ... fix bugs, add features, improve components ...

# 2. Update version in package.json
# Before: "version": "1.2.0"
# After:  "version": "1.3.0"

# 3. Publish update
npx uniweb module publish
# → Version 1.3.0 uploaded to registry

# 4. Sites receive updates automatically
# Sites with strategy "latest" → Get v1.3.0 immediately
# Sites with strategy "minor" → Get v1.3.0 (minor update)
# Sites with strategy "patch" → Stay on v1.2.x
# Sites with strategy "pinned" → Stay on pinned version
```

**No site owner action needed.** Improvements propagate based on each site's version strategy.

**Communication best practice:**

- Notify site owners about significant updates
- Document changes in release notes
- Highlight new features or important fixes
- Recommend version strategy adjustments when appropriate

---

### Workflow 5: Major Version (Breaking Changes)

**Goal:** Publish breaking changes without disrupting existing sites

```bash
# 1. Create major version
cd my-foundation
# ... make breaking changes ...
# Update package.json: "version": "2.0.0"

# 2. Publish v2 (managed path) or deploy v2 (self-hosted)
npx uniweb module publish
# Or deploy to your hosting for self-hosted

# 3. Existing sites remain on v1.x automatically
# No version strategy accepts major versions automatically
# All sites stay on v1.x.x (safe)

# 4. Migrate sites individually
# Site owners update configuration to v2:
#   { strategy: "minor" } → eventually crosses to v2.x
#   Or { strategy: "pinned", version: "2.0.0" } → explicit v2
```

**Alternative approach:** Publish v2 as entirely new Foundation

- `my-foundation-v2` as separate foundation
- Clearer separation between versions
- No version strategy confusion
- Easier to maintain both versions simultaneously

---

## Best Practices

### For Foundation Creators

**Versioning:**

- Follow semantic versioning strictly
- Patch (1.0.x) — Bug fixes only
- Minor (1.x.0) — New features, backward compatible
- Major (x.0.0) — Breaking changes

**Quality:**

- Test thoroughly before publishing
- Validate all schemas
- Document component parameters clearly
- Provide examples and presets

**Communication:**

- Notify site owners of significant updates
- Maintain changelog
- Document breaking changes clearly
- Provide migration guides for major versions

**Maintenance:**

- Respond to site owner issues promptly
- Keep dependencies updated
- Monitor site feedback
- Iterate based on real usage patterns

### For Site Owners

**Version Strategy Selection:**

- **Start with `"minor"`** (recommended default)
- Use `"patch"` for critical production sites
- Use `"latest"` only for sites you actively maintain
- Use `"pinned"` temporarily when debugging

**Testing:**

- Test Foundation updates in staging before production
- Monitor site after automatic updates
- Have rollback plan (change to pinned strategy if needed)

**Relationship with Foundation Creator:**

- Maintain communication channel
- Report bugs or issues promptly
- Request features when needed
- Respect that Foundation is the creator's intellectual property

**Budget Planning (Managed Path):**

- Platform/hosting costs (see [pricing page](https://www.uniweb.app/pricing))
- Separate agreement with Foundation creator for:
  - Development costs (one-time)
  - Maintenance and support (ongoing)
  - Feature requests
  - Emergency fixes

---

## Troubleshooting

### "Foundation Not Found"

**Problem:** Site cannot load Foundation.

**Solutions:**

1. **Managed path:** Verify Foundation URL in Uniweb App site settings
2. **Self-hosted:** Verify Foundation URL in `site.yml` is correct and accessible
3. Confirm Foundation is published/deployed
4. Check Foundation creator's username (case-sensitive for registry)
5. Verify hosting service is serving files correctly

---

### "No Access to Foundation" (Managed Path Only)

**Problem:** Site lacks permission to use the Foundation.

**Solutions:**

1. **If you're the Foundation creator:** Access should be automatic when you create sites. Verify the site was created properly.
2. **If you're using someone else's Foundation:** Contact the Foundation creator to request access for your site.
3. **For transferred sites:** Confirm access persisted through the transfer (it should automatically).
4. **Manual grant needed:** Foundation creator can grant access via Uniweb App or CLI.

---

### "Version Not Available"

**Problem:** Requested Foundation version doesn't exist.

**Solutions:**

1. Check available versions: `npx uniweb module info foundation-name`
2. Update version strategy to available version
3. Contact Foundation creator if specific version needed
4. Temporarily use `"pinned"` strategy with known working version
5. **Self-hosted:** Verify the version is actually deployed to your hosting

---

### "Build Failed During Publish"

**Problem:** Foundation build fails during `npx uniweb module publish`.

**Solutions:**

1. Test build locally first: `npm run build`
2. Check for TypeScript errors: `npm run type-check`
3. Validate schemas: `npx uniweb module validate`
4. Review webpack errors in console output
5. Ensure all component exports match schema files

---

### "Foundation Loads Slowly"

**Problem:** Foundation takes time to load on published site.

**Explanation:** Foundations load dynamically via Module Federation. First load may be slower as the bundle is fetched and cached.

**Optimizations:**

1. Foundation is cached after first load
2. **Managed path:** CDN distribution reduces latency globally
3. **Self-hosted:** Ensure your hosting has good CDN/caching
4. Bundle size optimization in Foundation code
5. Module Federation shares common dependencies

**Note:** Dynamic loading enables version updates without redeployment—this is by design.

---

### "Content Changes After Foundation Update"

**Problem:** Content appears different after automatic Foundation update.

**Solutions:**

1. Check Foundation changelog for changes
2. Review component parameter updates
3. Temporarily pin to previous version: `{ strategy: "pinned", version: "1.2.5" }`
4. Contact Foundation creator about unexpected changes
5. Update content to work with new Foundation version
6. Or request fix from Foundation creator

---

## Security Considerations

### Foundation Publishing

**Code Quality:**

- Review all code before publishing
- Audit dependencies for vulnerabilities
- Use `npm audit` regularly
- Never include sensitive data in Foundation code

**Access Control (Managed Path):**

- Grant site access only when appropriate
- Track which sites use your Foundation
- Monitor usage patterns

**Self-Hosted Security:**

- Ensure hosting infrastructure is secure
- Use HTTPS for all foundation URLs
- Keep hosting credentials secure

### Site Publishing

**Foundation Trust:**

- Only use Foundations from trusted sources
- Review Foundation creator credentials
- Understand what code runs on your site
- Monitor for unexpected behavior

**Access Validation (Managed Path):**

- Verify Foundation grants before client handoffs
- Confirm access persists after transfer
- Keep Foundation creator contact information

**Self-Hosted Security:**

- Ensure foundation URLs use HTTPS
- Validate foundation files haven't been tampered with
- Keep deployment credentials secure

---

## API Reference

### CLI Commands

```bash
# Authentication (managed path only)
npx uniweb login                    # Browser OAuth
npx uniweb login --key <api-key>    # API key for CI/CD
npx uniweb logout                   # Clear credentials

# Foundation management
npx uniweb module publish                  # Publish single foundation
npx uniweb module publish my-foundation    # Publish specific foundation
npx uniweb module list                     # List your foundations
npx uniweb module info <name>              # Show foundation details
npx uniweb module validate                 # Validate before publishing

# Access control (managed path only)
npx uniweb module grant <site-id>   # Grant site access
npx uniweb module access list       # List all grants

# Site building
npx uniweb build                    # Build static site
npx uniweb start                    # Development server
npx uniweb page add <name>          # Create new page

# Self-hosted automation
npx uniweb foundation setup-ci      # Add GitHub Actions workflow
```

### Configuration Files

**site.yml** (Site root):

```yaml
foundation:
  url: https://modules.uniweb.app/username/foundation-name
  # Or for self-hosted:
  # url: https://your-hosting.com/foundation-path
  strategy: minor

site:
  title: Site Title
  description: Site description
  domain: www.example.com
```

**package.json** (Foundation root):

```json
{
  "name": "my-foundation",
  "version": "1.0.0",
  "description": "Custom design system",
  "author": "Your Name",
  "repository": "https://github.com/username/my-foundation",
  "license": "MIT"
}
```

---

## Deployment Comparison

|                        | Managed Path            | Self-Hosted Path      |
| ---------------------- | ----------------------- | --------------------- |
| **Foundation**         | Registry via CLI        | Any static hosting    |
| **Site**               | Uniweb App              | Any static hosting    |
| **Visual Editor**      | ✅ Yes                  | ❌ No                 |
| **Access Control**     | ✅ Built-in             | ❌ No (public)        |
| **Version Management** | ✅ Automatic            | ⚠️ Manual             |
| **Hosting Cost**       | See pricing page        | $0 to Uniweb          |
| **Infrastructure**     | Managed by Uniweb       | You manage            |
| **Best For**           | Client work, commercial | Open source, personal |

---

## Pricing Summary

**Managed Path:**

- **Publishing Foundations:** Free
- **Draft Sites:** Free (unlimited)
- **Published Sites:** See [uniweb.app/pricing](https://www.uniweb.app/pricing)

Foundation options:

- Uniweb Essential — Free Foundation + hosting
- Uniweb Advanced — Premium Foundation + hosting
- Uniweb Signature — Enterprise Foundation + hosting
- Custom Foundation — Your Foundation + full platform integration

**Self-Hosted Path:**

- **Everything:** $0 to Uniweb
- **Your costs:** Static hosting provider fees (often free tier available)

---

## Next Steps

**Foundation Creators:**

- Read [Foundation Development Guide](https://docs.framework.uniweb.app/foundations)
- Explore [Component Schemas](https://docs.framework.uniweb.app/schemas)
- See [Example Foundations](https://github.com/uniwebcms/examples)

**Site Owners:**

- Learn about [Site Configuration](https://docs.framework.uniweb.app/sites)
- Explore [Visual Editor](https://docs.framework.uniweb.app/editor)
- Review [Content Best Practices](https://docs.framework.uniweb.app/content)

**Agencies:**

- Read [Client Workflows](https://docs.framework.uniweb.app/workflows)
- See [Transfer Process](https://docs.framework.uniweb.app/transfers)
- Review [Billing Guide](https://docs.framework.uniweb.app/billing)

**Questions?**

- Documentation: [docs.framework.uniweb.app](https://docs.framework.uniweb.app)
- Support: [support.uniweb.app](https://support.uniweb.app)
- Community: [discord.gg/uniweb](https://discord.gg/uniweb)
