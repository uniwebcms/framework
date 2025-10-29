# Uniweb Deployment Guide

This guide covers deploying Uniweb sites and Foundations to production environments.

## Quick Deployment with Uniweb App

The fastest way to deploy is using the managed Uniweb App platform:

### Publishing Your Foundation

```bash
# Authenticate (creates account if needed)
npx uniweb login

# Publish your Foundation
cd src/my-foundation
npx uniweb module publish
```

Your Foundation is now available in the registry for sites to use.

### Publishing Your Site

1. Go to [uniweb.app](https://uniweb.app)
2. Click **Create Site**
3. Select your Foundation (or choose from registry)
4. Build content with the visual editor
5. Click **Publish**

Your site is live with:

- Managed hosting with global CDN
- Automatic SSL certificates
- Continuous deployment from Git
- Visual editor for content updates

## Self-Hosted Deployment

For self-hosted deployments, you have several options depending on your infrastructure and requirements.

### Prerequisites

- Node.js ≥18.0.0
- Static file hosting or Node.js server
- (Optional) CDN for asset delivery

### Building for Production

```bash
# Build your site for production
npx uniweb build

# Output is in the build/ directory
```

The build process:

1. Compiles all markdown content
2. Bundles assets (images, CSS, JavaScript)
3. Optimizes for production (minification, tree-shaking)
4. Generates static pages and dynamic routes
5. Creates the module federation bundles

### Deployment Options

#### Static Hosting (Netlify, Vercel, Cloudflare Pages)

For sites using remote Foundations:

```bash
# Build command
npx uniweb build

# Publish directory
build/
```

**netlify.toml:**

```toml
[build]
  command = "npx uniweb build"
  publish = "build"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

**vercel.json:**

```json
{
  "buildCommand": "npx uniweb build",
  "outputDirectory": "build",
  "routes": [
    { "handle": "filesystem" },
    { "src": "/(.*)", "dest": "/index.html" }
  ]
}
```

#### Node.js Server

For sites requiring server-side features:

```bash
# Build for production
npx uniweb build --target server

# Start production server
npx uniweb serve
```

**Docker deployment:**

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install production dependencies
RUN npm ci --production

# Copy built site
COPY build ./build

# Expose port
EXPOSE 3000

# Start server
CMD ["npx", "uniweb", "serve"]
```

#### Traditional Web Server (nginx, Apache)

For serving static builds:

**nginx configuration:**

```nginx
server {
    listen 80;
    server_name yourdomain.com;
    root /var/www/your-site/build;
    index index.html;

    # Handle client-side routing
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

### Foundation Hosting

Foundations need to be hosted separately from sites (Module Federation requirement).

#### Publishing to Uniweb Registry

```bash
# From your Foundation directory
npx uniweb module publish
```

The registry handles:

- Module hosting and CDN delivery
- Version management
- Dependency resolution
- Cache invalidation

#### Self-Hosting Foundations

For self-hosted Foundations:

1. **Build the Foundation:**

```bash
cd src/my-foundation
npx uniweb module build
```

2. **Host the output:**
   The build creates module federation bundles that must be served from a static host with proper CORS headers.

**nginx configuration for Foundation hosting:**

```nginx
server {
    listen 80;
    server_name modules.yourdomain.com;
    root /var/www/modules;

    # CORS headers for module federation
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods "GET, OPTIONS";

    # Cache module bundles
    location ~* \.(js|css)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

3. **Update site configuration:**

```yaml
# Site config
foundation:
  module: https://modules.yourdomain.com/my-foundation
  version: "1.0.0"
```

## Environment Configuration

### Environment Variables

Create a `.env` file for environment-specific configuration:

```bash
# API endpoints
NEXT_PUBLIC_API_URL=https://api.yourdomain.com

# Analytics
NEXT_PUBLIC_ANALYTICS_ID=UA-XXXXXXXXX

# Foundation location (for development)
UNIWEB_FOUNDATION_URL=http://localhost:3001/my-foundation
```

### Build-Time Configuration

Configure build behavior in `uniweb.config.js`:

```js
export default {
  // Production optimization
  optimization: {
    minify: true,
    splitChunks: true,
    inlineRuntimeChunk: false,
  },

  // Asset handling
  assets: {
    publicPath: "https://cdn.yourdomain.com/",
    imageOptimization: true,
  },

  // Module federation
  moduleFederation: {
    remotes: {
      foundation: process.env.UNIWEB_FOUNDATION_URL,
    },
  },
};
```

## Version Strategies

Sites control how they receive Foundation updates:

### Automatic Updates (Latest)

```yaml
foundation:
  module: username/marketing
  version: "latest"
  strategy: automatic
```

Sites automatically use the latest Foundation version.

### Semantic Versioning

```yaml
foundation:
  module: username/marketing
  version: "^1.2.0" # Minor and patch updates only
  strategy: minor-only
```

```yaml
foundation:
  module: username/marketing
  version: "~1.2.0" # Patch updates only
  strategy: patch-only
```

### Pinned Version

```yaml
foundation:
  module: username/marketing
  version: "1.2.0" # Exact version
  strategy: pinned
```

Sites remain on the specified version until manually updated.

## Continuous Deployment

### GitHub Actions

```yaml
name: Deploy Site

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - uses: actions/setup-node@v3
        with:
          node-version: "18"

      - name: Install dependencies
        run: npm ci

      - name: Build site
        run: npx uniweb build
        env:
          NODE_ENV: production

      - name: Deploy to hosting
        run: |
          # Deploy to your hosting provider
          # Example: rsync, s3 sync, etc.
```

### GitLab CI/CD

```yaml
image: node:18

stages:
  - build
  - deploy

build:
  stage: build
  script:
    - npm ci
    - npx uniweb build
  artifacts:
    paths:
      - build/
  only:
    - main

deploy:
  stage: deploy
  script:
    -  # Deploy to your hosting
  only:
    - main
```

## Performance Optimization

### Content Delivery Network (CDN)

Configure CDN for static assets:

```js
// uniweb.config.js
export default {
  assets: {
    publicPath: "https://cdn.yourdomain.com/",
    uploadToS3: {
      bucket: "my-site-assets",
      region: "us-east-1",
    },
  },
};
```

### Caching Strategy

Implement aggressive caching for static assets:

```nginx
# Immutable assets (versioned)
location ~* \.(js|css)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}

# Images and fonts
location ~* \.(png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
    expires 1y;
    add_header Cache-Control "public, max-age=31536000";
}

# HTML (no caching for content)
location ~* \.html$ {
    add_header Cache-Control "no-cache, no-store, must-revalidate";
}
```

### Build Optimization

Enable production optimizations:

```js
// uniweb.config.js
export default {
  optimization: {
    minify: true,
    splitChunks: true,
    treeShaking: true,

    // Code splitting
    dynamicImports: true,

    // Bundle analysis
    analyze: process.env.ANALYZE === "true",
  },
};
```

## Monitoring and Analytics

### Error Tracking

Integrate error tracking in production:

```js
// Foundation initialization
import * as Sentry from "@sentry/react";

if (process.env.NODE_ENV === "production") {
  Sentry.init({
    dsn: process.env.SENTRY_DSN,
    environment: "production",
  });
}
```

### Performance Monitoring

Track site performance:

```js
// Track Core Web Vitals
import { getCLS, getFID, getFCP, getLCP, getTTFB } from "web-vitals";

function sendToAnalytics(metric) {
  // Send to your analytics provider
  const body = JSON.stringify(metric);
  const url = "/api/analytics";

  if (navigator.sendBeacon) {
    navigator.sendBeacon(url, body);
  } else {
    fetch(url, { body, method: "POST", keepalive: true });
  }
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getFCP(sendToAnalytics);
getLCP(sendToAnalytics);
getTTFB(sendToAnalytics);
```

## Security Considerations

### Content Security Policy

Implement CSP headers:

```nginx
add_header Content-Security-Policy "
    default-src 'self';
    script-src 'self' https://modules.yourdomain.com;
    style-src 'self' 'unsafe-inline';
    img-src 'self' data: https:;
    font-src 'self';
    connect-src 'self' https://api.yourdomain.com;
" always;
```

### HTTPS Configuration

Always use HTTPS in production:

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    # Modern SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # ... rest of configuration
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$server_name$request_uri;
}
```

## Troubleshooting

### Common Deployment Issues

**Foundation not loading:**

- Check CORS headers on Foundation host
- Verify Foundation URL in site config
- Check browser console for module federation errors

**Build failures:**

- Verify Node.js version (≥18.0.0)
- Check for missing dependencies
- Review build logs for specific errors

**Performance issues:**

- Enable production optimizations in config
- Implement CDN for static assets
- Check bundle sizes with `ANALYZE=true`

## Related Documentation

- [Understanding Uniweb](understanding-uniweb.md) - Architecture overview
- [Content Creators Guide](content-creators-guide.md) - Managing content
- [Developers Guide](developers-guide.md) - Building Foundations
- [Terminology Reference](terminology.md) - Key terms

## Getting Help

For deployment support:

- [Framework Website](https://framework.uniweb.app)
- [Documentation](https://docs.framework.uniweb.app)
- [Community Discord](https://discord.gg/uniweb)
- [GitHub Issues](https://github.com/uniwebcms/framework/issues)

---

**Note:** This guide covers general deployment patterns. Specific hosting providers may have additional requirements or optimizations. Check your provider's documentation for platform-specific guidance.
