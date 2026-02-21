# AGENTS.md - Guía del Desarrollador para Codefolio

## Descripción General del Proyecto
Sitio web de portafolio desarrollado con **Astro 5.x** y **TypeScript**. Sitio estático para mostrar proyectos, habilidades e historia personal. Utiliza ViewTransitions para navegación fluida entre páginas.

## Comandos de Compilación, Lint y Pruebas

### Scripts de npm
| Comando | Descripción |
|---------|-------------|
| `npm run dev` | Servidor de desarrollo en http://localhost:4321 |
| `npm run build` | Compilar para producción |
| `npm run preview` | Previsualizar compilación de producción |
| `npx astro check` | Verificación de tipos del proyecto |
| `npx astro sync` | Generar tipos de Astro |

### Ejecución de Pruebas
No hay un framework de pruebas configurado. Para añadir pruebas:
```bash
npm install -D vitest
```

Configurar en `package.json`:
```json
"scripts": {
  "test": "vitest",
  "test:run": "vitest run"
}
```

Ejecutar pruebas:
```bash
npm run test              # Modo watch
npm run test:run          # Ejecución única
npm run test -- --run src/components/Project.test.js  # Prueba específica
```

### Linting
No hay linting configurado. Recomendado:
```bash
npm install -D eslint @astrojs/eslint-plugin prettier
```

## Guías de Estilo de Código

### Estructura de Archivos
```
src/
├── components/   # Componentes .astro reutilizables
├── layouts/      # Layouts de página .astro
├── pages/        # Rutas .astro (archivos .astro en raíz = rutas)
├── env.d.ts     # Declaraciones de TypeScript
└── styles/      # Archivos CSS globales (si aplica)
```

### Componentes Astro
**Frontmatter**: Imports primero, luego interfaz Props, después desestructurar `Astro.props`

```astro
---
import { ViewTransitions } from 'astro:transitions';
interface Props {
  title: string;
  description?: string;
}
const { title, description = "Por defecto" } = Astro.props;
---
<Layout title={title}>
  <main>{title}</main>
</Layout>
```

**Template**: 
- Sangría de 2 espacios
- HTML semántico (`<header>`, `<main>`, `<nav>`, `<aside>`, `<article>`)
- Siempre incluir `alt` en imágenes

**Styles**: 
- Scoped por defecto en componentes
- Usar propiedades personalizadas CSS (`--color-*`, `--radius-*`, `--shadow-*`)
- Preferir CSS sobre preprocesadores

**Scripts**: Usar `addEventListener`, devolver funciones de limpieza

### Props en Componentes
Dos formas de tipar (ambas válidas):

1. **Con casting** (usado en Header.astro):
```astro
interface HeaderProps { title: string; }
const { title } = Astro.props as HeaderProps;
```

2. **Con desestructuración tipada** (recomendado):
```astro
interface Props { title: string; description?: string; }
const { title, description = "default" } = Astro.props;
```

### TypeScript
- Tipar siempre parámetros y valores de retorno
- Usar `interface` para objetos, `type` para uniones/primitivos
- Ejecutar `npx astro check` antes de commit
- Extiende `astro/tsconfigs/base` en tsconfig.json

### Convenciones de Nomenclatura
| Elemento | Convención | Ejemplo |
|---------|------------|---------|
| Componentes | PascalCase | `Header.astro` |
| Props interfaces | PascalCase + Props | `HeaderProps` |
| Funciones/variables | camelCase | `setupNav()` |
| Clases CSS | kebab-case | `.navbar-button` |
| Archivos | kebab-case | `nav.js` |
| Constantes | UPPER_SNAKE | `MAX_ITEMS` |

### Orden de Importación
1. Integrados de Astro (`astro:transitions`, `astro:assets`)
2. Librerías externas
3. Componentes internos
4. Importaciones de tipos (`import type`)
5. Importaciones de archivos relativos

### ViewTransitions
El proyecto usa `ViewTransitions` de `astro:transitions` para navegación SPA-like:

```astro
---
import { ViewTransitions } from 'astro:transitions';
---
<head>
  <ViewTransitions />
</head>
```

- Permite transiciones animadas entre páginas
- Mantiene estado del cliente durante navegación
- No requiere JavaScript adicional del cliente

### Sistema de Temas
El proyecto implementa tema claro/oscuro con CSS custom properties y selectores:

```css
[data-theme="light"] { /* estilos tema claro */ }
[data-theme="dark"] { /* estilos tema oscuro */ }
```

Variables CSS comunes:
- `--color-*`: colores (primary, text, background, card-bg, etc.)
- `--radius-*`: radios de borde
- `--shadow-*`: sombras
- `--glass-*`: efectos de cristal (blur, border)

### Imágenes
- `loading="lazy"` para imágenes fuera de viewport
- `loading="eager"` para imágenes above-the-fold (hero)
- Siempre incluir `alt` descriptivo

### Responsive Design
Breakpoints típicos en el proyecto:
- Desktop: > 991px
- Móvil/Tablet: ≤ 991px

### Manejo de Errores
- Usar encadenamiento opcional (`?.`) y coalescencia nula (`??`)
- Verificar existencia en DOM antes de manipular:
```javascript
const el = document.querySelector('.element');
if (!el) return;
```
- try-catch para operaciones asíncronas

### Accesibilidad
- Incluir siempre `alt` en imágenes
- Usar elementos HTML semánticos
- Incluir etiquetas meta para compartir en redes sociales
- Usar `aria-label` en enlaces sin texto

### Rendimiento
- Minimizar JS del lado del cliente
- Usar componente `<Image />` de Astro cuando sea posible
- Preferir generación estática sobre renderizado del lado del cliente
- Imágenes: lazy loading por defecto, eager solo para hero

## Archivos de Configuración
| Archivo | Propósito |
|---------|-----------|
| `astro.config.mjs` | Configuración de Astro |
| `tsconfig.json` | Configuración de TypeScript |
| `package.json` | Dependencias y scripts |

## Tareas Comunes

### Nueva Página
1. Crear `src/pages/nombre-pagina.astro`
2. Importar Layout existente
3. Usar Sidebar con `selectedItem` correcto

```astro
---
import Layout from '../layouts/Layout.astro';
import Sidebar from '../components/Sidebar.astro';
---
<Layout title="Título">
  <div class="content">
    <Sidebar selectedItem="nombre-item" />
    <main>...</main>
  </div>
</Layout>
```

### Nuevo Componente
1. Crear `src/components/ComponentName.astro`
2. Definir interfaz de Props
3. Implementar con frontmatter, plantilla, estilos

### Añadir Transiciones a Componentes
Usar `transition:name` para animaciones entre páginas:
```astro
<div transition:name="project-title">...</div>
```

## Flujo de Trabajo de Desarrollo
1. `git checkout -b feature/my-feature`
2. Probar con `npm run dev`
3. Verificar compilación: `npm run build`
4. Verificar tipos: `npx astro check`
5. Commit con formato convencional: `type(scope): descripción`
