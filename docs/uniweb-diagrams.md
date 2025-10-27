# Uniweb Framework: Visual Concepts

This document provides visual diagrams to help understand the core architectural concepts of Uniweb.

## 1. The Foundation Boundary: Exposed vs Internal Components

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#10b981','primaryTextColor':'#fff','primaryBorderColor':'#059669','lineColor':'#6b7280','secondaryColor':'#3b82f6','tertiaryColor':'#e0f2fe','fontSize':'14px'}}}%%
graph TB
    subgraph CC["🎨 Content Creator World"]
        MD[Markdown<br/>Frontmatter]
        style MD fill:#e0f2fe,stroke:#0ea5e9,stroke-width:2px,color:#0c4a6e
    end

    subgraph FB["🏛️ Foundation Boundary"]
        direction TB
        subgraph EC["✨ Exposed Components"]
            HERO[Hero<br/>✓ schema]
            FEAT[Features<br/>✓ schema]
            CTA[CTA<br/>✓ schema]
            style HERO fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
            style FEAT fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
            style CTA fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
        end

        subgraph IC["⚙️ Internal Components"]
            BTN[Button]
            CARD[Card]
            ICON[Icon]
            GRID[Grid]
            NPM[npm libs<br/>& tools]
            style BTN fill:#3b82f6,stroke:#1d4ed8,stroke-width:2px,color:#fff
            style CARD fill:#3b82f6,stroke:#1d4ed8,stroke-width:2px,color:#fff
            style ICON fill:#3b82f6,stroke:#1d4ed8,stroke-width:2px,color:#fff
            style GRID fill:#3b82f6,stroke:#1d4ed8,stroke-width:2px,color:#fff
            style NPM fill:#64748b,stroke:#475569,stroke-width:2px,color:#fff
        end

        HERO --> BTN
        HERO --> ICON
        FEAT --> CARD
        FEAT --> GRID
        CTA --> BTN
        BTN --> NPM
        CARD --> NPM
    end

    MD -.uses.-> HERO
    MD -.uses.-> FEAT
    MD -.uses.-> CTA

    style CC fill:#f0f9ff,stroke:#0ea5e9,stroke-width:2px
    style FB fill:#fafafa,stroke:#a3a3a3,stroke-width:2px,stroke-dasharray: 5 5
    style EC fill:#ecfdf5,stroke:#059669,stroke-width:2px
    style IC fill:#eff6ff,stroke:#1d4ed8,stroke-width:2px
```

**💡 Key insight:** Content creators only interact with exposed components (green). Internal components (blue) are implementation details using standard npm packages.

## 2. Traditional Component Library vs Foundation

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#f59e0b','secondaryColor':'#10b981','tertiaryColor':'#e0f2fe','fontSize':'14px','edgeLabelBackground':'rgba(255, 255, 255, 0.7)'}}}%%
graph LR
    subgraph TL["📦 Traditional Library"]
        direction TB
        DEV1[Dev] -->|import| LIB[Library]
        LIB -->|export| DEV2[Dev]
        DEV2 -->|JSX| APP[App]
        style LIB fill:#f59e0b,stroke:#d97706,stroke-width:2px,color:#fff
        style DEV1 fill:#fef3c7,stroke:#f59e0b,stroke-width:2px,color:#78350f
        style DEV2 fill:#fef3c7,stroke:#f59e0b,stroke-width:2px,color:#78350f
        style APP fill:#fed7aa,stroke:#ea580c,stroke-width:2px,color:#7c2d12
    end

    subgraph UF["✨ Uniweb Foundation"]
        direction TB
        DEV3[Dev] -->|build| FOUND[Foundation]
        FOUND -->|expose| INT[Interface]
        CONTENT[Creator] -->|config| INT
        INT -->|render| SITE[Site]
        style FOUND fill:#10b981,stroke:#059669,stroke-width:3px,color:#fff
        style INT fill:#10b981,stroke:#059669,stroke-width:3px,color:#fff
        style DEV3 fill:#d1fae5,stroke:#10b981,stroke-width:2px,color:#064e3b
        style CONTENT fill:#e0f2fe,stroke:#0ea5e9,stroke-width:2px,color:#0c4a6e
        style SITE fill:#6ee7b7,stroke:#059669,stroke-width:2px,color:#064e3b
    end

    style TL fill:#fffbeb,stroke:#f59e0b,stroke-width:2px
    style UF fill:#ecfdf5,stroke:#10b981,stroke-width:2px
```

**💡 Key insight:** Traditional libraries have dev-to-dev interfaces. Foundations have content-facing interfaces for declarative composition.

## 3. Content Flow: From Markdown to Rendered Site

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'actorBkg':'#e0f2fe','actorBorder':'#0ea5e9','actorTextColor':'#0c4a6e','noteBkgColor':'#fef3c7','noteTextColor':'#78350f','activationBkgColor':'#10b981','fontSize':'13px'}}}%%
sequenceDiagram
    participant CC as 👤 Creator
    participant MD as 📝 Markdown
    participant FW as ⚡ Framework
    participant FD as 🏛️ Foundation
    participant ST as 🌐 Site

    CC->>MD: Write content
    Note over MD: component: Hero<br/>layout: centered

    MD->>FW: Parse
    FW->>FW: Extract ref
    FW->>FD: Request Hero

    alt ✅ Valid
        FD->>FW: Component
        FW->>FW: Validate
        FW->>FD: Render
        FD->>ST: Output
    else ❌ Invalid
        FW->>CC: Error:<br/>Not exposed
    end
```

**💡 Key insight:** The Framework validates that content references exist in the Foundation's schema before rendering.

## 4. Foundation Serving Multiple Sites

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#10b981','secondaryColor':'#3b82f6','fontSize':'14px','edgeLabelBackground':'#fff'}}}%%
graph TB
    DEV[👨‍💻 Dev]
    style DEV fill:#64748b,stroke:#475569,stroke-width:2px,color:#fff

    subgraph FD["🏛️ Foundation"]
        COMP[Components]
        SCHEMA[Schemas]
        THEME[Themes]
        style COMP fill:#d1fae5,stroke:#10b981,stroke-width:2px,color:#064e3b
        style SCHEMA fill:#d1fae5,stroke:#10b981,stroke-width:2px,color:#064e3b
        style THEME fill:#d1fae5,stroke:#10b981,stroke-width:2px,color:#064e3b
    end

    style FD fill:#10b981,stroke:#059669,stroke-width:3px,color:#fff

    DEV -->|build & update| FD

    FD -->|Module Fed| S1[Site A<br/>latest]
    FD -->|Module Fed| S2[Site B<br/>^1.2.0]
    FD -->|Module Fed| S3[Site C<br/>1.1.5]

    style S1 fill:#3b82f6,stroke:#1d4ed8,stroke-width:2px,color:#fff
    style S2 fill:#3b82f6,stroke:#1d4ed8,stroke-width:2px,color:#fff
    style S3 fill:#3b82f6,stroke:#1d4ed8,stroke-width:2px,color:#fff

    T1[Team A] -.edit.-> S1
    T2[Team B] -.edit.-> S2
    T3[Team C] -.edit.-> S3

    style T1 fill:#e0f2fe,stroke:#0ea5e9,stroke-width:2px,color:#0c4a6e
    style T2 fill:#e0f2fe,stroke:#0ea5e9,stroke-width:2px,color:#0c4a6e
    style T3 fill:#e0f2fe,stroke:#0ea5e9,stroke-width:2px,color:#0c4a6e
```

**💡 Key insight:** One Foundation serves multiple sites. Each controls its update strategy via version config. Updates propagate at runtime without redeployment.

## 5. The Two Worlds Connected by Foundation

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#10b981','fontSize':'14px'}}}%%
graph TD
    subgraph DW["💻 Developer World"]
        direction TB
        REACT[React]
        NPM[npm]
        JS[JS/TS]
        BUILD[Tools]
        REACT --> CODE[Code]
        NPM --> CODE
        JS --> CODE
        BUILD --> CODE
        style REACT fill:#61dafb,stroke:#0891b2,stroke-width:2px
        style NPM fill:#dc2626,stroke:#991b1b,stroke-width:2px,color:#fff
        style JS fill:#f59e0b,stroke:#d97706,stroke-width:2px
        style BUILD fill:#8b5cf6,stroke:#6d28d9,stroke-width:2px,color:#fff
        style CODE fill:#64748b,stroke:#475569,stroke-width:2px,color:#fff
    end

    subgraph BR["🌉 Foundation Bridge"]
        CODE --> EXP[Exposed<br/>Components]
        EXP --> INT[Interface]
        style EXP fill:#10b981,stroke:#059669,stroke-width:3px,color:#fff
        style INT fill:#10b981,stroke:#059669,stroke-width:3px,color:#fff
    end

    subgraph CW["✍️ Content World"]
        direction TB
        INT --> SEL[Select]
        INT --> VIS[Editor]
        SEL --> PAGE[Pages]
        VIS --> PAGE
        PAGE --> PUB[Sites]
        style SEL fill:#bfdbfe,stroke:#3b82f6,stroke-width:2px,color:#1e3a8a
        style VIS fill:#bfdbfe,stroke:#3b82f6,stroke-width:2px,color:#1e3a8a
        style PAGE fill:#93c5fd,stroke:#2563eb,stroke-width:2px,color:#1e3a8a
        style PUB fill:#6ee7b7,stroke:#10b981,stroke-width:2px,color:#064e3b
    end

    style DW fill:#f8fafc,stroke:#64748b,stroke-width:2px,color:#000
    style BR fill:#0ca06f,stroke:#059669,stroke-width:3px,color:#fff
    style CW fill:#eff6ff,stroke:#3b82f6,stroke-width:2px,color:#000
```

**💡 Key insight:** Foundations bridge developers (code/npm) and creators (config/editors). The schema-defined interface connects them.

## 6. Schema-Driven Architecture

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#10b981','secondaryColor':'#3b82f6','fontSize':'14px','edgeLabelBackground':'rgba(255, 255, 255, 0.8)'}}}%%
graph LR
    subgraph SC["📋 Schema"]
        direction TB
        N[Name]
        P[Params]
        PR[Presets]
        V[Rules]
        style N fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
        style P fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
        style PR fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
        style V fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
    end

    N -->|enable| R[References]
    P -->|enable| C[Config]
    PR -->|enable| Q[Templates]
    V -->|enable| CH[Validation]

    R --> U1[Markdown]
    C --> U1
    Q --> U2[Editor UI]
    CH --> U3[Safety]

    style R fill:#6ee7b7,stroke:#10b981,stroke-width:2px,color:#064e3b
    style C fill:#6ee7b7,stroke:#10b981,stroke-width:2px,color:#064e3b
    style Q fill:#6ee7b7,stroke:#10b981,stroke-width:2px,color:#064e3b
    style CH fill:#6ee7b7,stroke:#10b981,stroke-width:2px,color:#064e3b

    style U1 fill:#bfdbfe,stroke:#3b82f6,stroke-width:2px,color:#1e3a8a
    style U2 fill:#bfdbfe,stroke:#3b82f6,stroke-width:2px,color:#1e3a8a
    style U3 fill:#fcd34d,stroke:#f59e0b,stroke-width:2px,color:#78350f

    style SC fill:#ecfdf5,stroke:#10b981,stroke-width:2px
```

**💡 Key insight:** The schema is the contract. It defines what creators can reference, configure, and validates at build time. Powers editor integration.

## 7. Update Propagation Flow

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'actorBkg':'#e0f2fe','noteBkgColor':'#ecfdf5','activationBkgColor':'#10b981','fontSize':'13px'}}}%%
sequenceDiagram
    participant D as 👨‍💻 Dev
    participant F as 🏛️ Found
    participant M as 📦 ModFed
    participant A as 🟢 Auto
    participant Mi as 🟡 Minor
    participant P as 🔴 Pin

    Note over D,F: Phase 1: Minor v1.3.0
    D->>F: Update
    F->>M: Publish

    Note over A: latest
    M->>A: ✅ v1.3.0

    Note over Mi: ^1.2.0
    M->>Mi: ✅ v1.3.0

    Note over P: 1.2.3
    M->>P: 📌 v1.2.3

    Note over D,F: Phase 2: Major v2.0.0
    D->>F: Update
    F->>M: Publish

    M->>A: ✅ v2.0.0
    M->>Mi: ⛔ v1.3.0
    M->>P: 📌 v1.2.3
```

**💡 Key insight:** Sites control update propagation via version strategies. Module Federation loads appropriate versions at runtime—no redeployment needed.

## 8. Progressive Complexity Spectrum

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'fontSize':'14px'}}}%%
graph LR
    subgraph S["🌱 Simple"]
        direction TB
        S1[Single Site]
        S2[Hardcoded]
        S3[Min Schema]
        S4[Dev Only]
        style S1 fill:#d1fae5,stroke:#10b981,stroke-width:2px,color:#064e3b
        style S2 fill:#d1fae5,stroke:#10b981,stroke-width:2px,color:#064e3b
        style S3 fill:#d1fae5,stroke:#10b981,stroke-width:2px,color:#064e3b
        style S4 fill:#d1fae5,stroke:#10b981,stroke-width:2px,color:#064e3b
    end

    subgraph M["🌿 Intermediate"]
        direction TB
        M1[Few Sites]
        M2[Params]
        M3[Options]
        M4[Small Team]
        style M1 fill:#fef3c7,stroke:#f59e0b,stroke-width:2px,color:#78350f
        style M2 fill:#fef3c7,stroke:#f59e0b,stroke-width:2px,color:#78350f
        style M3 fill:#fef3c7,stroke:#f59e0b,stroke-width:2px,color:#78350f
        style M4 fill:#fef3c7,stroke:#f59e0b,stroke-width:2px,color:#78350f
    end

    subgraph C["🌳 Complex"]
        direction TB
        C1[Many Sites]
        C2[System]
        C3[Rich]
        C4[Large Team]
        style C1 fill:#fecaca,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
        style C2 fill:#fecaca,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
        style C3 fill:#fecaca,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
        style C4 fill:#fecaca,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
    end

    S1 --> M1
    M1 --> C1
    S2 --> M2
    M2 --> C2
    S3 --> M3
    M3 --> C3
    S4 --> M4
    M4 --> C4

    style S fill:#ecfdf5,stroke:#10b981,stroke-width:2px
    style M fill:#fffbeb,stroke:#f59e0b,stroke-width:2px
    style C fill:#fee2e2,stroke:#dc2626,stroke-width:2px
```

**💡 Key insight:** Start simple and grow. The framework supports the entire spectrum without architectural rewrites.

---

## Summary

These diagrams illustrate the core architectural principles of Uniweb:

1. **🏗️ Boundary-based design**: Clear separation between exposed (content-facing) and internal (developer) components
2. **🔄 Dual interfaces**: Traditional code interfaces for internal development, declarative interfaces for content creators
3. **📋 Schema as contract**: Component schemas define the content-facing API and enable validation and tooling
4. **🎯 Single source of truth**: One Foundation serves many sites with controlled update propagation
5. **🌉 Two-world bridge**: Connects the developer ecosystem (React, npm) with the content creator ecosystem (markdown, visual editors)
6. **📈 Progressive complexity**: Supports everything from simple single-site projects to enterprise design systems

**The Foundation is the bridge** that connects these concepts, providing a content-facing interface while maintaining standard React development practices internally.
