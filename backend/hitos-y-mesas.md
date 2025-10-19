# Sistema de Hitos y Mesas - Fiscalizar Backend

## 📋 **Resumen**

El sistema de hitos y mesas permite gestionar la relación entre hitos electorales y las mesas que participan en ellos. Cada relación tiene un estado específico que permite hacer seguimiento de la participación de las mesas en los hitos.

## 🗄️ **Estructura de Base de Datos**

### **Tabla `HitoMesas`**

```sql
CREATE TABLE "HitoMesas" (
  id        SERIAL PRIMARY KEY,
  hitoId    INTEGER NOT NULL,
  mesaId    INTEGER NOT NULL,
  estado    VARCHAR DEFAULT 'pendiente',
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW(),
  enabled   BOOLEAN DEFAULT TRUE,
  
  -- Claves foráneas
  FOREIGN KEY (hitoId) REFERENCES "Hitos"(id) ON DELETE CASCADE,
  FOREIGN KEY (mesaId) REFERENCES "Mesas"(id) ON DELETE CASCADE,
  
  -- Restricción única
  UNIQUE(hitoId, mesaId)
);
```

#### **Campos:**
- **`id`**: Identificador único de la relación
- **`hitoId`**: ID del hito relacionado
- **`mesaId`**: ID de la mesa relacionada
- **`estado`**: Estado de la relación
- **`createdAt`**: Fecha de creación de la relación
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado de la relación (true/false)

## 🔗 **Relación entre Tablas**

### **Diagrama de Relación**

```
Hitos (1) ←→ (N) HitoMesas (N) ←→ (1) Mesas
```

### **Características de la Relación:**

1. **Relación N:N**: Un hito puede tener múltiples mesas, una mesa puede estar en múltiples hitos
2. **Estado específico**: Cada relación tiene su propio estado
3. **Cascade Delete**: Si se elimina un hito o mesa, se eliminan todas sus relaciones
4. **Restricción única**: No puede haber duplicados (hito-mesa)

## 📊 **Estados Disponibles**

| Estado | Descripción | Uso Típico |
|--------|-------------|------------|
| `pendiente` | Estado inicial por defecto | Mesa invitada pero sin respuesta |
| `confirmado` | Mesa confirmó participación | Mesa confirmó que participará |
| `activo` | Mesa está activa en el hito | Mesa participando activamente |
| `inactivo` | Mesa no está participando | Mesa no participa en el hito |
| `cancelado` | Hito cancelado para esta mesa | Hito cancelado o mesa excluida |

## 🚀 **Endpoints de la API**

### **Base URL**
```
http://localhost:3000/api
```

### **1. Gestión de Relaciones Hito-Mesa**

#### **Listar Relaciones por Hito**
```bash
# Obtener todas las relaciones de un hito
GET /api/hito-mesas?hitoId=1

# Filtrar por estado
GET /api/hito-mesas?hitoId=1&estado=confirmado
```

#### **Endpoints Semánticos**
```bash
# Obtener mesas de un hito
GET /api/hito-mesas/hito/{hitoId}

# Obtener mesas de un hito con estado específico
GET /api/hito-mesas/hito/{hitoId}/estado/{estado}
```

#### **Crear Relación Hito-Mesa**
```bash
POST /api/hito-mesas
Content-Type: application/json

{
  "hitoId": 1,
  "mesaId": 1,
  "estado": "pendiente"
}
```

#### **Actualizar Estado**
```bash
PATCH /api/hito-mesas/{id}/estado
Content-Type: application/json

{
  "estado": "confirmado"
}
```

#### **Crear Relaciones Masivas**
```bash
POST /api/hito-mesas/masivo
Content-Type: application/json

{
  "hitoId": 1,
  "mesaIds": [1, 2, 3],
  "estado": "pendiente"
}
```

### **2. Consultas Especializadas**

#### **Hitos por Mesa**
```bash
GET /api/hito-mesas/mesa/{mesaId}
```

#### **Estados Disponibles**
```bash
GET /api/hito-mesas/estados
```

## 🔧 **Ejemplos de Uso**

### **Crear un Hito y Asignar Mesas**

```bash
# 1. Crear el hito
curl -X POST http://localhost:3000/api/hitos \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación Electoral",
    "descripcion": "Capacitación obligatoria para todas las mesas"
  }'

# 2. Asignar mesas al hito
curl -X POST http://localhost:3000/api/hito-mesas/masivo \
  -H "Content-Type: application/json" \
  -d '{
    "hitoId": 1,
    "mesaIds": [1, 2, 3, 4],
    "estado": "pendiente"
  }'

# 3. Confirmar participación de una mesa específica
curl -X PATCH http://localhost:3000/api/hito-mesas/1/estado \
  -H "Content-Type: application/json" \
  -d '{
    "estado": "confirmado"
  }'
```

### **Consultar Participación**

```bash
# Ver todas las mesas de un hito
curl http://localhost:3000/api/hito-mesas/hito/1

# Ver todos los hitos de una mesa
curl http://localhost:3000/api/hito-mesas/mesa/1

# Ver solo mesas confirmadas de un hito
curl http://localhost:3000/api/hito-mesas/hito/1?estado=confirmado
```

## 📈 **Casos de Uso Típicos**

### **1. Capacitación Electoral**
- Crear hito "Capacitación Electoral"
- Asignar todas las mesas
- Marcar como "confirmado" cuando la mesa confirma asistencia
- Marcar como "activo" durante la capacitación
- Marcar como "inactivo" si no asiste

### **2. Reunión de Coordinación**
- Crear hito "Reunión de Coordinación"
- Asignar solo mesas específicas
- Seguir el flujo de estados según la participación

### **3. Seguimiento de Actividades**
- Consultar qué mesas participan en cada hito
- Ver el historial de hitos de cada mesa
- Generar reportes de participación

## 🔍 **Filtros y Búsquedas**

### **Filtros Disponibles**
- **Por hito**: `?hitoId=1`
- **Por mesa**: `?mesaId=1`
- **Por estado**: `?estado=confirmado`
- **Combinados**: `?hitoId=1&estado=activo`

### **Paginación**
- **Página**: `?page=1`
- **Límite**: `?limit=10`
- **Ejemplo**: `?page=2&limit=5`

## ⚠️ **Consideraciones Importantes**

1. **Estados**: Los estados son strings libres, pero se recomienda usar los valores estándar
2. **Relaciones**: No se pueden crear relaciones duplicadas (hito-mesa)
3. **Eliminación**: Las relaciones se marcan como `enabled: false` (soft delete)
4. **Cascade**: Al eliminar un hito o mesa, se eliminan todas sus relaciones
5. **Validación**: Se valida que el hito y mesa existan antes de crear la relación

## 🚀 **Próximos Pasos**

1. Implementar notificaciones automáticas por cambio de estado
2. Agregar campos adicionales como fecha de confirmación
3. Crear reportes de participación por período
4. Integrar con sistema de notificaciones por email/SMS
