# Tabla Zonas - AppSheet

## Información General

- **Nombre de la Tabla:** Zonas
- **Fuente:** AppSheet_Fiscalizacion
- **Calificador:** Zonas
- **Fuente de Datos:** google
- **Tipo de Fuente:** Sheets
- **Número de Columnas:** 3

## Estructura de Columnas

### 1. _RowNumber
- **Tipo:** Number
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** (vacía)
- **Mostrar:** No
- **Editable:** No
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** Number of this row
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

### 2. ID Zona
- **Tipo:** Text
- **Clave:** ✅ Sí
- **Etiqueta:** ✅ Sí
- **Fórmula:** (vacía)
- **Mostrar:** ✅ Sí
- **Editable:** ✅ Sí
- **Requerido:** ✅ Sí
- **Valor Inicial:** `UNIQUEID()`
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

### 3. Related Colegios
- **Tipo:** List
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** `REF_ROWS("Colegios")`
- **Mostrar:** ✅ Sí
- **Editable:** No
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** ✅ Sí
- **Escanear:** No
- **NFC:** No

## Notas Técnicas

- La tabla utiliza Google Sheets como fuente de datos
- El campo `ID Zona` es la clave primaria y se genera automáticamente con `UNIQUEID()`
- Existe una relación con la tabla "Colegios" a través del campo `Related Colegios`
- El campo `ID Zona` está configurado como clave, etiqueta, visible, editable y requerido
- El campo `Related Colegios` es de tipo List y no es editable, estableciendo una relación de referencia con la tabla "Colegios"
- El campo `Related Colegios` está habilitado para búsqueda
- Esta tabla representa la estructura jerárquica donde las Zonas contienen múltiples Colegios
- Es la tabla de nivel más alto en la jerarquía: Zonas → Colegios → Mesas → Fiscales
