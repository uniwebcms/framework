# Uniweb Framework for Developers: Building Foundations

This guide explains Uniweb's component architecture and provides practical guidance for building effective, reusable Foundations. As a developer, you'll create the React components that content creators use to build websites without writing code.

**Related documentation:**

- [Understanding Uniweb](understanding-uniweb.md) - Conceptual overview of the architecture
- [Content Creators Guide](content-creators-guide.md) - How content creators use your components
- [Terminology Reference](terminology.md) - Key terms and definitions

## Introduction

When building Foundations for websites, you're creating more than just React components—you're building a content-facing interface that non-technical users will interact with through markdown and visual editors.

**Key insight:** Your components have two audiences:

1. **Content creators** who reference them declaratively in markdown
2. **The runtime engine (RTE)** that instantiates them with structured content

This dual interface is what makes Foundations different from traditional component libraries. See [Understanding Uniweb](understanding-uniweb.md) for the conceptual foundation behind this architecture.

### Two Types of Components in Uniweb Framework

The Framework supports two distinct types of components:

1. **Exposed components**:

   - Components that content creators can select in markdown files
   - Follow the special `{ content, params, block }` interface
   - Require schema files (`component.config.js`)
   - Act as the bridge between content and code
   - Can be composed through section hierarchy (parent-child relationships)

2. **Internal components**:
   - Regular React components used within your implementations
   - Follow standard React patterns and can use any props structure
   - Don't require schema files or special interfaces
   - Not directly available to content creators
   - Handle most of the actual rendering and UI logic

This distinction is crucial: Uniweb fully supports standard React development patterns. In fact, most of your actual UI rendering should happen in internal components, with exposed components primarily handling the content/code bridge.

### How Exposed Components Differ from Standard React

Exposed components have some specific characteristics:

1. **Standardized Props Interface**

   - Receive `{ content, params, block }` instead of arbitrary props
   - Content is pre-parsed from markdown into a structured object
   - Parameters come from front matter configuration
   - The block object provides runtime context and methods

2. **Schema-Driven Configuration**

   - Include schemas (in `component.config.js`) that define their parameters
   - These schemas power documentation and visual editors at build time
   - Parameters have semantic meaning rather than implementation details

3. **Content/Code Separation**
   - Components don't embed content; they receive it at runtime
   - The same component can render completely different content
   - Content and component updates are independent

This separation creates a powerful system where content creators and developers can work independently.

> **Note**: During component development, you may hardcode content in a component as a way to test its rendering logic and adjust the code more easily.

## Setting Up Your Development Environment

### Foundation Structure

A Foundation follows this structure:

```
src/
└── my-foundation/              # Your Foundation module (workspace)
    ├── components/             # Individual components
    │   ├── Hero/
    │   │   ├── index.js            # Component implementation
    │   │   ├── component.config.js # Component schema (params, presets, etc)
    │   │   └── previews/           # Preset preview images (optional)
    │   └── Features/
    │       ├── index.js
    │       └── component.config.js
    ├── index.js             # Foundation entry point
    ├── package.json         # Foundation-specific dependencies
    └── module.yml           # Foundation metadata
```

### Getting Started

Create a new Foundation:

```bash
# Create a development project with a Foundation
npx @uniwebcms/framework@latest create my-project --site demo --module marketing
```

This creates:

- A Foundation module at `src/marketing`
- A demo site at `sites/demo` for testing

Then add components:

```bash
cd my-project
npx uniweb component add HeroSection
```

### Foundation Configuration

Your Foundation is defined in the `package.json` of the module workspace:

```json
{
  "name": "marketing",
  "version": "1.0.0",
  "description": "Marketing site components"
}
```

Each exposed component has its own `component.config.js` file:

```js
// components/HeroSection/component.config.js
export default {
  label: "Hero Section",
  description: "A full-width hero section with optional background image",
  category: "Layout",
};

export const parameters = [
  {
    name: "layout",
    label: "Layout",
    options: ["grid", "list", "carousel"],
    default: "grid",
    description: "How items are arranged and presented",
  },
  {
    name: "columns",
    label: "Columns",
    type: "number",
    min: 1,
    max: 4,
    default: 3,
    description: "Number of items per row (for grid layout)",
  },
];

export const presets = [
  {
    name: "standard",
    label: "Standard Grid",
    description: "Default grid layout with balanced content",
    preview: "previews/standard.jpg",
    settings: {
      layout: "grid",
      columns: 3,
    },
  },
  {
    name: "featured",
    label: "Featured Item",
    description: "Highlights a single item with detailed information",
    preview: "previews/featured.jpg",
    settings: {
      layout: "list",
      columns: 1,
    },
  },
];
```

## Building Components

### Component Interface

Every Foundation exposed component follows this interface:

```jsx
function HeroSection({ content, params, block }) {
  // Extract content from the structured content object
  const { title, paragraphs, images } = content.main;

  // Extract parameters with defaults
  const { layout = "standard", theme = "light" } = params;

  // Render the content according to the parameters
  return (
    <div className={`hero-section layout-${layout} theme-${theme}`}>
      <h2>{title}</h2>
      {paragraphs.map((paragraph, index) => (
        <p key={index} dangerouslySetInnerHTML={{ __html: paragraph }} />
      ))}
      {images.length > 0 && (
        <img
          src={images[0].src}
          alt={images[0].alt || ""}
          className="hero-image"
        />
      )}
    </div>
  );
}

// Initialize block defaults for persistent state
HeroSection.blockDefaults = {
  state: {
    supportsOverlay: true,
  },
};

export default HeroSection;
```

> **Important**: `ComponentName.blockDefaults` is runtime initialization for persistent block instances. It is different from `component.config.js`, which is build-time schema metadata used by content creators and visual editors.

### Understanding the Content Object

The `content` object contains structured data parsed from markdown:

```javascript
{
  // Main content (from H1 and following paragraphs)
  main: {
    title: "Welcome to Our Platform",
    paragraphs: ["Discover how our innovative solutions..."],
    images: [{
      src: "/images/hero.jpg",
      alt: "Hero Image",
      attributes: { background: true }
    }],
    links: [{
      text: "Get Started",
      url: "#getting-started",
      attributes: { "button-primary": true }
    }],
    list: [] // Any lists in the main content
  },

  // Content groups from H2 headings
  items: [
    {
      title: "Feature One",
      paragraphs: ["Feature one description..."],
      images: [],
      links: [],
      list: []
    },
    {
      title: "Feature Two",
      paragraphs: ["Feature two description..."],
      images: [],
      links: [],
      list: []
    }
  ]
}
```

The Uniweb RTE automatically structures the markdown content into this standardized object. This consistent structure makes it easy to access content in your components without having to parse markdown yourself.

### Working with Parameters

Parameters come from the front matter in markdown files:

```markdown
---
component: HeroSection
layout: centered
theme: dark
showButton: true
---
```

In your component, extract and use these parameters:

```jsx
function HeroSection({ content, params, block }) {
  // Extract with defaults
  const { layout = "standard", theme = "light", showButton = false } = params;

  return (
    <div className={`hero layout-${layout} theme-${theme}`}>
      <h1>{content.main.title}</h1>
      {showButton && <button>Get Started</button>}
    </div>
  );
}
```

**Best practices for parameters:**

- Always provide sensible defaults
- Use semantic names (`theme: "dark"` not `backgroundColor: "#333"`)
- Define parameters in `component.config.js` for validation and documentation

### Understanding the Block Object

The `block` object provides runtime context and utilities:

```jsx
function ComponentWithChildren({ content, params, block }) {
  // Access child blocks (from nested sections)
  const childBlocks = block.getChildBlocks();

  // Get block metadata
  const blockId = block.id;
  const blockType = block.type;

  // Access parent block
  const parentBlock = block.getParentBlock();

  return (
    <div>
      <h2>{content.main.title}</h2>
      {childBlocks.map((childBlock, index) => (
        <div key={index}>
          {/* Render child blocks */}
          {childBlock.render()}
        </div>
      ))}
    </div>
  );
}
```

**Common block methods:**

- `block.getChildBlocks()` - Get child blocks for parent-child relationships
- `block.getParentBlock()` - Get the parent block
- `block.render()` - Render a block (useful for child blocks)
- `block.id` - Unique identifier for this block instance
- `block.type` - Component type name

### Working with Structured Data

For content that doesn't map naturally to markdown, content creators can use JSON blocks:

````markdown
```json #team-member
{
  "name": "Sarah Chen",
  "role": "Lead Architect",
  "bio": "10+ years building distributed systems",
  "avatar": "/assets/sarah.jpg"
}
```
````

Access this structured data in your component:

```jsx
function TeamMember({ content, params, block }) {
  // Get structured data by schema reference
  const member = block.getBlockData("#team-member");

  return (
    <div className="team-member">
      <img src={member.avatar} alt={member.name} />
      <h3>{member.name}</h3>
      <p className="role">{member.role}</p>
      <p className="bio">{member.bio}</p>
    </div>
  );
}
```

The `#team-member` hashbang references a content schema that validates structure and enables visual editor form UIs.

## Component Development Patterns

### Using Internal Components

One of the most powerful patterns is using standard React components for rendering:

```jsx
// Exposed component that content creators can select
function TeamSection({ content, params, block }) {
  // Extract parameters
  const { columns = 3, layout = "grid", cardStyle = "standard" } = params;

  // Select the appropriate internal component
  const CardComponent =
    cardStyle === "minimal" ? MinimalTeamCard : StandardTeamCard;

  return (
    <div className={`team-section layout-${layout}`}>
      <h2>{content.main.title}</h2>
      <p className="introduction">{content.main.paragraphs[0]}</p>

      <div className={`team-grid cols-${columns}`}>
        {content.items.map((item, index) => (
          <CardComponent
            key={index}
            name={item.title}
            role={item.subtitle}
            bio={item.paragraphs[0]}
            image={item.images[0]}
          />
        ))}
      </div>
    </div>
  );
}

// Internal component - standard React, no special interface
function StandardTeamCard({ name, role, bio, image }) {
  return (
    <div className="team-member-card standard">
      {image && (
        <div className="member-image">
          <img src={image.src} alt={image.alt || name} />
        </div>
      )}
      <h3>{name}</h3>
      <p className="role">{role}</p>
      <p className="bio">{bio}</p>
    </div>
  );
}

// Alternate internal component with different design
function MinimalTeamCard({ name, role }) {
  return (
    <div className="team-member-card minimal">
      <div className="card-content">
        <h3>{name}</h3>
        <p className="role">{role}</p>
      </div>
    </div>
  );
}
```

In this pattern:

- `TeamSection` is an exposed component that content creators select
- `StandardTeamCard` and `MinimalTeamCard` are regular React components
- The RTE handles the exposed component's interface and content transformation
- The internal components focus on rendering and can use any standard React patterns

This separation allows you to:

- Use the full power of React's component model
- Create specialized internal components for different rendering needs
- Switch between internal implementations based on parameters
- Reuse internal components across multiple exposed components

### Creating Component Presets

Presets provide pre-configured component options:

```js
// Define presets in component.config.js
export const presets = [
  {
    name: "standard",
    label: "Standard Grid",
    description: "Default grid layout with 3 columns",
    settings: {
      layout: "grid",
      columns: 3,
      showBio: true,
    },
  },
  {
    name: "compact",
    label: "Compact List",
    description: "Compact list view without bios",
    settings: {
      layout: "list",
      showBio: false,
    },
  },
  {
    name: "featured",
    label: "Featured Team",
    description: "Highlight key team members",
    settings: {
      layout: "grid",
      columns: 1,
      showBio: true,
      showSocial: true,
      size: "large",
    },
  },
];
```

Content creators can then use presets in their front matter:

```markdown
---
component: TeamSection
preset: compact
---
```

### Parent-Child Component Relationships

Components can work together through parent-child relationships:

```jsx
// Parent component
function TabContainer({ content, params, block }) {
  const [activeTab, setActiveTab] = useState(0);
  const childBlocks = block.getChildBlocks();

  return (
    <div className="tab-container">
      <div className="tab-headers">
        {childBlocks.map((child, index) => (
          <button
            key={index}
            onClick={() => setActiveTab(index)}
            className={activeTab === index ? "active" : ""}
          >
            {child.content.main.title}
          </button>
        ))}
      </div>

      <div className="tab-content">{childBlocks[activeTab]?.render()}</div>
    </div>
  );
}

// Child component
function TabPanel({ content, params, block }) {
  return (
    <div className="tab-panel">
      <h3>{content.main.title}</h3>
      {content.main.paragraphs.map((p, i) => (
        <p key={i}>{p}</p>
      ))}
    </div>
  );
}
```

Content creators define the relationship in `page.yml`:

```yaml
sections:
  - tabs
    - panel1  # Child of tabs
    - panel2  # Child of tabs
    - panel3  # Child of tabs
```

### Interactive Components

Create components that respond to user interactions:

```jsx
function AccordionComponent({ content, params, block }) {
  const [openItem, setOpenItem] = useState(null);

  // Extract parameters with defaults
  const { allowMultiple = false, initialOpen = null } = params;

  // Set initial open item
  useEffect(() => {
    setOpenItem(initialOpen);
  }, [initialOpen]);

  // Toggle item open/closed
  const toggleItem = (index) => {
    if (allowMultiple) {
      setOpenItem((prevOpen) => {
        if (Array.isArray(prevOpen)) {
          return prevOpen.includes(index)
            ? prevOpen.filter((i) => i !== index)
            : [...prevOpen, index];
        }
        return [index];
      });
    } else {
      setOpenItem((prevOpen) => (prevOpen === index ? null : index));
    }
  };

  // Check if an item is open
  const isItemOpen = (index) => {
    if (allowMultiple && Array.isArray(openItem)) {
      return openItem.includes(index);
    }
    return openItem === index;
  };

  return (
    <div className="accordion">
      <h2>{content.main.title}</h2>

      {content.items.map((item, index) => (
        <div
          key={index}
          className={`accordion-item ${isItemOpen(index) ? "open" : "closed"}`}
        >
          <div className="accordion-header" onClick={() => toggleItem(index)}>
            <h3>{item.title}</h3>
            <span className="icon">{isItemOpen(index) ? "−" : "+"}</span>
          </div>

          {isItemOpen(index) && (
            <div className="accordion-content">
              {item.paragraphs.map((p, i) => (
                <p key={i}>{p}</p>
              ))}
            </div>
          )}
        </div>
      ))}
    </div>
  );
}
```

## Component Development Best Practices

1. **Start with the content structure**

   - Understand how content creators will structure their content
   - Design your component to handle common content patterns

2. **Use semantic parameters**

   - Create parameters based on meaning, not implementation
   - Example: Use `theme: "dark"` instead of `backgroundColor: "#333"`
   - This allows implementation to change without breaking content

3. **Provide sensible defaults**

   - Every parameter should have a reasonable default
   - Components should work without any explicit configuration

4. **Handle edge cases gracefully**

   - Missing content
   - Empty arrays
   - Invalid parameter values
   - No child blocks when expected

5. **Document thoroughly**

   - Write clear parameter descriptions in `component.config.js`
   - Provide usage examples
   - Document expected content structure

6. **Consider performance**

   - Use React's performance optimization tools (memo, useMemo, etc.)
   - Optimize rendering for components that might have many instances

7. **Make components composable**

   - Design components that work well together
   - Consider how components will interact through block state and parent-child relationships

8. **Keep exposed components focused**
   - Exposed components should primarily handle the content/code bridge
   - Move rendering logic to internal components
   - Use standard React patterns for internal components

## Testing and Development

### Development Server

Start the development server to test your components:

```bash
npx uniweb start
```

Visit `http://localhost:3000/sites/demo/` to see your test site.

### Creating Test Content

Create test content in your demo site to verify component behavior:

```bash
# Add a test page
npx uniweb page add test

# Add a section using your component
npx uniweb page section add test/hero

# Set the section content
npx uniweb page section set test/hero --content "---
component: HeroSection
layout: centered
---

# Test Hero

This is a test."
```

### Debugging Components

Create a debug component to inspect data:

```jsx
function DebugComponent({ content, params, block }) {
  return (
    <div
      style={{
        background: "#f5f5f5",
        padding: "20px",
        margin: "20px",
        fontFamily: "monospace",
      }}
    >
      <h3>Debug Information</h3>

      <details>
        <summary>Content Structure</summary>
        <pre>{JSON.stringify(content, null, 2)}</pre>
      </details>

      <details>
        <summary>Parameters</summary>
        <pre>{JSON.stringify(params, null, 2)}</pre>
      </details>

      <details>
        <summary>Block Info</summary>
        <pre>
          {JSON.stringify(
            {
              id: block.id,
              type: block.type,
              hasChildren: block.getChildBlocks().length > 0,
            },
            null,
            2
          )}
        </pre>
      </details>
    </div>
  );
}
```

## Publishing Your Foundation

Once your Foundation is ready, publish it to make it available to sites:

```bash
# Authenticate (creates account if needed)
npx uniweb login

# Publish your Foundation
cd src/my-foundation
npx uniweb module publish
```

Your Foundation will be available in the registry for content creators to use. See the [Deployment Guide](deployment-guide.md) for more details on publishing and versioning.

## Conclusion

Building Foundations for websites gives you the power to create reusable building blocks that content creators can use without writing code. By following the patterns and practices in this guide, you'll create components that are:

- **Flexible** - Adaptable to different content structures
- **Configurable** - Customizable through parameters and presets
- **Evolvable** - Able to grow without breaking existing content
- **Composable** - Work well together to create sophisticated websites

The separation between content and code gives both developers and content creators the freedom to work independently while creating cohesive websites.

Remember that great Foundation components focus on:

1. Creating a clear interface between content and presentation
2. Providing semantic parameters that make sense to content creators
3. Handling a variety of content structures gracefully
4. Evolving carefully to maintain backward compatibility

## Next Steps

**Continue learning:**

- [Understanding Uniweb](understanding-uniweb.md) - Deeper dive into the architecture
- [Content Creators Guide](content-creators-guide.md) - See how your components are used
- [Terminology Reference](terminology.md) - Key terms and definitions
- [Deployment Guide](deployment-guide.md) - Publishing your Foundation

**Get support:**

- [Framework Website](https://framework.uniweb.app) - Guides and resources
- [Documentation](https://docs.framework.uniweb.app) - Complete API reference
- [Examples](https://github.com/uniwebcms/examples) - Sample Foundations and components

By following these principles, you'll build Foundations that content creators will love to use.
