# Tabla Mesas - AppSheet

## Información General

- **Nombre de la Tabla:** Mesas
- **Fuente:** AppSheet_Fiscalizacion
- **Calificador:** Mesas
- **Fuente de Datos:** google
- **Tipo de Fuente:** Sheets
- **Número de Columnas:** 8

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

### 2. ID Mesa
- **Tipo:** Text
- **Clave:** ✅ Sí
- **Etiqueta:** No
- **Fórmula:** (vacía)
- **Mostrar:** No
- **Editable:** No
- **Requerido:** ✅ Sí
- **Valor Inicial:** `UNIQUEID()`
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

### 3. Colegio ID
- **Tipo:** Ref (Referencia)
- **Clave:** No
- **Etiqueta:** ✅ Sí
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

### 4. Fiscal ID
- **Tipo:** Ref (Referencia)
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

### 5. Total Votantes Padron
- **Tipo:** Number
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** (vacía)
- **Mostrar:** ✅ Sí
- **Editable:** ✅ Sí
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

### 6. Total Votos 11Hs
- **Tipo:** Number
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** (vacía)
- **Mostrar:** ✅ Sí
- **Editable:** ✅ Sí
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

### 7. Total Votos 15Hs
- **Tipo:** Number
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** (vacía)
- **Mostrar:** ✅ Sí
- **Editable:** ✅ Sí
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

### 8. Total Votos 18Hs
- **Tipo:** Number
- **Clave:** No
- **Etiqueta:** No
- **Fórmula:** (vacía)
- **Mostrar:** ✅ Sí
- **Editable:** ✅ Sí
- **Requerido:** No
- **Valor Inicial:** (vacío)
- **Nombre de Visualización:** (vacío)
- **Descripción:** (vacía)
- **Búsqueda:** No
- **Escanear:** No
- **NFC:** No

## Notas Técnicas

- La tabla utiliza Google Sheets como fuente de datos
- El campo `ID Mesa` es la clave primaria y se genera automáticamente con `UNIQUEID()`
- Existen referencias a otras tablas:
  - `Colegio ID`: Referencia a la tabla "Colegios" (configurado como etiqueta)
  - `Fiscal ID`: Referencia a la tabla "Fiscales"
- Los campos `Colegio ID` y `Fiscal ID` están habilitados para búsqueda
- La tabla registra información de votación en diferentes horarios:
  - `Total Votantes Padron`: Número total de votantes en el padrón
  - `Total Votos 11Hs`: Votos registrados a las 11:00 horas
  - `Total Votos 15Hs`: Votos registrados a las 15:00 horas
  - `Total Votos 18Hs`: Votos registrados a las 18:00 horas
- Todos los campos de conteo de votos son editables y visibles
- El campo `ID Mesa` está oculto en la interfaz pero es requerido para la integridad de datos
