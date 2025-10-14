# 🧠 Prompt Ideal para Construir la Aplicación en Cursor IA

## 🎯 Objetivo

Crear una **aplicación de gestión de asignaciones de personas a escuelas** para un operativo electoral, usando **TypeScript + Prisma + SQLite**. La app debe permitir cargar personas, asignarlas a escuelas, registrar capacitación, horarios, y generar reportes.

---

## ⚙️ Tecnología Base

* Lenguaje: **TypeScript**
* ORM: **Prisma**
* Base de datos: **SQLite** (`app.db`)
* Librerías adicionales: `csv-parse`, `fs`, `@prisma/client`

---

## 🗂️ Estructura del Proyecto

```
/prisma/schema.prisma
/data/personas.csv
/data/escuelas.csv
/data/asignaciones.csv
/scripts/importar.ts
/scripts/exportar.ts
/scripts/reportes.ts
/scripts/insertar_por_texto.ts
```

---

## 🧩 Modelo de Datos Prisma

### **Persona**

* `id` (auto)
* `dni` (string, único)
* `nombre`
* `apellido`
* `telefono` (opcional)
* `barrio` (opcional)
* `lugarVotacion` (string opcional)
* `mesa` (string opcional)
* `notas` (string opcional)
* timestamps (`createdAt`, `updatedAt`)

### **Escuela**

* `id` (auto)
* `codigo` (string único)
* `nombre`
* `direccion` (opcional)
* `ciudad` (opcional)

### **Asignacion**

* `id` (auto)
* `personaId` (FK)
* `escuelaId` (FK)
* `rol` (string opcional)
* `capacitado` (boolean)
* `horarioLlegada` (string tipo “07:30”)
* `estado` (enum: `PENDIENTE`, `CONFIRMADO`, `BAJA`)
* timestamps (`createdAt`, `updatedAt`)

🔗 **Relaciones**:

* Una `Persona` tiene muchas `Asignaciones`
* Una `Escuela` tiene muchas `Asignaciones`

---

## 🧮 Scripts Requeridos

### `importar.ts`

* Lee `personas.csv`, `escuelas.csv`, `asignaciones.csv`.
* Crea o actualiza registros en la base.
* Usa `connectOrCreate` para relacionar personas y escuelas.

### `reportes.ts`

* Genera reportes:

  * Faltan capacitar (`capacitado = false`)
  * Confirmados por escuela (conteo)
  * Horarios por escuela ordenados (`horarioLlegada` asc)
* Exporta resultados a consola o CSV.

### `insertar_por_texto.ts`

* Define `agregarDesdeTexto(texto: string)`
* Inserta líneas como:

  ```
  Ana,Torres,34111222,E-001,07:30,Sí
  Martín,López,33222111,E-002,08:00,No
  ```
* Crea personas y asignaciones con `connectOrCreate`.

---

## 💬 Modo Conversacional (en Chat de Cursor)

Quiero poder interactuar así:

* “Insertá estas personas en la base…” → genera código Prisma con `createMany()`
* “Quiénes llegan después de las 08:00” → propone consulta Prisma o SQL.
* “Mostrame los confirmados por escuela” → genera `findMany()` con filtros y orden.

---

## ✅ Extras

* Usar `@updatedAt` y `@default(now())` donde corresponda.
* Incluir comentarios breves explicativos.
* Mostrar pasos finales de uso:

  ```
  npm install
  npx prisma migrate dev
  ts-node scripts/importar.ts
  ts-node scripts/reportes.ts
  ```

---

## 🧭 Resultado Esperado

Un proyecto **listo para ejecutar** y **compatible con el chat de Cursor**, permitiendo:

* Ingresar datos desde texto.
* Generar y correr consultas dinámicas por chat.
* Exportar reportes.

---

## 💡 Siguiente Paso Recomendado

Una vez creado el proyecto con este prompt, pedirle a Cursor:

> “Generame datos de ejemplo para 5 personas, 3 escuelas y sus asignaciones.”

De esa forma tendrás una base inicial lista para probar los scripts y reportes.
