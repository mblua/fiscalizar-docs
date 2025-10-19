# Sistema de Hitos y Zonas - Fiscalizar Backend

## 📋 **Resumen**

El sistema de hitos y zonas permite gestionar la relación entre hitos electorales y las zonas que participan en ellos. Cada relación tiene un estado específico que permite hacer seguimiento de la participación de las zonas en los hitos.

## 🗄️ **Estructura de Base de Datos**

### **Tabla `HitoZonas`**

```sql
CREATE TABLE "HitoZonas" (
  id        SERIAL PRIMARY KEY,
  hitoId    INTEGER NOT NULL,
  zonaId    INTEGER NOT NULL,
  estado    VARCHAR DEFAULT 'pendiente',
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW(),
  enabled   BOOLEAN DEFAULT TRUE,
  
  -- Claves foráneas
  FOREIGN KEY (hitoId) REFERENCES "Hitos"(id) ON DELETE CASCADE,
  FOREIGN KEY (zonaId) REFERENCES "Zonas"(id) ON DELETE CASCADE,
  
  -- Restricción única
  UNIQUE(hitoId, zonaId)
);
```

#### **Campos:**
- **`id`**: Identificador único de la relación
- **`hitoId`**: ID del hito relacionado
- **`zonaId`**: ID de la zona relacionada
- **`estado`**: Estado de la relación
- **`createdAt`**: Fecha de creación de la relación
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado de la relación (true/false)

## 🔗 **Relación entre Tablas**

### **Diagrama de Relación**

```
Hitos (1) ←→ (N) HitoZonas (N) ←→ (1) Zonas
```

### **Características de la Relación:**

1. **Relación N:N**: Un hito puede tener múltiples zonas, una zona puede estar en múltiples hitos
2. **Estado específico**: Cada relación tiene su propio estado
3. **Cascade Delete**: Si se elimina un hito o zona, se eliminan todas sus relaciones
4. **Restricción única**: No puede haber duplicados (hito-zona)

## 📊 **Estados Disponibles**

| Estado | Descripción | Uso Típico |
|--------|-------------|------------|
| `pendiente` | Estado inicial por defecto | Zona invitada pero sin respuesta |
| `confirmado` | Zona confirmó participación | Zona confirmó que participará |
| `activo` | Zona está activa en el hito | Zona participando activamente |
| `inactivo` | Zona no está participando | Zona no participa en el hito |
| `cancelado` | Hito cancelado para esta zona | Hito cancelado o zona excluida |

## 🚀 **Endpoints de la API**

### **Base URL**
```
http://localhost:3000/api
```

### **1. Gestión de Relaciones Hito-Zona**

#### **Listar Relaciones por Hito**
```bash
# Obtener todas las relaciones de un hito
GET /api/hito-zonas?hitoId=1

# Filtrar por estado
GET /api/hito-zonas?hitoId=1&estado=confirmado
```

#### **Endpoints Semánticos**
```bash
# Obtener zonas de un hito
GET /api/hito-zonas/hito/{hitoId}

# Obtener zonas de un hito con estado específico
GET /api/hito-zonas/hito/{hitoId}/estado/{estado}
```

#### **Crear Relación Hito-Zona**
```bash
POST /api/hito-zonas
Content-Type: application/json

{
  "hitoId": 1,
  "zonaId": 1,
  "estado": "pendiente"
}
```

#### **Actualizar Estado**
```bash
PATCH /api/hito-zonas/{id}/estado
Content-Type: application/json

{
  "estado": "confirmado"
}
```

#### **Crear Relaciones Masivas**
```bash
POST /api/hito-zonas/masivo
Content-Type: application/json

{
  "hitoId": 1,
  "zonaIds": [1, 2, 3],
  "estado": "pendiente"
}
```

### **2. Consultas Especializadas**

#### **Hitos por Zona**
```bash
GET /api/hito-zonas/zona/{zonaId}
```

#### **Estados Disponibles**
```bash
GET /api/hito-zonas/estados
```

## 🔧 **Ejemplos de Uso**

### **Crear un Hito y Asignar Zonas**

```bash
# 1. Crear el hito
curl -X POST http://localhost:3000/api/hitos \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación Electoral",
    "descripcion": "Capacitación obligatoria para todas las zonas"
  }'

# 2. Asignar zonas al hito
curl -X POST http://localhost:3000/api/hito-zonas/masivo \
  -H "Content-Type: application/json" \
  -d '{
    "hitoId": 1,
    "zonaIds": [1, 2, 3],
    "estado": "pendiente"
  }'

# 3. Confirmar participación de una zona específica
curl -X PATCH http://localhost:3000/api/hito-zonas/1/estado \
  -H "Content-Type: application/json" \
  -d '{
    "estado": "confirmado"
  }'
```

### **Consultar Participación**

```bash
# Ver todas las zonas de un hito
curl http://localhost:3000/api/hito-zonas/hito/1

# Ver todos los hitos de una zona
curl http://localhost:3000/api/hito-zonas/zona/1

# Ver solo zonas confirmadas de un hito
curl http://localhost:3000/api/hito-zonas/hito/1?estado=confirmado
```

## 📈 **Casos de Uso Típicos**

### **1. Capacitación Electoral**
- Crear hito "Capacitación Electoral"
- Asignar todas las zonas
- Marcar como "confirmado" cuando la zona confirma asistencia
- Marcar como "activo" durante la capacitación
- Marcar como "inactivo" si no asiste

### **2. Reunión de Coordinación**
- Crear hito "Reunión de Coordinación"
- Asignar solo zonas específicas
- Seguir el flujo de estados según la participación

### **3. Seguimiento de Actividades**
- Consultar qué zonas participan en cada hito
- Ver el historial de hitos de cada zona
- Generar reportes de participación

## 🔍 **Filtros y Búsquedas**

### **Filtros Disponibles**
- **Por hito**: `?hitoId=1`
- **Por zona**: `?zonaId=1`
- **Por estado**: `?estado=confirmado`
- **Combinados**: `?hitoId=1&estado=activo`

### **Paginación**
- **Página**: `?page=1`
- **Límite**: `?limit=10`
- **Ejemplo**: `?page=2&limit=5`

## ⚠️ **Consideraciones Importantes**

1. **Estados**: Los estados son strings libres, pero se recomienda usar los valores estándar
2. **Relaciones**: No se pueden crear relaciones duplicadas (hito-zona)
3. **Eliminación**: Las relaciones se marcan como `enabled: false` (soft delete)
4. **Cascade**: Al eliminar un hito o zona, se eliminan todas sus relaciones
5. **Validación**: Se valida que el hito y zona existan antes de crear la relación

## 🚀 **Próximos Pasos**

1. Implementar notificaciones automáticas por cambio de estado
2. Agregar campos adicionales como fecha de confirmación
3. Crear reportes de participación por período
4. Integrar con sistema de notificaciones por email/SMS
