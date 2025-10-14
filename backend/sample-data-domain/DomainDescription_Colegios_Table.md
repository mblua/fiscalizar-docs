# Tabla Colegios - AppSheet

## Información General

- **Nombre de la Tabla:** Colegios
- **Fuente:** AppSheet_Fiscalizacion
- **Calificador:** Colegios
- **Fuente de Datos:** google
- **Tipo de Fuente:** Sheets
- **Número de Columnas:** 5

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

### 2. ID Colegio
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
- **Búsqueda:** ✅ Sí
- **Escanear:** No
- **NFC:** No

### 3. Direccion
- **Tipo:** Text
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** (vacía)
- **Mostrar:** ✅ Sí
- **Editable:** ✅ Sí
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** ✅ Sí
- **Escanear:** No
- **NFC:** No

### 4. Zona ID
- **Tipo:** Text
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** (vacía)
- **Mostrar:** No
- **Editable:** No
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacío)
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

### 5. Related Mesas
- **Tipo:** List
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** `REF_ROWS("Mesas", '...')`
- **Mostrar:** ✅ Sí
- **Editable:** No
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

## Notas Técnicas

- La tabla utiliza Google Sheets como fuente de datos
- El campo `ID Colegio` es la clave primaria y se genera automáticamente con `UNIQUEID()`
- Existe una relación con la tabla "Mesas" a través del campo `Related Mesas`
- Los campos `ID Colegio`, `Direccion` y `Related Mesas` están configurados para ser visibles en la interfaz
- Los campos `ID Colegio` y `Direccion` están habilitados para búsqueda
- El campo `Zona ID` está oculto en la interfaz pero presente en la estructura de datos
