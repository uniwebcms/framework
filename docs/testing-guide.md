# Testing Guide

You can test your components using three different approaches, each suited to different development needs.

1. **Local Development:** test using a **local mock site**
2. **Local Development with Tunnel:** create a **public tunnel** to your localhost and connect a uniweb.app website to your **locally hosted Foundation**
3. **GitHub Actions:** deploy your Foundation **without a local setup** using GitHub actions

Each of these approaches is explained below.

## 1. Local Development

A simple and effective testing technique for new components is to work with them locally using mock data.

[Uniweb RTE](https://github.com/uniwebcms/uniweb-rte) is the runtime engine that powers a website and connects it with a Foundation. It uses local website content (headings, text, images, etc.), making it easy to test how your components handle different content scenarios. It provides:

- A testing ground with structured mock data
- A runtime host for federated component modules
- A way to verify component behavior before connecting to a live site

### Getting Started

If you don't yet have a test website, create one with this command:

```bash
npx uniweb site add my-site --module my-foundation
```

The new site will use the local `my-foundation` from your project.

Next, simply start the site.

```bash
npx uniweb start
```

## 2. Uniweb App and Local Development with Tunnel

This Uniweb CLI includes a simple yet powerful solution for serving local files over the internet using a web server and a temporary Cloudflare Quick Tunnel.

[Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/) is a service that securely exposes your local development server to the internet, without the need for port forwarding or firewall configuration. This makes it easy to test and share your component Foundation with others during development.

**⚠ Important**: Make sure to install the `Cloudflared` CLI and check that it's in your PATH. You can find the latest installation instructions here: [Cloudflare Tunnel Downloads](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/)

- **macOS**: `brew install cloudflared`
- **Windows**: `winget install --id Cloudflare.cloudflared`
- **Linux**: [Cloudflare Package Repository ↗](https://pkg.cloudflare.com/)

> 🗒 You can also use [VS Code Port Forwarding](https://code.visualstudio.com/docs/editor/port-forwarding), or a permanent tunnel URL if you prefer. For instance, you can set up a [Cloudflare named tunnel](https://developers.cloudflare.com/pages/how-to/preview-with-cloudflare-tunnel/) or a [Pagekite tunnel](https://github.com/uniwebcms/uniweb-module-builder/blob/main/docs/pagekite.md). If you go this route, just remember to set the `TUNNEL_URL` property in your `.env.dev` file to the tunnel's URL.

### Getting Started

You will need two terminals: one to run your Foundation's hosting server, and one to watch the Foundation for changes.

1. **Terminal 1: Install packages and start web server with a tunnel**  
   `yarn && yarn serve --tunnel`
2. **Terminal 2: Watch for code changes**  
   `yarn watch`

The web server will serve files from the `build_dev` folder. Initially, this folder will have a single file named `quick-tunnel.txt` containing the URL of the current [Cloudflare quick tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/do-more-with-tunnels/trycloudflare/) pointing to http://localhost:3005. The quick tunnel URL changes every time the server starts and looks like `https://[tunnel-sub-domain].trycloudflare.com`.

The watch script will build a bundle of JavaScript files in dev mode and save them to the `build_dev/[module-name]` subfolder. All source files under the `src` folder are watched for changes, and the target bundles are rebuilt as needed.

The watch script output will give you the URL to connect your test website with your dev environment:

```bash
PUBLIC URL: https://[tunnel-sub-domain].com/my-foundation
```

> 🗒 Remember, when connecting a website with a module, the URL must include the module name in its path because there might be several modules hosted under the same domain.

## 3. Deploy with GitHub Actions

This method is the least practical but requires no local setup since it builds your modules using a GitHub Workflow, and hosts them with GitHub Pages.

### Getting Started

Initialize the Continuous Integration (CI) script for GitHub actions if not yet done.

```bash
npx uniweb github setup-ci
```

This creates `.github/workflows/deploy-foundation.yml` that:

1. Watches for changes to `package.json` version on main/master branch
2. Builds the foundation automatically
3. Publishes to GitHub Pages
4. Makes it accessible at your GitHub Pages URL

Build your Foundation with GitHub Actions, and make the build publicly available through GitHub Pages:

1. Go to the `⚙ Settings` tab of your GitHub repository, and then go to the `Pages` tab
2. Under the section **Build and deployment**, in the **Source** menu, make sure that `Deploy from a branch` is selected. In the **Branch** menu, select `gh-pages`, leave `/ (root)` as the folder, and then click the **Save** button.

The build process should start right away, but it may take a minute or two to complete. You can monitor its progress from the `⏵ Actions` tab in your GitHub repository. When it's ready, you should see a successful `✅ Deploy` stage with the URL to your GitHub pages, like `https://USER-NAME.github.io/REPO-NAME/`.

To find the URL for your new component Foundation, visit the GitHub pages URL. You should see a page titled **Available Modules**. At this point, the first and only module listed would be **my-foundation**. Use the copy button next to it to grab its URL. It should look something like `https://USER-NAME.github.io/REPO-NAME/my-foundation/`.

Now you're ready to use your Foundation in a Uniweb website! Head over to your Uniweb instance.

Create a new website via the **Skip template** option – we want to keep things simple and start from scratch.

Since your website doesn't have a component Foundation or content yet, it will be a blank page. Open the action menu `(⋅⋅⋅)▾` in the website studio and select "Manage components...". Then, paste the URL of your GitHub-hosted Foundation under the "Custom URL" tab and into the "Foundation URL" field and apply your changes.

Ta-da! 🎩✨ You should now see some content on your website, generated by the `Section` component in the `my-foundation` module of your repository. You can select a different component by clicking the Edit button at the top right corner of the Website Studio. This opens the Content Editor, where you can select which component renders each website page section.

### Version Management

We use semantic versioning to manage module updates. The version number (like 1.2.3) tells us about the type of changes:

- **Major**: The first number (1.x.x) indicates major versions with breaking changes
- **Minor**: The middle number (x.2.x) represents new features that won't break existing code
- **Path**: The last number (x.x.3) represents bug fixes and small non-breaking changes

New builds are created automatically in response to commits that include Foundations with higher version numbers than those of the last build. This means that you can trigger a new build by increasing the version number of your Foundation in its `package.json` file, and then committing your changes.

There are [version and push scripts](docs/scripts.md) to increase the version number of a module and commit the changes. For example,

```sh
cd src/my-foundation
npm version minor
```

will increase the second number of the version, commit the change, and push it. If you have multiple modules, this command will list them and let you select the target one.

When a website loads, it periodically checks if its component Foundation has compatible updates available:

- Bug fixes (x.x.3) are automatically applied
- New features (x.2.x) are applied when the site is republished
- Major updates (1.x.x) require manual review by site administrators

This version management system, combined with Uniweb's runtime architecture, means your updates can be instantly available across all authorized websites using your Foundation - a powerful feature for maintaining and improving websites at scale.

**Workflow:**

1. Update version in `package.json`: `"version": "1.2.0"`
2. Commit and push to main/master
3. GitHub Actions builds and publishes automatically
4. Foundation available at new version

### 👷 Enabling Dev Mode on a Website

Now that you have a temporary URL pointing to the current dev build of your Foundation, you can use it on a website in Dev Mode.

1. Create a website, or open an existing one, and turn on its **Dev Mode** via the action menu `(⋅⋅⋅)▾` of the **Website** studio
2. Set the **Component Foundation URL** to the URL produced in the last step. Continue developing the components in your module and reload the website to get the latest test bundle as it changes.

Testing is just the first step in your component Foundation's journey. Once you've verified your components work as intended, you can move on to publishing your Foundation for production use.

## Publishing Your Foundation

While you can use unregistered Foundations for testing and development using the methods described above, Foundations must be registered with Uniweb before they can be used in published websites made with [uniweb.app](https://www.uniweb.app) or other Uniweb instances.

Publishing and registration steps:

1. [Deploy and release](deployment-guide.md) a Foundation
2. Register the Foundation's URL and information in a Uniweb instance, such as [uniweb.app](https://www.uniweb.app)

### Why Registration Matters

Component Foundations represent significant intellectual property—they're powerful, reusable assets that encapsulate both design and functionality. The clean separation between content and presentation in Uniweb means Foundations are particularly valuable and need protection. Registration helps:

- Establish ownership and protect intellectual property rights
- Enable professional developers to monetize their work
- Maintain quality standards in the Uniweb ecosystem
- Provide accountability for Foundation maintenance and updates

### Registration and Licensing

To use a Foundation in published websites, it must be registered with the Uniweb instance where the websites are hosted (e.g., uniweb.app). Once registered, you can:

- Grant usage permissions to specific users or websites
- Manage your own licensing terms and compensation
- Control who can publish websites using your Foundation
- Update your Foundation across all authorized websites

Uniweb enforces these permissions, ensuring your Foundation is only used by those you've authorized. While Uniweb offers standard Foundations for quick no-code website creation, it also supports this marketplace of custom Foundations for more ambitious projects. Organizations can commission custom Foundations from professional developers or license existing ones, with clear protections for intellectual property.

For detailed information about registration, licensing, and intellectual property protection, see the [Deployment Guide](deployment-guide.md).
