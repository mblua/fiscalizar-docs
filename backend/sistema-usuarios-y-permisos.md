# Sistema de Usuarios y Permisos - Fiscalizar.org

## 📋 Resumen Ejecutivo

Se ha implementado un sistema completo de usuarios con roles y permisos granulares que permite controlar el acceso a los datos según el rol del usuario y su alcance territorial. El sistema incluye autenticación JWT, autorización basada en permisos, y filtrado automático de datos.

## 🎯 Roles Implementados

### 1. **ADMIN** - Administrador del Sistema
- **Permisos**: Acceso total a todo el sistema
- **Alcance**: Sin restricciones territoriales
- **Funcionalidades**:
  - Gestión completa de usuarios, roles y permisos
  - Acceso a todas las zonas, colegios, mesas y fiscales
  - Centro de control completo
  - Monitor general
  - Gestión de hitos
  - Mapa de colegios
  - Reportes y analytics

### 2. **COORDINADOR** - Coordinador General
- **Permisos**: Gestión de todas las entidades electorales
- **Alcance**: Sin restricciones territoriales
- **Funcionalidades**:
  - Gestión de zonas, colegios, mesas y fiscales
  - Centro de control completo
  - Monitor general
  - Gestión de hitos
  - Mapa de colegios
  - Reportes y analytics
- **Restricciones**: No puede gestionar usuarios ni roles

### 3. **FISCAL_ZONA** - Fiscal de Zona
- **Permisos**: Solo visualización de zonas y colegios
- **Alcance**: Solo su zona asignada
- **Funcionalidades**:
  - **Zonas**: Solo visualización de su zona asignada
  - **Colegios**: Solo visualización de colegios de su zona
  - **Mesas**: Gestión completa de mesas de su zona
  - **Fiscales**: Gestión completa de fiscales de su zona
  - **Centro de Control**: Solo datos de su zona
  - **Monitor General**: Acceso completo
  - **Hitos**: Gestión completa
  - **Mapa de Colegios**: Solo su zona

### 4. **FISCAL_GENERAL** - Fiscal General
- **Permisos**: Solo visualización de colegios
- **Alcance**: Solo su colegio asignado
- **Funcionalidades**:
  - **Colegios**: Solo visualización de su colegio asignado
  - **Mesas**: Gestión completa de mesas de su colegio
  - **Fiscales**: Gestión completa de fiscales de su colegio
  - **Centro de Control**: Solo datos de su colegio
  - **Monitor General**: Acceso completo
- **Restricciones**: No puede ver zonas, hitos ni mapa

### 5. **FISCAL_MESA** - Fiscal de Mesa
- **Permisos**: Solo visualización
- **Alcance**: Solo su colegio asignado
- **Funcionalidades**:
  - **Colegios**: Solo visualización de su colegio asignado
  - **Mesas**: Solo visualización de mesas de su colegio
  - **Fiscales**: Solo visualización de fiscales de su colegio
  - **Centro de Control**: Solo visualización de su colegio
  - **Monitor General**: Acceso completo
- **Restricciones**: No puede crear, editar ni eliminar entidades

## 🔐 Sistema de Autenticación

### Endpoints de Autenticación

#### **POST /api/auth/login**
```json
{
  "username": "admin",
  "password": "admin123"
}
```

**Respuesta exitosa:**
```json
{
  "message": "Login exitoso",
  "user": {
    "id": 1,
    "username": "admin",
    "email": "admin@fiscalizar.org",
    "nombre": "Administrador",
    "apellido": "Sistema",
    "roles": ["ADMIN"],
    "permisos": ["zonas.create", "zonas.read", "zonas.update", "..."],
    "alcance": {
      "zonas": [],
      "colegios": [],
      "mesas": []
    }
  },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### **POST /api/auth/register**
```json
{
  "username": "nuevo_usuario",
  "email": "usuario@ejemplo.com",
  "password": "password123",
  "nombre": "Juan",
  "apellido": "Pérez"
}
```

#### **GET /api/auth/profile**
Requiere autenticación JWT.

#### **GET /api/auth/verify**
Verifica si el token es válido.

#### **POST /api/auth/logout**
Cierra la sesión del usuario.

### Headers de Autenticación

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## 🛡️ Sistema de Autorización

### Permisos Implementados

#### **Zonas**
- `zonas.create` - Crear zonas
- `zonas.read` - Ver zonas
- `zonas.update` - Editar zonas
- `zonas.delete` - Eliminar zonas
- `zonas.manage` - Gestión completa de zonas

#### **Colegios**
- `colegios.create` - Crear colegios
- `colegios.read` - Ver colegios
- `colegios.update` - Editar colegios
- `colegios.delete` - Eliminar colegios
- `colegios.manage` - Gestión completa de colegios

#### **Mesas**
- `mesas.create` - Crear mesas
- `mesas.read` - Ver mesas
- `mesas.update` - Editar mesas
- `mesas.delete` - Eliminar mesas
- `mesas.manage` - Gestión completa de mesas

#### **Fiscales**
- `fiscales.create` - Crear fiscales
- `fiscales.read` - Ver fiscales
- `fiscales.update` - Editar fiscales
- `fiscales.delete` - Eliminar fiscales
- `fiscales.manage` - Gestión completa de fiscales
- `fiscales.promote` - Promover fiscales

#### **Usuarios**
- `usuarios.create` - Crear usuarios
- `usuarios.read` - Ver usuarios
- `usuarios.update` - Editar usuarios
- `usuarios.delete` - Eliminar usuarios
- `usuarios.manage` - Gestión completa de usuarios

#### **Roles**
- `roles.assign` - Asignar roles
- `roles.manage` - Gestión completa de roles

#### **Centro de Control**
- `centro_control.read` - Ver centro de control
- `centro_control.manage` - Gestión del centro de control

#### **Monitor General**
- `monitor.read` - Ver monitor general
- `monitor.manage` - Gestión del monitor general

#### **Hitos**
- `hitos.create` - Crear hitos
- `hitos.read` - Ver hitos
- `hitos.update` - Editar hitos
- `hitos.delete` - Eliminar hitos
- `hitos.manage` - Gestión completa de hitos

#### **Mapa de Colegios**
- `mapa.read` - Ver mapa de colegios
- `mapa.manage` - Gestión del mapa de colegios

#### **Reportes**
- `reportes.read` - Ver reportes
- `reportes.manage` - Gestión de reportes

## 🗺️ Alcance Territorial

### Asignación de Alcance

Los usuarios pueden ser asignados a:
- **Zonas**: Para FISCAL_ZONA
- **Colegios**: Para FISCAL_GENERAL y FISCAL_MESA
- **Mesas**: Para FISCAL_MESA

### Filtrado Automático

El sistema filtra automáticamente los datos según el rol y alcance del usuario:

#### **FISCAL_ZONA**
- Solo ve zonas asignadas
- Solo ve colegios de sus zonas
- Solo ve mesas de sus zonas
- Solo ve fiscales relacionados con sus zonas

#### **FISCAL_GENERAL**
- Solo ve colegios asignados
- Solo ve mesas de sus colegios
- Solo ve fiscales relacionados con sus colegios

#### **FISCAL_MESA**
- Solo ve colegios asignados
- Solo ve mesas asignadas
- Solo ve fiscales relacionados con sus colegios

## 📊 Ejemplos de Uso

### 1. Login y Obtención de Token

```javascript
// Login
const response = await fetch('/api/auth/login', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    username: 'admin',
    password: 'admin123'
  })
});

const { user, token } = await response.json();

// Guardar token para futuras requests
localStorage.setItem('authToken', token);
```

### 2. Request Autenticada

```javascript
// Obtener fiscales con autenticación
const response = await fetch('/api/fiscales', {
  headers: {
    'Authorization': `Bearer ${localStorage.getItem('authToken')}`
  }
});

const fiscales = await response.json();
```

### 3. Verificación de Permisos en Frontend

```javascript
// Verificar si el usuario puede crear fiscales
const canCreateFiscales = user.permisos.includes('fiscales.create');

// Verificar si el usuario puede gestionar usuarios
const canManageUsers = user.permisos.includes('usuarios.manage');

// Verificar rol específico
const isAdmin = user.roles.includes('ADMIN');
const isFiscalZona = user.roles.includes('FISCAL_ZONA');
```

### 4. Filtrado de Datos en Frontend

```javascript
// Los datos ya vienen filtrados del backend según el rol
// No es necesario filtrar en el frontend
const fiscales = await response.json();
// fiscales.data ya contiene solo los fiscales que el usuario puede ver
```

## 🔧 Configuración del Frontend

### 1. Interceptor de Requests

```javascript
// Axios interceptor para agregar token automáticamente
axios.interceptors.request.use((config) => {
  const token = localStorage.getItem('authToken');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

### 2. Manejo de Errores de Autenticación

```javascript
// Interceptor de respuestas para manejar errores 401
axios.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Token expirado o inválido
      localStorage.removeItem('authToken');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);
```

### 3. Context de Autenticación

```javascript
// React Context para manejar el estado de autenticación
const AuthContext = createContext({
  user: null,
  token: null,
  login: () => {},
  logout: () => {},
  isAuthenticated: false
});

// Hook personalizado
const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
};
```

### 4. Componente de Protección de Rutas

```javascript
// Componente para proteger rutas según permisos
const ProtectedRoute = ({ children, requiredPermission, requiredRole }) => {
  const { user } = useAuth();
  
  if (!user) {
    return <Navigate to="/login" />;
  }
  
  if (requiredPermission && !user.permisos.includes(requiredPermission)) {
    return <div>No tienes permisos para acceder a esta página</div>;
  }
  
  if (requiredRole && !user.roles.includes(requiredRole)) {
    return <div>No tienes el rol necesario para acceder a esta página</div>;
  }
  
  return children;
};
```

## 📝 Códigos de Error

### Autenticación
- **401 Unauthorized**: Token inválido o expirado
- **403 Forbidden**: Sin permisos para la acción
- **400 Bad Request**: Datos de login inválidos
- **409 Conflict**: Usuario o email ya existe

### Autorización
- **403 Forbidden**: Sin permisos para el recurso
- **403 Forbidden**: Sin alcance territorial sobre la entidad

## 🚀 Usuario Administrador por Defecto

Se ha creado un usuario administrador con las siguientes credenciales:

- **Username**: `admin`
- **Password**: `admin123`
- **Email**: `admin@fiscalizar.org`
- **Rol**: `ADMIN`
- **Permisos**: Todos los permisos del sistema

## 🔄 Flujo de Trabajo Recomendado

### 1. **Inicialización**
1. Usuario hace login con credenciales
2. Sistema devuelve token JWT y datos del usuario
3. Frontend guarda token y datos del usuario

### 2. **Navegación**
1. Frontend verifica permisos antes de mostrar opciones
2. Requests incluyen token JWT automáticamente
3. Backend filtra datos según rol y alcance

### 3. **Gestión de Usuarios**
1. Solo ADMIN puede crear usuarios
2. Asignar roles según necesidades
3. Asignar alcance territorial según rol

### 4. **Monitoreo**
1. Sistema registra todas las acciones en auditoría
2. Logs detallados de accesos y permisos
3. Métricas de uso por rol

## 📈 Beneficios del Sistema

✅ **Seguridad**: Autenticación JWT y autorización granular
✅ **Escalabilidad**: Roles jerárquicos con herencia de permisos
✅ **Flexibilidad**: Alcance territorial configurable
✅ **Auditoría**: Registro completo de accesos y acciones
✅ **Filtrado Automático**: Datos filtrados según rol y alcance
✅ **Compatibilidad**: Mantiene sistema legacy con client_secret
✅ **Usabilidad**: API simple y documentada para frontend

## 🔧 Próximos Pasos

1. **Implementar en Frontend**: Usar la API de autenticación
2. **Gestión de Usuarios**: Crear interfaz para administrar usuarios
3. **Asignación de Alcance**: Herramientas para asignar territorios
4. **Dashboard por Rol**: Interfaces específicas según rol
5. **Reportes de Auditoría**: Visualización de accesos y acciones

---

**Nota**: Este sistema está completamente implementado y listo para ser utilizado por el frontend. Todos los endpoints están documentados y funcionando.
