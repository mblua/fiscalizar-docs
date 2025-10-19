# Sistema de Hitos y Fiscales - Fiscalizar Backend

## 📋 **Resumen**

El sistema de hitos y fiscales permite gestionar la relación entre hitos electorales y los fiscales que participan en ellos. Cada relación tiene un estado específico que permite hacer seguimiento de la participación de los fiscales en los hitos.

## 🗄️ **Estructura de Base de Datos**

### **Tabla `Hitos`** (Con Estados Personalizados)

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

#### **Campos de Estados Personalizados:**
- **`estadoDefault`**: Estado por defecto para nuevas relaciones
- **`estado1Reconocimiento`**: Reconocimiento del estado 1
- **`estado1Color`**: Color del estado 1
- **`estado2Reconocimiento`**: Reconocimiento del estado 2
- **`estado2Color`**: Color del estado 2
- **`estado3Reconocimiento`**: Reconocimiento del estado 3
- **`estado3Color`**: Color del estado 3
- **`estado4Reconocimiento`**: Reconocimiento del estado 4
- **`estado4Color`**: Color del estado 4

### **Tabla `HitoFiscales`**

```sql
CREATE TABLE "HitoFiscales" (
  id        SERIAL PRIMARY KEY,
  hitoId    INTEGER NOT NULL,
  fiscalId  INTEGER NOT NULL,
  estado    VARCHAR DEFAULT 'pendiente',
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW(),
  enabled   BOOLEAN DEFAULT TRUE,
  
  -- Claves foráneas
  FOREIGN KEY (hitoId) REFERENCES "Hitos"(id) ON DELETE CASCADE,
  FOREIGN KEY (fiscalId) REFERENCES "Fiscales"(id) ON DELETE CASCADE,
  
  -- Restricción única
  UNIQUE(hitoId, fiscalId)
);
```

#### **Campos:**
- **`id`**: Identificador único de la relación
- **`hitoId`**: ID del hito relacionado
- **`fiscalId`**: ID del fiscal relacionado
- **`estado`**: Estado de la relación
- **`createdAt`**: Fecha de creación de la relación
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado de la relación (true/false)

## 🔗 **Relación entre Tablas**

### **Diagrama de Relación**

```
Hitos (1) ←→ (N) HitoFiscales (N) ←→ (1) Fiscales
```

### **Características de la Relación:**

1. **Relación N:N**: Un hito puede tener múltiples fiscales, un fiscal puede estar en múltiples hitos
2. **Estado específico**: Cada relación tiene su propio estado
3. **Cascade Delete**: Si se elimina un hito o fiscal, se eliminan todas sus relaciones
4. **Restricción única**: No puede haber duplicados (hito-fiscal)

## 📊 **Estados Disponibles**

### **Estados Estándar:**
| Estado | Descripción | Uso Típico |
|--------|-------------|------------|
| `pendiente` | Estado inicial por defecto | Fiscal invitado pero sin respuesta |
| `confirmado` | Fiscal confirmó participación | Fiscal confirmó que participará |
| `asistio` | Fiscal asistió al hito | Fiscal participó activamente |
| `no_asistio` | Fiscal no asistió | Fiscal no participó en el hito |
| `cancelado` | Hito cancelado para este fiscal | Hito cancelado o fiscal excluido |

### **Estados Personalizados por Hito:**
Cada hito puede definir hasta 4 estados personalizados con sus respectivos colores:

- **Estado 1**: `estado1Reconocimiento` + `estado1Color`
- **Estado 2**: `estado2Reconocimiento` + `estado2Color`
- **Estado 3**: `estado3Reconocimiento` + `estado3Color`
- **Estado 4**: `estado4Reconocimiento` + `estado4Color`

#### **Ejemplo de Configuración:**
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

## 🚀 **Endpoints de la API**

### **Base URL**
```
http://localhost:3000/api
```

### **1. Gestión de Relaciones Hito-Fiscal**

#### **Listar Relaciones por Hito**
```bash
# Obtener todas las relaciones de un hito
GET /api/hito-fiscales?hitoId=1

# Filtrar por estado
GET /api/hito-fiscales?hitoId=1&estado=confirmado
```

#### **Endpoints Semánticos**
```bash
# Obtener fiscales de un hito
GET /api/hito-fiscales/hito/{hitoId}

# Obtener fiscales de un hito con estado específico
GET /api/hito-fiscales/hito/{hitoId}/estado/{estado}
```

#### **Crear Relación Hito-Fiscal**
```bash
POST /api/hito-fiscales
Content-Type: application/json

{
  "hitoId": 1,
  "fiscalId": 1,
  "estado": "pendiente"
}
```

#### **Actualizar Estado**
```bash
PATCH /api/hito-fiscales/{id}/estado
Content-Type: application/json

{
  "estado": "confirmado"
}
```

#### **Crear Relaciones Masivas**
```bash
POST /api/hito-fiscales/masivo
Content-Type: application/json

{
  "hitoId": 1,
  "fiscalIds": [1, 2, 3],
  "estado": "pendiente"
}
```

### **2. Consultas Especializadas**

#### **Hitos por Fiscal**
```bash
GET /api/hito-fiscales/fiscal/{fiscalId}
```

#### **Estados Disponibles**
```bash
GET /api/hito-fiscales/estados
```

## 🔧 **Ejemplos de Uso**

### **Crear un Hito y Asignar Fiscales**

```bash
# 1. Crear el hito
curl -X POST http://localhost:3000/api/hitos \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación Electoral",
    "descripcion": "Capacitación obligatoria para todos los fiscales"
  }'

# 2. Asignar fiscales al hito
curl -X POST http://localhost:3000/api/hito-fiscales/masivo \
  -H "Content-Type: application/json" \
  -d '{
    "hitoId": 1,
    "fiscalIds": [1, 2, 3, 4],
    "estado": "pendiente"
  }'

# 3. Confirmar participación de un fiscal específico
curl -X PATCH http://localhost:3000/api/hito-fiscales/1/estado \
  -H "Content-Type: application/json" \
  -d '{
    "estado": "confirmado"
  }'
```

### **Consultar Participación**

```bash
# Ver todos los fiscales de un hito
curl http://localhost:3000/api/hito-fiscales/hito/1

# Ver todos los hitos de un fiscal
curl http://localhost:3000/api/hito-fiscales/fiscal/1

# Ver solo fiscales confirmados de un hito
curl http://localhost:3000/api/hito-fiscales/hito/1?estado=confirmado
```

## 📈 **Casos de Uso Típicos**

### **1. Capacitación Electoral**
- Crear hito "Capacitación Electoral"
- Asignar todos los fiscales
- Marcar como "confirmado" cuando el fiscal confirma asistencia
- Marcar como "asistio" después de la capacitación
- Marcar como "no_asistio" si no asiste

### **2. Reunión de Coordinación**
- Crear hito "Reunión de Coordinación"
- Asignar solo fiscales específicos
- Seguir el flujo de estados según la participación

### **3. Seguimiento de Actividades**
- Consultar qué fiscales participan en cada hito
- Ver el historial de hitos de cada fiscal
- Generar reportes de participación

## 🔍 **Filtros y Búsquedas**

### **Filtros Disponibles**
- **Por hito**: `?hitoId=1`
- **Por fiscal**: `?fiscalId=1`
- **Por estado**: `?estado=confirmado`
- **Combinados**: `?hitoId=1&estado=activo`

### **Paginación**
- **Página**: `?page=1`
- **Límite**: `?limit=10`
- **Ejemplo**: `?page=2&limit=5`

## ⚠️ **Consideraciones Importantes**

1. **Estados**: Los estados son strings libres, pero se recomienda usar los valores estándar
2. **Relaciones**: No se pueden crear relaciones duplicadas (hito-fiscal)
3. **Eliminación**: Las relaciones se marcan como `enabled: false` (soft delete)
4. **Cascade**: Al eliminar un hito o fiscal, se eliminan todas sus relaciones
5. **Validación**: Se valida que el hito y fiscal existan antes de crear la relación

## 🚀 **Próximos Pasos**

1. Implementar notificaciones automáticas por cambio de estado
2. Agregar campos adicionales como fecha de confirmación
3. Crear reportes de participación por período
4. Integrar con sistema de notificaciones por email/SMS
