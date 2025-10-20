# 🧪 Guía de Testing - Backend API Fiscalizar

## 📋 Información General

Este documento describe cómo probar el backend API del sistema de fiscalización electoral. El backend está implementado con Node.js, TypeScript, Prisma y PostgreSQL.

### 🏗️ Arquitectura Implementada
- **Runtime**: Node.js 18+
- **Lenguaje**: TypeScript estricto
- **ORM**: Prisma
- **Base de datos**: PostgreSQL
- **Framework**: Express.js
- **Puerto**: 3000

## 🗳️ Dominio Electoral Modelado

### Jerarquía de Entidades:
```
Zonas (nivel más alto)
├── Colegios
    ├── Mesas
        └── Fiscales (asignados)
```

### Modelos de Base de Datos:

#### **Zona**
- `id` (String, PK) - Identificador único
- `nombre` (String) - Nombre de la zona electoral
- `createdAt`, `updatedAt` - Timestamps

#### **Colegio**
- `id` (String, PK) - Identificador único
- `direccion` (String, opcional) - Dirección del colegio
- `zonaId` (String, FK) - Referencia a Zona
- `createdAt`, `updatedAt` - Timestamps

#### **Fiscal**
- `id` (String, PK) - Identificador único
- `dni` (String, opcional) - DNI del fiscal
- `email` (String, opcional) - Email del fiscal
- `celular` (String, opcional) - Teléfono del fiscal
- `direccion` (String, opcional) - Dirección del fiscal
- `tipo` (String, opcional) - Tipo de fiscal (General, Mesa, etc.)
- `disponibilidad` (String, opcional) - Disponibilidad del fiscal
- `notasInternas` (String, opcional) - Notas internas
- `estado` (String, opcional) - Estado del fiscal
- `createdAt`, `updatedAt` - Timestamps

#### **Mesa**
- `id` (String, PK) - Identificador único
- `colegioId` (String, FK) - Referencia a Colegio
- `fiscalId` (String, FK) - Referencia a Fiscal
- `totalVotantesPadron` (Int, opcional) - Total de votantes en el padrón
- `totalVotos11Hs` (Int, opcional) - Votos registrados a las 11:00
- `totalVotos15Hs` (Int, opcional) - Votos registrados a las 15:00
- `totalVotos18Hs` (Int, opcional) - Votos registrados a las 18:00
- `createdAt`, `updatedAt` - Timestamps

## 🚀 Endpoints de la API

### Base URL: `http://localhost:3000/api`

#### **Zonas** (`/api/zonas`)
- `GET /` - Listar zonas (con filtros y paginación)
- `GET /:id` - Obtener zona por ID
- `POST /` - Crear zona
- `PUT /:id` - Actualizar zona
- `DELETE /:id` - Eliminar zona

#### **Colegios** (`/api/colegios`)
- `GET /` - Listar colegios (con filtros y paginación)
- `GET /:id` - Obtener colegio por ID
- `POST /` - Crear colegio
- `PUT /:id` - Actualizar colegio
- `DELETE /:id` - Eliminar colegio
- `GET /zona/:zonaId` - Obtener colegios por zona

#### **Fiscales** (`/api/fiscales`)
- `GET /` - Listar fiscales (con filtros y paginación)
- `GET /:id` - Obtener fiscal por ID
- `POST /` - Crear fiscal
- `PUT /:id` - Actualizar fiscal
- `DELETE /:id` - Eliminar fiscal

#### **Mesas** (`/api/mesas`)
- `GET /` - Listar mesas (con filtros y paginación)
- `GET /:id` - Obtener mesa por ID
- `POST /` - Crear mesa
- `PUT /:id` - Actualizar mesa
- `DELETE /:id` - Eliminar mesa
- `GET /colegio/:colegioId` - Obtener mesas por colegio
- `GET /fiscal/:fiscalId` - Obtener mesas por fiscal

#### **CSV** (`/api/csv`)
- `POST /upload` - Subir y procesar archivo CSV (solo usuarios)
- `GET /history` - Historial de importaciones

#### **Health Check**
- `GET /health` - Estado del servidor

## 🧪 Instrucciones de Testing

### ⚠️ REGLA CRÍTICA PARA TESTING

**NUNCA ejecutes `npm run dev` en primer plano** - esto bloquea la terminal y no permite continuar con el testing.

**SIEMPRE usa `is_background: true`** para el comando de desarrollo.

### 🔧 Configuración Inicial

1. **Navegar al directorio del backend:**
   ```bash
   cd fiscalizar-backend
   ```

2. **Instalar dependencias:**
   ```bash
   npm install
   ```

3. **Configurar base de datos:**
   ```bash
   npx prisma generate
   npx prisma db push
   ```

4. **Poblar con datos de ejemplo:**
   ```bash
   npm run db:seed-electoral
   ```

5. **Iniciar servidor (EN BACKGROUND):**
   ```bash
   npm run dev  # con is_background: true
   ```

6. **Esperar 3-5 segundos:**
   ```bash
   Start-Sleep -Seconds 3
   ```

### 🧪 Tests a Realizar

#### **1. Health Check**
```bash
curl http://localhost:3000/health
```
**Resultado esperado:** Status 200, JSON con información del servidor

#### **2. Zonas**
```bash
# Listar zonas
curl http://localhost:3000/api/zonas

# Crear zona
curl -X POST http://localhost:3000/api/zonas \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Zona Test"}'

# Obtener zona por ID (usar ID de respuesta anterior)
curl http://localhost:3000/api/zonas/{id}
```

#### **3. Colegios**
```bash
# Listar colegios
curl http://localhost:3000/api/colegios

# Crear colegio
curl -X POST http://localhost:3000/api/colegios \
  -H "Content-Type: application/json" \
  -d '{"direccion":"Av. Test 123","zonaId":"zona-1"}'

# Obtener colegios por zona
curl http://localhost:3000/api/colegios/zona/zona-1
```

#### **4. Fiscales**
```bash
# Listar fiscales
curl http://localhost:3000/api/fiscales

# Crear fiscal
curl -X POST http://localhost:3000/api/fiscales \
  -H "Content-Type: application/json" \
  -d '{"dni":"12345678","email":"fiscal@test.com","tipo":"Fiscal Test"}'
```

#### **5. Mesas**
```bash
# Listar mesas
curl http://localhost:3000/api/mesas

# Crear mesa
curl -X POST http://localhost:3000/api/mesas \
  -H "Content-Type: application/json" \
  -d '{"colegioId":"colegio-1","fiscalId":"fiscal-1","totalVotantesPadron":500}'

# Obtener mesas por colegio
curl http://localhost:3000/api/mesas/colegio/colegio-1

# Obtener mesas por fiscal
curl http://localhost:3000/api/mesas/fiscal/fiscal-1
```

#### **6. Filtros y Paginación**
```bash
# Filtros en zonas
curl "http://localhost:3000/api/zonas?nombre=Norte"

# Filtros en colegios
curl "http://localhost:3000/api/colegios?direccion=Av"

# Filtros en fiscales
curl "http://localhost:3000/api/fiscales?email=fiscal"

# Paginación
curl "http://localhost:3000/api/colegios?page=1&limit=5"

# Ordenamiento
curl "http://localhost:3000/api/fiscales?sortBy=email&sortOrder=asc"
```

#### **7. Validaciones**
```bash
# Email inválido (debe fallar)
curl -X POST http://localhost:3000/api/fiscales \
  -H "Content-Type: application/json" \
  -d '{"email":"email-invalido"}'

# DNI duplicado (debe fallar)
curl -X POST http://localhost:3000/api/fiscales \
  -H "Content-Type: application/json" \
  -d '{"dni":"12345678","email":"nuevo@test.com"}'

# Nombre requerido (debe fallar)
curl -X POST http://localhost:3000/api/zonas \
  -H "Content-Type: application/json" \
  -d '{}'
```

#### **8. Actualizaciones**
```bash
# Actualizar zona
curl -X PUT http://localhost:3000/api/zonas/{id} \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Zona Actualizada"}'

# Actualizar fiscal
curl -X PUT http://localhost:3000/api/fiscales/{id} \
  -H "Content-Type: application/json" \
  -d '{"tipo":"Fiscal Actualizado","estado":"Activo"}'

# Actualizar mesa con conteos
curl -X PUT http://localhost:3000/api/mesas/{id} \
  -H "Content-Type: application/json" \
  -d '{"totalVotos11Hs":150,"totalVotos15Hs":300,"totalVotos18Hs":450}'
```

#### **9. Eliminaciones**
```bash
# Eliminar fiscal
curl -X DELETE http://localhost:3000/api/fiscales/{id}

# Eliminar mesa
curl -X DELETE http://localhost:3000/api/mesas/{id}

# Eliminar colegio
curl -X DELETE http://localhost:3000/api/colegios/{id}

# Eliminar zona
curl -X DELETE http://localhost:3000/api/zonas/{id}
```

### 📊 Respuestas Esperadas

#### **Formato de Respuesta Estándar:**
```json
{
  "success": true,
  "data": {...},
  "message": "Operación exitosa",
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

#### **Formato de Respuesta Paginada:**
```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "totalPages": 10
  },
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

#### **Formato de Error:**
```json
{
  "success": false,
  "message": "Descripción del error",
  "error": "Detalle técnico",
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

### 🔍 Verificaciones Adicionales

#### **1. Relaciones**
- Verificar que los colegios muestren su zona
- Verificar que las mesas muestren su colegio y fiscal
- Verificar que los fiscales muestren sus mesas asignadas

#### **2. Validaciones de Integridad**
- DNI único por fiscal
- Email único por fiscal
- Números no negativos en conteos de votos
- Referencias válidas (zonaId, colegioId, fiscalId)
- Jerarquía electoral respetada (Zona → Colegio → Mesa → Fiscal)

#### **3. Logging**
- Verificar que se generen logs estructurados
- Verificar diferentes niveles de log (INFO, ERROR, etc.)

### 🚨 Solución de Problemas

#### **Servidor no responde:**
```bash
# Verificar procesos
Get-Process | Where-Object {$_.ProcessName -like "*node*"}

# Detener procesos si es necesario
Get-Process | Where-Object {$_.ProcessName -like "*node*"} | Stop-Process -Force

# Reiniciar servidor
npm run dev  # con is_background: true
```

#### **Base de datos bloqueada:**
```bash
# Detener procesos
Get-Process | Where-Object {$_.ProcessName -like "*node*"} | Stop-Process -Force

# Eliminar archivos de bloqueo
Remove-Item -Path "*.db*" -Force

# Recrear base de datos
npx prisma db push
npm run db:seed-electoral
```

### 📝 Notas Importantes

- **Siempre usar `is_background: true`** para `npm run dev`
- **Esperar 3-5 segundos** después de iniciar el servidor
- **Verificar health check** antes de probar otros endpoints
- **Usar IDs reales** de las respuestas para pruebas posteriores
- **Probar tanto casos exitosos como de error**
- **Verificar que las relaciones funcionen correctamente**

### 🎯 Objetivo del Testing

Verificar que el sistema de fiscalización electoral funcione correctamente con:
- ✅ CRUD completo para todas las entidades
- ✅ Relaciones jerárquicas funcionando
- ✅ Validaciones robustas
- ✅ Filtros y paginación
- ✅ Manejo de errores
- ✅ Logging estructurado
- ✅ Respuestas consistentes
