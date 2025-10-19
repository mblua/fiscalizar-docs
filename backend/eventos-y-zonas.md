# Sistema de Eventos y Zonas - Fiscalizar Backend

## 📋 **Resumen**

El sistema de eventos y zonas permite gestionar la relación entre eventos electorales y las zonas que participan en ellos. Cada relación tiene un estado específico que permite hacer seguimiento de la participación de las zonas en los eventos.

## 🗄️ **Estructura de Base de Datos**

### **Tabla `Eventos`** (Ya existente)

```sql
CREATE TABLE "Eventos" (
  id          SERIAL PRIMARY KEY,
  nombre      VARCHAR NOT NULL,
  descripcion TEXT NOT NULL,
  fecha       TIMESTAMP DEFAULT NOW(),
  createdAt   TIMESTAMP DEFAULT NOW(),
  updatedAt   TIMESTAMP DEFAULT NOW(),
  enabled     BOOLEAN DEFAULT TRUE
);
```

#### **Campos:**
- **`id`**: Identificador único del evento
- **`nombre`**: Nombre del evento (ej: "Capacitación", "Reunión de Coordinación")
- **`descripcion`**: Descripción detallada del evento
- **`fecha`**: Fecha y hora del evento (automática)
- **`createdAt`**: Fecha de creación del registro
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado del evento (true/false)

### **Tabla `EventoZonas`** (Nueva)

```sql
CREATE TABLE "EventoZonas" (
  id        SERIAL PRIMARY KEY,
  eventoId  INTEGER NOT NULL,
  zonaId    INTEGER NOT NULL,
  estado    VARCHAR DEFAULT 'pendiente',
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW(),
  enabled   BOOLEAN DEFAULT TRUE,
  
  -- Claves foráneas
  FOREIGN KEY (eventoId) REFERENCES "Eventos"(id) ON DELETE CASCADE,
  FOREIGN KEY (zonaId) REFERENCES "Zonas"(id) ON DELETE CASCADE,
  
  -- Restricción única
  UNIQUE(eventoId, zonaId)
);
```

#### **Campos:**
- **`id`**: Identificador único de la relación
- **`eventoId`**: ID del evento (FK → Eventos.id)
- **`zonaId`**: ID de la zona (FK → Zonas.id)
- **`estado`**: Estado de la relación
- **`createdAt`**: Fecha de creación de la relación
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado de la relación (true/false)

## 🔗 **Relación entre Tablas**

### **Diagrama de Relación**

```
Eventos (1) ←→ (N) EventoZonas (N) ←→ (1) Zonas
```

### **Características de la Relación:**

1. **Relación N:N**: Un evento puede tener múltiples zonas, una zona puede estar en múltiples eventos
2. **Estado específico**: Cada relación tiene su propio estado
3. **Cascade Delete**: Si se elimina un evento o zona, se eliminan todas sus relaciones
4. **Restricción única**: No puede haber duplicados (evento-zona)

## 📊 **Estados Disponibles**

| Estado | Descripción | Uso Típico |
|--------|-------------|------------|
| `pendiente` | Estado inicial por defecto | Zona invitada pero sin respuesta |
| `confirmado` | Zona confirmó participación | Zona confirmó que participará |
| `activo` | Zona está activa en el evento | Zona participando activamente |
| `inactivo` | Zona no está participando | Zona no participa en el evento |
| `cancelado` | Evento cancelado para esta zona | Evento cancelado o zona excluida |

## 🚀 **Endpoints de la API**

### **Base URL**
```
http://localhost:3000/api
```

### **⚠️ Autenticación**
Los endpoints de `evento-zonas` **NO requieren autenticación** con `client_secret`. Son endpoints internos del sistema que pueden ser llamados directamente.

### **1. Gestión de Eventos** (Ya implementado)

#### **Listar Eventos**
```bash
GET /api/eventos
```

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "data": [
      {
        "id": 1,
        "nombre": "Capacitación General",
        "descripcion": "Capacitación obligatoria para todas las zonas",
        "fecha": "2025-10-18T22:48:14.354Z",
        "eventoZonas": [
          {
            "id": 1,
            "estado": "confirmado",
            "zona": {
              "id": 1,
              "nombre": "Zona Centro"
            }
          }
        ]
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### **Crear Evento**
```bash
POST /api/eventos
Content-Type: application/json

{
  "nombre": "Nuevo Evento",
  "descripcion": "Descripción del evento"
}
```

#### **Obtener Evento por ID**
```bash
GET /api/eventos/{id}
```

### **2. Gestión de Relaciones Evento-Zona**

#### **Listar Relaciones por Evento (Requerido)**
```bash
# Obtener todas las relaciones de un evento
GET /api/evento-zonas?eventoId=1

# Filtrar por estado
GET /api/evento-zonas?eventoId=1&estado=confirmado
```

#### **Endpoints Semánticos**
```bash
# Obtener zonas de un evento
GET /api/evento-zonas/evento/{eventoId}

# Obtener zonas de un evento con estado específico
GET /api/evento-zonas/evento/{eventoId}/estado/{estado}
```

#### **Crear Relación Evento-Zona**
```bash
POST /api/evento-zonas
Content-Type: application/json

{
  "eventoId": 1,
  "zonaId": 1,
  "estado": "pendiente"
}
```

#### **Actualizar Estado**
```bash
PATCH /api/evento-zonas/{id}/estado
Content-Type: application/json

{
  "estado": "confirmado"
}
```

#### **Crear Relaciones Masivas**
```bash
POST /api/evento-zonas/masivo
Content-Type: application/json

{
  "eventoId": 1,
  "zonaIds": [1, 2, 3],
  "estado": "pendiente"
}
```

### **3. Consultas Especializadas**

#### **Eventos por Zona**
```bash
GET /api/evento-zonas/zona/{zonaId}/eventos
```

#### **Estados Disponibles**
```bash
GET /api/evento-zonas/estados
```

#### **Estadísticas por Estado**
```bash
GET /api/evento-zonas/estadisticas
```

## 📝 **Ejemplos de Uso**

### **Caso 1: Crear un Evento y Asignar Zonas**

```bash
# 1. Crear el evento
curl -X POST "http://localhost:3000/api/eventos" \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación de Zonas",
    "descripcion": "Capacitación obligatoria para todas las zonas"
  }'

# 2. Asignar zonas al evento (SIN autenticación)
curl -X POST "http://localhost:3000/api/evento-zonas/masivo" \
  -H "Content-Type: application/json" \
  -d '{
    "eventoId": 1,
    "zonaIds": [1, 2, 3],
    "estado": "pendiente"
  }'
```

### **Caso 2: Seguimiento de Participación**

```bash
# 1. Ver qué zonas confirmaron participación (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-zonas/evento/1/estado/confirmado"

# 2. Actualizar estado a "activo" (SIN autenticación)
curl -X PATCH "http://localhost:3000/api/evento-zonas/1/estado" \
  -H "Content-Type: application/json" \
  -d '{"estado": "activo"}'

# 3. Ver estadísticas del evento (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-zonas/estadisticas"
```

### **Caso 3: Consultar Eventos de una Zona**

```bash
# Ver todos los eventos de una zona (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-zonas/zona/1/eventos"
```

## 🔍 **Consultas SQL Típicas**

### **Obtener Zonas de un Evento**
```sql
SELECT 
  ez.id,
  ez.estado,
  z.nombre as zona
FROM "EventoZonas" ez
JOIN "Zonas" z ON ez.zonaId = z.id
WHERE ez.eventoId = 1 
  AND ez.enabled = true
ORDER BY ez.estado, z.nombre;
```

### **Obtener Eventos de una Zona**
```sql
SELECT 
  ez.id,
  ez.estado,
  e.nombre,
  e.descripcion,
  e.fecha
FROM "EventoZonas" ez
JOIN "Eventos" e ON ez.eventoId = e.id
WHERE ez.zonaId = 1 
  AND ez.enabled = true
ORDER BY e.fecha DESC;
```

### **Estadísticas por Estado**
```sql
SELECT 
  estado,
  COUNT(*) as cantidad
FROM "EventoZonas"
WHERE eventoId = 1 
  AND enabled = true
GROUP BY estado
ORDER BY cantidad DESC;
```

## ⚠️ **Reglas de Negocio**

### **1. Validaciones Obligatorias**
- **EventoId requerido**: Todas las consultas de relaciones requieren especificar el evento
- **Estados válidos**: Solo se permiten los estados predefinidos
- **Relación única**: No puede haber duplicados (evento-zona)

### **2. Comportamientos del Sistema**
- **Cascade Delete**: Eliminar un evento o zona elimina todas sus relaciones
- **Soft Delete**: Las relaciones se desactivan (enabled: false) en lugar de eliminarse
- **Estados por defecto**: Las nuevas relaciones inician en estado "pendiente"

### **3. Flujo Típico de Estados**
```
pendiente → confirmado → activo
    ↓           ↓
cancelado   inactivo
```

## 🎯 **Casos de Uso del Negocio**

### **1. Gestión de Capacitaciones**
- Crear evento de capacitación
- Asignar zonas (estado: pendiente)
- Zonas confirman participación (estado: confirmado)
- Registrar participación activa (estado: activo/inactivo)

### **2. Reuniones de Coordinación**
- Crear evento de reunión
- Asignar zonas específicas
- Seguimiento de confirmaciones
- Reporte de participación

### **3. Eventos Especiales**
- Crear eventos específicos
- Asignar zonas seleccionadas
- Seguimiento personalizado
- Reportes de participación

## 📊 **Monitoreo y Reportes**

### **Métricas Disponibles**
- Total de zonas por evento
- Distribución por estado
- Zonas que no confirmaron
- Tasa de participación por evento
- Historial de eventos por zona

### **Endpoints de Reportes**
```bash
# Estadísticas generales
GET /api/evento-zonas/estadisticas

# Zonas pendientes de confirmación
GET /api/evento-zonas/evento/{id}/estado/pendiente

# Zonas que participaron activamente
GET /api/evento-zonas/evento/{id}/estado/activo
```

## 🔧 **Mantenimiento**

### **Limpieza de Datos**
```sql
-- Eliminar relaciones desactivadas antiguas
DELETE FROM "EventoZonas" 
WHERE enabled = false 
  AND updatedAt < NOW() - INTERVAL '30 days';
```

### **Optimización de Consultas**
- Usar índices en eventoId, zonaId y estado
- Paginación en consultas grandes
- Filtros por fecha para consultas históricas

---

**Nota**: Este sistema permite una gestión completa y flexible de la relación entre eventos electorales y zonas, con seguimiento detallado de estados y participación.
