# Mejores prácticas — Prisma + SQLite (directo y práctico)

A continuación tenés una guía concisa y accionable para diseñar esquemas y trabajar con Prisma + SQLite, incluyendo lo que hablamos (clave numérica `id` vs usar `nombre`, nombres de FK, índices, timestamps, migraciones, etc.).

---

## Principios generales

* **Siempre tener una clave primaria separada** (`id` numérico `Int` autoincremental o `String` con UUID generado por la app) en lugar de usar el `nombre` como PK, salvo que sea un **identificador oficial**, inmutable y estandarizado.
* **Campo legible (nombre)**: tener `nombre` como campo único (`UNIQUE`) cuando corresponda, pero distinto del `id`.
* **Nombre de FK claro**: usar `tablaSingularId` en la tabla hija (en Prisma: `tipoFiscalId`), y declarar la relación explícita.
* **Normalizar**: evita duplicar datos; guarda referencias mediante FK.
* **Índices** para columnas usadas en `WHERE`, `JOIN` y ordenamientos frecuentes.
* **Timestamps**: `createdAt` y `updatedAt`. Usá `@default(now())` y `@updatedAt`.
* **Soft delete**: mejor `isDeleted` o `deletedAt` que eliminar filas físicamente si necesitás histórico o recuperación.
* **Backups y migraciones**: SQLite tiene limitaciones con `ALTER TABLE` — planteá migraciones cuidadas y backups.

---

## Recomendación de tipos para SQLite (con Prisma)

* **PK simple y eficiente**: `id Int @id @default(autoincrement())`
* **Alternativa**: `id String @id` con UUID generado por la aplicación si necesitás identificadores fuera del DB.
* **Fechas**: `DateTime @default(now())` y `DateTime @updatedAt`.
* **Texto**: `String` con tamaño controlado por la app (SQLite no impone longitudes).

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
  provider = "sqlite"
  url      = "file:./dev.db"
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

## Migraciones y backups en SQLite

* **Hacé backup** antes de cada migración. SQLite no maneja bien cambios complejos con `ALTER TABLE` (Prisma crea tablas temporales y copia datos, pero siempre hay riesgo).
* Si necesitás cambios estructurales grandes: exportá, transformá y reimportá en un nuevo archivo DB.
* Considerá usar una base de datos más robusta (Postgres) si necesitás concurrencia y escalado.

---

## Integridad referencial y PRAGMA

* Asegurate que `foreign_keys` esté activo si accedés directamente con sqlite CLI: `PRAGMA foreign_keys = ON;`. Prisma activa y maneja esto internamente en la mayoría de los casos, pero es bueno saberlo.

---

## Rendimiento y límites de SQLite

* SQLite es excelente para apps pequeñas, testing y prototipos.
* Limitaciones: concurrencia de escritura (solo un writer a la vez), joins complejos y grandes volúmenes pueden degradar rendimiento.
* Para escalado o multi-tenant con alta concurrencia, migrar a Postgres/MySQL.

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
