# Mejores prácticas — Prisma + PostgreSQL (directo y práctico)

A continuación tenés una guía concisa y accionable para diseñar esquemas y trabajar con Prisma + PostgreSQL, incluyendo lo que hablamos (clave numérica `id` vs usar `nombre`, nombres de FK, índices, timestamps, migraciones, etc.).

---

## Principios generales

* **Siempre tener una clave primaria separada** (`id` numérico `Int` autoincremental o `String` con UUID generado por la app) en lugar de usar el `nombre` como PK, salvo que sea un **identificador oficial**, inmutable y estandarizado.
* **Campo legible (nombre)**: tener `nombre` como campo único (`UNIQUE`) cuando corresponda, pero distinto del `id`.
* **Nombre de FK claro**: usar `tablaSingularId` en la tabla hija (en Prisma: `tipoFiscalId`), y declarar la relación explícita.
* **Normalizar**: evita duplicar datos; guarda referencias mediante FK.
* **Índices** para columnas usadas en `WHERE`, `JOIN` y ordenamientos frecuentes.
* **Timestamps**: `createdAt` y `updatedAt`. Usá `@default(now())` y `@updatedAt`.
* **Soft delete**: mejor `isDeleted` o `deletedAt` que eliminar filas físicamente si necesitás histórico o recuperación.
* **Backups y migraciones**: PostgreSQL maneja bien las migraciones con `ALTER TABLE` — pero siempre es buena práctica hacer backups antes de cambios importantes.

---

## Recomendación de tipos para PostgreSQL (con Prisma)

* **PK simple y eficiente**: `id Int @id @default(autoincrement())`
* **Alternativa**: `id String @id` con UUID generado por la aplicación si necesitás identificadores fuera del DB.
* **Fechas**: `DateTime @default(now())` y `DateTime @updatedAt`.
* **Texto**: `String` con tamaño controlado por la app (PostgreSQL permite configurar longitudes máximas).

---

## Convenciones de nombres (claras y consistentes)

* Tabla: plural en DB (`colegios`, `fiscales`, `tipos_fiscales`) — en Prisma usá PascalCase para modelos (`model Colegio`).
* FK en tabla hija: `tablaReferenciadaId` → en Prisma: `tipoFiscalId`.
* Campo legible: `nombre` (y `nombre` único si corresponde).
* Evitá plurales raros en FK (no `tiposFiscalesId`).

---

## Ejemplo práctico: `fiscales` ↔ `tipos_fiscales` (Prisma schema)

```prisma
// schema.prisma (fragmento)
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model TipoFiscal {
  id        Int       @id @default(autoincrement())
  nombre    String    @unique
  fiscales  Fiscal[]
}

model Fiscal {
  id           Int        @id @default(autoincrement())
  nombre       String
  apellido     String
  tipoFiscalId Int
  tipoFiscal   TipoFiscal @relation(fields: [tipoFiscalId], references: [id])

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([tipoFiscalId])
}
```

Notas:

* `tipoFiscalId` es la FK y `tipoFiscal` la relación.
* `TipoFiscal.nombre` marcado `@unique` para evitar duplicados semánticos pero **no** como PK.
* `@@index([tipoFiscalId])` mejora joins y búsquedas por tipo.

---

## Índices y constraints

* `@unique` para campos que deben ser únicos (`codigo`, `email`, `nombre` si aplica).
* `@@index([columna])` para columnas usadas en filtros/joins frecuentes.
* Evitá indexar columnas muy volátiles (frecuentes updates) sin necesidad.

---

## Migraciones y backups en PostgreSQL

* **Hacé backup** antes de cada migración. PostgreSQL maneja bien los cambios complejos con `ALTER TABLE` y Prisma genera migraciones seguras.
* Para cambios estructurales grandes: PostgreSQL soporta transacciones DDL, lo que hace las migraciones más seguras.
* PostgreSQL es ideal para aplicaciones con alta concurrencia y escalado.

---

## Integridad referencial en PostgreSQL

* PostgreSQL tiene integridad referencial habilitada por defecto. Prisma maneja las foreign keys automáticamente y PostgreSQL las valida en tiempo real.

---

## Rendimiento y ventajas de PostgreSQL

* PostgreSQL es excelente para aplicaciones de producción, testing y prototipos.
* Ventajas: alta concurrencia de escritura, joins complejos optimizados, grandes volúmenes de datos, y escalado horizontal.
* Ideal para aplicaciones multi-tenant con alta concurrencia y requerimientos de rendimiento.

---

## Buenas prácticas de desarrollo con Prisma

* Usá el **Prisma Client** generado en la capa de negocio (no SQL crudo salvo necesario).
* Manejá transacciones para operaciones multi-tabla (`prisma.$transaction([...])`).
* Validá unicidad y reglas de negocio en la capa de aplicación y en la DB (constraints).
* Mantén los seeds y fixtures reproducibles para pruebas.

---

## Checklist rápido (aplicable a `fiscales`, `colegios`, etc.)

* [x] `id` separado (Int autoincrement o UUID por la app)
* [x] `nombre` como campo, marcado `@unique` si corresponde
* [x] FK nombrada `entidadId` (ej. `colegioId`, `tipoFiscalId`)
* [x] Relaciones Prisma declaradas con `@relation(fields: ..., references: ...)`
* [x] `createdAt` y `updatedAt` presentes
* [x] Índices para filtros/joins frecuentes
* [x] Evitar usar `nombre` como PK salvo identificador externo inmutable
* [x] Backups antes de migraciones
