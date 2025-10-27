# Uniweb Framework: Visual Concepts

This document provides visual diagrams to help understand the core architectural concepts of Uniweb.

## 1. The Foundation Boundary: Exposed vs Internal Components

```mermaid
graph TB
    subgraph "Content Creator World"
        MD[Markdown Frontmatter]
        style MD fill:#e1f5ff
    end
    
    subgraph "Foundation Boundary"
        direction TB
        subgraph "Exposed Components (Content-Facing Interface)"
            HERO[HeroSection<br/>schema ✓]
            FEAT[FeatureGrid<br/>schema ✓]
            CTA[CallToAction<br/>schema ✓]
            style HERO fill:#4caf50
            style FEAT fill:#4caf50
            style CTA fill:#4caf50
        end
        
        subgraph "Internal Components (Developer Implementation)"
            BTN[Button]
            CARD[Card]
            ICON[Icon]
            GRID[GridLayout]
            NPM[npm packages<br/>UI libraries<br/>utilities]
            style BTN fill:#90caf9
            style CARD fill:#90caf9
            style ICON fill:#90caf9
            style GRID fill:#90caf9
            style NPM fill:#b0bec5
        end
        
        HERO --> BTN
        HERO --> ICON
        FEAT --> CARD
        FEAT --> GRID
        CTA --> BTN
        BTN --> NPM
        CARD --> NPM
    end
    
    MD -.references.-> HERO
    MD -.references.-> FEAT
    MD -.references.-> CTA
    
    classDef contentWorld fill:#e1f5ff,stroke:#0288d1
    classDef exposedComp fill:#4caf50,stroke:#2e7d32
    classDef internalComp fill:#90caf9,stroke:#1976d2
```

**Key insight:** Content creators only interact with exposed components (green). Internal components (blue) are pure React implementation details using standard npm packages and composition patterns.

## 2. Traditional Component Library vs Foundation

```mermaid
graph LR
    subgraph "Traditional Component Library"
        direction TB
        DEV1[Developer] -->|imports in code| LIB[Component Library]
        LIB -->|exports components| DEV2[Developer]
        DEV2 -->|writes JSX| APP[Application]
    end
    
    subgraph "Uniweb Foundation"
        direction TB
        DEV3[Developer] -->|builds| FOUND[Foundation]
        FOUND -->|exposes via schema| INT[Content Interface]
        CONTENT[Content Creator] -->|writes frontmatter| INT
        INT -->|renders| SITE[Site]
    end
    
    style LIB fill:#ffb74d
    style FOUND fill:#4caf50
    style INT fill:#4caf50,stroke:#2e7d32,stroke-width:3px
```

**Key insight:** Traditional libraries have developer-to-developer interfaces. Foundations have content-facing interfaces designed for declarative composition.

## 3. Content Flow: From Markdown to Rendered Site

```mermaid
sequenceDiagram
    participant CC as Content Creator
    participant MD as Markdown File
    participant FW as Uniweb Framework
    participant FOUND as Foundation
    participant SITE as Rendered Site
    
    CC->>MD: Writes content with frontmatter
    Note over MD: ---<br/>component: HeroSection<br/>layout: centered<br/>---
    
    MD->>FW: Content at runtime
    FW->>FW: Parse frontmatter<br/>Extract component reference
    FW->>FOUND: Request HeroSection component
    
    alt Component exists in schema
        FOUND->>FW: Returns component
        FW->>FW: Validate options<br/>Preprocess content
        FW->>FOUND: Render with processed data
        FOUND->>SITE: Rendered component
    else Component not in schema
        FW->>CC: Build error:<br/>Component not exposed
    end
```

**Key insight:** The Framework acts as the bridge, validating that content references exist in the Foundation's schema before rendering.

## 4. Foundation Serving Multiple Sites

```mermaid
graph TB
    DEV[Developer]
    
    subgraph FOUND["Foundation (Single Source of Truth)"]
        direction TB
        COMP[Components]
        SCHEMA[Schemas]
        THEME[Themes]
        style FOUND fill:#4caf50,stroke:#2e7d32,stroke-width:3px
    end
    
    DEV -->|builds & updates| FOUND
    
    FOUND -->|Module Federation| SITE1[Site A<br/>version: latest]
    FOUND -->|Module Federation| SITE2[Site B<br/>version: ^1.2.0]
    FOUND -->|Module Federation| SITE3[Site C<br/>version: 1.1.5]
    
    style SITE1 fill:#90caf9
    style SITE2 fill:#90caf9
    style SITE3 fill:#90caf9
    
    CONTENT1[Content Team A] -.edits.-> SITE1
    CONTENT2[Content Team B] -.edits.-> SITE2
    CONTENT3[Content Team C] -.edits.-> SITE3
```

**Key insight:** One Foundation serves multiple sites. Each site controls its update strategy through version configuration. Updates propagate at runtime without site redeployment.

## 5. The Two Worlds Connected by Foundation

```mermaid
graph TD
    subgraph "Developer World"
        direction TB
        REACT[React Components]
        NPM[npm Packages]
        JS[JavaScript/TypeScript]
        BUILD[Build Tools]
        REACT --> CODE[Code Implementation]
        NPM --> CODE
        JS --> CODE
        BUILD --> CODE
        style REACT fill:#61dafb
        style NPM fill:#cb3837
        style JS fill:#f7df1e
        style BUILD fill:#8dd6f9
    end
    
    subgraph "Foundation Bridge"
        CODE --> EXPOSED[Exposed Components<br/>with Schemas]
        EXPOSED --> INTERFACE[Content-Facing Interface]
        style EXPOSED fill:#4caf50,stroke:#2e7d32,stroke-width:3px
        style INTERFACE fill:#4caf50,stroke:#2e7d32,stroke-width:3px
    end
    
    subgraph "Content Creator World"
        direction TB
        INTERFACE --> FRONT[Frontmatter Configuration]
        INTERFACE --> VISUAL[Visual Editor]
        FRONT --> PAGES[Composed Pages]
        VISUAL --> PAGES
        PAGES --> PUBLISH[Published Sites]
        style FRONT fill:#e1f5ff
        style VISUAL fill:#e1f5ff
        style PAGES fill:#e1f5ff
        style PUBLISH fill:#81c784
    end
```

**Key insight:** Foundations bridge two worlds. Developers work with code and npm packages. Content creators work with declarative configuration and visual editors. The schema-defined interface connects them.

## 6. Schema-Driven Architecture

```mermaid
graph LR
    subgraph "Component Schema"
        direction TB
        NAME[Component Name]
        PARAMS[Parameters]
        PRESETS[Presets]
        VALID[Validation Rules]
        style NAME fill:#4caf50
        style PARAMS fill:#4caf50
        style PRESETS fill:#4caf50
        style VALID fill:#4caf50
    end
    
    NAME -->|enables| REF[Content References<br/>component: HeroSection]
    PARAMS -->|enables| CONF[Frontmatter Config<br/>layout: centered]
    PRESETS -->|enables| QUICK[Quick Start Templates]
    VALID -->|enables| CHECK[Build Validation]
    
    REF --> USE1[Markdown Composition]
    CONF --> USE1
    QUICK --> USE2[Visual Editor UI]
    CHECK --> USE3[Type Safety]
    
    style USE1 fill:#e1f5ff
    style USE2 fill:#e1f5ff
    style USE3 fill:#ffb74d
```

**Key insight:** The component schema is the contract. It defines what content creators can reference, configure, and validates at build time. It also powers visual editor integration.

## 7. Update Propagation Flow

```mermaid
sequenceDiagram
    participant DEV as Developer
    participant FOUND as Foundation
    participant REG as Module Federation
    participant SITE1 as Site (auto)
    participant SITE2 as Site (minor)
    participant SITE3 as Site (pinned)
    
    DEV->>FOUND: Update component<br/>v1.2.3 → v1.3.0
    FOUND->>REG: Publish v1.3.0
    
    Note over SITE1: version: "latest"
    REG->>SITE1: Load v1.3.0<br/>✓ Immediate update
    
    Note over SITE2: version: "^1.2.0"
    REG->>SITE2: Load v1.3.0<br/>✓ Minor update allowed
    
    Note over SITE3: version: "1.2.3"
    REG->>SITE3: Load v1.2.3<br/>⊘ Pinned version
    
    DEV->>FOUND: Update component<br/>v1.3.0 → v2.0.0
    FOUND->>REG: Publish v2.0.0
    
    REG->>SITE1: Load v2.0.0<br/>✓ Immediate update
    REG->>SITE2: Load v1.3.0<br/>⊘ Major blocked
    REG->>SITE3: Load v1.2.3<br/>⊘ Pinned version
```

**Key insight:** Sites control how updates propagate through version strategies. Module Federation loads the appropriate version at runtime—no site redeployment needed.

## 8. Progressive Complexity Spectrum

```mermaid
graph LR
    subgraph "Simple"
        direction TB
        S1[Single Site]
        S2[Hardcoded Components]
        S3[Minimal Schemas<br/>name only]
        S4[Developer-Only Team]
        style S1 fill:#c8e6c9
        style S2 fill:#c8e6c9
        style S3 fill:#c8e6c9
        style S4 fill:#c8e6c9
    end
    
    subgraph "Intermediate"
        direction TB
        M1[Few Related Sites]
        M2[Parameterized Components]
        M3[Schema Options<br/>+ validation]
        M4[Small Content Team]
        style M1 fill:#fff59d
        style M2 fill:#fff59d
        M3 fill:#fff59d
        M4 fill:#fff59d
    end
    
    subgraph "Complex"
        direction TB
        C1[Many Client Sites]
        C2[Design System]
        C3[Rich Schemas<br/>+ presets + theming]
        C4[Large Content Teams<br/>+ Visual Editor]
        style C1 fill:#ffab91
        style C2 fill:#ffab91
        style C3 fill:#ffab91
        style C4 fill:#ffab91
    end
    
    S1 --> M1
    M1 --> C1
    S2 --> M2
    M2 --> C2
    S3 --> M3
    M3 --> C3
    S4 --> M4
    M4 --> C4
```

**Key insight:** Start simple (single site, minimal schemas) and grow as needs evolve. The framework supports the entire spectrum without requiring architectural rewrites.

---

## Summary

These diagrams illustrate the core architectural principles of Uniweb:

1. **Boundary-based design**: Clear separation between exposed (content-facing) and internal (developer) components
2. **Dual interfaces**: Traditional code interfaces for internal development, declarative interfaces for content creators
3. **Schema as contract**: Component schemas define the content-facing API and enable validation and tooling
4. **Single source of truth**: One Foundation serves many sites with controlled update propagation
5. **Two-world bridge**: Connects the developer ecosystem (React, npm) with the content creator ecosystem (markdown, visual editors)
6. **Progressive complexity**: Supports everything from simple single-site projects to enterprise design systems

The Foundation is the bridge that connects these concepts, providing a content-facing interface while maintaining standard React development practices internally.
