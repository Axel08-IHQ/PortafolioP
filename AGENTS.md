# AGENTS.md - Developer Guide for Codefolio

## Project Overview
Astro 5.x + TypeScript static portfolio site with ViewTransitions for smooth SPA-like navigation.

## Build, Lint & Test Commands

### npm Scripts
| Command | Description |
|---------|-------------|
| `npm run dev` | Dev server at http://localhost:4321 |
| `npm run build` | Production build |
| `npm run preview` | Preview production build |
| `npx astro check` | Type checking |
| `npx astro sync` | Generate Astro types |

### Testing
No test framework configured. To add tests:
```bash
npm install -D vitest
```

Add to `package.json`:
```json
"scripts": {
  "test": "vitest",
  "test:run": "vitest run"
}
```

Run tests:
```bash
npm run test              # Watch mode
npm run test:run         # Single run
npm run test -- --run src/components/Project.test.js  # Specific file
```

### Linting
No linting configured. Recommended:
```bash
npm install -D eslint @astrojs/eslint-plugin
```

## Code Style Guidelines

### File Structure
```
src/
├── components/   # Reusable .astro components
├── layouts/      # Page layouts
├── pages/        # Route .astro files
└── env.d.ts      # TypeScript declarations
```

### Astro Component Pattern
```astro
---
import { ViewTransitions } from 'astro:transitions';
interface Props {
  title: string;
  description?: string;
}
const { title, description = "default" } = Astro.props;
---
<Layout title={title}>
  <main>{title}</main>
</Layout>
```

### Props Typing
Two valid approaches:
```astro
<!-- Casting (used in Header.astro) -->
interface HeaderProps { title: string; }
const { title } = Astro.props as HeaderProps;

<!-- Typed destructuring (recommended) -->
interface Props { title: string; description?: string; }
const { title, description = "default" } = Astro.props;
```

### TypeScript Guidelines
- Always type parameters and return values
- Use `interface` for objects, `type` for unions/primitives
- Extend `astro/tsconfigs/base` in tsconfig.json
- Run `npx astro check` before committing

### Naming Conventions
| Element | Convention | Example |
|---------|------------|---------|
| Components | PascalCase | `Header.astro` |
| Props interfaces | PascalCase + Props | `HeaderProps` |
| Functions/variables | camelCase | `setupNav()` |
| CSS classes | kebab-case | `.navbar-button` |
| Files | kebab-case | `nav.js` |
| Constants | UPPER_SNAKE | `MAX_ITEMS` |

### Import Order
1. Astro built-ins (`astro:transitions`, `astro:assets`)
2. External libraries
3. Internal components
4. Type imports (`import type`)
5. Relative file imports

### CSS/Styling
- Scoped styles by default
- Use CSS custom properties (`--color-*`, `--radius-*`, `--shadow-*`)
- Prefer CSS over preprocessors

### Theme System
Uses CSS custom properties with data-theme selector:
```css
[data-theme="light"] { /* light styles */ }
[data-theme="dark"] { /* dark styles */ }
```

Common variables: `--color-*`, `--radius-*`, `--shadow-*`, `--glass-*`

### Images
- `loading="lazy"` for below-fold images
- `loading="eager"` for hero/above-fold
- Always include descriptive `alt`

### Error Handling
- Use optional chaining (`?.`) and nullish coalescing (`??`)
- Verify DOM existence before manipulation:
```javascript
const el = document.querySelector('.element');
if (!el) return;
```
- Use try-catch for async operations

### Accessibility
- Always include `alt` on images
- Use semantic HTML elements
- Include meta tags for social sharing
- Use `aria-label` on icon-only links

### Performance
- Minimize client-side JS
- Use Astro's `<Image />` component when possible
- Prefer static generation over client-side rendering
- Lazy load images by default, eager only for hero

### Responsive Breakpoints
- Desktop: > 991px
- Mobile/Tablet: ≤ 991px

## ViewTransitions
The project uses `ViewTransitions` from `astro:transitions`:
```astro
---
import { ViewTransitions } from 'astro:transitions';
---
<head>
  <ViewTransitions />
</head>
```

Use `transition:name` for animated transitions between pages:
```astro
<div transition:name="project-title">...</div>
```

## Common Tasks

### New Page
```astro
---
import Layout from '../layouts/Layout.astro';
import Sidebar from '../components/Sidebar.astro';
---
<Layout title="Title">
  <div class="content">
    <Sidebar selectedItem="item-name" />
    <main>...</main>
  </div>
</Layout>
```

### New Component
1. Create `src/components/ComponentName.astro`
2. Define Props interface
3. Implement with frontmatter, template, and styles

## Config Files
| File | Purpose |
|------|---------|
| `astro.config.mjs` | Astro configuration |
| `tsconfig.json` | TypeScript config |
| `package.json` | Dependencies and scripts |

## Development Workflow
1. `git checkout -b feature/my-feature`
2. Test with `npm run dev`
3. Verify build: `npm run build`
4. Check types: `npx astro check`
5. Commit with conventional format: `type(scope): description`
