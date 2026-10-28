# 06. Design Systems, Token Architecture & Atomic Systems

> "We're not designing pages; we're designing systems of components."  
> — Brad Frost (*Atomic Design*, 2013)

---

## 1. Atomic Design Methodology (Brad Frost)

To build software that scales across thousands of engineers and millions of users without visual degradation, interfaces are engineered in five hierarchical layers:

```
┌───────────┐      ┌─────────────┐      ┌───────────────┐      ┌─────────────┐      ┌─────────┐
│   ATOMS   │ ───► │  MOLECULES  │ ───► │   ORGANISMS   │ ───► │  TEMPLATES  │ ───► │  PAGES  │
└───────────┘      └─────────────┘      └───────────────┘      └─────────────┘      └─────────┘
  Buttons            Search Form          Global Header          Wireframe            Real Data
  Inputs             (Input + Button)     (Logo + Nav + Search)  Page Layout          Instantiated
  Labels                                                         Structure            Interface
```

1. **Atoms:** Foundational HTML elements and design primitives (labels, inputs, buttons, color tokens, icons). Incapable of being broken down further without losing utility.
2. **Molecules:** Simple groups of UI elements acting together as a discrete unit (e.g., a search form combining a label atom, an input atom, and a button atom).
3. **Organisms:** Complex, self-contained components formed by combinations of molecules and atoms (e.g., a global header containing a logo, navigation links, and search form).
4. **Templates:** Page-level layout skeletons that arrange organisms, defining underlying grid, responsive flow, and content slots without concrete data.
5. **Pages:** Specific instances of templates populated with production data, edge-case content, long strings, localized text, and dynamic states.

---

## 2. Comparative Analysis of World-Class Design Systems

Modern design systems converge on shared principles while diverging in aesthetic expression and density:

| System | Origin / Core Domain | Signature Architectural Trait | Elevation & Depth Strategy |
|---|---|---|---|
| **Google Material 3 (Material You)** | Consumer Android / Web / ChromeOS | Dynamic color extraction from user wallpaper; tonal palettes. | Surface tonal tinting rather than harsh shadows. |
| **Microsoft Fluent 2** | Enterprise Windows / Office / Teams | Mica & Acrylic translucency; cross-platform cohesion. | Multi-layer light-refracting acrylic materials + drop shadows. |
| **IBM Carbon** | Enterprise Cloud / Data / Dev Tools | Strict 2x grid, high density, stark technical aesthetic. | 0px border lines, dark contrast layers, zero decorative fluff. |
| **Shopify Polaris** | E-commerce / Merchant Workflows | Task-oriented, ruthless operational clarity, high-trust forms. | Subdued surface cards, distinct high-contrast badge statuses. |
| **Atlassian Design System** | Collaboration (Jira / Confluence) | Status lozenges, avatar stacks, contextual inline edit. | Layered neutral surfaces with subtle border demarcations. |

---

## 3. The 3-Tier Design Token Architecture

Design tokens are the single source of truth for all visual values across Web (CSS/JS), iOS (Swift), Android (Kotlin), and Design (Figma).

```
Tier 1: GLOBAL TOKENS (Primitives)
  └─ Defines raw values without semantic intent:
     { "blue-500": "#2563eb", "space-16": "16px", "radius-8": "8px" }

Tier 2: SEMANTIC TOKENS (System Intent)
  └─ References Tier 1 tokens, assigning functional meaning:
     { "color-bg-interactive": "{blue-500}", "color-text-danger": "{red-600}" }

Tier 3: COMPONENT TOKENS (Scoped Context)
  └─ References Tier 2 tokens, scoped strictly to a component:
     { "button-primary-bg": "{color-bg-interactive}", "dialog-radius": "{radius-12}" }
```

### Invariant:
Application code and frontend components must **never reference Tier 1 Global Tokens directly**. Referencing raw `#2563eb` or `blue-500` breaks dark mode and theme switching. UI components must bind exclusively to **Tier 2 (Semantic)** or **Tier 3 (Component)** tokens.

---

## 4. W3C Design Tokens Community Group (DTCG) Specification

The standard cross-platform JSON token representation:

```json
{
  "color": {
    "primitive": {
      "blue": {
        "500": {
          "$value": "#2563eb",
          "$type": "color",
          "$description": "Brand blue primary base tone"
        },
        "600": {
          "$value": "#1d4ed8",
          "$type": "color"
        }
      },
      "neutral": {
        "0": { "$value": "#ffffff", "$type": "color" },
        "900": { "$value": "#0f172a", "$type": "color" }
      }
    },
    "semantic": {
      "surface": {
        "base": {
          "$value": "{color.primitive.neutral.0}",
          "$type": "color"
        },
        "overlay": {
          "$value": "{color.primitive.neutral.0}",
          "$type": "color"
        }
      },
      "interactive": {
        "default": {
          "$value": "{color.primitive.blue.500}",
          "$type": "color"
        },
        "hover": {
          "$value": "{color.primitive.blue.600}",
          "$type": "color"
        }
      }
    }
  },
  "spacing": {
    "1": { "$value": "4px", "$type": "dimension" },
    "2": { "$value": "8px", "$type": "dimension" },
    "4": { "$value": "16px", "$type": "dimension" },
    "8": { "$value": "32px", "$type": "dimension" }
  },
  "radii": {
    "sm": { "$value": "4px", "$type": "dimension" },
    "md": { "$value": "8px", "$type": "dimension" },
    "lg": { "$value": "16px", "$type": "dimension" },
    "full": { "$value": "9999px", "$type": "dimension" }
  }
}
```

---

## 5. Token Compilation Pipeline

Using Style Dictionary or custom toolchains, DTCG JSON tokens are compiled down to targeted platform artifacts:
* **Web:** CSS Custom Properties (`:root { --color-interactive-default: #2563eb; }`)
* **iOS:** Swift Structs / Enums (`public static let colorInteractiveDefault = Color(...)`)
* **Android:** Kotlin Jetpack Compose Theme (`val ColorInteractiveDefault = Color(0xFF2563EB)`)
* **Tailwind CSS:** `theme.extend` token mappings.
