# 🗳️ Fiscalizar.org - Sistema de Gestión Electoral

## 📋 Descripción General

**Fiscalizar.org** es una plataforma integral diseñada para la organización, capacitación, asignación y asistencia de fiscales electorales en Argentina. El sistema está orientado a partidos políticos, coaliciones y ONGs que necesitan coordinar operativos electorales de manera eficiente y transparente.

### 🎯 Problema que Soluciona

La organización de fiscales electorales presenta múltiples desafíos:

- **Coordinación compleja**: Manejar cientos o miles de fiscales distribuidos en múltiples zonas, colegios y mesas
- **Asignación territorial**: Asignar fiscales a ubicaciones específicas respetando jerarquías organizativas
- **Seguimiento operativo**: Monitorear el estado de las mesas, conteos de votos y documentación
- **Comunicación eficiente**: Mantener contacto con fiscales distribuidos geográficamente
- **Reportes y análisis**: Generar estadísticas y reportes para la toma de decisiones

## 🏗️ Arquitectura del Sistema

### Backend (API REST)
- **Tecnología**: Node.js 18+ con TypeScript
- **Framework**: Express.js
- **Base de datos**: PostgreSQL (desarrollo: SQLite)
- **ORM**: Prisma
- **Seguridad**: Helmet, CORS configurado
- **Logging**: Sistema estructurado con niveles

### Frontend (Aplicación Web)
- **Framework**: React 19 con TypeScript
- **Build Tool**: Vite
- **Estilos**: Tailwind CSS + shadcn/ui
- **Estado**: Zustand
- **Datos**: TanStack Query v5
- **Navegación**: React Router v6
- **Formularios**: React Hook Form + Zod
- **Gráficos**: Recharts

## 🗂️ Dominio Electoral

### Jerarquía de Entidades

El sistema modela la estructura electoral argentina con la siguiente jerarquía:

```
Zonas (Nivel más alto)
├── Colegios (Establecimientos educativos)
    ├── Mesas (Mesas de votación)
        └── Fiscales (Asignados por tipo de rol)
```

### Entidades Principales

#### 🏢 **Zonas**
- Unidad territorial principal de organización electoral
- Contienen múltiples colegios
- Nivel más alto de la jerarquía organizativa

#### 🏫 **Colegios**
- Establecimientos educativos donde se vota
- Pertenecen a una zona específica
- Contienen múltiples mesas de votación

#### 🗳️ **Mesas**
- Mesas de votación específicas dentro de un colegio
- Registran conteos de votos en diferentes horarios
- Tienen fiscales asignados para su supervisión

#### 👥 **Fiscales**
- Personas con responsabilidades de fiscalización
- Se clasifican por tipo de rol operativo
- Pueden votar en una mesa diferente a la que fiscalizan

#### 🎭 **Tipos de Fiscales**
- **Fiscal General**: Supervisa un colegio completo
- **Fiscal de Mesa**: Supervisa una mesa específica
- **Fiscal de Zona**: Supervisa múltiples colegios en una zona

## 🔧 Funcionalidades del Backend

### API REST Completa

#### **Gestión de Zonas**
- CRUD completo (Crear, Leer, Actualizar, Eliminar)
- Filtros por nombre y estado
- Paginación y ordenamiento
- Soft delete (habilitar/deshabilitar)

#### **Gestión de Colegios**
- CRUD completo con validaciones
- Asignación a zonas
- Filtros por zona, dirección y estado
- Relaciones con zonas y mesas

#### **Gestión de Fiscales**
- CRUD completo con validaciones robustas
- Asignación a tipos de fiscal
- Registro de lugar de votación
- Disponibilidad y notas internas
- Filtros por DNI, email, tipo y estado

#### **Gestión de Mesas**
- CRUD completo
- Asignación a colegios y fiscales
- Registro de conteos de votos por horario
- Filtros por colegio, fiscal y estado

### Características Técnicas

#### **Arquitectura en Capas**
```
Controllers → Services → Repositories → Database
```

#### **Validaciones Robustas**
- Validación de datos de entrada
- Verificación de unicidad (DNI, email)
- Validación de relaciones (zonas, colegios, mesas)
- Manejo de errores estructurado

#### **Sistema de Logging**
- Logs estructurados con niveles (INFO, ERROR, WARN)
- Trazabilidad de operaciones
- Monitoreo de rendimiento

#### **Seguridad**
- Middleware de autenticación
- Validación de headers
- CORS configurado
- Helmet para headers de seguridad

## 🎨 Funcionalidades del Frontend

### Dashboard Principal
- **Estadísticas generales** del sistema
- **Gráficos interactivos** con Recharts
- **Actividad reciente** y accesos rápidos
- **Métricas de rendimiento** electoral

### Gestión de Entidades

#### **Zonas**
- Listado con filtros y búsqueda
- Formularios de creación y edición
- Visualización de colegios asociados
- Estadísticas por zona

#### **Colegios**
- Gestión completa de establecimientos
- Asignación a zonas
- Visualización de mesas
- Mapa de ubicaciones (futuro)

#### **Mesas**
- Control de mesas de votación
- Asignación de fiscales
- Registro de conteos en tiempo real
- Estado operativo

#### **Fiscales**
- Gestión completa de fiscales
- Asignación por tipo y zona
- Registro de disponibilidad
- Comunicación y notificaciones

### Características Técnicas del Frontend

#### **Responsive Design**
- Mobile-first approach
- Adaptable a todos los dispositivos
- Navegación optimizada para móviles

#### **Estado Global**
- Zustand para gestión de estado
- TanStack Query para cache de datos
- Sincronización automática

#### **Formularios Avanzados**
- React Hook Form + Zod
- Validación en tiempo real
- Manejo de errores intuitivo

#### **Testing Completo**
- Vitest + React Testing Library
- Tests unitarios y de integración
- Cobertura de código

## 🔐 Sistema de Autenticación y Permisos

### Estado Actual
- **Autenticación básica** por `client_secret`
- **Sin sistema de roles** implementado
- **Sin restricciones de alcance** territorial

### Diseño Conceptual (Futuro)
- **Roles jerárquicos**: ADMIN → COORDINADOR → FISCAL_ZONA → FISCAL_GENERAL → FISCAL_MESA
- **Alcance territorial**: Restricciones basadas en zonas, colegios y mesas asignadas
- **Herencia de permisos**: Roles superiores incluyen permisos de roles inferiores

## 📊 Flujo de Trabajo Electoral

### 1. **Configuración Inicial**
- Crear zonas electorales
- Registrar colegios por zona
- Definir mesas por colegio

### 2. **Gestión de Fiscales**
- Registrar fiscales con datos personales
- Asignar tipo de fiscal (General, Mesa, Zona)
- Registrar lugar de votación
- Definir disponibilidad

### 3. **Asignación Operativa**
- Asignar fiscales a mesas específicas
- Coordinar horarios y responsabilidades
- Gestionar cobertura territorial

### 4. **Seguimiento en Tiempo Real**
- Registro de conteos de votos por horario
- Monitoreo del estado de las mesas
- Comunicación con fiscales

### 5. **Reportes y Análisis**
- Estadísticas por zona, colegio y mesa
- Análisis de participación
- Reportes de cobertura

## 🚀 Comandos de Desarrollo

### Backend
```bash
# Instalación
npm install

# Desarrollo (SIEMPRE en background)
npm run dev  # con is_background: true

# Base de datos
npx prisma generate
npx prisma db push
npm run db:seed-electoral
```

### Frontend
```bash
# Instalación
npm install

# Desarrollo (SIEMPRE en background)
npm run dev  # con is_background: true

# Build y Preview
npm run build
npm run preview

# Testing
npm run test
npm run test:ui
```

## 📈 Beneficios del Sistema

### Para Organizaciones Políticas
- **Coordinación eficiente** de operativos electorales
- **Visibilidad completa** del estado de las mesas
- **Comunicación centralizada** con fiscales
- **Reportes detallados** para toma de decisiones

### Para Fiscales
- **Claridad en asignaciones** y responsabilidades
- **Comunicación directa** con coordinadores
- **Registro simplificado** de datos operativos
- **Acceso móvil** a información relevante

### Para el Proceso Electoral
- **Transparencia** en la fiscalización
- **Trazabilidad** de operaciones
- **Eficiencia** en la coordinación
- **Calidad** en la supervisión

## 🔮 Roadmap Futuro

### Funcionalidades Planificadas
- **Sistema de roles y permisos** completo
- **Carga de documentación fotográfica** (certificados, actas)
- **Canales de conversación** por zona/colegio/mesa
- **Notificaciones push** y SMS
- **Mapas interactivos** de ubicaciones
- **Reportes avanzados** con visualizaciones

### Mejoras Técnicas
- **Autenticación JWT** robusta
- **API GraphQL** para consultas complejas
- **PWA** para funcionalidad offline
- **Microservicios** para escalabilidad
- **Docker** para deployment

## 📞 Contacto y Soporte

Para más información sobre el sistema o soporte técnico, consultar la documentación técnica en `fiscalizar-docs/` o contactar al equipo de desarrollo.

---

**Fiscalizar.org** - Transformando la gestión electoral en Argentina 🇦🇷
