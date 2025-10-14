# 🚀 Prompt Supernova para Frontend - Sistema de Fiscalización Electoral

## 📋 Objetivo del Proyecto

Crear una **aplicación web moderna y responsiva** para la gestión integral de fiscales electorales en Argentina. La aplicación **"Fiscalizar.org - LLA C15"** debe permitir la navegación fluida entre entidades interconectadas (Zonas → Colegios → Mesas → Fiscales) con una experiencia de usuario optimizada tanto en dispositivos móviles como de escritorio, utilizando los colores oficiales de La Libertad Avanza.

## 🏗️ Arquitectura del Frontend

### 📁 Ubicación del Proyecto

**IMPORTANTE**: El proyecto frontend debe desarrollarse en la carpeta `@fiscalizar-frontend/` como directorio raíz. Esta carpeta debe crearse en el mismo nivel que `fiscalizar-backend/` en la estructura del repositorio.

### Stack Tecnológico Supernova 2024

- **Framework**: React 18+ con TypeScript (strict mode)
- **Routing**: TanStack Router v1 (más moderno que React Router)
- **Estado**: Zustand (simple) o Jotai (más moderno)
- **UI**: Tailwind CSS + shadcn/ui (más moderno que Headless UI)
- **HTTP Client**: TanStack Query v5 + Axios
- **Formularios**: React Hook Form + Zod + React Hook Form DevTools
- **Gráficos**: Recharts + D3.js para visualizaciones avanzadas
- **PWA**: Workbox para funcionalidad offline
- **Testing**: Vitest + React Testing Library + Playwright
- **Linting**: ESLint + Prettier + TypeScript strict

### Estructura del Proyecto Supernova

```
@fiscalizar-frontend/
├── src/
│   ├── components/
│   │   ├── ui/                 # Componentes base (shadcn/ui)
│   │   ├── layout/             # Layout principal y navegación
│   │   ├── forms/              # Formularios específicos
│   │   ├── charts/             # Componentes de visualización
│   │   └── features/           # Componentes por feature (NUEVO)
│   │       ├── zonas/          # Componentes específicos de zonas
│   │       ├── colegios/       # Componentes específicos de colegios
│   │       ├── mesas/          # Componentes específicos de mesas
│   │       └── fiscales/       # Componentes específicos de fiscales
│   ├── pages/
│   │   ├── zonas/              # Gestión de zonas
│   │   ├── colegios/           # Gestión de colegios
│   │   ├── mesas/              # Gestión de mesas
│   │   ├── fiscales/           # Gestión de fiscales
│   │   └── dashboard/          # Panel principal
│   ├── hooks/                  # Custom hooks
│   ├── services/               # API calls
│   ├── store/                  # Estado global
│   ├── types/                  # TypeScript types
│   ├── lib/                    # Configuraciones (NUEVO)
│   │   ├── utils.ts           # Utilidades generales
│   │   ├── validations.ts     # Esquemas Zod
│   │   ├── constants.ts       # Constantes
│   │   └── api.ts             # Configuración de API
│   └── utils/                  # Utilidades
├── public/                     # Assets estáticos
├── stories/                    # Storybook stories
├── tests/                      # Tests organizados
├── package.json               # Dependencias del proyecto
├── tailwind.config.js         # Configuración de Tailwind
├── vite.config.ts            # Configuración de Vite
├── tsconfig.json             # Configuración de TypeScript
├── vitest.config.ts          # Configuración de testing
├── playwright.config.ts      # Configuración de E2E
└── .storybook/               # Configuración de Storybook
```

## 🗂️ Estructura de Datos del Backend

### Jerarquía Electoral

```
Zonas (nivel más alto)
├── Colegios (pertenecen a zonas)
    ├── Mesas (pertenecen a colegios)
        └── Fiscales (asignados a mesas)
```

### Entidades Principales

#### **Zona**

- `id` (Int, PK) - Identificador único
- `nombre` (String, único) - Nombre de la zona
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete

#### **Colegio**

- `id` (Int, PK) - Identificador único
- `nombre` (String, único) - Nombre del colegio
- `direccion` (String, opcional) - Dirección
- `zonaId` (Int, FK) - Referencia a Zona
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete

#### **Fiscal**

- `id` (Int, PK) - Identificador único
- `dni` (String, único) - DNI del fiscal
- `nombre`, `apellido` (String, opcional) - Datos personales
- `email` (String, opcional) - Email de contacto
- `celular` (String, opcional) - Teléfono
- `direccion` (String, opcional) - Dirección
- `tipoFiscalId` (Int, FK) - Tipo de fiscal
- `mesaDondeVotaId` (Int, FK) - Mesa donde vota
- `colegioDondeVotaId` (Int, FK) - Colegio donde vota
- `disponibilidad` (String, opcional) - Horarios disponibles
- `notasInternas` (String, opcional) - Notas internas
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete

#### **Mesa**

- `id` (Int, PK) - Identificador único
- `colegioId` (Int, FK) - Referencia a Colegio
- `totalVotantesPadron` (Int, opcional) - Total de votantes
- `totalVotos11Hs` (Int, opcional) - Votos a las 11:00
- `totalVotos15Hs` (Int, opcional) - Votos a las 15:00
- `totalVotos18Hs` (Int, opcional) - Votos a las 18:00
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete

#### **TipoFiscal**

- `id` (Int, PK) - Identificador único
- `nombre` (String, único) - Nombre del tipo
- `descripcion` (String, opcional) - Descripción
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete

## 🎨 Diseño y UX

### Principios de Diseño

- **Mobile First**: Diseño responsivo que funcione perfectamente en móviles
- **Navegación Intuitiva**: Breadcrumbs y navegación contextual
- **Carga Rápida**: Lazy loading y optimización de imágenes
- **Accesibilidad**: Cumplir estándares WCAG 2.1 AA
- **Consistencia**: Sistema de diseño unificado
- **Identidad Partidaria**: Colores y elementos visuales de La Libertad Avanza
- **Performance**: Core Web Vitals optimizados

### Identidad Visual - La Libertad Avanza

La aplicación debe reflejar la identidad visual oficial de La Libertad Avanza:

- **Color Principal**: Violeta (#8B5CF6) como color dominante
- **Color Secundario**: Amarillo (#F59E0B) para acentos y elementos complementarios
- **Tipografía**: Inter (moderna y legible)
- **Iconografía**: Lucide React (consistente y moderna)
- **Logo**: Incluir referencias al águila multicolor característica de LLA
- **Estilo**: Profesional, moderno y confiable para generar confianza en el proceso electoral

### Paleta de Colores - La Libertad Avanza (Mejorada)

```css
:root {
  /* Colores principales de LLA */
  --primary-50: #f3f0ff;
  --primary-100: #e9e5ff;
  --primary-200: #d4c7ff;
  --primary-300: #b8a3ff;
  --primary-400: #9c7cff;
  --primary-500: #8B5CF6; /* Violeta principal de LLA */
  --primary-600: #7C3AED;
  --primary-700: #6D28D9;
  --primary-800: #5B21B6;
  --primary-900: #4C1D95;

  --secondary-50: #fffbeb;
  --secondary-100: #fef3c7;
  --secondary-200: #fde68a;
  --secondary-300: #fcd34d;
  --secondary-400: #fbbf24;
  --secondary-500: #F59E0B; /* Amarillo principal */
  --secondary-600: #D97706;
  --secondary-700: #B45309;
  --secondary-800: #92400e;
  --secondary-900: #78350f;

  /* Colores semánticos */
  --success: #10B981;
  --warning: #F59E0B;
  --danger: #EF4444;
  --info: #3B82F6;

  /* Escala de grises */
  --gray-50: #F9FAFB;
  --gray-100: #F3F4F6;
  --gray-200: #E5E7EB;
  --gray-300: #D1D5DB;
  --gray-400: #9CA3AF;
  --gray-500: #6B7280;
  --gray-600: #4B5563;
  --gray-700: #374151;
  --gray-800: #1F2937;
  --gray-900: #111827;
}
```

### Configuración de Tailwind Mejorada

```javascript
// tailwind.config.js
module.exports = {
  content: ["./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#f3f0ff',
          100: '#e9e5ff',
          200: '#d4c7ff',
          300: '#b8a3ff',
          400: '#9c7cff',
          500: '#8B5CF6',
          600: '#7C3AED',
          700: '#6D28D9',
          800: '#5B21B6',
          900: '#4C1D95',
        },
        secondary: {
          50: '#fffbeb',
          100: '#fef3c7',
          200: '#fde68a',
          300: '#fcd34d',
          400: '#fbbf24',
          500: '#F59E0B',
          600: '#D97706',
          700: '#B45309',
          800: '#92400e',
          900: '#78350f',
        }
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
      },
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in-out',
        'slide-up': 'slideUp 0.3s ease-out',
        'bounce-gentle': 'bounceGentle 2s infinite',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { transform: 'translateY(10px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
        bounceGentle: {
          '0%, 100%': { transform: 'translateY(0)' },
          '50%': { transform: 'translateY(-5px)' },
        }
      }
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
    require('@tailwindcss/aspect-ratio'),
  ],
}
```

### Header y Branding

```typescript
interface HeaderProps {
  title: string; // "Fiscalizar.org - LLA C15"
  subtitle?: string; // "Sistema de Fiscalización Electoral"
  logo?: string; // Ruta al logo de LLA
  user?: UserInfo;
  navigation: NavigationItem[];
  breadcrumbs?: BreadcrumbItem[];
}

interface BreadcrumbItem {
  label: string;
  href?: string;
  current?: boolean;
}
```

**Elementos del Header:**
- Logo de La Libertad Avanza con águila multicolor
- Título principal: "Fiscalizar.org - LLA C15"
- Subtítulo: "Sistema de Fiscalización Electoral"
- Navegación principal con colores violeta/amarillo
- Breadcrumbs contextuales
- Información del usuario logueado
- Botón de logout

### Componentes UI Base (shadcn/ui)

- **Botones**: Primarios (violeta), secundarios (amarillo), outline, ghost, destructive
- **Formularios**: Input, Select, Checkbox, RadioGroup, Switch, Textarea
- **Tablas**: DataTable con paginación, filtros y ordenamiento
- **Modales**: Dialog, AlertDialog, Sheet para diferentes contextos
- **Cards**: Card, CardHeader, CardContent, CardFooter
- **Badges**: Para estados y categorías
- **Loading**: Skeleton, Spinner, Progress
- **Alertas**: Alert con variantes (default, destructive, warning)
- **Navigation**: Tabs, Accordion, Collapsible
- **Feedback**: Toast, Progress, Separator

## 📱 Páginas y Funcionalidades

### 1. Dashboard Principal

```typescript
interface DashboardStats {
  totalZonas: number;
  totalColegios: number;
  totalMesas: number;
  totalFiscales: number;
  fiscalesConfirmados: number;
  mesasConFiscal: number;
  colegiosConMesas: number;
  coberturaPorcentaje: number;
  ultimaActualizacion: Date;
}

interface DashboardChart {
  distribucionPorZona: ChartData[];
  estadoFiscales: ChartData[];
  evolucionVotos: ChartData[];
  coberturaColegios: ChartData[];
}
```

**Componentes:**
- Cards con estadísticas generales (animadas)
- Gráficos interactivos de distribución por zona
- Lista de actividades recientes con timestamps
- Accesos rápidos a secciones principales
- Filtros globales para datos en tiempo real

### 2. Gestión de Zonas

**Funcionalidades:**
- Lista paginada de zonas con filtros avanzados
- Crear/editar/eliminar zonas con validación
- Ver estadísticas detalladas por zona
- Navegación directa a colegios de la zona
- Exportación de datos por zona

**Filtros disponibles:**
- Búsqueda por nombre (debounce)
- Ordenamiento por nombre, fecha de creación, cantidad de colegios
- Filtro por estado (activo/inactivo)
- Filtro por rango de fechas
- Búsqueda global con highlighting

### 3. Gestión de Colegios

**Funcionalidades:**
- Lista paginada con filtros avanzados
- Crear/editar/eliminar colegios
- Asignación a zona con validación
- Ver mesas del colegio en tiempo real
- Ver fiscales asignados con estado
- Mapa integrado para ubicación

**Filtros disponibles:**
- Búsqueda por nombre/dirección
- Filtro por zona (multiselect)
- Ordenamiento múltiple
- Filtro por estado
- Filtro por cantidad de mesas
- Búsqueda geográfica

### 4. Gestión de Mesas

**Funcionalidades:**
- Lista paginada con filtros
- Crear/editar/eliminar mesas
- Asignación de fiscales con drag & drop
- Registro de conteos de votos en tiempo real
- Historial de cambios con auditoría
- Validación de datos de votación

**Filtros disponibles:**
- Búsqueda por colegio
- Filtro por fiscal asignado
- Filtro por estado de votación
- Ordenamiento por número de votantes
- Filtro por rango de votos
- Búsqueda por DNI del fiscal

### 5. Gestión de Fiscales

**Funcionalidades:**
- Lista paginada con filtros avanzados
- Crear/editar/eliminar fiscales
- Asignación a mesas con validación
- Gestión de disponibilidad con calendario
- Notas internas con markdown
- Historial de asignaciones
- Validación de DNI argentino

**Filtros disponibles:**
- Búsqueda por DNI, nombre, email
- Filtro por tipo de fiscal
- Filtro por disponibilidad
- Filtro por mesa asignada
- Ordenamiento múltiple
- Filtro por estado de confirmación

## 🔄 Navegación Interconectada

### Flujo de Navegación

1. **Dashboard** → **Zonas** → **Colegios** → **Mesas** → **Fiscales**
2. **Fiscales** → **Mesa asignada** → **Colegio** → **Zona**
3. **Breadcrumbs** contextuales en cada página
4. **Enlaces contextuales** entre entidades relacionadas
5. **Navegación por teclado** (accesibilidad)

### Componentes de Navegación

```typescript
interface NavigationItem {
  label: string;
  href: string;
  icon: LucideIcon;
  badge?: number;
  children?: NavigationItem[];
  disabled?: boolean;
  external?: boolean;
}

interface BreadcrumbItem {
  label: string;
  href?: string;
  current?: boolean;
  icon?: LucideIcon;
}
```

## 📊 Visualizaciones y Reportes

### Gráficos Principales

- **Distribución por zona**: Gráfico de barras interactivo
- **Estado de fiscales**: Gráfico de dona con animaciones
- **Evolución de votos**: Gráfico de líneas con tooltips
- **Cobertura por colegio**: Gráfico de barras horizontales
- **Mapa de calor**: Distribución geográfica
- **Timeline**: Evolución temporal de datos

### Reportes Exportables

- Lista de fiscales por zona (PDF/Excel)
- Reporte de cobertura por colegio
- Estadísticas de votación en tiempo real
- Exportación a PDF/Excel/CSV
- Reportes programados por email
- Dashboard ejecutivo con KPIs

## 🚀 Optimizaciones de Rendimiento

### Técnicas de Optimización

- **Suspense**: Para lazy loading de componentes
- **Concurrent Features**: useTransition, useDeferredValue
- **Virtual Scrolling**: Para listas largas (react-window)
- **Memoización**: React.memo, useMemo, useCallback
- **Code Splitting**: Por rutas y componentes
- **Image Optimization**: WebP, lazy loading, responsive
- **Service Worker**: Cache estratégico con Workbox
- **Bundle Analysis**: webpack-bundle-analyzer

### Métricas Objetivo

- **First Contentful Paint**: < 1.5s
- **Largest Contentful Paint**: < 2.5s
- **Cumulative Layout Shift**: < 0.1
- **First Input Delay**: < 100ms
- **Time to Interactive**: < 3.0s

### PWA Features

- **Offline Support**: Cache de datos críticos
- **Push Notifications**: Para actualizaciones importantes
- **Install Prompt**: Para instalación en móviles
- **Background Sync**: Sincronización automática
- **App Shell**: Carga instantánea

## 📱 Responsividad

### Breakpoints Mejorados

```css
/* Mobile First con breakpoints optimizados */
@media (min-width: 640px) {
  /* sm - Móviles grandes */
}
@media (min-width: 768px) {
  /* md - Tablets */
}
@media (min-width: 1024px) {
  /* lg - Laptops */
}
@media (min-width: 1280px) {
  /* xl - Desktops */
}
@media (min-width: 1536px) {
  /* 2xl - Pantallas grandes */
}
```

### Adaptaciones por Dispositivo

- **Mobile**: Navegación por tabs, formularios simplificados, gestos táctiles
- **Tablet**: Sidebar colapsable, grid adaptativo, orientación landscape
- **Desktop**: Sidebar fijo, múltiples columnas, shortcuts de teclado
- **Touch**: Gestos de swipe, pinch to zoom, haptic feedback

## 🔐 Seguridad y Validación

### Validaciones Frontend

- **Formularios**: React Hook Form + Zod con validación en tiempo real
- **Tipos**: TypeScript estricto con `strict: true`
- **Sanitización**: DOMPurify para contenido HTML
- **Autenticación**: JWT + Refresh tokens
- **Autorización**: Role-based access control (RBAC)
- **Rate Limiting**: Protección contra spam
- **CSRF Protection**: Tokens de seguridad

### Manejo de Errores

- **Boundaries**: Error boundaries de React con fallbacks
- **Toast**: Notificaciones de error/éxito con acciones
- **Retry**: Reintentos automáticos con backoff
- **Fallbacks**: Estados de error amigables
- **Logging**: Sentry para monitoreo de errores
- **User Feedback**: Mensajes claros y accionables

### Variables de Entorno

```env
VITE_API_BASE_URL=http://localhost:3000/api
VITE_APP_NAME=Fiscalizar.org - LLA C15
VITE_APP_VERSION=1.0.0
VITE_APP_ENV=development
VITE_SENTRY_DSN=your-sentry-dsn
VITE_ANALYTICS_ID=your-analytics-id
VITE_MAP_API_KEY=your-map-api-key
```

## 🧪 Testing

### Estrategia de Testing

- **Unit Tests**: Vitest + React Testing Library
- **Integration Tests**: MSW (Mock Service Worker)
- **E2E Tests**: Playwright
- **Visual Tests**: Chromatic
- **Performance Tests**: Lighthouse CI
- **Accessibility Tests**: axe-core

### Cobertura Objetivo

- **Unit Tests**: 85%+ cobertura
- **Integration Tests**: Flujos críticos completos
- **E2E Tests**: Happy paths + edge cases
- **Performance**: Core Web Vitals optimizados
- **Accessibility**: WCAG 2.1 AA compliance

### Configuración de Testing

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    setupFiles: ['./src/test/setup.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      thresholds: {
        global: {
          branches: 80,
          functions: 80,
          lines: 80,
          statements: 80
        }
      }
    }
  }
})
```

## 🔧 Configuración de Desarrollo

### Herramientas de Desarrollo

- **Linting**: ESLint + Prettier + TypeScript strict
- **Type Checking**: TypeScript estricto
- **Testing**: Vitest + React Testing Library + Playwright
- **E2E**: Playwright con múltiples navegadores
- **Storybook**: Para documentación de componentes
- **Bundle Analysis**: webpack-bundle-analyzer
- **Performance**: Lighthouse CI

### Scripts de Package.json

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage",
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "lint:fix": "eslint . --ext ts,tsx --fix",
    "type-check": "tsc --noEmit",
    "storybook": "storybook dev -p 6006",
    "build-storybook": "storybook build",
    "analyze": "npx vite-bundle-analyzer",
    "lighthouse": "lighthouse http://localhost:5173 --output html --output-path ./lighthouse-report.html"
  }
}
```

## 📦 Deployment

### Configuración de Producción

- **Build**: Vite optimizado con code splitting
- **Hosting**: Vercel, Netlify o AWS S3 + CloudFront
- **CDN**: Para assets estáticos
- **Monitoring**: Sentry para errores
- **Analytics**: Google Analytics 4
- **Performance**: Lighthouse CI

### CI/CD Pipeline

```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm run test:coverage
      - run: npm run test:e2e
      - run: npm run lighthouse
```

## 🎯 Resultado Esperado

Una aplicación web moderna que permita:

1. **Navegación fluida** entre entidades interconectadas
2. **Gestión completa** de zonas, colegios, mesas y fiscales
3. **Experiencia móvil** optimizada con PWA
4. **Rendimiento excelente** en todos los dispositivos
5. **Interfaz intuitiva** y accesible (WCAG 2.1 AA)
6. **Reportes visuales** y exportables
7. **Funcionalidad offline** robusta
8. **Escalabilidad** para futuras funcionalidades
9. **Testing completo** con alta cobertura
10. **Monitoreo** y analytics integrados

## 🚀 Comandos de Inicio Supernova

```bash
# Navegar al directorio raíz del proyecto
cd E:\0_mmb\0_repos\fiscalizar.org

# Crear la carpeta del frontend
mkdir @fiscalizar-frontend
cd @fiscalizar-frontend

# Crear proyecto React con Vite
npm create vite@latest . -- --template react-ts

# Instalar dependencias principales
npm install @tanstack/react-query@latest @tanstack/react-router@latest
npm install zustand lucide-react
npm install @radix-ui/react-accordion @radix-ui/react-alert-dialog
npm install @radix-ui/react-avatar @radix-ui/react-button
npm install @radix-ui/react-card @radix-ui/react-checkbox
npm install @radix-ui/react-dialog @radix-ui/react-dropdown-menu
npm install @radix-ui/react-form @radix-ui/react-hover-card
npm install @radix-ui/react-input @radix-ui/react-label
npm install @radix-ui/react-navigation-menu @radix-ui/react-popover
npm install @radix-ui/react-progress @radix-ui/react-radio-group
npm install @radix-ui/react-scroll-area @radix-ui/react-select
npm install @radix-ui/react-separator @radix-ui/react-sheet
npm install @radix-ui/react-slider @radix-ui/react-switch
npm install @radix-ui/react-tabs @radix-ui/react-toast
npm install @radix-ui/react-toggle @radix-ui/react-tooltip

# Instalar dependencias de desarrollo
npm install -D @types/node
npm install -D @vitejs/plugin-react
npm install -D vitest @testing-library/react @testing-library/jest-dom
npm install -D @playwright/test
npm install -D @storybook/react @storybook/react-vite
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install -D prettier eslint-config-prettier eslint-plugin-prettier
npm install -D @tailwindcss/forms @tailwindcss/typography @tailwindcss/aspect-ratio
npm install -D class-variance-authority clsx tailwind-merge
npm install -D @hookform/resolvers react-hook-form zod
npm install -D date-fns recharts
npm install -D workbox-webpack-plugin

# Configurar Tailwind con colores de LLA
npx tailwindcss init -p

# Configurar Vitest
npx vitest init

# Configurar Playwright
npx playwright install

# Configurar Storybook
npx storybook@latest init

# Desarrollo
npm run dev
```

### 📁 Estructura Final del Repositorio

```
fiscalizar.org/
├── @fiscalizar-frontend/     # Proyecto frontend (NUEVO)
│   ├── src/
│   │   ├── components/
│   │   │   ├── ui/
│   │   │   ├── layout/
│   │   │   ├── forms/
│   │   │   ├── charts/
│   │   │   └── features/
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── store/
│   │   ├── types/
│   │   ├── lib/
│   │   └── utils/
│   ├── public/
│   ├── stories/
│   ├── tests/
│   ├── .storybook/
│   ├── package.json
│   ├── tailwind.config.js
│   ├── vite.config.ts
│   ├── tsconfig.json
│   ├── vitest.config.ts
│   ├── playwright.config.ts
│   └── ...
├── fiscalizar-backend/       # Proyecto backend (EXISTENTE)
│   ├── src/
│   ├── prisma/
│   └── ...
├── docs/                     # Documentación
└── ...
```

## 💡 Próximos Pasos

1. **Crear la carpeta `@fiscalizar-frontend/`** en el directorio raíz del proyecto
2. **Configurar el proyecto** con las dependencias supernova
3. **Implementar shadcn/ui** con componentes base
4. **Configurar TanStack Router** para navegación moderna
5. **Implementar Zustand** para estado global
6. **Conectar con TanStack Query** para manejo de datos
7. **Desarrollar las páginas principales** con componentes reutilizables
8. **Implementar la navegación interconectada** con breadcrumbs
9. **Optimizar para móviles** con PWA features
10. **Agregar testing completo** con Vitest + Playwright
11. **Implementar visualizaciones** con Recharts
12. **Configurar CI/CD** y deployment
13. **Agregar monitoreo** con Sentry
14. **Testing y deployment** final

---

**Este prompt supernova proporciona una base sólida y moderna para crear una aplicación frontend de última generación, responsiva y altamente funcional para el sistema de fiscalización electoral, utilizando las mejores prácticas de 2024.**

