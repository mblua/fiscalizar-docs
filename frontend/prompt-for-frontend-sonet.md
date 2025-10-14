# 🎯 Prompt Sonet para Frontend - Sistema de Fiscalización Electoral

## 📋 Análisis y Recomendación

Después de analizar ambos prompts, mi recomendación combina lo mejor de ambos enfoques con decisiones tecnológicas más pragmáticas y mantenibles a largo plazo.

### 🔍 Análisis Comparativo

**Prompt Supernova (Más Avanzado):**
- ✅ Stack muy moderno (TanStack Router, shadcn/ui, Vitest)
- ✅ Testing completo (Unit + E2E + Visual)
- ✅ PWA features avanzadas
- ✅ Performance optimizations
- ❌ Complejidad excesiva para MVP
- ❌ Curva de aprendizaje alta
- ❌ Dependencias muy nuevas (riesgo de breaking changes)

**Prompt Ideal (Más Conservador):**
- ✅ Stack estable y probado
- ✅ Curva de aprendizaje moderada
- ✅ Documentación abundante
- ✅ Comunidad grande
- ❌ Menos optimizaciones de performance
- ❌ Testing limitado
- ❌ UI components menos modernos

## 🏗️ Mi Recomendación: Stack Sonet

### Stack Tecnológico Balanceado

- **Framework**: React 18+ con TypeScript (strict mode)
- **Routing**: React Router v6 (estable y bien documentado)
- **Estado**: Zustand (simple pero poderoso)
- **UI**: Tailwind CSS + Radix UI + Custom components
- **HTTP Client**: TanStack Query v4 (estable) + Axios
- **Formularios**: React Hook Form + Zod
- **Gráficos**: Recharts (maduro y confiable)
- **PWA**: Workbox (cuando sea necesario)
- **Testing**: Vitest + React Testing Library (moderno pero estable)
- **Linting**: ESLint + Prettier + TypeScript strict

### 🎯 Principios de Diseño

1. **Pragmatismo**: Tecnologías estables con buen ecosistema
2. **Mantenibilidad**: Código limpio y bien documentado
3. **Escalabilidad**: Arquitectura que crezca con el proyecto
4. **Performance**: Optimizaciones cuando realmente importen
5. **Developer Experience**: Herramientas que aceleren el desarrollo

## 📁 Estructura del Proyecto Sonet

```
@fiscalizar-frontend/
├── src/
│   ├── components/
│   │   ├── ui/                 # Componentes base (Radix + Custom)
│   │   ├── layout/             # Layout y navegación
│   │   ├── forms/              # Formularios específicos
│   │   ├── charts/             # Visualizaciones
│   │   └── features/           # Componentes por dominio
│   │       ├── zonas/          # Componentes de zonas
│   │       ├── colegios/       # Componentes de colegios
│   │       ├── mesas/          # Componentes de mesas
│   │       └── fiscales/       # Componentes de fiscales
│   ├── pages/                  # Páginas principales
│   │   ├── dashboard/          # Panel principal
│   │   ├── zonas/             # Gestión de zonas
│   │   ├── colegios/          # Gestión de colegios
│   │   ├── mesas/             # Gestión de mesas
│   │   └── fiscales/          # Gestión de fiscales
│   ├── hooks/                  # Custom hooks
│   ├── services/               # API calls y lógica de negocio
│   ├── store/                  # Estado global (Zustand)
│   ├── types/                  # TypeScript types
│   ├── lib/                    # Configuraciones y utilidades
│   │   ├── utils.ts           # Utilidades generales
│   │   ├── validations.ts     # Esquemas Zod
│   │   ├── constants.ts       # Constantes
│   │   └── api.ts             # Configuración de API
│   └── styles/                 # Estilos globales
├── public/                     # Assets estáticos
├── tests/                      # Tests organizados
├── package.json               # Dependencias
├── tailwind.config.js         # Configuración de Tailwind
├── vite.config.ts            # Configuración de Vite
├── tsconfig.json             # Configuración de TypeScript
├── vitest.config.ts          # Configuración de testing
└── .env.example              # Variables de entorno
```

## 🎨 Diseño y UX

### Identidad Visual - La Libertad Avanza

**Paleta de Colores Optimizada:**

```css
:root {
  /* Colores principales de LLA */
  --primary-50: #f3f0ff;
  --primary-100: #e9e5ff;
  --primary-200: #d4c7ff;
  --primary-300: #b8a3ff;
  --primary-400: #9c7cff;
  --primary-500: #8B5CF6; /* Violeta principal */
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
}
```

### Configuración de Tailwind Balanceada

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
        'fade-in': 'fadeIn 0.3s ease-in-out',
        'slide-up': 'slideUp 0.2s ease-out',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { transform: 'translateY(10px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        }
      }
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}
```

## 🏗️ Arquitectura de Componentes

### Componentes UI Base (Radix + Custom)

```typescript
// Componentes base reutilizables
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'outline' | 'ghost';
  size: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
  loading?: boolean;
}

interface FormFieldProps {
  label: string;
  error?: string;
  required?: boolean;
  children: React.ReactNode;
}

interface DataTableProps<T> {
  data: T[];
  columns: ColumnDef<T>[];
  pagination?: boolean;
  searchable?: boolean;
  sortable?: boolean;
}
```

### Gestión de Estado (Zustand)

```typescript
// store/useAppStore.ts
interface AppState {
  // UI State
  sidebarOpen: boolean;
  theme: 'light' | 'dark';
  
  // Data State
  zonas: Zona[];
  colegios: Colegio[];
  mesas: Mesa[];
  fiscales: Fiscal[];
  
  // Loading States
  loading: {
    zonas: boolean;
    colegios: boolean;
    mesas: boolean;
    fiscales: boolean;
  };
  
  // Actions
  setSidebarOpen: (open: boolean) => void;
  setTheme: (theme: 'light' | 'dark') => void;
  setZonas: (zonas: Zona[]) => void;
  setColegios: (colegios: Colegio[]) => void;
  setMesas: (mesas: Mesa[]) => void;
  setFiscales: (fiscales: Fiscal[]) => void;
  setLoading: (key: string, loading: boolean) => void;
}
```

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
  coberturaPorcentaje: number;
}

interface DashboardChart {
  distribucionPorZona: ChartData[];
  estadoFiscales: ChartData[];
  evolucionVotos: ChartData[];
}
```

**Componentes del Dashboard:**
- Cards con estadísticas (con animaciones sutiles)
- Gráficos interactivos con Recharts
- Lista de actividades recientes
- Accesos rápidos a secciones principales
- Filtros globales para datos en tiempo real

### 2. Gestión de Entidades

**Funcionalidades Comunes:**
- Lista paginada con filtros avanzados
- Crear/editar/eliminar con validación
- Búsqueda en tiempo real (debounce)
- Ordenamiento múltiple
- Exportación de datos
- Navegación contextual entre entidades

**Filtros Específicos por Entidad:**

**Zonas:**
- Búsqueda por nombre
- Ordenamiento por nombre, fecha, cantidad de colegios
- Filtro por estado (activo/inactivo)

**Colegios:**
- Búsqueda por nombre/dirección
- Filtro por zona (multiselect)
- Ordenamiento múltiple
- Filtro por cantidad de mesas

**Mesas:**
- Búsqueda por colegio
- Filtro por fiscal asignado
- Filtro por estado de votación
- Ordenamiento por número de votantes

**Fiscales:**
- Búsqueda por DNI, nombre, email
- Filtro por tipo de fiscal
- Filtro por disponibilidad
- Filtro por mesa asignada
- Ordenamiento múltiple

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
}

interface BreadcrumbItem {
  label: string;
  href?: string;
  current?: boolean;
  icon?: LucideIcon;
}
```

## 📊 Visualizaciones y Reportes

### Gráficos Principales (Recharts)

- **Distribución por zona**: Gráfico de barras interactivo
- **Estado de fiscales**: Gráfico de dona con animaciones
- **Evolución de votos**: Gráfico de líneas con tooltips
- **Cobertura por colegio**: Gráfico de barras horizontales
- **Timeline**: Evolución temporal de datos

### Reportes Exportables

- Lista de fiscales por zona (PDF/Excel)
- Reporte de cobertura por colegio
- Estadísticas de votación en tiempo real
- Exportación a PDF/Excel/CSV
- Dashboard ejecutivo con KPIs

## 🚀 Optimizaciones de Rendimiento

### Técnicas de Optimización (Pragmáticas)

- **Lazy Loading**: Para componentes pesados
- **Memoización**: React.memo, useMemo, useCallback cuando sea necesario
- **Code Splitting**: Por rutas principales
- **Image Optimization**: WebP, lazy loading
- **Bundle Analysis**: Para identificar optimizaciones

### Métricas Objetivo (Realistas)

- **First Contentful Paint**: < 2.0s
- **Largest Contentful Paint**: < 3.0s
- **Cumulative Layout Shift**: < 0.1
- **Time to Interactive**: < 4.0s

## 📱 Responsividad

### Breakpoints Optimizados

```css
/* Mobile First con breakpoints pragmáticos */
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
```

### Adaptaciones por Dispositivo

- **Mobile**: Navegación por tabs, formularios simplificados
- **Tablet**: Sidebar colapsable, grid adaptativo
- **Desktop**: Sidebar fijo, múltiples columnas

## 🔐 Seguridad y Validación

### Validaciones Frontend

- **Formularios**: React Hook Form + Zod con validación en tiempo real
- **Tipos**: TypeScript estricto con `strict: true`
- **Sanitización**: Inputs seguros
- **Autenticación**: JWT + Refresh tokens
- **Autorización**: Role-based access control (RBAC)

### Manejo de Errores

- **Boundaries**: Error boundaries de React con fallbacks
- **Toast**: Notificaciones de error/éxito
- **Retry**: Reintentos automáticos con backoff
- **Fallbacks**: Estados de error amigables

## 🧪 Testing

### Estrategia de Testing (Balanceada)

- **Unit Tests**: Vitest + React Testing Library (80% cobertura)
- **Integration Tests**: MSW (Mock Service Worker)
- **E2E Tests**: Playwright (flujos críticos)
- **Performance Tests**: Lighthouse CI

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
          branches: 70,
          functions: 70,
          lines: 70,
          statements: 70
        }
      }
    }
  }
})
```

## 🔧 Configuración de Desarrollo

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
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "lint:fix": "eslint . --ext ts,tsx --fix",
    "type-check": "tsc --noEmit",
    "analyze": "npx vite-bundle-analyzer"
  }
}
```

## 📦 Deployment

### Configuración de Producción

- **Build**: Vite optimizado con code splitting
- **Hosting**: Vercel o Netlify
- **CDN**: Para assets estáticos
- **Monitoring**: Sentry para errores
- **Analytics**: Google Analytics 4

### Variables de Entorno

```env
VITE_API_BASE_URL=http://localhost:3000/api
VITE_APP_NAME=Fiscalizar.org - LLA C15
VITE_APP_VERSION=1.0.0
VITE_APP_ENV=development
VITE_SENTRY_DSN=your-sentry-dsn
VITE_ANALYTICS_ID=your-analytics-id
```

## 🎯 Resultado Esperado

Una aplicación web moderna que permita:

1. **Navegación fluida** entre entidades interconectadas
2. **Gestión completa** de zonas, colegios, mesas y fiscales
3. **Experiencia móvil** optimizada
4. **Rendimiento excelente** en todos los dispositivos
5. **Interfaz intuitiva** y accesible
6. **Reportes visuales** y exportables
7. **Funcionalidad offline** básica
8. **Escalabilidad** para futuras funcionalidades
9. **Testing completo** con cobertura adecuada
10. **Monitoreo** y analytics integrados

## 🚀 Comandos de Inicio Sonet

```bash
# Navegar al directorio raíz del proyecto
cd E:\0_mmb\0_repos\fiscalizar.org

# Crear la carpeta del frontend
mkdir @fiscalizar-frontend
cd @fiscalizar-frontend

# Crear proyecto React con Vite
npm create vite@latest . -- --template react-ts

# Instalar dependencias principales
npm install @tanstack/react-query@^4 @tanstack/react-query-devtools
npm install react-router-dom
npm install zustand
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
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install -D prettier eslint-config-prettier eslint-plugin-prettier
npm install -D @tailwindcss/forms @tailwindcss/typography
npm install -D class-variance-authority clsx tailwind-merge
npm install -D @hookform/resolvers react-hook-form zod
npm install -D date-fns recharts
npm install -D lucide-react

# Configurar Tailwind con colores de LLA
npx tailwindcss init -p

# Configurar Vitest
npx vitest init

# Configurar Playwright
npx playwright install

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
│   │   └── styles/
│   ├── tests/
│   ├── package.json
│   ├── tailwind.config.js
│   ├── vite.config.ts
│   ├── tsconfig.json
│   ├── vitest.config.ts
│   └── .env.example
├── fiscalizar-backend/       # Proyecto backend (EXISTENTE)
│   ├── src/
│   ├── prisma/
│   └── ...
├── docs/                     # Documentación
└── ...
```

## 💡 Próximos Pasos

1. **Crear la carpeta `@fiscalizar-frontend/`** en el directorio raíz del proyecto
2. **Configurar el proyecto** con las dependencias balanceadas
3. **Implementar Radix UI** con componentes base
4. **Configurar React Router** para navegación
5. **Implementar Zustand** para estado global
6. **Conectar con TanStack Query** para manejo de datos
7. **Desarrollar las páginas principales** con componentes reutilizables
8. **Implementar la navegación interconectada** con breadcrumbs
9. **Optimizar para móviles** con responsive design
10. **Agregar testing** con Vitest + Playwright
11. **Implementar visualizaciones** con Recharts
12. **Configurar CI/CD** y deployment
13. **Agregar monitoreo** con Sentry
14. **Testing y deployment** final

---

**Este prompt Sonet proporciona un balance óptimo entre modernidad y estabilidad, priorizando la mantenibilidad a largo plazo y la experiencia del desarrollador, mientras mantiene todas las funcionalidades requeridas para el sistema de fiscalización electoral.**


