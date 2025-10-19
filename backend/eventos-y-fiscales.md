# Sistema de Eventos y Fiscales - Fiscalizar Backend

## 📋 **Resumen**

El sistema de eventos y fiscales permite gestionar la relación entre eventos electorales y los fiscales que participan en ellos. Cada relación tiene un estado específico que permite hacer seguimiento de la participación de los fiscales en los eventos.

## 🗄️ **Estructura de Base de Datos**

### **Tabla `Eventos`**

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

### **Tabla `EventoFiscales`**

```sql
CREATE TABLE "EventoFiscales" (
  id        SERIAL PRIMARY KEY,
  eventoId  INTEGER NOT NULL,
  fiscalId  INTEGER NOT NULL,
  estado    VARCHAR DEFAULT 'pendiente',
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW(),
  enabled   BOOLEAN DEFAULT TRUE,
  
  -- Claves foráneas
  FOREIGN KEY (eventoId) REFERENCES "Eventos"(id) ON DELETE CASCADE,
  FOREIGN KEY (fiscalId) REFERENCES "Fiscales"(id) ON DELETE CASCADE,
  
  -- Restricción única
  UNIQUE(eventoId, fiscalId)
);
```

#### **Campos:**
- **`id`**: Identificador único de la relación
- **`eventoId`**: ID del evento (FK → Eventos.id)
- **`fiscalId`**: ID del fiscal (FK → Fiscales.id)
- **`estado`**: Estado de la relación
- **`createdAt`**: Fecha de creación de la relación
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado de la relación (true/false)

## 🔗 **Relación entre Tablas**

### **Diagrama de Relación**

```
Eventos (1) ←→ (N) EventoFiscales (N) ←→ (1) Fiscales
```

### **Características de la Relación:**

1. **Relación N:N**: Un evento puede tener múltiples fiscales, un fiscal puede estar en múltiples eventos
2. **Estado específico**: Cada relación tiene su propio estado
3. **Cascade Delete**: Si se elimina un evento o fiscal, se eliminan todas sus relaciones
4. **Restricción única**: No puede haber duplicados (evento-fiscal)

## 📊 **Estados Disponibles**

| Estado | Descripción | Uso Típico |
|--------|-------------|------------|
| `pendiente` | Estado inicial por defecto | Fiscal invitado pero sin respuesta |
| `confirmado` | Fiscal confirmó asistencia | Fiscal confirmó que asistirá |
| `asistio` | Fiscal asistió al evento | Evento realizado, fiscal presente |
| `no_asistio` | Fiscal no asistió | Evento realizado, fiscal ausente |
| `cancelado` | Evento cancelado para este fiscal | Evento cancelado o fiscal excluido |

## 🚀 **Endpoints de la API**

### **Base URL**
```
http://localhost:3000/api
```

### **⚠️ Autenticación**
Los endpoints de `evento-fiscales` **NO requieren autenticación** con `client_secret`. Son endpoints internos del sistema que pueden ser llamados directamente.

### **1. Gestión de Eventos**

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
        "descripcion": "Capacitación obligatoria para todos los fiscales",
        "fecha": "2025-10-18T22:48:14.354Z",
        "eventoFiscales": [
          {
            "id": 1,
            "estado": "confirmado",
            "fiscal": {
              "id": 92,
              "dni": "33897577",
              "nombre": "Damian Herrera"
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

### **2. Gestión de Relaciones Evento-Fiscal**

#### **Listar Relaciones por Evento (Requerido)**
```bash
# Obtener todas las relaciones de un evento
GET /api/evento-fiscales?eventoId=1

# Filtrar por estado
GET /api/evento-fiscales?eventoId=1&estado=confirmado
```

#### **Endpoints Semánticos**
```bash
# Obtener fiscales de un evento
GET /api/evento-fiscales/evento/{eventoId}

# Obtener fiscales de un evento con estado específico
GET /api/evento-fiscales/evento/{eventoId}/estado/{estado}
```

#### **Crear Relación Evento-Fiscal**
```bash
POST /api/evento-fiscales
Content-Type: application/json

{
  "eventoId": 1,
  "fiscalId": 92,
  "estado": "pendiente"
}
```

#### **Actualizar Estado**
```bash
PATCH /api/evento-fiscales/{id}/estado
Content-Type: application/json

{
  "estado": "confirmado"
}
```

#### **Crear Relaciones Masivas**
```bash
POST /api/evento-fiscales/masivo
Content-Type: application/json

{
  "eventoId": 1,
  "fiscalIds": [92, 93, 94],
  "estado": "pendiente"
}
```

### **3. Consultas Especializadas**

#### **Eventos por Fiscal**
```bash
GET /api/evento-fiscales/fiscal/{fiscalId}/eventos
```

#### **Estados Disponibles**
```bash
GET /api/evento-fiscales/estados
```

#### **Estadísticas por Estado**
```bash
GET /api/evento-fiscales/estadisticas
```

## 📝 **Ejemplos de Uso**

### **Caso 1: Crear un Evento y Asignar Fiscales**

```bash
# 1. Crear el evento
curl -X POST "http://localhost:3000/api/eventos" \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación de Fiscales",
    "descripcion": "Capacitación obligatoria para todos los fiscales de la zona"
  }'

# 2. Asignar fiscales al evento
curl -X POST "http://localhost:3000/api/evento-fiscales/masivo" \
  -H "Content-Type: application/json" \
  -d '{
    "eventoId": 1,
    "fiscalIds": [92, 93, 94],
    "estado": "pendiente"
  }'
```

### **Caso 2: Seguimiento de Asistencia**

```bash
# 1. Ver quién confirmó asistencia (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-fiscales/evento/1/estado/confirmado"

# 2. Actualizar estado a "asistio" (SIN autenticación)
curl -X PATCH "http://localhost:3000/api/evento-fiscales/1/estado" \
  -H "Content-Type: application/json" \
  -d '{"estado": "asistio"}'

# 3. Ver estadísticas del evento (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-fiscales/estadisticas"
```

### **Caso 3: Consultar Eventos de un Fiscal**

```bash
# Ver todos los eventos de un fiscal (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-fiscales/fiscal/92/eventos"
```

## 🔍 **Consultas SQL Típicas**

### **Obtener Fiscales de un Evento**
```sql
SELECT 
  ef.id,
  ef.estado,
  f.dni,
  f.nombre,
  f.apellido,
  f.email
FROM "EventoFiscales" ef
JOIN "Fiscales" f ON ef.fiscalId = f.id
WHERE ef.eventoId = 1 
  AND ef.enabled = true
ORDER BY ef.estado, f.apellido;
```

### **Obtener Eventos de un Fiscal**
```sql
SELECT 
  ef.id,
  ef.estado,
  e.nombre,
  e.descripcion,
  e.fecha
FROM "EventoFiscales" ef
JOIN "Eventos" e ON ef.eventoId = e.id
WHERE ef.fiscalId = 92 
  AND ef.enabled = true
ORDER BY e.fecha DESC;
```

### **Estadísticas por Estado**
```sql
SELECT 
  estado,
  COUNT(*) as cantidad
FROM "EventoFiscales"
WHERE eventoId = 1 
  AND enabled = true
GROUP BY estado
ORDER BY cantidad DESC;
```

## ⚠️ **Reglas de Negocio**

### **1. Validaciones Obligatorias**
- **EventoId requerido**: Todas las consultas de relaciones requieren especificar el evento
- **Estados válidos**: Solo se permiten los estados predefinidos
- **Relación única**: No puede haber duplicados (evento-fiscal)

### **2. Comportamientos del Sistema**
- **Cascade Delete**: Eliminar un evento o fiscal elimina todas sus relaciones
- **Soft Delete**: Las relaciones se desactivan (enabled: false) en lugar de eliminarse
- **Estados por defecto**: Las nuevas relaciones inician en estado "pendiente"

### **3. Flujo Típico de Estados**
```
pendiente → confirmado → asistio
    ↓           ↓
cancelado   no_asistio
```

## 🎯 **Casos de Uso del Negocio**

### **1. Gestión de Capacitaciones**
- Crear evento de capacitación
- Asignar fiscales (estado: pendiente)
- Fiscales confirman asistencia (estado: confirmado)
- Registrar asistencia real (estado: asistio/no_asistio)

### **2. Reuniones de Coordinación**
- Crear evento de reunión
- Asignar fiscales de una zona específica
- Seguimiento de confirmaciones
- Reporte de asistencia

### **3. Eventos Especiales**
- Crear eventos específicos
- Asignar fiscales seleccionados
- Seguimiento personalizado
- Reportes de participación

## 📊 **Monitoreo y Reportes**

### **Métricas Disponibles**
- Total de fiscales por evento
- Distribución por estado
- Fiscales que no confirmaron
- Tasa de asistencia por evento
- Historial de eventos por fiscal

### **Endpoints de Reportes**
```bash
# Estadísticas generales
GET /api/evento-fiscales/estadisticas

# Fiscales pendientes de confirmación
GET /api/evento-fiscales/evento/{id}/estado/pendiente

# Fiscales que asistieron
GET /api/evento-fiscales/evento/{id}/estado/asistio
```

## 🔧 **Mantenimiento**

### **Limpieza de Datos**
```sql
-- Eliminar relaciones desactivadas antiguas
DELETE FROM "EventoFiscales" 
WHERE enabled = false 
  AND updatedAt < NOW() - INTERVAL '30 days';
```

### **Optimización de Consultas**
- Usar índices en eventoId, fiscalId y estado
- Paginación en consultas grandes
- Filtros por fecha para consultas históricas

---

**Nota**: Este sistema permite una gestión completa y flexible de la relación entre eventos electorales y fiscales, con seguimiento detallado de estados y participación.
