### Fiscalizar.org — Diseño de Dominio y Modelo de Permisos

Este documento describe cómo funciona la aplicación a nivel de negocio: la jerarquía del dominio electoral, los roles del sistema, la herencia de permisos, el alcance territorial (scope) y las reglas de autorización por tipo de acción, incluyendo la carga de documentación fotográfica asociada a mesas (por ejemplo, Certificados de Escrutinio).

---

## Dominio Electoral

- **Zonas**: Unidad territorial principal de organización electoral.
- **Colegios**: Establecimientos educativos donde se vota, pertenecen a una zona.
- **Mesas**: Mesas de votación específicas dentro de un colegio.
- **Fiscales**: Personas con responsabilidades de fiscalización. Se clasifican por tipo de rol operativo.

Relación jerárquica: Zonas → Colegios → Mesas → Fiscales (asignados según funciones y alcance).

---

### Lugar de votación de Fiscales

En el modelo operativo, los fiscales también deben votar y, por lo general, NO votan en la misma mesa donde fiscalizan.

- Registro de votación:
  - Para cada fiscal se debe registrar su `colegioDondeVota` y, si se conoce, su `mesaDondeVota`.
  - Estos datos son independientes de las asignaciones de fiscalización (`mesasAFiscalizar`) y pueden pertenecer a otro colegio/zona.

- Implicancias operativas:
  - Planificación de traslados y tiempos: el sistema debe permitir visualizar conflictos de horario entre la mesa donde vota y la mesa donde fiscaliza.
  - Comunicación: avisos previos a fiscales sobre ventanas de tiempo recomendadas para ir a votar y regresar a fiscalizar.
  - Cobertura: permitir reasignaciones temporales si un fiscal necesita ausentarse para votar.

- Permisos sobre estos datos:
  - Visualización: ADMIN, COORDINADOR y el propio fiscal; FISCAL_ZONA y FISCAL_GENERAL dentro de su alcance territorial.
  - Edición:
    - ADMIN y COORDINADOR: siempre.
    - FISCAL_ZONA/FISCAL_GENERAL: solo si el fiscal pertenece a su alcance (zona/colegio).
    - El propio fiscal puede proponer actualización de `colegioDondeVota/mesaDondeVota` (sujeto a revisión si se define moderación).

- Notas de consistencia:
  - No se exige que `mesaDondeVota` exista en el mismo colegio/zona de fiscalización.
  - Cuando no se conoce la mesa, se registra únicamente `colegioDondeVota` y se permite completar la mesa más tarde.

---

## Roles del Sistema y Herencia

El sistema define roles con herencia para simplificar la administración de permisos. La herencia implica que un rol incluye las capacidades de los roles “inferiores”. Adicionalmente, cada rol (salvo ADMIN) opera dentro de un alcance territorial (scope) asignado.

- **Administrador (ADMIN)**
  - Acceso total a todo el sistema. Sin restricciones de alcance.
  - Incluye (por herencia) todos los demás roles.

- **Coordinador (COORDINADOR)**
  - Gestión organizativa: crear/editar/eliminar Zonas y Colegios; administrar vínculos Zona ↔ Colegio.
  - Puede crear y gestionar Fiscales de Zona.
  - Incluye permisos de “Fiscal de Zona”.
  - Alcance: global para organización o restringido según configuración de scopes.

- **Fiscal de Zona (FISCAL_ZONA)**
  - Incluye permisos de “Fiscal General”.
  - Sus permisos aplican solo a sus zona/s asignadas.

- **Fiscal General (FISCAL_GENERAL)**
  - Incluye permisos de “Fiscal de Mesa”.
  - Sus permisos aplican solo a su/s colegio/s asignado/s.

- **Fiscal de Mesa (FISCAL_MESA)**
  - Sus permisos aplican solo a su/s mesa/s asignada/s.

Herencia formal utilizada por la aplicación:

- ADMIN → COORDINADOR → FISCAL_ZONA → FISCAL_GENERAL → FISCAL_MESA

---

## Alcance Territorial (Scope)

Para roles no-ADMIN, todas las acciones están sujetas a su alcance territorial:

- **FISCAL_ZONA**: Autorizado únicamente sobre recursos que pertenezcan a sus `zonaIds` asignadas (y, por extensión, a los colegios y mesas de dichas zonas).
- **FISCAL_GENERAL**: Autorizado únicamente sobre recursos que pertenezcan a sus `colegioIds` asignados (y, por extensión, a las mesas de dichos colegios).
- **FISCAL_MESA**: Autorizado únicamente sobre sus `mesaIds` asignadas.

La aplicación valida el alcance comparando el recurso objetivo (zona, colegio o mesa) con las listas asignadas al usuario.

---

## Permisos por Tipo de Acción

Las siguientes reglas describen quién puede realizar cada acción. Siempre se aplican en este orden: 1) ADMIN sin restricciones; 2) verificación de rol (con herencia); 3) verificación de alcance territorial.

### Zonas
- Crear/editar/eliminar zona: ADMIN, COORDINADOR.
- Ver zonas: todos los roles autenticados; invitados según política pública.

### Colegios
- Crear/editar/eliminar colegio: ADMIN, COORDINADOR.
- Asignar/quitar colegio a zona: ADMIN, COORDINADOR.
- Ver colegios: todos los roles autenticados; invitados según política pública.

### Fiscales
- Crear “Fiscal de Zona”: ADMIN, COORDINADOR.
- Crear “Fiscal General”: ADMIN, COORDINADOR, FISCAL_ZONA (solo dentro de sus zonas asignadas).
- Crear “Fiscal de Mesa”: ADMIN, COORDINADOR, FISCAL_GENERAL (solo dentro de sus colegios asignados).
- Lectura/gestión de fiscales: sujeta al alcance territorial y tipo de fiscal.

#### Transiciones de Rol (Promociones)

Reglas de cambio de rol entre tipos de fiscales, respetando alcance territorial:

- **ADMIN**: puede realizar cualquier transformación de rol (sin restricciones de alcance).
- **COORDINADOR**:
  - Puede transformar un **Fiscal de Mesa → Fiscal General** (si el fiscal pertenece a su ámbito organizativo).
  - Puede transformar un **Fiscal General → Fiscal de Zona** (asignando la/s zona/s correspondientes).
- **FISCAL_ZONA**:
  - Puede transformar un **Fiscal de Mesa → Fiscal General**, únicamente si el fiscal y la(s) mesa(s)/colegio(s) involucrados pertenecen a sus **zonaIds** asignadas.

Notas:
- Las transiciones deben actualizar las asignaciones de alcance (p. ej., al promover a **Fiscal General**, asignar `colegioIds`; al promover a **Fiscal de Zona**, asignar `zonaIds`).
- Se recomienda registrar auditoría (quién realizó el cambio, cuándo y desde qué rol).

### Mesas
- Crear mesa: ADMIN, COORDINADOR, FISCAL_GENERAL (solo en sus colegios asignados).
- Editar/eliminar mesa: ADMIN, COORDINADOR; FISCAL_GENERAL dentro de su alcance (según reglas definidas por la organización).
- Actualización operativa (carga de datos y estado): ADMIN, COORDINADOR, FISCAL_GENERAL en su alcance; FISCAL_MESA solo en sus mesas asignadas.

### Carga de Fotos/Documentación Asociadas a Mesas

Se permite adjuntar documentación visual (por ejemplo, **Certificados de Escrutinio**, actas, fotos de telegramas, etc.) a una mesa específica.

- **Quién puede subir**:
  - ADMIN y COORDINADOR: cualquier mesa.
  - FISCAL_ZONA: mesas ubicadas dentro de sus zonas asignadas.
  - FISCAL_GENERAL: mesas ubicadas dentro de sus colegios asignados.
  - FISCAL_MESA: únicamente sus mesas asignadas.

- **Reglas y recomendaciones de negocio**:
  - Cada archivo debe asociarse a una mesa (`mesaId`) y contar con metadatos mínimos: tipo de documento (p. ej., `certificado_escrutinio`), autor, fecha/hora de captura y notas opcionales.
  - Validar límites de tamaño, tipo de archivo permitido (p. ej., JPG/PNG/PDF), y cantidad máxima por mesa para evitar abuso.
  - Mantener un registro de versión/historial por documento para auditoría.
  - (Opcional) Flujo de moderación: marcar como “pendiente/verificado/rechazado” según políticas internas.

---

## Ejemplos de Autorización (Escenarios Típicos)

1) Un **FISCAL_ZONA** crea un “Fiscal General” para un colegio de su zona → permitido si el colegio pertenece a una de sus `zonaIds`.

2) Un **FISCAL_GENERAL** crea una **Mesa** en un colegio asignado → permitido si `colegioId ∈ colegioIds` del usuario.

3) Un **FISCAL_MESA** actualiza el estado y carga un **Certificado de Escrutinio** de su mesa → permitido si `mesaId ∈ mesaIds` del usuario.

4) Un **COORDINADOR** asigna un **Colegio** a una **Zona** → permitido sin restricciones de alcance (gestión organizativa global).

5) Un **FISCAL_ZONA** sube fotos a una mesa de otra zona → denegado; fuera de su alcance territorial.

---

## Consideraciones Operativas

- La herencia de roles se resuelve en tiempo de ejecución expandiendo las capacidades del usuario según su rol mayor (p. ej., FISCAL_ZONA hereda FISCAL_GENERAL y FISCAL_MESA).
- El alcance territorial se deriva de las asignaciones registradas (p. ej., `UserZona`, `UserColegio`, `UserMesa`) y se valida en cada acción sensible.
- Las acciones organizativas (crear/eliminar zonas y colegios) generalmente son exclusivas de ADMIN/COORDINADOR.
- Las acciones operativas se apegan estrictamente al alcance: zonas → colegios → mesas.

---

## Canales de Conversación por Zona/Colegio/Mesa

El sistema contempla canales de conversación tipo “foro” asociados a cada entidad territorial para coordinación operativa y trazabilidad.

- **Tipos de canal**:
  - Canal de **Zona**: discusiones y avisos generales para todos los actores asociados a la zona.
  - Canal de **Colegio**: coordinación específica entre responsables del colegio y fiscales asignados.
  - Canal de **Mesa**: comunicación operativa directa (por ejemplo, incidencias, avances, fotos de documentación).

- **Participación y permisos**:
  - Lectura y escritura para usuarios relacionados con el recurso del canal, respetando alcance territorial:
    - ADMIN/COORDINADOR: acceso global.
    - FISCAL_ZONA: canales de sus zonas (y, por extensión, colegios y mesas de dichas zonas).
    - FISCAL_GENERAL: canales de sus colegios (y mesas dentro de esos colegios).
    - FISCAL_MESA: solo los canales de sus mesas asignadas.
  - (Opcional) Roles de moderación por canal para marcar mensajes como importantes, cerrar hilos o fijar posts.

- **Recomendaciones funcionales**:
  - Estructura de posts con título, contenido, autor, timestamps, adjuntos (imágenes/documentos), etiquetas.
  - Notificaciones configurables (push/email) por menciones, respuestas o palabras clave.
  - Buscador por texto, autor, etiqueta y rango temporal.
  - (Opcional) Estados de hilos: abierto, en seguimiento, resuelto.

- **Privacidad y cumplimiento**:
  - Respetar el alcance territorial en la visibilidad de hilos y adjuntos.
  - Registrar auditoría (quién publica, edita o elimina) y conservar histórico según políticas.

---

## Glosario

- **Alcance (Scope)**: Conjunto de zonas, colegios y/o mesas sobre los que un usuario puede operar.
- **Herencia de roles**: Mecanismo por el que un rol superior incluye permisos de roles inferiores.
- **Jurisprudencia (en este contexto)**: Autoridad/competencia sobre recursos de una zona/colegio/mesa asignados al usuario.

---

## Resumen Ejecutivo

La aplicación implementa una jerarquía territorial Zonas → Colegios → Mesas, con un modelo de roles con herencia (ADMIN, COORDINADOR, FISCAL_ZONA, FISCAL_GENERAL, FISCAL_MESA). Las operaciones organizativas recaen en ADMIN/COORDINADOR, mientras que las operativas dependen del alcance asignado a cada usuario. La carga y gestión de documentación fotográfica asociada a mesas está habilitada para quienes tengan competencia sobre dichas mesas, con controles de tipo, tamaño, metadatos y auditoría.


