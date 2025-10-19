# API de Hitos - Fiscalizar Backend

## 📋 **Resumen**

Documentación completa de los endpoints relacionados con el sistema de hitos electorales. Los hitos permiten gestionar actividades del proceso electoral con estados personalizados y relaciones con zonas, colegios, mesas y fiscales.

## 🗄️ **Estructura de Base de Datos**

### **Tabla `Hitos`**

```sql
CREATE TABLE "Hitos" (
  id          SERIAL PRIMARY KEY,
  nombre      VARCHAR NOT NULL UNIQUE,
  descripcion TEXT NOT NULL,
  fecha       TIMESTAMP DEFAULT NOW(),
  createdAt   TIMESTAMP DEFAULT NOW(),
  updatedAt   TIMESTAMP DEFAULT NOW(),
  enabled     BOOLEAN DEFAULT TRUE,
  
  -- Campos de estados personalizados
  estadoDefault        VARCHAR,  -- Estado por defecto para nuevas relaciones
  estado1Reconocimiento VARCHAR, -- Reconocimiento del estado 1
  estado1Color         VARCHAR,  -- Color del estado 1
  estado2Reconocimiento VARCHAR, -- Reconocimiento del estado 2
  estado2Color         VARCHAR,  -- Color del estado 2
  estado3Reconocimiento VARCHAR, -- Reconocimiento del estado 3
  estado3Color         VARCHAR,  -- Color del estado 3
  estado4Reconocimiento VARCHAR, -- Reconocimiento del estado 4
  estado4Color         VARCHAR   -- Color del estado 4
);
```

### **Campos de Estados Personalizados:**

| Campo | Tipo | Descripción | Ejemplo |
|-------|------|-------------|---------|
| `estadoDefault` | `VARCHAR` | Estado por defecto para nuevas relaciones | `"pendiente"` |
| `estado1Reconocimiento` | `VARCHAR` | Reconocimiento del estado 1 | `"Confirmado"` |
| `estado1Color` | `VARCHAR` | Color del estado 1 | `"#28a745"` |
| `estado2Reconocimiento` | `VARCHAR` | Reconocimiento del estado 2 | `"En Progreso"` |
| `estado2Color` | `VARCHAR` | Color del estado 2 | `"#ffc107"` |
| `estado3Reconocimiento` | `VARCHAR` | Reconocimiento del estado 3 | `"Completado"` |
| `estado3Color` | `VARCHAR` | Color del estado 3 | `"#17a2b8"` |
| `estado4Reconocimiento` | `VARCHAR` | Reconocimiento del estado 4 | `"Cancelado"` |
| `estado4Color` | `VARCHAR` | Color del estado 4 | `"#dc3545"` |

## 🚀 **Endpoints de la API**

### **Base URL**
```
http://localhost:3000/api
```

### **⚠️ Autenticación**
Los endpoints de hitos **NO requieren autenticación** con `client_secret`. Son endpoints internos del sistema que pueden ser llamados directamente.

## 📋 **1. Gestión de Hitos**

### **Listar Hitos**
```bash
GET /api/hitos
```

#### **Parámetros de Consulta:**
- `page` (opcional): Número de página (default: 1)
- `limit` (opcional): Elementos por página (default: 10)
- `nombre` (opcional): Filtrar por nombre
- `fechaDesde` (opcional): Filtrar desde fecha
- `fechaHasta` (opcional): Filtrar hasta fecha
- `zonaId` (opcional): Filtrar por zona
- `colegioId` (opcional): Filtrar por colegio
- `mesaId` (opcional): Filtrar por mesa
- `fiscalId` (opcional): Filtrar por fiscal

#### **Ejemplo de Uso:**
```bash
GET /api/hitos?page=1&limit=10&nombre=Capacitación
```

#### **Respuesta:**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": 1,
        "nombre": "Capacitación de Fiscales",
        "descripcion": "Capacitación general para todos los fiscales",
        "fecha": "2025-10-19T23:22:22.112Z",
        "createdAt": "2025-10-19T23:22:22.112Z",
        "updatedAt": "2025-10-19T23:22:32.298Z",
        "enabled": true,
        "estadoDefault": "activo",
        "estado1Reconocimiento": "Asistió",
        "estado1Color": "#007bff",
        "estado2Reconocimiento": "En Curso",
        "estado2Color": "#fd7e14",
        "estado3Reconocimiento": "Finalizado",
        "estado3Color": "#20c997",
        "estado4Reconocimiento": "No Asistió",
        "estado4Color": "#6f42c1",
        "hitoFiscales": [],
        "hitoMesas": [],
        "hitoColegios": [],
        "hitoZonas": []
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 1,
      "totalPages": 1
    }
  },
  "message": "Hitos obtenidos exitosamente",
  "timestamp": "2025-10-19T23:22:26.126Z"
}
```

### **Obtener Hito por ID**
```bash
GET /api/hitos/{id}
```

#### **Respuesta:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "nombre": "Capacitación de Fiscales",
    "descripcion": "Capacitación general para todos los fiscales",
    "fecha": "2025-10-19T23:22:22.112Z",
    "createdAt": "2025-10-19T23:22:22.112Z",
    "updatedAt": "2025-10-19T23:22:32.298Z",
    "enabled": true,
    "estadoDefault": "activo",
    "estado1Reconocimiento": "Asistió",
    "estado1Color": "#007bff",
    "estado2Reconocimiento": "En Curso",
    "estado2Color": "#fd7e14",
    "estado3Reconocimiento": "Finalizado",
    "estado3Color": "#20c997",
    "estado4Reconocimiento": "No Asistió",
    "estado4Color": "#6f42c1",
    "hitoFiscales": [],
    "hitoMesas": [],
    "hitoColegios": [],
    "hitoZonas": []
  },
  "message": "Hito obtenido exitosamente",
  "timestamp": "2025-10-19T23:22:26.126Z"
}
```

### **Crear Hito**
```bash
POST /api/hitos
Content-Type: application/json
```

#### **Cuerpo de la Petición:**
```json
{
  "nombre": "Capacitación de Fiscales",
  "descripcion": "Capacitación general para todos los fiscales",
  "fecha": "2025-10-20T10:00:00.000Z",
  "estadoDefault": "pendiente",
  "estado1Reconocimiento": "Confirmado",
  "estado1Color": "#28a745",
  "estado2Reconocimiento": "En Progreso",
  "estado2Color": "#ffc107",
  "estado3Reconocimiento": "Completado",
  "estado3Color": "#17a2b8",
  "estado4Reconocimiento": "Cancelado",
  "estado4Color": "#dc3545",
  "zonaId": 1,
  "colegioId": 1,
  "mesaId": 1,
  "fiscalId": 1
}
```

#### **Campos Requeridos:**
- `nombre`: Nombre del hito
- `descripcion`: Descripción del hito

#### **Campos Opcionales:**
- `fecha`: Fecha del hito (default: ahora)
- `estadoDefault`: Estado por defecto para nuevas relaciones
- `estado1Reconocimiento`: Reconocimiento del estado 1
- `estado1Color`: Color del estado 1
- `estado2Reconocimiento`: Reconocimiento del estado 2
- `estado2Color`: Color del estado 2
- `estado3Reconocimiento`: Reconocimiento del estado 3
- `estado3Color`: Color del estado 3
- `estado4Reconocimiento`: Reconocimiento del estado 4
- `estado4Color`: Color del estado 4
- `zonaId`: ID de zona para relacionar
- `colegioId`: ID de colegio para relacionar
- `mesaId`: ID de mesa para relacionar
- `fiscalId`: ID de fiscal para relacionar

#### **Respuesta:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "nombre": "Capacitación de Fiscales",
    "descripcion": "Capacitación general para todos los fiscales",
    "fecha": "2025-10-19T23:22:22.112Z",
    "createdAt": "2025-10-19T23:22:22.112Z",
    "updatedAt": "2025-10-19T23:22:22.112Z",
    "enabled": true,
    "estadoDefault": "pendiente",
    "estado1Reconocimiento": "Confirmado",
    "estado1Color": "#28a745",
    "estado2Reconocimiento": "En Progreso",
    "estado2Color": "#ffc107",
    "estado3Reconocimiento": "Completado",
    "estado3Color": "#17a2b8",
    "estado4Reconocimiento": "Cancelado",
    "estado4Color": "#dc3545"
  },
  "message": "Hito creado exitosamente",
  "timestamp": "2025-10-19T23:22:22.112Z"
}
```

### **Actualizar Hito**
```bash
PUT /api/hitos/{id}
Content-Type: application/json
```

#### **Cuerpo de la Petición:**
```json
{
  "nombre": "Capacitación de Fiscales Actualizada",
  "descripcion": "Capacitación general actualizada",
  "fecha": "2025-10-21T10:00:00.000Z",
  "estadoDefault": "activo",
  "estado1Reconocimiento": "Asistió",
  "estado1Color": "#007bff",
  "estado2Reconocimiento": "En Curso",
  "estado2Color": "#fd7e14",
  "estado3Reconocimiento": "Finalizado",
  "estado3Color": "#20c997",
  "estado4Reconocimiento": "No Asistió",
  "estado4Color": "#6f42c1",
  "zonaId": 1
}
```

#### **Respuesta:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "nombre": "Capacitación de Fiscales Actualizada",
    "descripcion": "Capacitación general actualizada",
    "fecha": "2025-10-19T23:22:22.112Z",
    "createdAt": "2025-10-19T23:22:22.112Z",
    "updatedAt": "2025-10-19T23:22:32.298Z",
    "enabled": true,
    "estadoDefault": "activo",
    "estado1Reconocimiento": "Asistió",
    "estado1Color": "#007bff",
    "estado2Reconocimiento": "En Curso",
    "estado2Color": "#fd7e14",
    "estado3Reconocimiento": "Finalizado",
    "estado3Color": "#20c997",
    "estado4Reconocimiento": "No Asistió",
    "estado4Color": "#6f42c1"
  },
  "message": "Hito actualizado exitosamente",
  "timestamp": "2025-10-19T23:22:32.298Z"
}
```

### **Eliminar Hito (Soft Delete)**
```bash
DELETE /api/hitos/{id}
```

#### **Respuesta:**
```json
{
  "success": true,
  "data": null,
  "message": "Hito eliminado exitosamente",
  "timestamp": "2025-10-19T23:22:35.123Z"
}
```

### **Obtener Hitos por Entidad**
```bash
GET /api/hitos/entity/{entityType}/{entityId}
```

#### **Parámetros:**
- `entityType`: Tipo de entidad (`zona`, `colegio`, `mesa`, `fiscal`)
- `entityId`: ID de la entidad

#### **Ejemplo:**
```bash
GET /api/hitos/entity/fiscal/5
GET /api/hitos/entity/colegio/3
GET /api/hitos/entity/mesa/10
GET /api/hitos/entity/zona/2
```

#### **Respuesta:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "nombre": "Capacitación de Fiscales",
      "descripcion": "Capacitación general para todos los fiscales",
      "fecha": "2025-10-19T23:22:22.112Z",
      "estadoDefault": "activo",
      "estado1Reconocimiento": "Asistió",
      "estado1Color": "#007bff",
      "estado2Reconocimiento": "En Curso",
      "estado2Color": "#fd7e14",
      "estado3Reconocimiento": "Finalizado",
      "estado3Color": "#20c997",
      "estado4Reconocimiento": "No Asistió",
      "estado4Color": "#6f42c1"
    }
  ],
  "message": "Hitos recientes obtenidos exitosamente",
  "timestamp": "2025-10-19T23:22:26.126Z"
}
```

## 🔗 **2. Relaciones con Entidades**

### **Hitos y Zonas**
- **Documentación**: [hitos-y-zonas.md](./hitos-y-zonas.md)
- **Endpoints**: `/api/hito-zonas`

### **Hitos y Colegios**
- **Documentación**: [hitos-y-colegios.md](./hitos-y-colegios.md)
- **Endpoints**: `/api/hito-colegios`

### **Hitos y Mesas**
- **Documentación**: [hitos-y-mesas.md](./hitos-y-mesas.md)
- **Endpoints**: `/api/hito-mesas`

### **Hitos y Fiscales**
- **Documentación**: [hitos-y-fiscales.md](./hitos-y-fiscales.md)
- **Endpoints**: `/api/hito-fiscales`

## 🎨 **3. Estados Personalizados**

### **Configuración de Estados**

Los hitos permiten configurar hasta 4 estados personalizados con sus respectivos colores:

#### **Estado por Defecto:**
- `estadoDefault`: Estado inicial para nuevas relaciones

#### **Estados Personalizados:**
- `estado1Reconocimiento` + `estado1Color`: Estado 1 y su color
- `estado2Reconocimiento` + `estado2Color`: Estado 2 y su color
- `estado3Reconocimiento` + `estado3Color`: Estado 3 y su color
- `estado4Reconocimiento` + `estado4Color`: Estado 4 y su color

### **Ejemplo de Configuración:**
```json
{
  "estadoDefault": "pendiente",
  "estado1Reconocimiento": "Confirmado",
  "estado1Color": "#28a745",
  "estado2Reconocimiento": "En Progreso",
  "estado2Color": "#ffc107",
  "estado3Reconocimiento": "Completado",
  "estado3Color": "#17a2b8",
  "estado4Reconocimiento": "Cancelado",
  "estado4Color": "#dc3545"
}
```

## 🔧 **4. Ejemplos de Uso**

### **Crear Hito con Estados Personalizados**
```bash
curl -X POST http://localhost:3000/api/hitos \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación Electoral",
    "descripcion": "Capacitación obligatoria para todos los fiscales",
    "estadoDefault": "pendiente",
    "estado1Reconocimiento": "Confirmado",
    "estado1Color": "#28a745",
    "estado2Reconocimiento": "En Progreso",
    "estado2Color": "#ffc107",
    "estado3Reconocimiento": "Completado",
    "estado3Color": "#17a2b8",
    "estado4Reconocimiento": "Cancelado",
    "estado4Color": "#dc3545"
  }'
```

### **Actualizar Estados de un Hito**
```bash
curl -X PUT http://localhost:3000/api/hitos/1 \
  -H "Content-Type: application/json" \
  -d '{
    "estadoDefault": "activo",
    "estado1Reconocimiento": "Asistió",
    "estado1Color": "#007bff",
    "estado2Reconocimiento": "En Curso",
    "estado2Color": "#fd7e14",
    "estado3Reconocimiento": "Finalizado",
    "estado3Color": "#20c997",
    "estado4Reconocimiento": "No Asistió",
    "estado4Color": "#6f42c1"
  }'
```

### **Consultar Hitos con Filtros**
```bash
# Filtrar por nombre
curl "http://localhost:3000/api/hitos?nombre=Capacitación"

# Filtrar por fecha
curl "http://localhost:3000/api/hitos?fechaDesde=2025-10-01&fechaHasta=2025-10-31"

# Filtrar por zona
curl "http://localhost:3000/api/hitos?zonaId=1"

# Combinar filtros
curl "http://localhost:3000/api/hitos?nombre=Capacitación&zonaId=1&page=1&limit=5"
```

## 📊 **5. Casos de Uso Típicos**

### **1. Capacitación Electoral**
- Crear hito con estados: `pendiente` → `confirmado` → `asistio` → `completado`
- Asignar a todas las zonas/colegios/mesas/fiscales
- Seguir el flujo de estados según la participación

### **2. Reunión de Coordinación**
- Crear hito con estados personalizados
- Asignar solo entidades específicas
- Usar estados como: `invitado` → `confirmado` → `activo` → `finalizado`

### **3. Seguimiento de Actividades**
- Consultar hitos por entidad
- Generar reportes de participación
- Analizar patrones de asistencia

## ⚠️ **6. Consideraciones Importantes**

### **Validaciones:**
- `nombre` y `descripcion` son requeridos
- `nombre` debe ser único
- Los colores deben ser válidos (formato hexadecimal recomendado)
- Al menos una entidad relacionada debe ser especificada en actualizaciones

### **Estados:**
- Los estados son strings libres
- Se recomienda usar valores consistentes
- Los colores deben ser válidos para la interfaz

### **Relaciones:**
- Un hito puede tener múltiples entidades
- Las relaciones se crean automáticamente
- Se pueden actualizar las relaciones existentes

### **Eliminación:**
- Los hitos se eliminan con soft delete (`enabled: false`)
- Las relaciones se mantienen pero se marcan como inactivas
- Se puede restaurar un hito eliminado

## 🚀 **7. Próximos Pasos**

1. **Notificaciones**: Implementar notificaciones automáticas por cambio de estado
2. **Reportes**: Crear reportes de participación por período
3. **Integración**: Conectar con sistema de notificaciones por email/SMS
4. **Analytics**: Agregar métricas de participación y asistencia
5. **UI**: Crear interfaz para gestión visual de estados y colores

## 📚 **8. Documentación Relacionada**

- [Sistema de Hitos y Zonas](./hitos-y-zonas.md)
- [Sistema de Hitos y Colegios](./hitos-y-colegios.md)
- [Sistema de Hitos y Mesas](./hitos-y-mesas.md)
- [Sistema de Hitos y Fiscales](./hitos-y-fiscales.md)
- [Dominio de Negocio y Permisos](./dominio-negocio-y-permisos.md)
- [Testing de API](./backend-api-testing.md)
