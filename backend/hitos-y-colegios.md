# Sistema de Hitos y Colegios - Fiscalizar Backend

## 📋 **Resumen**

El sistema de hitos y colegios permite gestionar la relación entre hitos electorales y los colegios que participan en ellos. Cada relación tiene un estado específico que permite hacer seguimiento de la participación de los colegios en los hitos.

## 🗄️ **Estructura de Base de Datos**

### **Tabla `Hitos`** (Renombrada de Eventos)

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

#### **Campos:**
- **`id`**: Identificador único del hito
- **`nombre`**: Nombre del hito (ej: "Capacitación", "Reunión de Coordinación")
- **`descripcion`**: Descripción detallada del hito
- **`fecha`**: Fecha y hora del hito (automática)
- **`createdAt`**: Fecha de creación del registro
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado del hito (true/false)

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

### **Tabla `HitoColegios`** (Renombrada de EventoColegios)

```sql
CREATE TABLE "HitoColegios" (
  id        SERIAL PRIMARY KEY,
  hitoId    INTEGER NOT NULL,
  colegioId INTEGER NOT NULL,
  estado    VARCHAR DEFAULT 'pendiente',
  createdAt TIMESTAMP DEFAULT NOW(),
  updatedAt TIMESTAMP DEFAULT NOW(),
  enabled   BOOLEAN DEFAULT TRUE,
  
  -- Claves foráneas
  FOREIGN KEY (hitoId) REFERENCES "Hitos"(id) ON DELETE CASCADE,
  FOREIGN KEY (colegioId) REFERENCES "Colegios"(id) ON DELETE CASCADE,
  
  -- Restricción única
  UNIQUE(hitoId, colegioId)
);
```

#### **Campos:**
- **`id`**: Identificador único de la relación
- **`hitoId`**: ID del hito relacionado
- **`colegioId`**: ID del colegio relacionado
- **`estado`**: Estado de la relación
- **`createdAt`**: Fecha de creación de la relación
- **`updatedAt`**: Fecha de última actualización
- **`enabled`**: Estado de la relación (true/false)

## 🔗 **Relación entre Tablas**

### **Diagrama de Relación**

```
Hitos (1) ←→ (N) HitoColegios (N) ←→ (1) Colegios
```

### **Características de la Relación:**

1. **Relación N:N**: Un hito puede tener múltiples colegios, un colegio puede estar en múltiples hitos
2. **Estado específico**: Cada relación tiene su propio estado
3. **Cascade Delete**: Si se elimina un hito o colegio, se eliminan todas sus relaciones
4. **Restricción única**: No puede haber duplicados (hito-colegio)

## 📊 **Estados Disponibles**

### **Estados Estándar:**
| Estado | Descripción | Uso Típico |
|--------|-------------|------------|
| `pendiente` | Estado inicial por defecto | Colegio invitado pero sin respuesta |
| `confirmado` | Colegio confirmó participación | Colegio confirmó que participará |
| `activo` | Colegio está activo en el hito | Colegio participando activamente |
| `inactivo` | Colegio no está participando | Colegio no participa en el hito |
| `cancelado` | Hito cancelado para este colegio | Hito cancelado o colegio excluido |

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

### **⚠️ Autenticación**
Los endpoints de `hito-colegios` **NO requieren autenticación** con `client_secret`. Son endpoints internos del sistema que pueden ser llamados directamente.

### **1. Gestión de Hitos** (Ya implementado)

#### **Listar Hitos**
```bash
GET /api/hitos
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
        "fecha": "2025-10-19T22:48:14.354Z",
        "hitoColegios": [
          {
            "id": 1,
            "estado": "confirmado",
            "colegio": {
              "id": 1,
              "nombre": "Escuela Primaria 123"
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

#### **Crear Hito**
```bash
POST /api/hitos
Content-Type: application/json

{
  "nombre": "Nuevo Hito",
  "descripcion": "Descripción del hito",
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

#### **Obtener Hito por ID**
```bash
GET /api/hitos/{id}
```

### **2. Gestión de Relaciones Hito-Colegio**

#### **Listar Relaciones por Hito (Requerido)**
```bash
# Obtener todas las relaciones de un hito
GET /api/hito-colegios?hitoId=1

# Filtrar por estado
GET /api/hito-colegios?hitoId=1&estado=confirmado
```

#### **Endpoints Semánticos**
```bash
# Obtener colegios de un hito
GET /api/hito-colegios/hito/{hitoId}

# Obtener colegios de un hito con estado específico
GET /api/hito-colegios/hito/{hitoId}/estado/{estado}
```

#### **Crear Relación Hito-Colegio**
```bash
POST /api/hito-colegios
Content-Type: application/json

{
  "hitoId": 1,
  "colegioId": 1,
  "estado": "pendiente"
}
```

#### **Actualizar Estado**
```bash
PATCH /api/hito-colegios/{id}/estado
Content-Type: application/json

{
  "estado": "confirmado"
}
```

#### **Crear Relaciones Masivas**
```bash
POST /api/hito-colegios/masivo
Content-Type: application/json

{
  "hitoId": 1,
  "colegioIds": [1, 2, 3],
  "estado": "pendiente"
}
```

### **3. Consultas Especializadas**

#### **Hitos por Colegio**
```bash
GET /api/hito-colegios/colegio/{colegioId}
```

#### **Estados Disponibles**
```bash
GET /api/hito-colegios/estados
```

## 🔧 **Ejemplos de Uso**

### **Crear un Hito y Asignar Colegios**

```bash
# 1. Crear el hito
curl -X POST http://localhost:3000/api/hitos \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Capacitación Electoral",
    "descripcion": "Capacitación obligatoria para todos los fiscales"
  }'

# 2. Asignar colegios al hito
curl -X POST http://localhost:3000/api/hito-colegios/masivo \
  -H "Content-Type: application/json" \
  -d '{
    "hitoId": 1,
    "colegioIds": [1, 2, 3, 4],
    "estado": "pendiente"
  }'

# 3. Confirmar participación de un colegio específico
curl -X PATCH http://localhost:3000/api/hito-colegios/1/estado \
  -H "Content-Type: application/json" \
  -d '{
    "estado": "confirmado"
  }'
```

### **Consultar Participación**

```bash
# Ver todos los colegios de un hito
curl http://localhost:3000/api/hito-colegios/hito/1

# Ver todos los hitos de un colegio
curl http://localhost:3000/api/hito-colegios/colegio/1

# Ver solo colegios confirmados de un hito
curl http://localhost:3000/api/hito-colegios/hito/1?estado=confirmado
```

## 📈 **Casos de Uso Típicos**

### **1. Capacitación Electoral**
- Crear hito "Capacitación Electoral"
- Asignar todos los colegios
- Marcar como "confirmado" cuando el colegio confirma asistencia
- Marcar como "activo" durante la capacitación
- Marcar como "inactivo" si no asiste

### **2. Reunión de Coordinación**
- Crear hito "Reunión de Coordinación"
- Asignar solo colegios específicos
- Seguir el flujo de estados según la participación

### **3. Seguimiento de Actividades**
- Consultar qué colegios participan en cada hito
- Ver el historial de hitos de cada colegio
- Generar reportes de participación

## 🔍 **Filtros y Búsquedas**

### **Filtros Disponibles**
- **Por hito**: `?hitoId=1`
- **Por colegio**: `?colegioId=1`
- **Por estado**: `?estado=confirmado`
- **Combinados**: `?hitoId=1&estado=activo`

### **Paginación**
- **Página**: `?page=1`
- **Límite**: `?limit=10`
- **Ejemplo**: `?page=2&limit=5`

## ⚠️ **Consideraciones Importantes**

1. **Estados**: Los estados son strings libres, pero se recomienda usar los valores estándar
2. **Relaciones**: No se pueden crear relaciones duplicadas (hito-colegio)
3. **Eliminación**: Las relaciones se marcan como `enabled: false` (soft delete)
4. **Cascade**: Al eliminar un hito o colegio, se eliminan todas sus relaciones
5. **Validación**: Se valida que el hito y colegio existan antes de crear la relación

## 🚀 **Próximos Pasos**

1. Implementar notificaciones automáticas por cambio de estado
2. Agregar campos adicionales como fecha de confirmación
3. Crear reportes de participación por período
4. Integrar con sistema de notificaciones por email/SMS
