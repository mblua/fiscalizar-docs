# 🔐 Implementación de Autenticación JWT - Frontend

## 📋 Resumen del Sistema

El backend de Fiscalizar.org implementa un sistema completo de autenticación JWT con roles y permisos granulares. Este documento explica cómo implementar la autenticación en el frontend.

## 🏗️ Arquitectura del Sistema

### Backend (Ya implementado)
- **Autenticación**: JWT tokens con expiración
- **Autorización**: Roles y permisos granulares
- **Filtrado de datos**: Automático según rol del usuario
- **Auditoría**: Log de todas las acciones

### Frontend (A implementar)
- **Login**: Formulario de autenticación
- **Gestión de tokens**: Almacenamiento y renovación
- **Interceptores HTTP**: Inyección automática de tokens
- **Manejo de errores**: 401 (no autenticado), 403 (sin permisos)

## 🚀 Implementación Paso a Paso

### 1. Configuración Base

#### Variables de Entorno
```env
# .env
VITE_API_BASE_URL=http://localhost:3000/api
VITE_APP_NAME=Fiscalizar.org
```

#### Configuración de Axios
```typescript
// src/lib/api.ts
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:3000/api';

export const api = axios.create({
  baseURL: API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Interceptor para agregar token automáticamente
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('auth_token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Interceptor para manejar respuestas
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Token expirado o inválido
      localStorage.removeItem('auth_token');
      localStorage.removeItem('user_data');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);
```

### 2. Tipos TypeScript

```typescript
// src/types/auth.ts
export interface LoginRequest {
  username: string;
  password: string;
}

export interface LoginResponse {
  success: boolean;
  token: string;
  user: {
    id: number;
    username: string;
    email: string;
    nombre?: string;
    apellido?: string;
    roles: string[];
    permisos: string[];
    alcance: {
      zonas: number[];
      colegios: number[];
      mesas: number[];
    };
  };
  expiresIn: number;
}

export interface User {
  id: number;
  username: string;
  email: string;
  nombre?: string;
  apellido?: string;
  roles: string[];
  permisos: string[];
  alcance: {
    zonas: number[];
    colegios: number[];
    mesas: number[];
  };
}

export interface AuthState {
  isAuthenticated: boolean;
  user: User | null;
  token: string | null;
  loading: boolean;
  error: string | null;
}
```

### 3. Servicios de Autenticación

```typescript
// src/services/authService.ts
import { api } from '../lib/api';
import { LoginRequest, LoginResponse, User } from '../types/auth';

export class AuthService {
  /**
   * Login de usuario
   */
  static async login(credentials: LoginRequest): Promise<LoginResponse> {
    try {
      const response = await api.post<LoginResponse>('/auth/login', credentials);
      
      if (response.data.success && response.data.token) {
        // Guardar token y datos del usuario
        localStorage.setItem('auth_token', response.data.token);
        localStorage.setItem('user_data', JSON.stringify(response.data.user));
        
        return response.data;
      }
      
      throw new Error('Login fallido');
    } catch (error: any) {
      throw new Error(error.response?.data?.message || 'Error de conexión');
    }
  }

  /**
   * Logout de usuario
   */
  static async logout(): Promise<void> {
    try {
      await api.post('/auth/logout');
    } catch (error) {
      // Ignorar errores de logout
    } finally {
      // Limpiar datos locales
      localStorage.removeItem('auth_token');
      localStorage.removeItem('user_data');
    }
  }

  /**
   * Obtener perfil del usuario
   */
  static async getProfile(): Promise<User> {
    const response = await api.get<User>('/auth/profile');
    return response.data;
  }

  /**
   * Verificar si el usuario está autenticado
   */
  static isAuthenticated(): boolean {
    const token = localStorage.getItem('auth_token');
    return !!token;
  }

  /**
   * Obtener datos del usuario desde localStorage
   */
  static getCurrentUser(): User | null {
    const userData = localStorage.getItem('user_data');
    return userData ? JSON.parse(userData) : null;
  }

  /**
   * Verificar si el usuario tiene un permiso específico
   */
  static hasPermission(permission: string): boolean {
    const user = this.getCurrentUser();
    return user?.permisos?.includes(permission) || false;
  }

  /**
   * Verificar si el usuario tiene un rol específico
   */
  static hasRole(role: string): boolean {
    const user = this.getCurrentUser();
    return user?.roles?.includes(role) || false;
  }

  /**
   * Verificar si el usuario puede acceder a una entidad específica
   */
  static canAccessEntity(type: 'zona' | 'colegio' | 'mesa', entityId: number): boolean {
    const user = this.getCurrentUser();
    if (!user) return false;

    // ADMIN y COORDINADOR tienen acceso a todo
    if (user.roles.includes('ADMIN') || user.roles.includes('COORDINADOR')) {
      return true;
    }

    // Verificar alcance específico
    switch (type) {
      case 'zona':
        return user.alcance.zonas.includes(entityId);
      case 'colegio':
        return user.alcance.colegios.includes(entityId);
      case 'mesa':
        return user.alcance.mesas.includes(entityId);
      default:
        return false;
    }
  }
}
```

### 4. Store de Autenticación (Zustand)

```typescript
// src/stores/authStore.ts
import { create } from 'zustand';
import { AuthService } from '../services/authService';
import { LoginRequest, User, AuthState } from '../types/auth';

interface AuthStore extends AuthState {
  login: (credentials: LoginRequest) => Promise<void>;
  logout: () => Promise<void>;
  checkAuth: () => void;
  clearError: () => void;
}

export const useAuthStore = create<AuthStore>((set, get) => ({
  isAuthenticated: false,
  user: null,
  token: null,
  loading: false,
  error: null,

  login: async (credentials: LoginRequest) => {
    set({ loading: true, error: null });
    
    try {
      const response = await AuthService.login(credentials);
      
      set({
        isAuthenticated: true,
        user: response.user,
        token: response.token,
        loading: false,
        error: null,
      });
    } catch (error: any) {
      set({
        isAuthenticated: false,
        user: null,
        token: null,
        loading: false,
        error: error.message,
      });
      throw error;
    }
  },

  logout: async () => {
    set({ loading: true });
    
    try {
      await AuthService.logout();
    } catch (error) {
      // Ignorar errores
    } finally {
      set({
        isAuthenticated: false,
        user: null,
        token: null,
        loading: false,
        error: null,
      });
    }
  },

  checkAuth: () => {
    const isAuth = AuthService.isAuthenticated();
    const user = AuthService.getCurrentUser();
    const token = localStorage.getItem('auth_token');

    set({
      isAuthenticated: isAuth,
      user,
      token,
    });
  },

  clearError: () => {
    set({ error: null });
  },
}));
```

### 5. Componente de Login

```tsx
// src/pages/LoginPage.tsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { useAuthStore } from '../stores/authStore';
import { Button } from '../components/ui/button';
import { Input } from '../components/ui/input';
import { Label } from '../components/ui/label';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '../components/ui/card';
import { Alert, AlertDescription } from '../components/ui/alert';

export default function LoginPage() {
  const [formData, setFormData] = useState({
    username: '',
    password: '',
  });
  
  const { login, loading, error, clearError } = useAuthStore();
  const navigate = useNavigate();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    clearError();

    try {
      await login(formData);
      navigate('/dashboard');
    } catch (error) {
      // Error manejado por el store
    }
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value,
    }));
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
      <Card className="w-full max-w-md">
        <CardHeader className="space-y-1">
          <CardTitle className="text-2xl font-bold text-center">
            Fiscalizar.org
          </CardTitle>
          <CardDescription className="text-center">
            Ingresa tus credenciales para acceder al sistema
          </CardDescription>
        </CardHeader>
        
        <CardContent>
          <form onSubmit={handleSubmit} className="space-y-4">
            {error && (
              <Alert variant="destructive">
                <AlertDescription>{error}</AlertDescription>
              </Alert>
            )}
            
            <div className="space-y-2">
              <Label htmlFor="username">Usuario</Label>
              <Input
                id="username"
                name="username"
                type="text"
                value={formData.username}
                onChange={handleChange}
                required
                disabled={loading}
                placeholder="Ingresa tu usuario"
              />
            </div>
            
            <div className="space-y-2">
              <Label htmlFor="password">Contraseña</Label>
              <Input
                id="password"
                name="password"
                type="password"
                value={formData.password}
                onChange={handleChange}
                required
                disabled={loading}
                placeholder="Ingresa tu contraseña"
              />
            </div>
            
            <Button 
              type="submit" 
              className="w-full" 
              disabled={loading}
            >
              {loading ? 'Iniciando sesión...' : 'Iniciar Sesión'}
            </Button>
          </form>
        </CardContent>
      </Card>
    </div>
  );
}
```

### 6. Hook de Autenticación

```typescript
// src/hooks/useAuth.ts
import { useEffect } from 'react';
import { useAuthStore } from '../stores/authStore';

export function useAuth() {
  const { isAuthenticated, user, loading, error, checkAuth, logout } = useAuthStore();

  useEffect(() => {
    checkAuth();
  }, [checkAuth]);

  return {
    isAuthenticated,
    user,
    loading,
    error,
    logout,
  };
}
```

### 7. Hook de Permisos

```typescript
// src/hooks/usePermissions.ts
import { AuthService } from '../services/authService';

export function usePermissions() {
  const hasPermission = (permission: string): boolean => {
    return AuthService.hasPermission(permission);
  };

  const hasRole = (role: string): boolean => {
    return AuthService.hasRole(role);
  };

  const canAccessEntity = (type: 'zona' | 'colegio' | 'mesa', entityId: number): boolean => {
    return AuthService.canAccessEntity(type, entityId);
  };

  const canRead = (resource: string): boolean => {
    return hasPermission(`${resource}.read`);
  };

  const canCreate = (resource: string): boolean => {
    return hasPermission(`${resource}.create`);
  };

  const canUpdate = (resource: string): boolean => {
    return hasPermission(`${resource}.update`);
  };

  const canDelete = (resource: string): boolean => {
    return hasPermission(`${resource}.delete`);
  };

  const canManage = (resource: string): boolean => {
    return hasPermission(`${resource}.manage`);
  };

  return {
    hasPermission,
    hasRole,
    canAccessEntity,
    canRead,
    canCreate,
    canUpdate,
    canDelete,
    canManage,
  };
}
```

### 8. Componente de Protección de Rutas

```tsx
// src/components/ProtectedRoute.tsx
import { ReactNode } from 'react';
import { Navigate } from 'react-router-dom';
import { useAuth } from '../hooks/useAuth';

interface ProtectedRouteProps {
  children: ReactNode;
  requiredPermission?: string;
  requiredRole?: string;
}

export function ProtectedRoute({ 
  children, 
  requiredPermission, 
  requiredRole 
}: ProtectedRouteProps) {
  const { isAuthenticated, user, loading } = useAuth();

  if (loading) {
    return <div>Cargando...</div>;
  }

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  if (requiredRole && !user?.roles.includes(requiredRole)) {
    return <Navigate to="/unauthorized" replace />;
  }

  if (requiredPermission && !user?.permisos.includes(requiredPermission)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return <>{children}</>;
}
```

### 9. Uso en Componentes

```tsx
// src/components/FiscalesList.tsx
import { usePermissions } from '../hooks/usePermissions';
import { Button } from './ui/button';

export function FiscalesList() {
  const { canRead, canCreate, canUpdate, canDelete } = usePermissions();

  if (!canRead('fiscales')) {
    return <div>No tienes permisos para ver fiscales</div>;
  }

  return (
    <div>
      <h1>Lista de Fiscales</h1>
      
      {canCreate('fiscales') && (
        <Button>Crear Fiscal</Button>
      )}
      
      {/* Lista de fiscales */}
      <div>
        {/* Renderizar fiscales */}
      </div>
    </div>
  );
}
```

### 10. Configuración de Rutas

```tsx
// src/App.tsx
import { BrowserRouter, Routes, Route, Navigate } from 'react-router-dom';
import { ProtectedRoute } from './components/ProtectedRoute';
import { useAuth } from './hooks/useAuth';
import LoginPage from './pages/LoginPage';
import Dashboard from './pages/Dashboard';
import FiscalesPage from './pages/FiscalesPage';
import ZonasPage from './pages/ZonasPage';

function App() {
  const { isAuthenticated } = useAuth();

  return (
    <BrowserRouter>
      <Routes>
        <Route 
          path="/login" 
          element={isAuthenticated ? <Navigate to="/dashboard" /> : <LoginPage />} 
        />
        
        <Route 
          path="/dashboard" 
          element={
            <ProtectedRoute>
              <Dashboard />
            </ProtectedRoute>
          } 
        />
        
        <Route 
          path="/fiscales" 
          element={
            <ProtectedRoute requiredPermission="fiscales.read">
              <FiscalesPage />
            </ProtectedRoute>
          } 
        />
        
        <Route 
          path="/zonas" 
          element={
            <ProtectedRoute requiredPermission="zonas.read">
              <ZonasPage />
            </ProtectedRoute>
          } 
        />
        
        <Route path="/" element={<Navigate to="/dashboard" />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

## 🔧 Configuración de Desarrollo

### 1. Instalación de Dependencias

```bash
npm install axios zustand react-router-dom
npm install -D @types/node
```

### 2. Estructura de Archivos

```
src/
├── components/
│   ├── ui/           # Componentes base (shadcn/ui)
│   └── ProtectedRoute.tsx
├── hooks/
│   ├── useAuth.ts
│   └── usePermissions.ts
├── lib/
│   └── api.ts
├── pages/
│   ├── LoginPage.tsx
│   ├── Dashboard.tsx
│   └── ...
├── services/
│   └── authService.ts
├── stores/
│   └── authStore.ts
├── types/
│   └── auth.ts
└── App.tsx
```

## 🚨 Manejo de Errores

### Errores Comunes

1. **401 Unauthorized**: Token expirado o inválido
   - **Solución**: Redirigir al login automáticamente

2. **403 Forbidden**: Sin permisos para la acción
   - **Solución**: Mostrar mensaje de "Sin permisos"

3. **Network Error**: Problemas de conexión
   - **Solución**: Mostrar mensaje de "Error de conexión"

### Implementación de Manejo de Errores

```typescript
// src/utils/errorHandler.ts
export function handleApiError(error: any): string {
  if (error.response) {
    switch (error.response.status) {
      case 401:
        return 'Sesión expirada. Por favor, inicia sesión nuevamente.';
      case 403:
        return 'No tienes permisos para realizar esta acción.';
      case 404:
        return 'Recurso no encontrado.';
      case 500:
        return 'Error interno del servidor.';
      default:
        return error.response.data?.message || 'Error desconocido.';
    }
  } else if (error.request) {
    return 'Error de conexión. Verifica tu conexión a internet.';
  } else {
    return 'Error inesperado.';
  }
}
```

## 📱 Usuarios de Prueba

### Usuario Administrador
- **Username**: `admin`
- **Password**: `admin123`
- **Roles**: `ADMIN`
- **Permisos**: Todos los permisos

### Usuario Coordinador
- **Username**: `coordinador`
- **Password**: `coordinador123`
- **Roles**: `COORDINADOR`
- **Permisos**: Acceso completo a datos

### Usuario Fiscal Zona
- **Username**: `fiscal_zona`
- **Password**: `fiscal_zona123`
- **Roles**: `FISCAL_ZONA`
- **Permisos**: Acceso limitado a su zona

## 🎯 Endpoints de la API

### Autenticación
- `POST /api/auth/login` - Login
- `POST /api/auth/logout` - Logout
- `GET /api/auth/profile` - Perfil del usuario
- `POST /api/auth/verify` - Verificar token

### Recursos (Todos requieren Bearer token)
- `GET /api/zonas` - Listar zonas
- `GET /api/colegios` - Listar colegios
- `GET /api/mesas` - Listar mesas
- `GET /api/fiscales` - Listar fiscales
- `GET /api/hitos` - Listar hitos

## 🔐 Seguridad

### Mejores Prácticas

1. **Nunca almacenar tokens en sessionStorage**
2. **Implementar renovación automática de tokens**
3. **Validar permisos en el frontend Y backend**
4. **Usar HTTPS en producción**
5. **Implementar logout automático por inactividad**

### Implementación de Renovación de Tokens

```typescript
// src/services/tokenService.ts
export class TokenService {
  private static readonly TOKEN_REFRESH_THRESHOLD = 5 * 60 * 1000; // 5 minutos

  static shouldRefreshToken(): boolean {
    const token = localStorage.getItem('auth_token');
    if (!token) return false;

    try {
      const payload = JSON.parse(atob(token.split('.')[1]));
      const expirationTime = payload.exp * 1000;
      const currentTime = Date.now();
      
      return (expirationTime - currentTime) < this.TOKEN_REFRESH_THRESHOLD;
    } catch {
      return true;
    }
  }

  static async refreshToken(): Promise<string | null> {
    try {
      const response = await api.post('/auth/refresh');
      const newToken = response.data.token;
      localStorage.setItem('auth_token', newToken);
      return newToken;
    } catch {
      return null;
    }
  }
}
```

## 📚 Recursos Adicionales

- **Documentación del Backend**: `fiscalizar-docs/backend/sistema-usuarios-y-permisos.md`
- **API Base URL**: `http://localhost:3000/api`
- **Prisma Studio**: `http://localhost:5555` (para ver datos)

## ✅ Checklist de Implementación

- [ ] Configurar variables de entorno
- [ ] Implementar configuración de Axios
- [ ] Crear tipos TypeScript
- [ ] Implementar AuthService
- [ ] Crear store de Zustand
- [ ] Implementar componente de Login
- [ ] Crear hooks de autenticación y permisos
- [ ] Implementar ProtectedRoute
- [ ] Configurar rutas de la aplicación
- [ ] Implementar manejo de errores
- [ ] Probar con usuarios de prueba
- [ ] Implementar renovación de tokens
- [ ] Configurar logout automático

---

**¡El sistema está listo para ser implementado!** 🚀
