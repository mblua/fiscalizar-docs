### Fiscalizar.org — Diseño de Dominio y Modelo de Permisos

Este documento describe cómo funciona la aplicación a nivel de negocio: la jerarquía del dominio electoral, los roles del sistema, la herencia de permisos, el alcance territorial (scope) y las reglas de autorización por tipo de acción, incluyendo la carga de documentación fotográfica asociada a mesas (por ejemplo, Certificados de Escrutinio).

> **⚠️ ESTADO ACTUAL**: Este documento describe el **diseño conceptual** del sistema de permisos y roles. La implementación actual del backend **NO incluye** el sistema de roles, permisos ni alcance territorial descrito aquí. El sistema actual solo implementa autenticación básica por `client_secret` y operaciones CRUD sin restricciones de permisos.

---

## Dominio Electoral

- **Zonas**: Unidad territorial principal de organización electoral.
- **Colegios**: Establecimientos educativos donde se vota, pertenecen a una zona.
- **Mesas**: Mesas de votación específicas dentro de un colegio.
- **Fiscales**: Personas con responsabilidades de fiscalización. Se clasifican por tipo de rol operativo.
- **TiposFiscales**: Categorías de fiscales (Fiscal General, Fiscal de Mesa, Fiscal de Zona).

Relación jerárquica: Zonas → Colegios → Mesas → Fiscales (asignados según funciones y alcance).

### Implementación Actual del Modelo de Datos

El sistema implementa correctamente las siguientes entidades en PostgreSQL:

#### **Zona**
- `id` (Int, PK) - Identificador único
- `nombre` (String, único) - Nombre de la zona
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete
- Relación: `colegios Colegio[]`

#### **Colegio**
- `id` (Int, PK) - Identificador único
- `nombre` (String, único) - Nombre del colegio
- `direccion` (String, opcional) - Dirección
- `zonaId` (Int, FK) - Referencia a Zona
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete
- Relaciones: `zona Zona?`, `mesas Mesa[]`, `fiscalesQueVotan Fiscal[]`, `fiscalColegios FiscalColegio[]`

#### **TipoFiscal**
- `id` (Int, PK) - Identificador único
- `nombre` (String, único) - Nombre del tipo
- `descripcion` (String, opcional) - Descripción
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete
- Relación: `fiscales Fiscal[]`

#### **Fiscal**
- `id` (Int, PK) - Identificador único
- `dni` (String, único) - DNI del fiscal
- `nombre`, `apellido` (String, opcional) - Datos personales
- `email` (String, opcional) - Email de contacto
- `celular` (String, opcional) - Teléfono
- `direccion` (String, opcional) - Dirección
- `tipoFiscalId` (Int, FK) - Referencia a TipoFiscal
- `mesaDondeVotaId` (Int, FK) - Mesa donde vota
- `colegioDondeVotaId` (Int, FK) - Colegio donde vota
- `disponibilidad` (String, opcional) - Horarios disponibles
- `notasInternas` (String, opcional) - Notas internas
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete
- Relaciones: `tipoFiscal TipoFiscal?`, `colegioDondeVota Colegio?`, `mesasAFiscalizar Mesa[]`, `mesaDondeVota Mesa?`, `fiscalColegios FiscalColegio[]`

#### **Mesa**
- `id` (Int, PK) - Identificador único
- `colegioId` (Int, FK) - Referencia a Colegio
- `numero` (String, único) - Número de mesa
- `totalVotantesPadron` (Int, opcional) - Total de votantes en el padrón
- `totalVotos11Hs` (Int, opcional) - Votos a las 11:00
- `totalVotos15Hs` (Int, opcional) - Votos a las 15:00
- `totalVotos18Hs` (Int, opcional) - Votos a las 18:00
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete
- Relaciones: `colegio Colegio?`, `fiscalesAFiscalizar Fiscal[]`, `fiscalesQueVotan Fiscal[]`

#### **FiscalColegio** (Tabla intermedia N:N)
- `id` (Int, PK) - Identificador único
- `fiscalId` (Int, FK) - Referencia a Fiscal
- `colegioId` (Int, FK) - Referencia a Colegio
- `createdAt`, `updatedAt` - Timestamps
- `enabled` (Boolean) - Soft delete
- Relaciones: `fiscal Fiscal`, `colegio Colegio`

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

## Sistema de Autenticación Actual

### Implementación Actual
El sistema actual implementa **solo autenticación básica** mediante `client_secret`:

```typescript
// src/middleware/auth.ts
const CLIENT_SECRET = 'TodosLosPerrosVanAlCielo';

export const authMiddleware = (req: Request, res: Response, next: NextFunction) => {
  const clientSecret = req.headers['client_secret'] as string;
  
  if (!clientSecret || clientSecret !== CLIENT_SECRET) {
    return res.status(401).json({
      error: 'Unauthorized',
      message: 'Header client_secret es requerido'
    });
  }
  
  return next();
};
```

### Limitaciones del Sistema Actual
- ❌ **Sin roles de usuario**: No hay diferenciación entre tipos de usuarios
- ❌ **Sin permisos**: Todas las operaciones están disponibles para cualquier usuario autenticado
- ❌ **Sin alcance territorial**: No hay restricciones basadas en zonas, colegios o mesas
- ❌ **Sin herencia de permisos**: No existe sistema de roles jerárquicos
- ❌ **Sin validación de autorización**: No se verifica si un usuario puede realizar una acción específica

---

## Diseño Conceptual: Roles del Sistema y Herencia

> **NOTA**: Esta sección describe el **diseño conceptual** que debería implementarse en el futuro. Actualmente NO está implementado.

El sistema debería definir roles con herencia para simplificar la administración de permisos. La herencia implica que un rol incluye las capacidades de los roles "inferiores". Adicionalmente, cada rol (salvo ADMIN) operaría dentro de un alcance territorial (scope) asignado.

- **Administrador (ADMIN)**
  - Acceso total a todo el sistema. Sin restricciones de alcance.
  - Incluye (por herencia) todos los demás roles.

- **Coordinador (COORDINADOR)**
  - Gestión organizativa: crear/editar/eliminar Zonas y Colegios; administrar vínculos Zona ↔ Colegio.
  - Puede crear y gestionar Fiscales de Zona.
  - Incluye permisos de "Fiscal de Zona".
  - Alcance: global para organización o restringido según configuración de scopes.

- **Fiscal de Zona (FISCAL_ZONA)**
  - Incluye permisos de "Fiscal General".
  - Sus permisos aplican solo a sus zona/s asignadas.

- **Fiscal General (FISCAL_GENERAL)**
  - Incluye permisos de "Fiscal de Mesa".
  - Sus permisos aplican solo a su/s colegio/s asignado/s.

- **Fiscal de Mesa (FISCAL_MESA)**
  - Sus permisos aplican solo a su/s mesa/s asignada/s.

Herencia formal que debería utilizarse:

- ADMIN → COORDINADOR → FISCAL_ZONA → FISCAL_GENERAL → FISCAL_MESA

---

## Alcance Territorial (Scope)

> **NOTA**: Esta sección describe el **diseño conceptual** que debería implementarse. Actualmente NO está implementado.

Para roles no-ADMIN, todas las acciones deberían estar sujetas a su alcance territorial:

- **FISCAL_ZONA**: Autorizado únicamente sobre recursos que pertenezcan a sus `zonaIds` asignadas (y, por extensión, a los colegios y mesas de dichas zonas).
- **FISCAL_GENERAL**: Autorizado únicamente sobre recursos que pertenezcan a sus `colegioIds` asignados (y, por extensión, a las mesas de dichos colegios).
- **FISCAL_MESA**: Autorizado únicamente sobre sus `mesaIds` asignadas.

La aplicación debería validar el alcance comparando el recurso objetivo (zona, colegio o mesa) con las listas asignadas al usuario.

### Estado Actual del Alcance Territorial
- ❌ **No implementado**: No hay validación de alcance territorial
- ❌ **Sin restricciones**: Cualquier usuario autenticado puede acceder a cualquier recurso
- ❌ **Sin asignaciones**: No existe sistema de asignación de usuarios a zonas/colegios/mesas

---

## Permisos por Tipo de Acción

> **NOTA**: Esta sección describe el **diseño conceptual** que debería implementarse. Actualmente NO está implementado.

Las siguientes reglas describen quién debería poder realizar cada acción. Siempre se aplicarían en este orden: 1) ADMIN sin restricciones; 2) verificación de rol (con herencia); 3) verificación de alcance territorial.

### Estado Actual de los Permisos
- ❌ **Sin restricciones**: Todas las operaciones CRUD están disponibles para cualquier usuario autenticado
- ❌ **Sin validación de roles**: No se verifica el tipo de usuario antes de permitir operaciones
- ❌ **Sin validación de alcance**: No se verifica si el usuario tiene permisos sobre el recurso específico

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

### Estado Actual de la Implementación

**✅ Implementado:**
- Jerarquía territorial Zonas → Colegios → Mesas correctamente modelada en PostgreSQL
- Relaciones entre entidades bien definidas y funcionales
- Operaciones CRUD completas para todas las entidades
- Autenticación básica por `client_secret`
- Sistema de soft delete (`enabled` field)
- Paginación y filtros en endpoints
- Logging estructurado
- Validaciones de datos básicas

**❌ No Implementado:**
- Sistema de roles y permisos
- Alcance territorial (scope)
- Herencia de permisos
- Validación de autorización por operación
- Sistema de usuarios con roles
- Carga de documentación fotográfica
- Canales de conversación
- Transiciones de rol (promociones)

### Diseño Conceptual vs Implementación

Este documento describe el **diseño conceptual** del sistema de permisos y roles que debería implementarse en el futuro. La implementación actual del backend **NO incluye** el sistema de roles, permisos ni alcance territorial descrito aquí.

**Próximos Pasos Recomendados:**
1. Implementar sistema de usuarios con roles
2. Agregar validación de permisos en middleware
3. Implementar alcance territorial
4. Agregar sistema de carga de documentación
5. Implementar canales de conversación


