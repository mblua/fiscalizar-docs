# Sistema de Eventos y Mesas - Fiscalizar Backend

## 📋 **Resumen**

El sistema de eventos y mesas permite gestionar la relación entre eventos electorales y las mesas que participan en ellos. Cada relación tiene un estado específico que permite hacer seguimiento de la participación de las mesas en los eventos.

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

### **Tabla `EventoMesas`** (Nueva)

```sql
CREATE TABLE "EventoMesas" (
  id        SERIAL PRIMARY KEY,
  eventoId  INTEGER NOT NULL,
  mesaId    INTEGER NOT NULL,
  estado    VARCHAR DEFAULT 'pendiente',
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW(),
  enabled   BOOLEAN DEFAULT TRUE,
  
  -- Claves foráneas
  FOREIGN KEY (eventoId) REFERENCES "Eventos"(id) ON DELETE CASCADE,
  FOREIGN KEY (mesaId) REFERENCES "Mesas"(id) ON DELETE CASCADE,
  
  -- Restricción única
  UNIQUE(eventoId, mesaId)
);
```

#### **Campos:**
- **`id`**: Identificador único de la relación
- **`eventoId`**: ID del evento (FK → Eventos.id)
- **`mesaId`**: ID de la mesa (FK → Mesas.id)
- **`estado`**: Estado de la relación
- **`createdAt`**: Fecha de creación de la relación
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado de la relación (true/false)

## 🔗 **Relación entre Tablas**

### **Diagrama de Relación**

```
Eventos (1) ←→ (N) EventoMesas (N) ←→ (1) Mesas
```

### **Características de la Relación:**

1. **Relación N:N**: Un evento puede tener múltiples mesas, una mesa puede estar en múltiples eventos
2. **Estado específico**: Cada relación tiene su propio estado
3. **Cascade Delete**: Si se elimina un evento o mesa, se eliminan todas sus relaciones
4. **Restricción única**: No puede haber duplicados (evento-mesa)

## 📊 **Estados Disponibles**

| Estado | Descripción | Uso Típico |
|--------|-------------|------------|
| `pendiente` | Estado inicial por defecto | Mesa invitada pero sin respuesta |
| `confirmado` | Mesa confirmó participación | Mesa confirmó que participará |
| `activo` | Mesa está activa en el evento | Mesa participando activamente |
| `inactivo` | Mesa no está participando | Mesa no participa en el evento |
| `cancelado` | Evento cancelado para esta mesa | Evento cancelado o mesa excluida |

## 🚀 **Endpoints de la API**

### **Base URL**
```
http://localhost:3000/api
```

### **⚠️ Autenticación**
Los endpoints de `evento-mesas` **NO requieren autenticación** con `client_secret`. Son endpoints internos del sistema que pueden ser llamados directamente.

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
        "descripcion": "Capacitación obligatoria para todas las mesas",
        "fecha": "2025-10-18T22:48:14.354Z",
        "eventoMesas": [
          {
            "id": 1,
            "estado": "confirmado",
            "mesa": {
              "id": 1,
              "numero": "Mesa 001",
              "colegio": {
                "id": 1,
                "nombre": "Escuela Primaria N° 1"
              }
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

### **2. Gestión de Relaciones Evento-Mesa**

#### **Listar Relaciones por Evento (Requerido)**
```bash
# Obtener todas las relaciones de un evento
GET /api/evento-mesas?eventoId=1

# Filtrar por estado
GET /api/evento-mesas?eventoId=1&estado=confirmado
```

#### **Endpoints Semánticos**
```bash
# Obtener mesas de un evento
GET /api/evento-mesas/evento/{eventoId}

# Obtener mesas de un evento con estado específico
GET /api/evento-mesas/evento/{eventoId}/estado/{estado}
```

#### **Crear Relación Evento-Mesa**
```bash
POST /api/evento-mesas
Content-Type: application/json

{
  "eventoId": 1,
  "mesaId": 1,
  "estado": "pendiente"
}
```

#### **Actualizar Estado**
```bash
PATCH /api/evento-mesas/{id}/estado
Content-Type: application/json

{
  "estado": "confirmado"
}
```

#### **Crear Relaciones Masivas**
```bash
POST /api/evento-mesas/masivo
Content-Type: application/json

{
  "eventoId": 1,
  "mesaIds": [1, 2, 3],
  "estado": "pendiente"
}
```

### **3. Consultas Especializadas**

#### **Eventos por Mesa**
```bash
GET /api/evento-mesas/mesa/{mesaId}/eventos
```

#### **Estados Disponibles**
```bash
GET /api/evento-mesas/estados
```

#### **Estadísticas por Estado**
```bash
GET /api/evento-mesas/estadisticas
```

## 📝 **Ejemplos de Uso**

### **Caso 1: Crear un Evento y Asignar Mesas**

```bash
# 1. Crear el evento
curl -X POST "http://localhost:3000/api/eventos" \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación de Mesas",
    "descripcion": "Capacitación obligatoria para todas las mesas de la zona"
  }'

# 2. Asignar mesas al evento (SIN autenticación)
curl -X POST "http://localhost:3000/api/evento-mesas/masivo" \
  -H "Content-Type: application/json" \
  -d '{
    "eventoId": 1,
    "mesaIds": [1, 2, 3],
    "estado": "pendiente"
  }'
```

### **Caso 2: Seguimiento de Participación**

```bash
# 1. Ver qué mesas confirmaron participación (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-mesas/evento/1/estado/confirmado"

# 2. Actualizar estado a "activo" (SIN autenticación)
curl -X PATCH "http://localhost:3000/api/evento-mesas/1/estado" \
  -H "Content-Type: application/json" \
  -d '{"estado": "activo"}'

# 3. Ver estadísticas del evento (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-mesas/estadisticas"
```

### **Caso 3: Consultar Eventos de una Mesa**

```bash
# Ver todos los eventos de una mesa (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-mesas/mesa/1/eventos"
```

## 🔍 **Consultas SQL Típicas**

### **Obtener Mesas de un Evento**
```sql
SELECT 
  em.id,
  em.estado,
  m.numero,
  c.nombre as colegio,
  z.nombre as zona
FROM "EventoMesas" em
JOIN "Mesas" m ON em.mesaId = m.id
JOIN "Colegios" c ON m.colegioId = c.id
JOIN "Zonas" z ON c.zonaId = z.id
WHERE em.eventoId = 1 
  AND em.enabled = true
ORDER BY em.estado, m.numero;
```

### **Obtener Eventos de una Mesa**
```sql
SELECT 
  em.id,
  em.estado,
  e.nombre,
  e.descripcion,
  e.fecha
FROM "EventoMesas" em
JOIN "Eventos" e ON em.eventoId = e.id
WHERE em.mesaId = 1 
  AND em.enabled = true
ORDER BY e.fecha DESC;
```

### **Estadísticas por Estado**
```sql
SELECT 
  estado,
  COUNT(*) as cantidad
FROM "EventoMesas"
WHERE eventoId = 1 
  AND enabled = true
GROUP BY estado
ORDER BY cantidad DESC;
```

## ⚠️ **Reglas de Negocio**

### **1. Validaciones Obligatorias**
- **EventoId requerido**: Todas las consultas de relaciones requieren especificar el evento
- **Estados válidos**: Solo se permiten los estados predefinidos
- **Relación única**: No puede haber duplicados (evento-mesa)

### **2. Comportamientos del Sistema**
- **Cascade Delete**: Eliminar un evento o mesa elimina todas sus relaciones
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
- Asignar mesas (estado: pendiente)
- Mesas confirman participación (estado: confirmado)
- Registrar participación activa (estado: activo/inactivo)

### **2. Reuniones de Coordinación**
- Crear evento de reunión
- Asignar mesas de una zona específica
- Seguimiento de confirmaciones
- Reporte de participación

### **3. Eventos Especiales**
- Crear eventos específicos
- Asignar mesas seleccionadas
- Seguimiento personalizado
- Reportes de participación

## 📊 **Monitoreo y Reportes**

### **Métricas Disponibles**
- Total de mesas por evento
- Distribución por estado
- Mesas que no confirmaron
- Tasa de participación por evento
- Historial de eventos por mesa

### **Endpoints de Reportes**
```bash
# Estadísticas generales
GET /api/evento-mesas/estadisticas

# Mesas pendientes de confirmación
GET /api/evento-mesas/evento/{id}/estado/pendiente

# Mesas que participaron activamente
GET /api/evento-mesas/evento/{id}/estado/activo
```

## 🔧 **Mantenimiento**

### **Limpieza de Datos**
```sql
-- Eliminar relaciones desactivadas antiguas
DELETE FROM "EventoMesas" 
WHERE enabled = false 
  AND updatedAt < NOW() - INTERVAL '30 days';
```

### **Optimización de Consultas**
- Usar índices en eventoId, mesaId y estado
- Paginación en consultas grandes
- Filtros por fecha para consultas históricas

---

**Nota**: Este sistema permite una gestión completa y flexible de la relación entre eventos electorales y mesas, con seguimiento detallado de estados y participación.
