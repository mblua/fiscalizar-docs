# PostgreSQL en Windows con Docker - Tutorial Completo

## 📋 Prerrequisitos

### 1. Docker Desktop para Windows
- **Descargar**: [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- **Requisitos**: Windows 10/11 con WSL2 habilitado
- **RAM mínima**: 4GB (recomendado 8GB+)

### 2. Verificar instalación de Docker
```powershell
# Verificar que Docker esté instalado y funcionando
docker --version
docker-compose --version

# Verificar que Docker Desktop esté ejecutándose
docker info
```

---

## 🚀 Instalación de PostgreSQL con Docker

### Opción 1: Comando Docker Simple

#### 1. Crear contenedor PostgreSQL
```powershell
# Crear y ejecutar contenedor PostgreSQL
docker run --name postgres-fiscalizar \
  -e POSTGRES_DB=fiscalizar \
  -e POSTGRES_USER=fiscalizar_user \
  -e POSTGRES_PASSWORD=fiscalizar_password \
  -p 5432:5432 \
  -v postgres_data:/var/lib/postgresql/data \
  -d postgres:15
```

#### 2. Verificar que esté funcionando
```powershell
# Ver contenedores en ejecución
docker ps

# Ver logs del contenedor
docker logs postgres-fiscalizar

# Probar conexión
docker exec -it postgres-fiscalizar psql -U fiscalizar_user -d fiscalizar
```

### Opción 2: Docker Compose (Recomendado)

#### 1. Crear archivo `docker-compose.yml`
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    container_name: postgres-fiscalizar
    environment:
      POSTGRES_DB: fiscalizar
      POSTGRES_USER: fiscalizar_user
      POSTGRES_PASSWORD: fiscalizar_password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init-scripts:/docker-entrypoint-initdb.d
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U fiscalizar_user -d fiscalizar"]
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  postgres_data:
```

#### 2. Ejecutar con Docker Compose
```powershell
# Iniciar servicios
docker-compose up -d

# Ver logs
docker-compose logs -f postgres

# Verificar estado
docker-compose ps
```

---

## 🔧 Configuración Avanzada

### 1. Variables de Entorno Personalizadas
```yaml
# docker-compose.yml con configuración avanzada
version: '3.8'

services:
  postgres:
    image: postgres:15
    container_name: postgres-fiscalizar
    environment:
      POSTGRES_DB: fiscalizar
      POSTGRES_USER: fiscalizar_user
      POSTGRES_PASSWORD: fiscalizar_password
      # Configuraciones adicionales
      POSTGRES_INITDB_ARGS: "--encoding=UTF-8 --lc-collate=C --lc-ctype=C"
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./postgresql.conf:/etc/postgresql/postgresql.conf
      - ./init-scripts:/docker-entrypoint-initdb.d
    command: postgres -c config_file=/etc/postgresql/postgresql.conf
    restart: unless-stopped
```

### 2. Archivo de configuración PostgreSQL
```ini
# postgresql.conf
# Configuraciones básicas para desarrollo
max_connections = 100
shared_buffers = 256MB
effective_cache_size = 1GB
maintenance_work_mem = 64MB
checkpoint_completion_target = 0.9
wal_buffers = 16MB
default_statistics_target = 100
random_page_cost = 1.1
effective_io_concurrency = 200
```

### 3. Scripts de inicialización
```sql
-- init-scripts/01-create-extensions.sql
-- Crear extensiones útiles
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";
CREATE EXTENSION IF NOT EXISTS "btree_gin";
```

---

## 🛠️ Comandos de Gestión

### Gestión del Contenedor
```powershell
# Iniciar contenedor
docker start postgres-fiscalizar

# Detener contenedor
docker stop postgres-fiscalizar

# Reiniciar contenedor
docker restart postgres-fiscalizar

# Eliminar contenedor
docker rm postgres-fiscalizar

# Ver logs en tiempo real
docker logs -f postgres-fiscalizar
```

### Gestión con Docker Compose
```powershell
# Iniciar servicios
docker-compose up -d

# Detener servicios
docker-compose down

# Reiniciar servicios
docker-compose restart

# Ver logs
docker-compose logs -f

# Eliminar todo (incluyendo volúmenes)
docker-compose down -v
```

### Conexión y Administración
```powershell
# Conectar a PostgreSQL
docker exec -it postgres-fiscalizar psql -U fiscalizar_user -d fiscalizar

# Crear backup
docker exec postgres-fiscalizar pg_dump -U fiscalizar_user fiscalizar > backup.sql

# Restaurar backup
docker exec -i postgres-fiscalizar psql -U fiscalizar_user -d fiscalizar < backup.sql
```

---

## 🔌 Conexión desde Aplicaciones

### String de Conexión
```
# Para Prisma (schema.prisma)
datasource db {
  provider = "postgresql"
  url      = "postgresql://fiscalizar_user:fiscalizar_password@localhost:5432/fiscalizar"
}

# Para Node.js con pg
const connectionString = "postgresql://fiscalizar_user:fiscalizar_password@localhost:5432/fiscalizar";
```

### Variables de Entorno (.env)
```env
# .env
DATABASE_URL="postgresql://fiscalizar_user:fiscalizar_password@localhost:5432/fiscalizar"
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=fiscalizar
POSTGRES_USER=fiscalizar_user
POSTGRES_PASSWORD=fiscalizar_password
```

---

## 🧪 Testing y Verificación

### 1. Verificar Conexión
```powershell
# Test de conectividad
telnet localhost 5432

# Test con psql
docker exec -it postgres-fiscalizar psql -U fiscalizar_user -d fiscalizar -c "SELECT version();"
```

### 2. Verificar Base de Datos
```sql
-- Conectar a PostgreSQL
docker exec -it postgres-fiscalizar psql -U fiscalizar_user -d fiscalizar

-- Ver bases de datos
\l

-- Ver tablas
\dt

-- Ver usuarios
\du

-- Salir
\q
```

### 3. Test de Rendimiento
```sql
-- Crear tabla de prueba
CREATE TABLE test_table (
    id SERIAL PRIMARY KEY,
    data TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Insertar datos de prueba
INSERT INTO test_table (data) 
SELECT 'Test data ' || generate_series(1, 1000);

-- Verificar rendimiento
EXPLAIN ANALYZE SELECT * FROM test_table WHERE id > 500;
```

---

## 🔒 Seguridad y Mejores Prácticas

### 1. Configuración de Seguridad
```yaml
# docker-compose.yml con configuración segura
version: '3.8'

services:
  postgres:
    image: postgres:15
    container_name: postgres-fiscalizar
    environment:
      POSTGRES_DB: fiscalizar
      POSTGRES_USER: fiscalizar_user
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}  # Usar variable de entorno
    ports:
      - "127.0.0.1:5432:5432"  # Solo acceso local
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped
    # Configuraciones de seguridad
    security_opt:
      - no-new-privileges:true
    read_only: false
```

### 2. Backup Automático
```powershell
# Script de backup (backup.ps1)
$timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$backupFile = "backup_$timestamp.sql"

docker exec postgres-fiscalizar pg_dump -U fiscalizar_user fiscalizar > $backupFile
Write-Host "Backup creado: $backupFile"
```

### 3. Monitoreo
```powershell
# Ver uso de recursos
docker stats postgres-fiscalizar

# Ver logs de errores
docker logs postgres-fiscalizar 2>&1 | Select-String "ERROR"
```

---

## 🚨 Solución de Problemas

### Problemas Comunes

#### 1. Puerto 5432 ocupado
```powershell
# Verificar qué usa el puerto
netstat -ano | findstr :5432

# Cambiar puerto en docker-compose.yml
ports:
  - "5433:5432"  # Usar puerto 5433 en lugar de 5432
```

#### 2. Contenedor no inicia
```powershell
# Ver logs detallados
docker logs postgres-fiscalizar

# Verificar configuración
docker inspect postgres-fiscalizar

# Reiniciar Docker Desktop
# Reiniciar WSL2: wsl --shutdown
```

#### 3. Problemas de permisos
```powershell
# Verificar permisos de volúmenes
docker volume inspect postgres_data

# Recrear volumen si es necesario
docker volume rm postgres_data
docker-compose up -d
```

#### 4. Conexión rechazada
```powershell
# Verificar que el contenedor esté ejecutándose
docker ps | findstr postgres

# Verificar configuración de red
docker network ls
docker network inspect bridge
```

---

## 📊 Monitoreo y Mantenimiento

### 1. Scripts de Mantenimiento
```powershell
# Script de limpieza (cleanup.ps1)
Write-Host "Limpiando contenedores parados..."
docker container prune -f

Write-Host "Limpiando imágenes no utilizadas..."
docker image prune -f

Write-Host "Limpiando volúmenes no utilizados..."
docker volume prune -f
```

### 2. Monitoreo de Recursos
```powershell
# Ver uso de recursos en tiempo real
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}\t{{.BlockIO}}"

# Ver logs de PostgreSQL
docker logs postgres-fiscalizar --tail 100
```

### 3. Actualización de PostgreSQL
```powershell
# Backup antes de actualizar
docker exec postgres-fiscalizar pg_dump -U fiscalizar_user fiscalizar > backup_before_update.sql

# Detener contenedor actual
docker-compose down

# Actualizar imagen en docker-compose.yml
# postgres:15 -> postgres:16

# Iniciar nueva versión
docker-compose up -d
```

---

## 🎯 Integración con Fiscalizar Backend

### 1. Actualizar Prisma Schema
```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// ... resto del schema
```

  
## 📚 Recursos Adicionales

### Documentación Oficial
- [PostgreSQL Docker Hub](https://hub.docker.com/_/postgres)
- [Docker Compose Reference](https://docs.docker.com/compose/)
- [Prisma PostgreSQL](https://www.prisma.io/docs/concepts/database-connectors/postgresql)

### Herramientas Recomendadas
- **pgAdmin**: Interfaz gráfica para PostgreSQL
- **DBeaver**: Cliente universal de base de datos
- **TablePlus**: Cliente moderno para bases de datos

### Comandos Útiles
```powershell
# Instalar pgAdmin con Docker
docker run --name pgadmin -p 8080:80 -e PGADMIN_DEFAULT_EMAIL=admin@fiscalizar.org -e PGADMIN_DEFAULT_PASSWORD=admin -d dpage/pgadmin4

# Acceder a pgAdmin: http://localhost:8080
# Servidor: postgres-fiscalizar
# Puerto: 5432
# Usuario: fiscalizar_user
# Contraseña: fiscalizar_password
```

---

## ✅ Checklist de Instalación

- [ ] Docker Desktop instalado y funcionando
- [ ] Contenedor PostgreSQL ejecutándose
- [ ] Puerto 5432 accesible
- [ ] Base de datos `fiscalizar` creada
- [ ] Usuario `fiscalizar_user` configurado
- [ ] Conexión desde aplicación funcionando
- [ ] Backup configurado
- [ ] Monitoreo implementado

---

**¡PostgreSQL con Docker está listo para usar en tu proyecto Fiscalizar!** 🚀
