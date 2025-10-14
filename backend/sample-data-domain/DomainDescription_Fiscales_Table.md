# Tabla Fiscales - AppSheet

## Información General

- **Nombre de la Tabla:** Fiscales
- **Fuente:** AppSheet_Fiscalizacion
- **Calificador:** Fiscales
- **Fuente de Datos:** google
- **Tipo de Fuente:** Sheets
- **Número de Columnas:** 11

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

### 2. ID Fiscal
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

### 3. DNI
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

### 4. Email
- **Tipo:** Email
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

### 5. Celular
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

### 6. Direccion
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

### 7. Tipo
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

### 8. Disponibilidad
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

### 9. Notas Internas
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

### 10. Estado
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

### 11. Related Mesas
- **Tipo:** List
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** `REF_ROWS("Mesas", ...)`
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
- El campo `ID Fiscal` es la clave primaria y se genera automáticamente con `UNIQUEID()`
- Existe una relación con la tabla "Mesas" a través del campo `Related Mesas`
- Todos los campos excepto `_RowNumber` están configurados para ser visibles en la interfaz
- Los campos `DNI`, `Email`, `Celular`, `Direccion`, `Tipo`, `Disponibilidad`, `Notas Internas`, `Estado` y `Related Mesas` están habilitados para búsqueda
- El campo `Email` tiene tipo específico "Email" para validación automática
- El campo `Related Mesas` es de tipo List y no es editable, estableciendo una relación de referencia con la tabla "Mesas"
