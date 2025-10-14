# 🎯 Prompt Ideal para Frontend - Sistema de Fiscalización Electoral

## 📋 Objetivo del Proyecto

Crear una **aplicación web moderna y responsiva** para la gestión integral de fiscales electorales en Argentina. La aplicación **"Fiscalizar.org - LLA C15"** debe permitir la navegación fluida entre entidades interconectadas (Zonas → Colegios → Mesas → Fiscales) con una experiencia de usuario optimizada tanto en dispositivos móviles como de escritorio, utilizando los colores oficiales de La Libertad Avanza.

## 🏗️ Arquitectura del Frontend

### 📁 Ubicación del Proyecto

**IMPORTANTE**: El proyecto frontend debe desarrollarse en el directorio raíz. 

### Stack Tecnológico Recomendado

- **Framework**: React 18+ con TypeScript
- **Routing**: React Router v6
- **Estado**: Zustand o Redux Toolkit
- **UI**: Tailwind CSS + Headless UI
- **HTTP Client**: Axios o React Query
- **Formularios**: React Hook Form + Zod
- **Gráficos**: Chart.js o Recharts
- **PWA**: Workbox para funcionalidad offline

### Estructura del Proyecto

```
├── src/
│   ├── components/
│   │   ├── ui/                 # Componentes base reutilizables
│   │   ├── layout/             # Layout principal y navegación
│   │   ├── forms/              # Formularios específicos
│   │   └── charts/             # Componentes de visualización
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
│   └── utils/                  # Utilidades
├── public/                     # Assets estáticos
├── package.json               # Dependencias del proyecto
├── tailwind.config.js         # Configuración de Tailwind
├── vite.config.ts            # Configuración de Vite
└── tsconfig.json             # Configuración de TypeScript
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
- **Accesibilidad**: Cumplir estándares WCAG 2.1
- **Consistencia**: Sistema de diseño unificado
- **Identidad Partidaria**: Colores y elementos visuales de La Libertad Avanza

### Identidad Visual - La Libertad Avanza

La aplicación debe reflejar la identidad visual oficial de La Libertad Avanza:

- **Color Principal**: Violeta (#8B5CF6) como color dominante
- **Color Secundario**: Amarillo (#F59E0B) para acentos y elementos complementarios
- **Tipografía**: Moderna y legible, preferiblemente sans-serif
- **Iconografía**: Elementos que reflejen transparencia, democracia y participación ciudadana
- **Logo**: Incluir referencias al águila multicolor característica de LLA
- **Estilo**: Profesional, moderno y confiable para generar confianza en el proceso electoral

### Paleta de Colores - La Libertad Avanza

```css
:root {
  --primary: #8B5CF6; /* Violeta principal de LLA */
  --primary-light: #A78BFA; /* Violeta claro */
  --primary-dark: #7C3AED; /* Violeta oscuro */
  --secondary: #F59E0B; /* Amarillo complementario */
  --secondary-light: #FCD34D; /* Amarillo claro */
  --secondary-dark: #D97706; /* Amarillo oscuro */
  --success: #10B981; /* Verde confirmación */
  --warning: #F59E0B; /* Amarillo advertencia */
  --danger: #EF4444; /* Rojo error */
  --info: #3B82F6; /* Azul información */
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

### Header y Branding

```typescript
interface HeaderProps {
  title: string; // "Fiscalizar.org - LLA C15"
  subtitle?: string; // "Sistema de Fiscalización Electoral"
  logo?: string; // Ruta al logo de LLA
  user?: UserInfo;
  navigation: NavigationItem[];
}
```

**Elementos del Header:**
- Logo de La Libertad Avanza con águila multicolor
- Título principal: "Fiscalizar.org - LLA C15"
- Subtítulo: "Sistema de Fiscalización Electoral"
- Navegación principal con colores violeta/amarillo
- Información del usuario logueado
- Botón de logout

### Componentes UI Base

- **Botones**: Primarios (violeta), secundarios (amarillo), outline, ghost
- **Formularios**: Inputs, selects, checkboxes, radio buttons
- **Tablas**: Con paginación, filtros y ordenamiento
- **Modales**: Para confirmaciones y formularios
- **Cards**: Para mostrar información resumida
- **Badges**: Para estados y categorías
- **Loading**: Skeletons y spinners
- **Alertas**: Con colores de LLA (violeta para info, amarillo para warning)

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
}
```

**Componentes:**

- Cards con estadísticas generales
- Gráficos de distribución por zona
- Lista de actividades recientes
- Accesos rápidos a secciones principales

### 2. Gestión de Zonas

**Funcionalidades:**

- Lista paginada de zonas con filtros
- Crear/editar/eliminar zonas
- Ver estadísticas por zona
- Navegación a colegios de la zona

**Filtros disponibles:**

- Búsqueda por nombre
- Ordenamiento por nombre, fecha de creación
- Filtro por estado (activo/inactivo)

### 3. Gestión de Colegios

**Funcionalidades:**

- Lista paginada con filtros avanzados
- Crear/editar/eliminar colegios
- Asignación a zona
- Ver mesas del colegio
- Ver fiscales asignados

**Filtros disponibles:**

- Búsqueda por nombre/dirección
- Filtro por zona
- Ordenamiento múltiple
- Filtro por estado

### 4. Gestión de Mesas

**Funcionalidades:**

- Lista paginada con filtros
- Crear/editar/eliminar mesas
- Asignación de fiscales
- Registro de conteos de votos
- Historial de cambios

**Filtros disponibles:**

- Búsqueda por colegio
- Filtro por fiscal asignado
- Filtro por estado de votación
- Ordenamiento por número de votantes

### 5. Gestión de Fiscales

**Funcionalidades:**

- Lista paginada con filtros avanzados
- Crear/editar/eliminar fiscales
- Asignación a mesas
- Gestión de disponibilidad
- Notas internas
- Historial de asignaciones

**Filtros disponibles:**

- Búsqueda por DNI, nombre, email
- Filtro por tipo de fiscal
- Filtro por disponibilidad
- Filtro por mesa asignada
- Ordenamiento múltiple

## 🔄 Navegación Interconectada

### Flujo de Navegación

1. **Dashboard** → **Zonas** → **Colegios** → **Mesas** → **Fiscales**
2. **Fiscales** → **Mesa asignada** → **Colegio** → **Zona**
3. **Breadcrumbs** en cada página
4. **Enlaces contextuales** entre entidades relacionadas

### Componentes de Navegación

```typescript
interface NavigationItem {
  label: string;
  href: string;
  icon: React.ComponentType;
  badge?: number;
  children?: NavigationItem[];
}
```

## 📊 Visualizaciones y Reportes

### Gráficos Principales

- **Distribución por zona**: Gráfico de barras
- **Estado de fiscales**: Gráfico de dona
- **Evolución de votos**: Gráfico de líneas
- **Cobertura por colegio**: Gráfico de barras horizontales

### Reportes Exportables

- Lista de fiscales por zona
- Reporte de cobertura por colegio
- Estadísticas de votación
- Exportación a PDF/Excel

## 🚀 Optimizaciones de Rendimiento

### Técnicas de Optimización

- **Lazy Loading**: Carga bajo demanda de componentes
- **Virtual Scrolling**: Para listas largas
- **Memoización**: React.memo, useMemo, useCallback
- **Code Splitting**: Por rutas y componentes
- **Image Optimization**: WebP, lazy loading
- **Service Worker**: Cache estratégico

### PWA Features

- **Offline Support**: Cache de datos críticos
- **Push Notifications**: Para actualizaciones importantes
- **Install Prompt**: Para instalación en móviles
- **Background Sync**: Sincronización automática

## 📱 Responsividad

### Breakpoints

```css
/* Mobile First */
@media (min-width: 640px) {
  /* sm */
}
@media (min-width: 768px) {
  /* md */
}
@media (min-width: 1024px) {
  /* lg */
}
@media (min-width: 1280px) {
  /* xl */
}
```

### Adaptaciones por Dispositivo

- **Mobile**: Navegación por tabs, formularios simplificados
- **Tablet**: Sidebar colapsable, grid adaptativo
- **Desktop**: Sidebar fijo, múltiples columnas

## 🔐 Seguridad y Validación

### Validaciones Frontend

- **Formularios**: Validación en tiempo real
- **Tipos**: TypeScript estricto
- **Sanitización**: Inputs seguros
- **Autenticación**: JWT tokens

### Manejo de Errores

- **Boundaries**: Error boundaries de React
- **Toast**: Notificaciones de error/éxito
- **Retry**: Reintentos automáticos
- **Fallbacks**: Estados de error amigables

## 🧪 Testing

### Estrategia de Testing

- **Unit Tests**: Jest + React Testing Library
- **Integration Tests**: Cypress
- **E2E Tests**: Playwright
- **Visual Tests**: Chromatic

### Cobertura Objetivo

- **Unit Tests**: 80%+ cobertura
- **Integration Tests**: Flujos críticos
- **E2E Tests**: Happy paths principales

## 📦 Deployment

### Configuración de Producción

- **Build**: Vite o Webpack optimizado
- **Hosting**: Vercel, Netlify o AWS
- **CDN**: Para assets estáticos
- **Monitoring**: Sentry para errores

### Variables de Entorno

```env
VITE_API_BASE_URL=http://localhost:3000/api
VITE_APP_NAME=Fiscalizar.org - LLA C15
VITE_APP_VERSION=1.0.0
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

## 🚀 Comandos de Inicio

```bash
# Navegar al directorio raíz del proyecto
cd E:\0_mmb\0_repos\fiscalizar-frontend

# Crear proyecto React con Vite en el directorio raíz
npm create vite@latest . -- --template react-ts

# Instalar dependencias
npm install @tanstack/react-query axios react-router-dom
npm install @headlessui/react @heroicons/react
npm install tailwindcss @tailwindcss/forms
npm install react-hook-form @hookform/resolvers zod
npm install recharts date-fns

# Configurar Tailwind con colores de LLA
npx tailwindcss init -p

# Desarrollo
npm run dev
```

### 📁 Estructura Final del Repositorio

```
fiscalizar-frontend/
├── src/                      # Código fuente del frontend
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   └── ...
├── public/                   # Assets estáticos
├── package.json             # Dependencias del proyecto
├── tailwind.config.js       # Configuración de Tailwind
├── vite.config.ts          # Configuración de Vite
├── tsconfig.json           # Configuración de TypeScript
├── fiscalizar-docs/        # Documentación del proyecto
└── docs-frontend/          # Documentación específica del frontend
```

## 💡 Próximos Pasos

1. **Configurar el proyecto** con las dependencias base en el directorio raíz
2. **Implementar el sistema de routing** con React Router
3. **Crear los componentes UI base** con Tailwind y colores de LLA
4. **Implementar el estado global** con Zustand
5. **Conectar con la API** del backend
6. **Desarrollar las páginas principales** una por una
7. **Implementar la navegación interconectada**
8. **Optimizar para móviles** y testing
9. **Agregar visualizaciones** y reportes
10. **Testing y deployment**

---

**Este prompt proporciona una base sólida para crear una aplicación frontend moderna, responsiva y altamente funcional para el sistema de fiscalización electoral.**
