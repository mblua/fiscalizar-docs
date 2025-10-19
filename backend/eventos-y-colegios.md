# Sistema de Eventos y Colegios - Fiscalizar Backend

## 📋 **Resumen**

El sistema de eventos y colegios permite gestionar la relación entre eventos electorales y los colegios que participan en ellos. Cada relación tiene un estado específico que permite hacer seguimiento de la participación de los colegios en los eventos.

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

### **Tabla `EventoColegios`** (Nueva)

```sql
CREATE TABLE "EventoColegios" (
  id        SERIAL PRIMARY KEY,
  eventoId  INTEGER NOT NULL,
  colegioId INTEGER NOT NULL,
  estado    VARCHAR DEFAULT 'pendiente',
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW(),
  enabled   BOOLEAN DEFAULT TRUE,
  
  -- Claves foráneas
  FOREIGN KEY (eventoId) REFERENCES "Eventos"(id) ON DELETE CASCADE,
  FOREIGN KEY (colegioId) REFERENCES "Colegios"(id) ON DELETE CASCADE,
  
  -- Restricción única
  UNIQUE(eventoId, colegioId)
);
```

#### **Campos:**
- **`id`**: Identificador único de la relación
- **`eventoId`**: ID del evento (FK → Eventos.id)
- **`colegioId`**: ID del colegio (FK → Colegios.id)
- **`estado`**: Estado de la relación
- **`createdAt`**: Fecha de creación de la relación
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado de la relación (true/false)

## 🔗 **Relación entre Tablas**

### **Diagrama de Relación**

```
Eventos (1) ←→ (N) EventoColegios (N) ←→ (1) Colegios
```

### **Características de la Relación:**

1. **Relación N:N**: Un evento puede tener múltiples colegios, un colegio puede estar en múltiples eventos
2. **Estado específico**: Cada relación tiene su propio estado
3. **Cascade Delete**: Si se elimina un evento o colegio, se eliminan todas sus relaciones
4. **Restricción única**: No puede haber duplicados (evento-colegio)

## 📊 **Estados Disponibles**

| Estado | Descripción | Uso Típico |
|--------|-------------|------------|
| `pendiente` | Estado inicial por defecto | Colegio invitado pero sin respuesta |
| `confirmado` | Colegio confirmó participación | Colegio confirmó que participará |
| `activo` | Colegio está activo en el evento | Colegio participando activamente |
| `inactivo` | Colegio no está participando | Colegio no participa en el evento |
| `cancelado` | Evento cancelado para este colegio | Evento cancelado o colegio excluido |

## 🚀 **Endpoints de la API**

### **Base URL**
```
http://localhost:3000/api
```

### **⚠️ Autenticación**
Los endpoints de `evento-colegios` **NO requieren autenticación** con `client_secret`. Son endpoints internos del sistema que pueden ser llamados directamente.

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
        "descripcion": "Capacitación obligatoria para todos los colegios",
        "fecha": "2025-10-18T22:48:14.354Z",
        "eventoColegios": [
          {
            "id": 1,
            "estado": "confirmado",
            "colegio": {
              "id": 1,
              "nombre": "Escuela Primaria N° 1",
              "zona": {
                "id": 1,
                "nombre": "Zona Centro"
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

### **2. Gestión de Relaciones Evento-Colegio**

#### **Listar Relaciones por Evento (Requerido)**
```bash
# Obtener todas las relaciones de un evento
GET /api/evento-colegios?eventoId=1

# Filtrar por estado
GET /api/evento-colegios?eventoId=1&estado=confirmado
```

#### **Endpoints Semánticos**
```bash
# Obtener colegios de un evento
GET /api/evento-colegios/evento/{eventoId}

# Obtener colegios de un evento con estado específico
GET /api/evento-colegios/evento/{eventoId}/estado/{estado}
```

#### **Crear Relación Evento-Colegio**
```bash
POST /api/evento-colegios
Content-Type: application/json

{
  "eventoId": 1,
  "colegioId": 1,
  "estado": "pendiente"
}
```

#### **Actualizar Estado**
```bash
PATCH /api/evento-colegios/{id}/estado
Content-Type: application/json

{
  "estado": "confirmado"
}
```

#### **Crear Relaciones Masivas**
```bash
POST /api/evento-colegios/masivo
Content-Type: application/json

{
  "eventoId": 1,
  "colegioIds": [1, 2, 3],
  "estado": "pendiente"
}
```

### **3. Consultas Especializadas**

#### **Eventos por Colegio**
```bash
GET /api/evento-colegios/colegio/{colegioId}/eventos
```

#### **Estados Disponibles**
```bash
GET /api/evento-colegios/estados
```

#### **Estadísticas por Estado**
```bash
GET /api/evento-colegios/estadisticas
```

## 📝 **Ejemplos de Uso**

### **Caso 1: Crear un Evento y Asignar Colegios**

```bash
# 1. Crear el evento
curl -X POST "http://localhost:3000/api/eventos" \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación de Colegios",
    "descripcion": "Capacitación obligatoria para todos los colegios de la zona"
  }'

# 2. Asignar colegios al evento (SIN autenticación)
curl -X POST "http://localhost:3000/api/evento-colegios/masivo" \
  -H "Content-Type: application/json" \
  -d '{
    "eventoId": 1,
    "colegioIds": [1, 2, 3],
    "estado": "pendiente"
  }'
```

### **Caso 2: Seguimiento de Participación**

```bash
# 1. Ver qué colegios confirmaron participación (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-colegios/evento/1/estado/confirmado"

# 2. Actualizar estado a "activo" (SIN autenticación)
curl -X PATCH "http://localhost:3000/api/evento-colegios/1/estado" \
  -H "Content-Type: application/json" \
  -d '{"estado": "activo"}'

# 3. Ver estadísticas del evento (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-colegios/estadisticas"
```

### **Caso 3: Consultar Eventos de un Colegio**

```bash
# Ver todos los eventos de un colegio (SIN autenticación)
curl -X GET "http://localhost:3000/api/evento-colegios/colegio/1/eventos"
```

## 🔍 **Consultas SQL Típicas**

### **Obtener Colegios de un Evento**
```sql
SELECT 
  ec.id,
  ec.estado,
  c.nombre as colegio,
  z.nombre as zona
FROM "EventoColegios" ec
JOIN "Colegios" c ON ec.colegioId = c.id
JOIN "Zonas" z ON c.zonaId = z.id
WHERE ec.eventoId = 1 
  AND ec.enabled = true
ORDER BY ec.estado, c.nombre;
```

### **Obtener Eventos de un Colegio**
```sql
SELECT 
  ec.id,
  ec.estado,
  e.nombre,
  e.descripcion,
  e.fecha
FROM "EventoColegios" ec
JOIN "Eventos" e ON ec.eventoId = e.id
WHERE ec.colegioId = 1 
  AND ec.enabled = true
ORDER BY e.fecha DESC;
```

### **Estadísticas por Estado**
```sql
SELECT 
  estado,
  COUNT(*) as cantidad
FROM "EventoColegios"
WHERE eventoId = 1 
  AND enabled = true
GROUP BY estado
ORDER BY cantidad DESC;
```

## ⚠️ **Reglas de Negocio**

### **1. Validaciones Obligatorias**
- **EventoId requerido**: Todas las consultas de relaciones requieren especificar el evento
- **Estados válidos**: Solo se permiten los estados predefinidos
- **Relación única**: No puede haber duplicados (evento-colegio)

### **2. Comportamientos del Sistema**
- **Cascade Delete**: Eliminar un evento o colegio elimina todas sus relaciones
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
- Asignar colegios (estado: pendiente)
- Colegios confirman participación (estado: confirmado)
- Registrar participación activa (estado: activo/inactivo)

### **2. Reuniones de Coordinación**
- Crear evento de reunión
- Asignar colegios de una zona específica
- Seguimiento de confirmaciones
- Reporte de participación

### **3. Eventos Especiales**
- Crear eventos específicos
- Asignar colegios seleccionados
- Seguimiento personalizado
- Reportes de participación

## 📊 **Monitoreo y Reportes**

### **Métricas Disponibles**
- Total de colegios por evento
- Distribución por estado
- Colegios que no confirmaron
- Tasa de participación por evento
- Historial de eventos por colegio

### **Endpoints de Reportes**
```bash
# Estadísticas generales
GET /api/evento-colegios/estadisticas

# Colegios pendientes de confirmación
GET /api/evento-colegios/evento/{id}/estado/pendiente

# Colegios que participaron activamente
GET /api/evento-colegios/evento/{id}/estado/activo
```

## 🔧 **Mantenimiento**

### **Limpieza de Datos**
```sql
-- Eliminar relaciones desactivadas antiguas
DELETE FROM "EventoColegios" 
WHERE enabled = false 
  AND updatedAt < NOW() - INTERVAL '30 days';
```

### **Optimización de Consultas**
- Usar índices en eventoId, colegioId y estado
- Paginación en consultas grandes
- Filtros por fecha para consultas históricas

---

**Nota**: Este sistema permite una gestión completa y flexible de la relación entre eventos electorales y colegios, con seguimiento detallado de estados y participación.
