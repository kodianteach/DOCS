# 📋 Estándares de Código - SSP Platform

Guía de estándares de código, buenas prácticas y principios de Clean Code para el proyecto SSP Platform.

---

## 📋 Tabla de Contenidos

- [Principios Generales](#-principios-generales)
  - [Idioma del Código](#-idioma-del-código-obligatorio)
  - [SOLID](#solid)
- [Convenciones de Nomenclatura](#-convenciones-de-nomenclatura)
- [Estructura de Archivos](#-estructura-de-archivos)
  - [Organización de la Capa de Dominio](#-organización-de-la-capa-de-dominio-obligatorio)
- [TypeScript](#-typescript)
- [Angular](#-angular)
- [Estilos CSS](#-estilos-css)
- [Soporte de Temas (Light/Dark)](#-soporte-de-temas-lightdark)
- [Validaciones de Formularios](#-validaciones-de-formularios)
- [Internacionalización (i18n)](#-internacionalización-i18n)
- [Sistema de Notificaciones Global](#-sistema-de-notificaciones-global)
- [DataTable con API Paginada](#-datatable-con-api-paginada-obligatorio)
- [Testing](#-testing)
- [Git](#-git)
- [Documentación](#-documentación)

---

## 🎯 Principios Generales

### 🌐 Idioma del Código (OBLIGATORIO)

**OBLIGATORIO** que todo el código interno de la aplicación esté escrito en **inglés**. Esto incluye:

| Elemento | Idioma | Ejemplo |
|----------|--------|---------|
| **Nombres de variables** | Inglés | `firstName`, `isLoading`, `userList` |
| **Nombres de funciones/métodos** | Inglés | `getUserById()`, `calculateTotal()` |
| **Nombres de clases/interfaces** | Inglés | `UserService`, `AuthGateway`, `Patient` |
| **Nombres de archivos** | Inglés | `user.service.ts`, `auth.gateway.ts` |
| **Nombres de carpetas** | Inglés | `use-cases`, `adapters`, `models` |
| **Propiedades de objetos** | Inglés | `{ email, phoneNumber, createdAt }` |
| **Constantes y enums** | Inglés | `MAX_RETRIES`, `UserStatus.ACTIVE` |
| **Comentarios de código** | Inglés | `// Validate user credentials` |
| **Mensajes de log** | Inglés | `log.info("User created successfully")` |
| **Documentación técnica (JSDoc)** | Inglés | `@param credentials - User login credentials` |

#### Excepciones (Textos visibles al usuario)

Los **únicos textos en español** son aquellos que el usuario final verá en la interfaz:

| Elemento | Idioma | Ubicación |
|----------|--------|-----------|
| Textos de UI | Español | Archivos `i18n/*.json` |
| Mensajes de error para usuario | Español | Archivos `i18n/*.json` |
| Labels y placeholders | Español | Archivos `i18n/*.json` |
| Tooltips y ayudas | Español | Archivos `i18n/*.json` |

```typescript
// ❌ PROHIBIDO: Código en español
const nombreCompleto = 'Juan Pérez';
const estaActivo = true;
function obtenerUsuarioPorId(id: string) { }
class ServicioUsuario { }

// ✅ CORRECTO: Código en inglés
const fullName = 'Juan Pérez';
const isActive = true;
function getUserById(id: string) { }
class UserService { }

// ✅ CORRECTO: Textos de UI en español (via i18n)
// es.json: { "app.user.fullName": "Nombre Completo" }
<label>{{ 'app.user.fullName' | translate }}</label>
```

#### Beneficios del Código en Inglés

| Beneficio | Descripción |
|-----------|-------------|
| **Estándar global** | El inglés es el idioma estándar de la industria del software |
| **Colaboración** | Facilita trabajo con equipos internacionales |
| **Documentación** | Compatible con documentación de librerías y frameworks |
| **Consistencia** | Evita mezcla de idiomas en el código |
| **Profesionalismo** | Código más profesional y mantenible |

---

### SOLID

| Principio | Descripción | Aplicación |
|-----------|-------------|------------|
| **S** - Single Responsibility | Una clase debe tener una única responsabilidad | Cada componente, servicio y use case tiene una única razón para cambiar |
| **O** - Open/Closed | Abierto para extensión, cerrado para modificación | Usar interfaces y herencia para extender funcionalidad |
| **L** - Liskov Substitution | Los objetos derivados deben poder sustituir a sus bases | Los adapters pueden intercambiarse sin afectar el dominio |
| **I** - Interface Segregation | Interfaces específicas mejor que una general | Gateways con métodos específicos por dominio |
| **D** - Dependency Inversion | Depender de abstracciones, no implementaciones | Use cases dependen de gateways (interfaces), no adapters |

### Clean Code

```typescript
// ❌ MAL: Nombres poco descriptivos
const d = new Date();
const u = this.getU();

// ✅ BIEN: Nombres descriptivos
const createdAt = new Date();
const currentUser = this.getCurrentUser();
```

```typescript
// ❌ MAL: Funciones con muchos parámetros
function createUser(name, email, age, role, status, avatar, phone) { }

// ✅ BIEN: Usar objetos de configuración
interface CreateUserParams {
  name: string;
  email: string;
  age: number;
  role: UserRole;
  status?: UserStatus;
  avatar?: string;
  phone?: string;
}

function createUser(params: CreateUserParams): User { }
```

### DRY (Don't Repeat Yourself)

```typescript
// ❌ MAL: Código duplicado
if (user.role === 'admin') { /* lógica admin */ }
// En otro archivo...
if (user.role === 'admin') { /* misma lógica */ }

// ✅ BIEN: Extraer a función reutilizable
// domain/models/user.model.ts
export function isAdmin(user: User): boolean {
  return user.role === 'admin';
}

// Uso
if (isAdmin(user)) { /* lógica admin */ }
```

### KISS (Keep It Simple, Stupid)

```typescript
// ❌ MAL: Complejidad innecesaria
const isActive = user.status === 'active' ? true : false;

// ✅ BIEN: Simple y directo
const isActive = user.status === 'active';
```

---

## 📝 Convenciones de Nomenclatura

### General

| Tipo | Convención | Ejemplo |
|------|------------|---------|
| Clases | PascalCase | `UserService`, `AuthAdapter` |
| Interfaces | PascalCase | `User`, `AuthCredentials` |
| Funciones/Métodos | camelCase | `getUserById`, `onSubmit` |
| Variables | camelCase | `currentUser`, `isLoading` |
| Constantes | UPPER_SNAKE_CASE | `API_URL`, `MAX_RETRIES` |
| Archivos | kebab-case | `user.service.ts`, `auth.guard.ts` |
| Carpetas | kebab-case | `use-cases`, `form-field` |

### ⚠️ NO Usar Archivos Barrel (index.ts)

**PROHIBIDO** crear archivos `index.ts` para re-exportar módulos. Esta práctica "ensucia" el proyecto y causa problemas.

```typescript
// ❌ PROHIBIDO: Crear archivos index.ts (barrel exports)
// models/index.ts
export * from './user.model';
export * from './auth.model';

// ❌ PROHIBIDO: Importar desde carpeta (usa index.ts implícitamente)
import { User, AuthCredentials } from '../models';
import { AuthGateway } from '../gateways';

// ✅ CORRECTO: Importar usando path aliases
import { User } from '@domain/models/user.model';
import { AuthCredentials } from '@domain/models/auth.model';
import { AuthGateway } from '@domain/gateways/auth.gateway';
```

#### Razones para NO usar index.ts:

| Problema | Descripción |
|----------|-------------|
| **Circular dependencies** | Los barrels pueden crear dependencias circulares difíciles de detectar |
| **Bundle size** | Puede incluir código no utilizado en el bundle final |
| **Debugging** | Dificulta rastrear el origen de las importaciones |
| **Refactoring** | Cambios en barrels afectan múltiples archivos |
| **IDE performance** | Reduce rendimiento del autocompletado en proyectos grandes |
| **Claridad** | Oculta la ubicación real de los módulos |

### 🔗 Path Aliases (OBLIGATORIO)

**OBLIGATORIO** usar path aliases en lugar de rutas relativas (`../`, `../../`). Los path aliases mejoran la legibilidad y mantenibilidad del código.

#### Configuración en tsconfig.json:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@domain/*": ["src/app/domain/*"],
      "@infrastructure/*": ["src/app/infrastructure/*"],
      "@presentation/*": ["src/app/presentation/*"],
      "@env/*": ["src/environments/*"]
    }
  }
}
```

#### Uso correcto de Path Aliases:

```typescript
// ❌ PROHIBIDO: Usar rutas relativas
import { User } from '../../../domain/models/user.model';
import { AuthGateway } from '../../domain/gateways/auth.gateway';
import { ButtonComponent } from '../../atoms/button/button.component';
import { environment } from '../../../../environments/environment';

// ✅ CORRECTO: Usar path aliases
import { User } from '@domain/models/user.model';
import { AuthGateway } from '@domain/gateways/auth.gateway';
import { ButtonComponent } from '@presentation/ui/atoms/button/button.component';
import { environment } from '@env/environment';
```

#### Aliases disponibles:

| Alias | Ruta | Uso |
|-------|------|-----|
| `@domain/*` | `src/app/domain/*` | Modelos, gateways, use cases |
| `@infrastructure/*` | `src/app/infrastructure/*` | Adapters, interceptors, guards |
| `@presentation/*` | `src/app/presentation/*` | Componentes UI, pages |
| `@env/*` | `src/environments/*` | Variables de entorno |

#### Ejemplos por capa:

```typescript
// Desde Infrastructure importando Domain
import { AuthGateway } from '@domain/gateways/auth.gateway';
import { User } from '@domain/models/user.model';
import { LoginUseCase } from '@domain/use-cases/login.use-case';

// Desde Infrastructure importando otro archivo de Infrastructure
import { HttpAdapter } from '@infrastructure/adapters/http/http.adapter';
import { AuthInterceptor } from '@infrastructure/interceptors/auth.interceptor';

// Desde Presentation importando componentes
import { ButtonComponent } from '@presentation/ui/atoms/button/button.component';
import { NavbarComponent } from '@presentation/ui/organisms/navbar/navbar.component';
import { MainLayoutComponent } from '@presentation/ui/templates/main-layout/main-layout.component';

// Importando environments
import { environment } from '@env/environment';
```

#### Beneficios de Path Aliases:

| Beneficio | Descripción |
|-----------|-------------|
| **Legibilidad** | Imports más claros y fáciles de entender |
| **Refactoring** | Mover archivos no requiere actualizar imports relativos |
| **Consistencia** | Mismo formato de import desde cualquier archivo |
| **Menos errores** | Evita confusión con múltiples niveles de `../` |
| **IDE support** | Mejor autocompletado y navegación |

### Prefijos y Sufijos

| Tipo | Patrón | Ejemplo |
|------|--------|---------|
| Componentes | `*.component.ts` | `button.component.ts` |
| Servicios | `*.service.ts` | `user.service.ts` |
| Use Cases | `*.use-case.ts` | `login.use-case.ts` |
| Gateways | `*.gateway.ts` | `auth.gateway.ts` |
| Adapters | `*.adapter.ts` | `http.adapter.ts` |
| Guards | `*.guard.ts` | `auth.guard.ts` |
| Interceptors | `*.interceptor.ts` | `error.interceptor.ts` |
| Models | `*.model.ts` | `user.model.ts` |
| Pages | `*.page.ts` | `login.page.ts` |

### Nomenclatura de Variables

```typescript
// Booleanos: usar prefijos is, has, can, should
const isLoading = true;
const hasPermission = false;
const canEdit = true;
const shouldRefresh = false;

// Arrays: usar plurales
const users: User[] = [];
const selectedItems: Item[] = [];

// Observables: sufijo $
const user$ = this.userService.getUser();
const isLoading$ = new BehaviorSubject<boolean>(false);

// Event handlers: prefijo on
onSubmit(event: Event): void { }
onClick(item: Item): void { }
onChange(value: string): void { }

// Callbacks/Emisores: prefijo on o sufijo ed
@Output() clicked = new EventEmitter<void>();
@Output() valueChange = new EventEmitter<string>();
@Output() onUserSelected = new EventEmitter<User>();
```

---

## 📁 Estructura de Archivos

### 🏠 Componentes Específicos de Página vs Generales (OBLIGATORIO)

Los componentes (átomos, moléculas y organismos) se organizan según su alcance de uso:

#### Componentes Generales (Reutilizables)
Cuando un componente es **reutilizable en múltiples páginas**, se crea en la carpeta `src/app/presentation/ui/`:

```
src/app/presentation/ui/
├── atoms/
│   └── button/              # Usado en login, dashboard, etc.
├── molecules/
│   └── form-field/          # Usado en múltiples formularios
├── organisms/
│   └── navbar/              # Usado en todas las páginas
└── templates/
    └── main-layout/         # Layout general de la app
```

#### Componentes Específicos de Página
Cuando un componente es **exclusivo de una página**, se crea **dentro de la carpeta de la página** en su propia subcarpeta:

```
src/app/presentation/pages/
└── login/
    ├── login.page.ts
    ├── login.page.html
    ├── login.page.css
    └── components/          # Componentes específicos de esta página
        ├── atoms/
        │   └── quick-login-button/
        │       ├── quick-login-button.component.ts
        │       ├── quick-login-button.component.html
        │       └── quick-login-button.component.css
        ├── molecules/
        │   └── feature-badge/
        │       ├── feature-badge.component.ts
        │       ├── feature-badge.component.html
        │       └── feature-badge.component.css
        └── organisms/
            └── hero-section/
                ├── hero-section.component.ts
                ├── hero-section.component.html
                └── hero-section.component.css
```

#### Importación de Componentes Específicos

```typescript
// ❌ PROHIBIDO: Crear componentes de página en ui/ general
import { HeroSection } from '@presentation/ui/organisms/hero-section/hero-section.component';

// ✅ CORRECTO: Importar desde la carpeta de la página (ruta relativa permitida para componentes locales)
import { HeroSectionComponent } from './components/organisms/hero-section/hero-section.component';
import { FeatureBadgeComponent } from './components/molecules/feature-badge/feature-badge.component';
```

#### Criterios de Decisión

| Pregunta | Sí → General (`ui/`) | No → Específico (`page/components/`) |
|----------|---------------------|--------------------------------------|
| ¿Se usará en más de una página? | ✅ | ❌ |
| ¿Es un componente de diseño del sistema (Design System)? | ✅ | ❌ |
| ¿Tiene sentido fuera del contexto de esta página? | ✅ | ❌ |

#### Ejemplo Práctico: Página de Login

```
login/
├── login.page.ts
├── login.page.html
├── login.page.css
└── components/
    ├── molecules/
    │   └── feature-badge/          # Solo para mostrar features en login
    └── organisms/
        ├── hero-section/           # Hero exclusivo de login
        └── quick-login-section/    # Sección demo solo para login
```

**Nota:** Si un componente inicialmente específico de una página comienza a necesitarse en otras páginas, debe **refactorizarse** y moverse a `ui/` como componente general.

---

### 🔄 Reutilización de Componentes (OBLIGATORIO - Prioridad Máxima)

**LA REUTILIZACIÓN DE CÓDIGO ES LA MÁXIMA PRIORIDAD.** Antes de crear cualquier componente nuevo, **OBLIGATORIO** seguir este proceso de validación:

#### Proceso de Validación Antes de Crear un Componente

```
┌─────────────────────────────────────────────────────────────────┐
│  PASO 1: ¿Existe el componente en ui/?                          │
│  Revisar: src/app/presentation/ui/                              │
│  ├── atoms/      (button, icon, input, label, spinner)          │
│  ├── molecules/  (alert, button, card, form-field, input-field, │
│  │                search-box, select-field)                     │
│  ├── organisms/  (data-table, footer, login-form, navbar,       │
│  │                sidebar)                                      │
│  └── templates/  (auth-layout, main-layout)                     │
└─────────────────────────────────────────────────────────────────┘
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
              SÍ EXISTE            NO EXISTE
                    │                   │
                    ▼                   ▼
           ✅ REUTILIZAR       ¿Será reutilizable por
           Importar y usar      otros componentes?
                                        │
                              ┌─────────┴─────────┐
                              ▼                   ▼
                             SÍ                  NO
                              │                   │
                              ▼                   ▼
                    ✅ CREAR EN ui/      ✅ CREAR EN page/
                    Como átomo,          components/ como
                    molécula u           componente específico
                    organismo
```

#### Checklist de Validación (OBLIGATORIO antes de crear)

```markdown
□ 1. Revisar carpeta ui/atoms/ - ¿Existe un átomo similar?
□ 2. Revisar carpeta ui/molecules/ - ¿Existe una molécula similar?
□ 3. Revisar carpeta ui/organisms/ - ¿Existe un organismo similar?
□ 4. Si no existe: ¿Lo usarán otros componentes en el futuro?
     → SÍ: Crear en ui/ siguiendo Atomic Design
     → NO: Crear en page/components/
```

#### Ejemplo Práctico: Necesito un Botón

```typescript
// ❌ PROHIBIDO: Crear botón inline sin verificar
<button class="btn-primary" (click)="submit()">
  <i class="bi bi-plus"></i>
  Crear
</button>

// ✅ PASO 1: Verificar si existe en ui/
// Revisar: src/app/presentation/ui/molecules/button/
// ¡SÍ EXISTE! → Reutilizar

// ✅ CORRECTO: Importar y usar el componente existente
import { ButtonComponent } from '@presentation/ui/molecules/button/button.component';

// En el template:
<app-button 
  variant="primary" 
  icon="plus-circle"
  (buttonClick)="submit()">
  Crear
</app-button>
```

#### Ejemplo: Necesito un Campo de Formulario

```typescript
// ❌ PROHIBIDO: Crear campo de formulario desde cero
<div class="form-group">
  <label for="email">Email</label>
  <input type="email" id="email" class="form-control" />
  <span class="error">Email inválido</span>
</div>

// ✅ PASO 1: Verificar si existe en ui/molecules/
// Revisar: src/app/presentation/ui/molecules/input-field/
// ¡SÍ EXISTE! → Reutilizar

// ✅ CORRECTO: Usar InputFieldComponent
import { InputFieldComponent } from '@presentation/ui/molecules/input-field/input-field.component';

<app-input-field
  formControlName="email"
  label="Email"
  type="email"
  [isInvalid]="isFieldInvalid('email')"
  [errorMessage]="getFieldError('email')">
</app-input-field>
```

#### Ejemplo: Necesito un Componente Nuevo (No Existe)

```typescript
// SITUACIÓN: Necesito un "Rating Stars" component
// PASO 1: Verificar ui/ → No existe
// PASO 2: ¿Lo usarán otros componentes? → SÍ (reviews, productos, etc.)
// DECISIÓN: Crear en ui/molecules/

// ✅ CORRECTO: Crear en ui/molecules/rating-stars/
src/app/presentation/ui/molecules/
└── rating-stars/
    ├── rating-stars.component.ts
    ├── rating-stars.component.html
    └── rating-stars.component.css
```

#### Componentes Disponibles en ui/ (Referencia Rápida)

| Categoría | Componentes Disponibles | Uso |
|-----------|------------------------|-----|
| **Atoms** | `button`, `icon`, `input`, `label`, `spinner` | Elementos básicos sin lógica |
| **Molecules** | `alert`, `button`, `card`, `form-field`, `input-field`, `search-box`, `select-field` | Combinaciones de átomos |
| **Organisms** | `data-table`, `footer`, `login-form`, `navbar`, `sidebar` | Secciones completas de UI |
| **Templates** | `auth-layout`, `main-layout` | Layouts de página |

#### Reglas de Oro

| Regla | Descripción |
|-------|-------------|
| **🥇 Prioridad 1** | SIEMPRE buscar primero en `ui/` antes de crear |
| **🥈 Prioridad 2** | Si no existe y será reutilizable → Crear en `ui/` |
| **🥉 Prioridad 3** | Si es específico de una página → Crear en `page/components/` |
| **⚠️ Nunca** | Duplicar código de un componente existente |
| **⚠️ Nunca** | Crear estilos inline para botones, inputs, cards, etc. |

#### Beneficios de la Reutilización

| Beneficio | Impacto |
|-----------|---------||
| **Consistencia** | UI uniforme en toda la aplicación |
| **Mantenibilidad** | Cambios en un solo lugar afectan toda la app |
| **Velocidad** | Desarrollo más rápido al no recrear componentes |
| **Menos bugs** | Componentes probados y validados |
| **Bundle size** | Menos código duplicado = bundle más pequeño |

---

### 📦 Organización de la Capa de Dominio (OBLIGATORIO)

**OBLIGATORIO** organizar los archivos de la capa de dominio (`models`, `gateways`, `use-cases`) agrupados por **entidad o concepto de negocio**. Cada entidad debe tener su propia carpeta dentro de cada subcarpeta del dominio.

#### Estructura de la Capa de Dominio

```
src/app/domain/
├── models/
│   ├── user/
│   │   ├── user.model.ts
│   │   ├── user-role.model.ts
│   │   └── user-status.model.ts
│   ├── patient/
│   │   ├── patient.model.ts
│   │   ├── document-type.model.ts
│   │   └── gender.model.ts
│   ├── company/
│   │   └── company.model.ts
│   ├── project/
│   │   ├── project.model.ts
│   │   └── project-invitation.model.ts
│   └── auth/
│       └── auth.model.ts
│
├── gateways/
│   ├── user/
│   │   └── user.gateway.ts
│   ├── patient/
│   │   └── patient.gateway.ts
│   ├── company/
│   │   └── company.gateway.ts
│   ├── project/
│   │   └── project.gateway.ts
│   └── auth/
│       └── auth.gateway.ts
│
└── use-cases/
    ├── user/
    │   ├── get-users.use-case.ts
    │   ├── get-current-user.use-case.ts
    │   └── update-user.use-case.ts
    ├── patient/
    │   ├── create-patient.use-case.ts
    │   ├── get-patients.use-case.ts
    │   └── update-patient.use-case.ts
    ├── company/
    │   ├── create-company.use-case.ts
    │   ├── get-companies.use-case.ts
    │   ├── get-company-by-id.use-case.ts
    │   ├── update-company.use-case.ts
    │   └── delete-company.use-case.ts
    ├── project/
    │   ├── create-project.use-case.ts
    │   ├── get-projects-paginated.use-case.ts
    │   └── delete-project.use-case.ts
    └── auth/
        ├── login.use-case.ts
        ├── logout.use-case.ts
        ├── register.use-case.ts
        └── request-password-reset.use-case.ts
```

#### ❌ Estructura Incorrecta (NO HACER)

```
# ❌ PROHIBIDO: Archivos sueltos sin agrupar por entidad
src/app/domain/
├── models/
│   ├── user.model.ts
│   ├── patient.model.ts
│   ├── company.model.ts
│   └── auth.model.ts
├── gateways/
│   ├── user.gateway.ts
│   ├── patient.gateway.ts
│   └── auth.gateway.ts
└── use-cases/
    ├── login.use-case.ts
    ├── create-patient.use-case.ts
    ├── get-users.use-case.ts
    └── create-company.use-case.ts
```

#### ✅ Estructura Correcta (OBLIGATORIO)

```
# ✅ CORRECTO: Archivos agrupados por entidad/concepto de negocio
src/app/domain/
├── models/
│   └── patient/
│       ├── patient.model.ts
│       ├── document-type.model.ts
│       └── gender.model.ts
├── gateways/
│   └── patient/
│       └── patient.gateway.ts
└── use-cases/
    └── patient/
        ├── create-patient.use-case.ts
        ├── get-patients.use-case.ts
        └── update-patient.use-case.ts
```

#### Ejemplo de Imports con la Nueva Estructura

```typescript
// ❌ PROHIBIDO: Imports sin carpeta de entidad
import { Patient } from '@domain/models/patient.model';
import { PatientGateway } from '@domain/gateways/patient.gateway';
import { CreatePatientUseCase } from '@domain/use-cases/create-patient.use-case';

// ✅ CORRECTO: Imports con carpeta de entidad
import { Patient } from '@domain/models/patient/patient.model';
import { DocumentType, Gender } from '@domain/models/patient/document-type.model';
import { PatientGateway } from '@domain/gateways/patient/patient.gateway';
import { CreatePatientUseCase } from '@domain/use-cases/patient/create-patient.use-case';
import { GetPatientsUseCase } from '@domain/use-cases/patient/get-patients.use-case';
```

#### Beneficios de Agrupar por Entidad

| Beneficio | Descripción |
|-----------|-------------|
| **Cohesión** | Todo lo relacionado con una entidad está junto |
| **Navegación** | Fácil encontrar todos los archivos de una entidad |
| **Escalabilidad** | Agregar nuevas entidades sin desordenar carpetas existentes |
| **Mantenibilidad** | Cambios en una entidad no afectan otras carpetas |
| **Claridad** | Estructura clara que refleja el dominio del negocio |

#### Reglas para la Organización del Dominio

| Regla | Descripción |
|-------|-------------|
| **1 entidad = 1 carpeta** | Cada entidad de negocio tiene su carpeta en models, gateways y use-cases |
| **Nombre de carpeta** | Usar el nombre de la entidad en singular y kebab-case |
| **Modelos relacionados** | Enums y tipos auxiliares van en la misma carpeta de la entidad |
| **Use cases específicos** | Cada operación es un archivo separado dentro de la carpeta |
| **Gateway por entidad** | Un gateway por entidad para mantener Single Responsibility |

---

### Componentes Angular

| Beneficio | Impacto |
|-----------|---------|
| **Consistencia** | UI uniforme en toda la aplicación |
| **Mantenibilidad** | Cambios en un solo lugar afectan toda la app |
| **Velocidad** | Desarrollo más rápido al no recrear componentes |
| **Menos bugs** | Componentes probados y validados |
| **Bundle size** | Menos código duplicado = bundle más pequeño |

---

### Componentes Angular

```
component-name/
├── component-name.component.ts       # Lógica del componente
├── component-name.component.html     # Template
├── component-name.component.css      # Estilos
└── component-name.component.spec.ts  # Tests (opcional)
```

### Orden de Imports

```typescript
// 1. Angular core
import { Component, Input, Output } from '@angular/core';
import { CommonModule } from '@angular/common';

// 2. Angular modules
import { RouterModule } from '@angular/router';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';

// 3. Third-party libraries
import { Observable, BehaviorSubject } from 'rxjs';

// 4. Project modules - Domain (usando path aliases)
import { User } from '@domain/models/user.model';
import { AuthGateway } from '@domain/gateways/auth.gateway';

// 5. Project modules - Infrastructure (usando path aliases)
import { AuthAdapter } from '@infrastructure/adapters/auth/auth.adapter';

// 6. Project modules - Presentation (usando path aliases)
import { ButtonComponent } from '@presentation/ui/atoms/button/button.component';

// 7. Environment
import { environment } from '@env/environment';
```

### Orden dentro de una Clase

```typescript
@Component({ ... })
export class ExampleComponent {
  // 1. Static properties
  static readonly MAX_ITEMS = 10;

  // 2. Signal Inputs (reemplazan @Input)
  readonly title = input<string>('');
  readonly items = input<Item[]>([]);
  readonly user = input.required<User>();

  // 3. Outputs (reemplazan @Output + EventEmitter)
  readonly itemSelected = output<Item>();
  readonly clicked = output<void>();

  // 4. ViewChild/ContentChild
  readonly inputRef = viewChild<ElementRef>('inputRef');

  // 5. Computed signals (valores derivados - reemplazan getters con lógica)
  readonly itemCount = computed(() => this.items().length);
  readonly isValid = computed(() => this.title().length > 0);

  // 6. Signals para estado interno
  readonly isLoading = signal(false);
  readonly selectedItem = signal<Item | null>(null);

  // 7. Injected dependencies
  private readonly userService = inject(UserService);
  private readonly router = inject(Router);

  // 8. Constructor (minimal o vacío)
  constructor() {
    // Effects si son necesarios
    effect(() => {
      console.log('Items changed:', this.items().length);
    });
  }

  // 9. Public methods
  selectItem(item: Item): void {
    this.selectedItem.set(item);
    this.itemSelected.emit(item);
  }

  // 10. Event handlers
  onItemClick(item: Item): void {
    this.selectItem(item);
  }

  // 11. Private methods
  private loadItems(): void { }
}
```

### NO Lógica en el Frontend (OBLIGATORIO)

**PROHIBIDO** implementar lógica de negocio en componentes de presentación.
**OBLIGATORIO** usar solo `computed()` o getters simples para transformaciones de vista.

```typescript
// ❌ PROHIBIDO: Lógica de negocio en el componente
@Component({ ... })
export class BadComponent {
  calculateDiscount(price: number, quantity: number): number {
    if (quantity > 10) return price * 0.9;
    if (quantity > 5) return price * 0.95;
    return price;
  }

  validateEmail(email: string): boolean {
    const regex = /^[^@]+@[^@]+\.[^@]+$/;
    return regex.test(email);
  }
}

// ✅ CORRECTO: Solo computed/getters para transformaciones de vista
@Component({ ... })
export class GoodComponent {
  readonly items = input<Item[]>([]);
  readonly user = input<User | null>(null);

  // Computed para valores derivados de la vista
  readonly itemCount = computed(() => this.items().length);
  readonly hasItems = computed(() => this.items().length > 0);
  readonly displayName = computed(() => {
    const user = this.user();
    return user ? `${user.firstName} ${user.lastName}` : 'Anónimo';
  });

  // La lógica de negocio va en Use Cases del dominio
  private readonly calculateDiscountUseCase = inject(CalculateDiscountUseCase);
  
  getDiscount(item: Item): number {
    return this.calculateDiscountUseCase.execute(item);
  }
}
```

#### Regla general:

| Tipo de lógica | Ubicación correcta |
|----------------|--------------------|
| Validaciones de negocio | Domain → Use Cases |
| Cálculos complejos | Domain → Use Cases |
| Transformaciones de datos | Domain → Models |
| Formateo para vista | Component → `computed()` |
| Derivaciones simples | Component → `computed()` |

---

## 📘 TypeScript

### Tipado Estricto

```typescript
// ❌ MAL: any
function processData(data: any): any { }

// ✅ BIEN: Tipos específicos
function processData(data: UserData): ProcessedResult { }

// ❌ MAL: Tipos implícitos en parámetros
function calculate(a, b) { return a + b; }

// ✅ BIEN: Tipos explícitos
function calculate(a: number, b: number): number { 
  return a + b; 
}
```

### Uso de Interfaces y Types

```typescript
// Interfaces para objetos/entidades
interface User {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
}

// Type aliases para uniones y utilidades
type UserRole = 'admin' | 'user' | 'guest';
type UserStatus = 'active' | 'inactive' | 'suspended';

// Type para funciones
type ValidationFn = (value: string) => boolean;

// Type para resultados
type Result<T, E = Error> = 
  | { success: true; data: T }
  | { success: false; error: E };
```

### Inmutabilidad

```typescript
// ❌ MAL: Mutar objetos directamente
user.name = 'New Name';
users.push(newUser);

// ✅ BIEN: Crear nuevas instancias
const updatedUser = { ...user, name: 'New Name' };
const updatedUsers = [...users, newUser];

// ✅ BIEN: Usar readonly para propiedades inmutables
interface Config {
  readonly apiUrl: string;
  readonly timeout: number;
}
```

### Null Safety

```typescript
// ❌ MAL: No verificar null
const userName = user.name.toUpperCase();

// ✅ BIEN: Optional chaining
const userName = user?.name?.toUpperCase();

// ✅ BIEN: Nullish coalescing
const displayName = user?.name ?? 'Usuario Anónimo';

// ✅ BIEN: Guard clauses
function processUser(user: User | null): void {
  if (!user) {
    return;
  }
  // Procesar usuario...
}
```

---

## 🅰️ Angular

### Componentes Standalone

```typescript
// ✅ Todos los componentes deben ser standalone
@Component({
  selector: 'app-button',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './button.component.html',
  styleUrl: './button.component.css',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ButtonComponent { }
```

### Change Detection

```typescript
// ✅ Usar OnPush para mejor rendimiento
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class OptimizedComponent { }
```

### Inyección de Dependencias

```typescript
// ✅ PREFERIDO: Usar inject()
@Component({ ... })
export class ModernComponent {
  private userService = inject(UserService);
  private router = inject(Router);
}

// ⚠️ LEGACY: Constructor injection (evitar en nuevos componentes)
@Component({ ... })
export class LegacyComponent {
  constructor(
    private userService: UserService,
    private router: Router
  ) { }
}
```

### Templates (OBLIGATORIO - Angular 17+)

**PROHIBIDO** usar la sintaxis antigua de directivas estructurales (`*ngIf`, `*ngFor`, `*ngSwitch`). 
**OBLIGATORIO** usar la nueva sintaxis de control flow con `@`.

```typescript
// ❌ PROHIBIDO: Sintaxis antigua
<div *ngIf="isLoading">
  <app-spinner></app-spinner>
</div>

<div *ngFor="let item of items; trackBy: trackById">
  <app-item [data]="item"></app-item>
</div>

<div [ngSwitch]="status">
  <span *ngSwitchCase="'active'">Activo</span>
  <span *ngSwitchDefault>Desconocido</span>
</div>

// ✅ CORRECTO: Nueva sintaxis de control flow
@if (isLoading) {
  <app-spinner></app-spinner>
}

@for (item of items; track item.id) {
  <app-item [data]="item"></app-item>
}

@switch (status) {
  @case ('active') { <span>Activo</span> }
  @case ('inactive') { <span>Inactivo</span> }
  @default { <span>Desconocido</span> }
}
```

#### Ventajas de la nueva sintaxis:

| Beneficio | Descripción |
|-----------|-------------|
| **Rendimiento** | Mejor tree-shaking y bundle size |
| **Legibilidad** | Sintaxis más clara y familiar |
| **Type safety** | Mejor inferencia de tipos |
| **No requiere imports** | No necesita CommonModule |

### Signals (OBLIGATORIO - Angular 17+)

**PROHIBIDO** usar `@Input()` y `@Output()` decoradores.
**OBLIGATORIO** usar Signal Inputs (`input()`) y Output functions (`output()`).

```typescript
// ❌ PROHIBIDO: Decoradores legacy
@Component({ ... })
export class LegacyComponent {
  @Input() title = '';
  @Input() items: Item[] = [];
  @Output() itemSelected = new EventEmitter<Item>();
}

// ✅ CORRECTO: Signal Inputs y Outputs
import { Component, input, output, computed, signal } from '@angular/core';

@Component({ ... })
export class ModernComponent {
  // Signal Inputs (reemplazan @Input)
  readonly title = input<string>('');                    // Con valor por defecto
  readonly items = input<Item[]>([]);                    // Array con default
  readonly user = input.required<User>();                // Input requerido
  readonly config = input<Config, string>('', {          // Con transform
    transform: (value: string) => JSON.parse(value)
  });

  // Outputs (reemplazan @Output + EventEmitter)
  readonly itemSelected = output<Item>();                // Emite Item
  readonly clicked = output<void>();                     // Sin payload

  // Computed signals para valores derivados
  readonly itemCount = computed(() => this.items().length);
  readonly hasItems = computed(() => this.items().length > 0);

  // Signal para estado interno
  readonly isLoading = signal(false);

  // Métodos
  selectItem(item: Item): void {
    this.itemSelected.emit(item);
  }
}
```

#### Acceso a valores de Signal Inputs:

```typescript
// En el componente
const currentTitle = this.title();           // Llamar como función
const allItems = this.items();
const firstItem = this.items()[0];

// En el template
{{ title() }}
@for (item of items(); track item.id) { ... }
```

#### Ventajas de Signal Inputs:

| Beneficio | Descripción |
|-----------|-------------|
| **Reactividad** | Se integran con el sistema de signals |
| **Type safety** | Mejor inferencia de tipos |
| **Change detection** | Mejor rendimiento con OnPush |
| **Simplicidad** | No requiere ngOnChanges para detectar cambios |

### Gestión de Suscripciones

```typescript
// ✅ BIEN: Usar takeUntilDestroyed o async pipe
@Component({ ... })
export class SafeComponent {
  private destroyRef = inject(DestroyRef);
  
  data$ = this.dataService.getData().pipe(
    takeUntilDestroyed(this.destroyRef)
  );
}

// ✅ BIEN: Async pipe en template
// Template: {{ data$ | async }}
```

---

## 🎨 Estilos CSS

### Uso de Variables CSS

```css
/* ✅ BIEN: Usar custom properties */
.button {
  background-color: var(--primary-color);
  padding: var(--spacing-md);
  border-radius: var(--border-radius-sm);
  transition: all var(--transition-fast);
}

/* ❌ MAL: Valores hardcodeados */
.button {
  background-color: #007bff;
  padding: 16px;
  border-radius: 4px;
}
```

### BEM (Block Element Modifier)

```css
/* Block */
.card { }

/* Element */
.card__header { }
.card__body { }
.card__footer { }

/* Modifier */
.card--highlighted { }
.card--disabled { }
```

### Encapsulación de Componentes

```css
/* ✅ BIEN: Usar :host para estilos del componente host */
:host {
  display: block;
  width: 100%;
}

:host(.is-active) {
  border-color: var(--primary-color);
}

/* ✅ BIEN: Evitar estilos globales en componentes */
```

---

## 🌓 Soporte de Temas (Light/Dark)

### Obligatorio: Todos los Componentes con Soporte de Temas

**OBLIGATORIO** que todos los componentes visuales (átomos, moléculas, organismos) soporten tanto tema claro como tema oscuro.

#### Patrón de Implementación

Usar clases modificadoras `.theme-light` y `.theme-dark` para alternar entre temas:

```css
/* Estilos base (tema oscuro por defecto) */
.data-table-container {
  background: var(--dark-bg-gradient);
  border: 1px solid var(--dark-border);
  color: var(--dark-text-primary);
}

/* Tema claro - usar clase modificadora */
.theme-light.data-table-container {
  background: var(--white);
  border: 1px solid var(--gray-200);
  color: var(--text-primary);
}
```

#### Variables CSS Obligatorias

Siempre usar variables CSS del archivo `styles.css`, **NUNCA** colores hardcodeados:

```css
/* ❌ PROHIBIDO: Colores hardcodeados */
.component {
  background-color: #ffffff;
  color: #1e293b;
  border: 1px solid #e5e7eb;
}

/* ✅ CORRECTO: Variables CSS globales */
.component {
  background-color: var(--white);
  color: var(--text-primary);
  border: 1px solid var(--gray-200);
}
```

#### Variables Disponibles por Tema

| Propósito | Tema Claro | Tema Oscuro |
|-----------|------------|-------------|
| **Fondo primario** | `--white`, `--bg-primary` | `--dark-bg-primary` |
| **Fondo secundario** | `--gray-50`, `--bg-secondary` | `--dark-bg-secondary` |
| **Texto primario** | `--text-primary`, `--gray-800` | `--dark-text-primary` |
| **Texto secundario** | `--text-secondary`, `--gray-600` | `--dark-text-secondary` |
| **Bordes** | `--gray-200`, `--gray-300` | `--dark-border` |
| **Bordes hover** | `--gray-400` | `--dark-border-hover` |

#### Ejemplo Completo: Componente con Soporte de Temas

```css
/* card.component.css */

/* Tema oscuro (base) */
.card {
  background: var(--dark-card-bg);
  border: 1px solid var(--dark-border);
  border-radius: var(--border-radius-md);
  color: var(--dark-text-primary);
}

.card__title {
  color: var(--dark-text-primary);
}

.card__description {
  color: var(--dark-text-secondary);
}

/* Tema claro */
.theme-light.card {
  background: var(--white);
  border: 1px solid var(--gray-200);
  color: var(--text-primary);
}

.theme-light .card__title {
  color: var(--gray-800);
}

.theme-light .card__description {
  color: var(--gray-600);
}
```

#### Aplicar Tema desde el Componente

```typescript
// En el componente
@Input() theme: 'light' | 'dark' = 'light';

get themeClass(): string {
  return this.theme === 'light' ? 'theme-light' : 'theme-dark';
}
```

```html
<!-- En el template -->
<div class="card" [class]="themeClass">
  <!-- contenido -->
</div>
```

#### Checklist de Temas

- [ ] El componente tiene estilos base (tema oscuro)
- [ ] El componente tiene estilos `.theme-light`
- [ ] Todos los colores usan variables CSS
- [ ] NO hay colores hardcodeados (#ffffff, rgba(), etc.)
- [ ] El componente acepta input `theme` para alternar

---

## 📝 Validaciones de Formularios

### Obligatorio: Validaciones según Tipo de Campo

**OBLIGATORIO** implementar validaciones apropiadas según el tipo de dato que se espera en cada campo del formulario.

#### Tabla de Validaciones por Tipo de Campo

| Tipo de Campo | Validaciones Obligatorias | Ejemplo |
|---------------|--------------------------|---------|
| **Email** | `required`, `email`, `pattern` (regex email) | `Validators.email` |
| **Teléfono** | `required`, `minLength(10)`, `maxLength(15)`, `pattern` (solo números) | `Validators.pattern(/^[0-9]+$/)` |
| **Nombres/Apellidos** | `required`, `minLength(2)`, `maxLength(50)`, `pattern` (solo letras y espacios) | `Validators.pattern(/^[a-zA-ZáéíóúÁÉÍÓÚñÑ\s]+$/)` |
| **Documento ID** | `required`, `minLength(6)`, `maxLength(20)`, `pattern` (alfanumérico) | `Validators.pattern(/^[a-zA-Z0-9]+$/)` |
| **Contraseña** | `required`, `minLength(8)`, `pattern` (mayúscula, minúscula, número) | Ver ejemplo abajo |
| **Fecha** | `required`, validación de rango | Fecha no futura para nacimiento |
| **Número** | `required`, `min`, `max`, `pattern` (solo números) | `Validators.min(0)` |
| **Texto general** | `required`, `maxLength` | `Validators.maxLength(500)` |

#### Implementación de Validaciones

```typescript
// ✅ CORRECTO: Formulario con validaciones completas
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({ ... })
export class RegistroPacienteComponent {
  private fb = inject(FormBuilder);

  // Patrones de validación reutilizables
  private readonly PATTERNS = {
    onlyLetters: /^[a-zA-ZáéíóúÁÉÍÓÚñÑ\s]+$/,
    onlyNumbers: /^[0-9]+$/,
    alphanumeric: /^[a-zA-Z0-9]+$/,
    email: /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/,
    phone: /^[0-9]{10,15}$/,
    password: /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d@$!%*?&]{8,}$/
  };

  form = this.fb.group({
    // Nombres - Solo letras, 2-50 caracteres
    nombres: ['', [
      Validators.required,
      Validators.minLength(2),
      Validators.maxLength(50),
      Validators.pattern(this.PATTERNS.onlyLetters)
    ]],

    // Apellidos - Solo letras, 2-50 caracteres  
    apellidos: ['', [
      Validators.required,
      Validators.minLength(2),
      Validators.maxLength(50),
      Validators.pattern(this.PATTERNS.onlyLetters)
    ]],

    // Email - Formato válido
    email: ['', [
      Validators.required,
      Validators.email,
      Validators.pattern(this.PATTERNS.email)
    ]],

    // Teléfono - Solo números, 10-15 dígitos
    telefono: ['', [
      Validators.required,
      Validators.minLength(10),
      Validators.maxLength(15),
      Validators.pattern(this.PATTERNS.onlyNumbers)
    ]],

    // Documento - Alfanumérico, 6-20 caracteres
    documento: ['', [
      Validators.required,
      Validators.minLength(6),
      Validators.maxLength(20),
      Validators.pattern(this.PATTERNS.alphanumeric)
    ]],

    // Contraseña - Mínimo 8 caracteres, mayúscula, minúscula, número
    password: ['', [
      Validators.required,
      Validators.minLength(8),
      Validators.pattern(this.PATTERNS.password)
    ]],

    // Edad - Número entre 0 y 150
    edad: ['', [
      Validators.required,
      Validators.min(0),
      Validators.max(150),
      Validators.pattern(this.PATTERNS.onlyNumbers)
    ]]
  });
}
```

#### Validadores Personalizados Reutilizables

Crear validadores en `src/app/infrastructure/validators/`:

```typescript
// src/app/infrastructure/validators/custom.validators.ts
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

export class CustomValidators {
  
  /**
   * Valida que el campo contenga solo letras y espacios
   */
  static onlyLetters(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      if (!control.value) return null;
      const valid = /^[a-zA-ZáéíóúÁÉÍÓÚñÑ\s]+$/.test(control.value);
      return valid ? null : { onlyLetters: true };
    };
  }

  /**
   * Valida que el campo contenga solo números
   */
  static onlyNumbers(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      if (!control.value) return null;
      const valid = /^[0-9]+$/.test(control.value);
      return valid ? null : { onlyNumbers: true };
    };
  }

  /**
   * Valida formato de teléfono colombiano
   */
  static phoneNumber(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      if (!control.value) return null;
      const valid = /^[0-9]{10,15}$/.test(control.value);
      return valid ? null : { phoneNumber: true };
    };
  }

  /**
   * Valida que la fecha no sea futura
   */
  static notFutureDate(): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      if (!control.value) return null;
      const inputDate = new Date(control.value);
      const today = new Date();
      return inputDate <= today ? null : { futureDate: true };
    };
  }

  /**
   * Valida que dos campos coincidan (ej: contraseña y confirmación)
   */
  static mustMatch(controlName: string, matchingControlName: string): ValidatorFn {
    return (formGroup: AbstractControl): ValidationErrors | null => {
      const control = formGroup.get(controlName);
      const matchingControl = formGroup.get(matchingControlName);

      if (!control || !matchingControl) return null;

      if (matchingControl.errors && !matchingControl.errors['mustMatch']) {
        return null;
      }

      if (control.value !== matchingControl.value) {
        matchingControl.setErrors({ mustMatch: true });
        return { mustMatch: true };
      } else {
        matchingControl.setErrors(null);
        return null;
      }
    };
  }
}
```

#### Uso de Validadores Personalizados

```typescript
import { CustomValidators } from '@infrastructure/validators/custom.validators';

form = this.fb.group({
  nombres: ['', [Validators.required, CustomValidators.onlyLetters()]],
  telefono: ['', [Validators.required, CustomValidators.phoneNumber()]],
  fechaNacimiento: ['', [Validators.required, CustomValidators.notFutureDate()]],
  password: ['', Validators.required],
  confirmPassword: ['', Validators.required]
}, {
  validators: CustomValidators.mustMatch('password', 'confirmPassword')
});
```

#### Mensajes de Error

```typescript
// Método para obtener mensaje de error
getFieldError(fieldName: string): string {
  const control = this.form.get(fieldName);
  if (!control?.errors || !control.touched) return '';

  const errors = control.errors;

  if (errors['required']) return 'Este campo es requerido';
  if (errors['minlength']) return `Mínimo ${errors['minlength'].requiredLength} caracteres`;
  if (errors['maxlength']) return `Máximo ${errors['maxlength'].requiredLength} caracteres`;
  if (errors['email']) return 'Formato de email inválido';
  if (errors['pattern']) return 'Formato inválido';
  if (errors['onlyLetters']) return 'Solo se permiten letras';
  if (errors['onlyNumbers']) return 'Solo se permiten números';
  if (errors['phoneNumber']) return 'Número de teléfono inválido';
  if (errors['futureDate']) return 'La fecha no puede ser futura';
  if (errors['mustMatch']) return 'Los campos no coinciden';
  if (errors['min']) return `Valor mínimo: ${errors['min'].min}`;
  if (errors['max']) return `Valor máximo: ${errors['max'].max}`;

  return 'Campo inválido';
}
```

#### Checklist de Validaciones

- [ ] Todos los campos tienen `Validators.required` si son obligatorios
- [ ] Campos de texto tienen `maxLength` definido
- [ ] Campos numéricos usan `pattern` para solo números
- [ ] Campos de nombre/apellido validan solo letras
- [ ] Campos de teléfono validan formato y longitud
- [ ] Campos de email usan `Validators.email`
- [ ] Campos de fecha validan rangos lógicos
- [ ] Contraseñas tienen requisitos de complejidad
- [ ] Mensajes de error son claros y específicos

---

## 🌐 Internacionalización (i18n)

### Obligatorio: Todos los Textos en Archivos de Traducción

**OBLIGATORIO** que todos los textos visibles al usuario estén definidos en los archivos de traducción (`src/assets/i18n/es.json`). **PROHIBIDO** escribir textos directamente en los templates HTML.

#### Estructura del Archivo de Traducciones

```json
// src/assets/i18n/es.json
{
  "app": {
    "nombreModulo": {
      "nombreSeccion": {
        "clave": "Texto visible al usuario"
      }
    }
  }
}
```

#### Convención de Claves

| Nivel | Descripción | Ejemplo |
|-------|-------------|---------|
| 1 | Módulo/Feature | `app.pacientes` |
| 2 | Sección/Componente | `app.pacientes.lista` |
| 3 | Elemento específico | `app.pacientes.lista.titulo` |

#### Ejemplo de Estructura

```json
{
  "app": {
    "common": {
      "buttons": {
        "save": "Guardar",
        "cancel": "Cancelar",
        "delete": "Eliminar",
        "edit": "Editar",
        "create": "Crear",
        "search": "Buscar",
        "back": "Volver",
        "next": "Siguiente",
        "previous": "Anterior",
        "confirm": "Confirmar"
      },
      "labels": {
        "loading": "Cargando...",
        "noResults": "No se encontraron resultados",
        "required": "Campo requerido",
        "optional": "Opcional"
      },
      "messages": {
        "success": "Operación exitosa",
        "error": "Ha ocurrido un error",
        "confirmDelete": "¿Está seguro que desea eliminar este registro?"
      }
    },
    "pacientes": {
      "list": {
        "title": "Listado de Pacientes",
        "subtitle": "Gestiona la información de los pacientes",
        "searchPlaceholder": "Buscar pacientes...",
        "newPatient": "Nuevo Paciente",
        "columns": {
          "name": "Nombre",
          "document": "Documento",
          "phone": "Teléfono",
          "email": "Correo",
          "status": "Estado",
          "actions": "Acciones"
        }
      },
      "form": {
        "title": "Registro de Paciente",
        "steps": {
          "personal": "Datos Personales",
          "contact": "Contacto",
          "medical": "Información Médica",
          "confirm": "Confirmación"
        },
        "fields": {
          "firstName": "Nombres",
          "lastName": "Apellidos",
          "documentType": "Tipo de Documento",
          "documentNumber": "Número de Documento",
          "birthDate": "Fecha de Nacimiento",
          "phone": "Teléfono",
          "email": "Correo Electrónico",
          "address": "Dirección"
        },
        "placeholders": {
          "firstName": "Ingrese los nombres",
          "lastName": "Ingrese los apellidos",
          "phone": "+57 301 234 5678",
          "email": "correo@ejemplo.com"
        },
        "errors": {
          "required": "Este campo es requerido",
          "invalidEmail": "Formato de correo inválido",
          "invalidPhone": "Número de teléfono inválido",
          "onlyLetters": "Solo se permiten letras",
          "minLength": "Mínimo {{min}} caracteres",
          "maxLength": "Máximo {{max}} caracteres"
        }
      }
    }
  }
}
```

#### Uso en Templates

```html
<!-- ❌ PROHIBIDO: Texto hardcodeado -->
<h1>Listado de Pacientes</h1>
<button>Guardar</button>
<span>Este campo es requerido</span>

<!-- ✅ CORRECTO: Usar pipe translate -->
<h1>{{ 'app.pacientes.list.title' | translate }}</h1>
<button>{{ 'app.common.buttons.save' | translate }}</button>
<span>{{ 'app.common.labels.required' | translate }}</span>
```

#### Uso con Parámetros

```html
<!-- En es.json -->
{
  "app.pacientes.list.showing": "Mostrando {{count}} de {{total}} pacientes"
}

<!-- En el template -->
<span>{{ 'app.pacientes.list.showing' | translate: { count: 10, total: 100 } }}</span>
```

#### Uso en Componentes TypeScript

```typescript
import { TranslateService } from '@ngx-translate/core';

@Component({ ... })
export class PacientesComponent {
  private translate = inject(TranslateService);

  showSuccessMessage(): void {
    const message = this.translate.instant('app.common.messages.success');
    this.notificationService.show(message, 'success');
  }

  // Con parámetros
  showCountMessage(count: number, total: number): void {
    const message = this.translate.instant('app.pacientes.list.showing', { count, total });
    console.log(message); // "Mostrando 10 de 100 pacientes"
  }
}
```

#### Configuración del Módulo

```typescript
// app.config.ts
import { TranslateModule, TranslateLoader } from '@ngx-translate/core';
import { TranslateHttpLoader } from '@ngx-translate/http-loader';
import { HttpClient } from '@angular/common/http';

export function HttpLoaderFactory(http: HttpClient) {
  return new TranslateHttpLoader(http, './assets/i18n/', '.json');
}

export const appConfig: ApplicationConfig = {
  providers: [
    importProvidersFrom(
      TranslateModule.forRoot({
        defaultLanguage: 'es',
        loader: {
          provide: TranslateLoader,
          useFactory: HttpLoaderFactory,
          deps: [HttpClient]
        }
      })
    )
  ]
};
```

#### Importar en Componentes Standalone

```typescript
import { TranslateModule } from '@ngx-translate/core';

@Component({
  selector: 'app-pacientes',
  standalone: true,
  imports: [CommonModule, TranslateModule],
  templateUrl: './pacientes.component.html'
})
export class PacientesComponent { }
```

#### Checklist de i18n

- [ ] Todos los textos visibles están en `es.json`
- [ ] NO hay textos hardcodeados en templates HTML
- [ ] Las claves siguen la convención `app.modulo.seccion.elemento`
- [ ] Los mensajes de error de validación están traducidos
- [ ] Los placeholders están traducidos
- [ ] Los tooltips y titles están traducidos
- [ ] Los mensajes de notificación usan traducciones
- [ ] `TranslateModule` está importado en componentes standalone

---

## 🧪 Testing

### Estructura de Tests

```typescript
describe('LoginUseCase', () => {
  let useCase: LoginUseCase;
  let authGateway: jest.Mocked<AuthGateway>;
  let storageGateway: jest.Mocked<StorageGateway>;

  beforeEach(() => {
    // Arrange - Setup
    authGateway = createMock<AuthGateway>();
    storageGateway = createMock<StorageGateway>();
    useCase = new LoginUseCase(authGateway, storageGateway);
  });

  describe('execute', () => {
    it('should return success result when credentials are valid', () => {
      // Arrange
      const credentials: AuthCredentials = {
        email: 'test@example.com',
        password: 'password123'
      };
      const expectedSession = createMockSession();
      authGateway.login.mockReturnValue(of(success(expectedSession)));

      // Act
      const result$ = useCase.execute(credentials);

      // Assert
      result$.subscribe(result => {
        expect(result.success).toBe(true);
        expect(result.data).toEqual(expectedSession);
      });
    });

    it('should store session when login succeeds', () => {
      // ...
    });

    it('should return error when credentials are invalid', () => {
      // ...
    });
  });
});
```

### Nomenclatura de Tests

```typescript
// Patrón: should [expected behavior] when [condition]
it('should display error message when form is invalid', () => { });
it('should navigate to dashboard when login succeeds', () => { });
it('should disable button when loading is true', () => { });
```

---

## 📦 Git

### Conventional Commits

```bash
# Formato: <tipo>(<alcance>): <descripción>

# Tipos permitidos:
feat(auth): add login functionality
fix(ui): resolve button alignment issue
docs(readme): update installation instructions
style(button): format code according to guidelines
refactor(user): extract validation logic to separate function
test(login): add unit tests for LoginUseCase
chore(deps): update angular to version 18
```

### Estructura de Branches

```
main                    # Producción
├── develop             # Desarrollo
    ├── feature/auth-login       # Nueva funcionalidad
    ├── feature/user-profile     # Nueva funcionalidad
    ├── fix/button-alignment     # Corrección de bug
    ├── refactor/clean-services  # Refactoring
    └── hotfix/security-patch    # Corrección urgente
```

### Pull Requests

- Título descriptivo siguiendo conventional commits
- Descripción detallada de los cambios
- Referencias a issues relacionados
- Screenshots para cambios de UI
- Tests pasando
- Code review aprobado

---

## 📖 Documentación

### Comentarios en Código

```typescript
/**
 * LoginUseCase - Handles user authentication
 * 
 * This use case orchestrates the login process including:
 * - Credential validation
 * - Session management
 * - Remember me functionality
 * 
 * @example
 * ```typescript
 * const result = await this.loginUseCase.execute({
 *   email: 'user@example.com',
 *   password: 'password123'
 * }).toPromise();
 * ```
 */
@Injectable({ providedIn: 'root' })
export class LoginUseCase {
  /**
   * Execute login operation
   * @param credentials - User login credentials
   * @returns Observable with login result
   */
  execute(credentials: AuthCredentials): Observable<Result<AuthSession>> {
    // Implementation...
  }
}
```

### Documentación de Componentes

```typescript
/**
 * Button Component
 * 
 * A reusable button component following Bootstrap styling.
 * 
 * @example
 * ```html
 * <app-button 
 *   variant="primary"
 *   [loading]="isSubmitting"
 *   (clicked)="onSubmit()"
 * >
 *   Submit
 * </app-button>
 * ```
 * 
 * @inputs
 * - variant: Button style variant ('primary', 'secondary', etc.)
 * - loading: Shows loading spinner when true
 * - disabled: Disables the button
 * 
 * @outputs
 * - clicked: Emitted when button is clicked
 */
@Component({ ... })
export class ButtonComponent { }
```

---

## 🔔 Sistema de Notificaciones Global

El sistema de notificaciones proporciona feedback visual al usuario mediante alertas que se superponen a todo el contenido, incluyendo modales.

### Arquitectura

```
infrastructure/
├── services/
│   └── notification.service.ts    # Servicio singleton para gestionar notificaciones

presentation/
├── ui/
│   └── organisms/
│       └── notification-container/
│           └── notification-container.component.ts  # Componente visual
```

### NotificationService

Servicio singleton que gestiona las notificaciones de la aplicación. Usa Signals para reactividad.

```typescript
import { NotificationService } from '@infrastructure/services/notification.service';

@Component({ ... })
export class MyComponent {
  private readonly notificationService = inject(NotificationService);

  onSuccess(): void {
    // Muestra mensaje del backend o personalizado
    this.notificationService.success('Operación completada exitosamente');
  }

  onError(): void {
    this.notificationService.error('Error al procesar la solicitud');
  }

  onWarning(): void {
    this.notificationService.warning('Atención: Esta acción es irreversible');
  }

  onInfo(): void {
    this.notificationService.info('Información importante');
  }
}
```

### Tipos de Notificación

| Tipo | Método | Color | Uso |
|------|--------|-------|-----|
| **success** | `success(message, duration?)` | Verde | Operaciones exitosas |
| **error** | `error(message, duration?)` | Rojo | Errores y fallos |
| **warning** | `warning(message, duration?)` | Amarillo | Advertencias |
| **info** | `info(message, duration?)` | Azul | Información general |

### Duración de Notificaciones

```typescript
// Duración por defecto: 5000ms (5 segundos)
this.notificationService.success('Mensaje');

// Duración personalizada: 10 segundos
this.notificationService.success('Mensaje', 10000);

// Sin auto-cerrado: 0
this.notificationService.error('Error crítico', 0);
```

### Integración con API Response

**OBLIGATORIO**: Mostrar el mensaje que viene del backend cuando está disponible.

```typescript
this.createPatientUseCase.execute(request).subscribe({
  next: (result) => {
    if (result.success) {
      // ✅ Usar mensaje del backend
      this.notificationService.success(result.message || 'Paciente creado exitosamente');
    } else {
      // ✅ Usar mensaje de error del backend
      this.notificationService.error(result.error.message);
    }
  },
  error: (error) => {
    this.notificationService.error(error.message || 'Error al procesar la solicitud');
  }
});
```

### Configuración en App Component

El `NotificationContainerComponent` debe estar incluido en el `app.component`:

```typescript
// app.ts
import { NotificationContainerComponent } from '@presentation/ui/organisms/notification-container/notification-container.component';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet, NotificationContainerComponent],
  templateUrl: './app.html'
})
export class App { }
```

```html
<!-- app.html -->
<app-notification-container />
<router-outlet />
```

### Características del Componente

| Característica | Descripción |
|---------------|-------------|
| **Z-index** | `9999` - Se superpone a modales y todo contenido |
| **Posición** | Fixed en la parte superior central |
| **Animación** | Slide-in desde arriba |
| **Botón cerrar** | Cada notificación tiene botón para cerrar manualmente |
| **Auto-dismiss** | Se cierra automáticamente según duración configurada |
| **Stacking** | Múltiples notificaciones se apilan verticalmente |

### Buenas Prácticas

```typescript
// ❌ MAL: Mensajes genéricos sin usar respuesta del backend
this.notificationService.success('Operación exitosa');

// ✅ CORRECTO: Usar mensaje del backend
this.notificationService.success(result.message || 'Operación exitosa');

// ❌ MAL: No mostrar notificación en errores
if (result.error) {
  console.error(result.error);
}

// ✅ CORRECTO: Siempre notificar al usuario
if (result.error) {
  this.notificationService.error(result.error.message);
}
```

---

## 📊 DataTable con API Paginada (OBLIGATORIO)

Cuando se use el componente `DataTableComponent`, se **DEBE** implementar el flujo completo de paginación siguiendo este patrón.

### Modelos de Paginación

```typescript
// domain/models/common/pagination.model.ts

/**
 * Request para obtener datos paginados
 */
export interface PageRequest {
  page: number;           // Página actual (0-indexed)
  size: number;           // Elementos por página
  sortBy?: string;        // Campo de ordenamiento
  sortDirection?: 'asc' | 'desc';  // Dirección de ordenamiento
  search?: string;        // Búsqueda global
}

/**
 * Response paginado del backend
 */
export interface PageResponse<T> {
  content: T[];           // Datos de la página actual
  page: number;           // Página actual
  size: number;           // Tamaño de página
  totalElements: number;  // Total de elementos
  totalPages: number;     // Total de páginas
  sortBy: string;         // Campo de ordenamiento actual
  sortDirection: string;  // Dirección de ordenamiento actual
  first: boolean;         // ¿Es primera página?
  last: boolean;          // ¿Es última página?
}
```

### Implementación del Gateway

```typescript
// domain/gateways/entity/entity.gateway.ts
import { Observable } from 'rxjs';
import { Result } from '@domain/models/common/api-response.model';
import { PageRequest, PageResponse } from '@domain/models/common/pagination.model';
import { Entity } from '@domain/models/entity/entity.model';

export abstract class EntityGateway {
  abstract getAll(): Observable<Result<Entity[]>>;
  abstract getAllPaginated(request: PageRequest): Observable<Result<PageResponse<Entity>>>;
  abstract getById(id: number): Observable<Result<Entity>>;
  abstract create(data: CreateEntityRequest): Observable<Result<Entity>>;
  abstract update(id: number, data: CreateEntityRequest): Observable<Result<Entity>>;
  abstract delete(id: number): Observable<Result<void>>;
}
```

### Implementación del Adapter

```typescript
// infrastructure/adapters/entity/entity.adapter.ts
import { Injectable, inject } from '@angular/core';
import { Observable } from 'rxjs';
import { map, catchError } from 'rxjs/operators';
import { EntityGateway } from '@domain/gateways/entity/entity.gateway';
import { PageRequest, PageResponse } from '@domain/models/common/pagination.model';
import { Result, success, failure, ApiError } from '@domain/models/common/api-response.model';
import { HttpAdapter } from '@infrastructure/adapters/http/http.adapter';

@Injectable({ providedIn: 'root' })
export class EntityAdapter extends EntityGateway {
  private readonly http = inject(HttpAdapter);

  /**
   * Obtiene entidades paginadas con filtro y ordenamiento
   * @param request Parámetros de paginación
   */
  override getAllPaginated(request: PageRequest): Observable<Result<PageResponse<Entity>>> {
    // Construir query params
    const params = new URLSearchParams();
    params.set('page', request.page.toString());
    params.set('size', request.size.toString());
    if (request.sortBy) params.set('sortBy', request.sortBy);
    if (request.sortDirection) params.set('sortDirection', request.sortDirection);
    if (request.search) params.set('search', request.search);
    
    return this.http.get<PageResponse<Entity>>(`/entities/paginated?${params.toString()}`).pipe(
      map(page => success(page)),
      catchError(error => this.handleError(error))
    );
  }

  private handleError(error: unknown): Observable<Result<never>> {
    const apiError: ApiError = {
      code: 'ENTITY_ERROR',
      message: error instanceof Error ? error.message : 'Error al procesar la solicitud',
      timestamp: new Date()
    };
    return new Observable(subscriber => {
      subscriber.next(failure(apiError));
      subscriber.complete();
    });
  }
}
```

### Implementación del UseCase

```typescript
// domain/use-cases/entity/get-entities-paginated.use-case.ts
import { Injectable, inject } from '@angular/core';
import { Observable, catchError, of } from 'rxjs';
import { EntityGateway } from '@domain/gateways/entity/entity.gateway';
import { PageRequest, PageResponse } from '@domain/models/common/pagination.model';
import { Result, failure } from '@domain/models/common/api-response.model';

@Injectable({ providedIn: 'root' })
export class GetEntitiesPaginatedUseCase {
  private readonly entityGateway = inject(EntityGateway);

  execute(request: PageRequest): Observable<Result<PageResponse<Entity>>> {
    return this.entityGateway.getAllPaginated(request).pipe(
      catchError(error => of(failure({
        code: 'GET_ENTITIES_PAGINATED_ERROR',
        message: error.message || 'Error al obtener entidades paginadas',
        timestamp: new Date()
      })))
    );
  }
}
```

### Uso en Componente de Página

```typescript
// presentation/pages/entities/entities.page.ts
import { Component, signal, computed, inject, OnInit } from '@angular/core';
import { DataTableComponent, TableColumn } from '@presentation/ui/organisms/data-table/data-table.component';
import { GetEntitiesPaginatedUseCase } from '@domain/use-cases/entity/get-entities-paginated.use-case';
import { PageRequest, PageResponse } from '@domain/models/common/pagination.model';
import { NotificationService } from '@infrastructure/services/notification.service';

@Component({
  selector: 'app-entities-page',
  standalone: true,
  imports: [DataTableComponent],
  template: `
    <app-data-table
      [columns]="columns"
      [data]="entities()"
      [loading]="isLoading()"
      [totalItems]="totalItems()"
      [pageSize]="pageSize()"
      [currentPage]="currentPage()"
      (pageChange)="onPageChange($event)"
      (sortChange)="onSortChange($event)"
      (searchChange)="onSearchChange($event)"
    />
  `
})
export class EntitiesPage implements OnInit {
  private readonly getEntitiesUseCase = inject(GetEntitiesPaginatedUseCase);
  private readonly notificationService = inject(NotificationService);

  // Estado reactivo
  readonly entities = signal<Entity[]>([]);
  readonly isLoading = signal(false);
  readonly totalItems = signal(0);
  readonly pageSize = signal(10);
  readonly currentPage = signal(0);
  readonly sortBy = signal('id');
  readonly sortDirection = signal<'asc' | 'desc'>('asc');
  readonly searchTerm = signal('');

  // Configuración de columnas
  readonly columns: TableColumn[] = [
    { key: 'id', label: 'ID', sortable: true },
    { key: 'name', label: 'Nombre', sortable: true },
    { key: 'status', label: 'Estado', sortable: true },
    { key: 'createdAt', label: 'Fecha Registro', sortable: true }
  ];

  ngOnInit(): void {
    this.loadData();
  }

  loadData(): void {
    this.isLoading.set(true);
    
    const request: PageRequest = {
      page: this.currentPage(),
      size: this.pageSize(),
      sortBy: this.sortBy(),
      sortDirection: this.sortDirection(),
      search: this.searchTerm() || undefined
    };

    this.getEntitiesUseCase.execute(request).subscribe({
      next: (result) => {
        this.isLoading.set(false);
        if (result.success) {
          this.entities.set(result.data.content);
          this.totalItems.set(result.data.totalElements);
        } else {
          this.notificationService.error(result.error.message);
        }
      },
      error: (error) => {
        this.isLoading.set(false);
        this.notificationService.error(error.message || 'Error al cargar datos');
      }
    });
  }

  onPageChange(page: number): void {
    this.currentPage.set(page);
    this.loadData();
  }

  onSortChange(event: { column: string; direction: 'asc' | 'desc' }): void {
    this.sortBy.set(event.column);
    this.sortDirection.set(event.direction);
    this.loadData();
  }

  onSearchChange(search: string): void {
    this.searchTerm.set(search);
    this.currentPage.set(0); // Reset to first page on search
    this.loadData();
  }
}
```

### Flujo Completo de Paginación

```
┌─────────────────────────────────────────────────────────────────┐
│  FRONTEND                                                        │
│                                                                  │
│  DataTableComponent ──► Page/UseCase ──► Adapter ──► HTTP       │
│       ↑                                                    │     │
│       │                                                    │     │
│       └────────────── Result<PageResponse<T>> ◄────────────┘     │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│  BACKEND                                                         │
│                                                                  │
│  RouterRest ──► Handler ──► Repository ──► Database             │
│       ↑              │                         │                 │
│       │              ▼                         │                 │
│       └──── ApiResponse<PageResponse<T>> ◄────┘                 │
└─────────────────────────────────────────────────────────────────┘
```

### Query Params del Endpoint

| Parámetro | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| `page` | number | 0 | Página actual (0-indexed) |
| `size` | number | 10 | Elementos por página |
| `sortBy` | string | "id" | Campo de ordenamiento |
| `sortDirection` | string | "asc" | Dirección: "asc" o "desc" |
| `search` | string | - | Búsqueda global (opcional) |

### Ejemplo de Request/Response

```
GET /api/entities/paginated?page=0&size=10&sortBy=name&sortDirection=asc&search=test

Response:
{
  "success": true,
  "status": 200,
  "message": "Elementos encontrados: 25",
  "data": {
    "content": [...],
    "page": 0,
    "size": 10,
    "totalElements": 25,
    "totalPages": 3,
    "sortBy": "name",
    "sortDirection": "asc",
    "first": true,
    "last": false
  }
}
```

### Checklist para Implementar DataTable Paginado

#### Frontend
- [ ] Crear/verificar modelos `PageRequest` y `PageResponse`
- [ ] Agregar método `getAllPaginated` en Gateway
- [ ] Implementar método en Adapter con query params
- [ ] Crear UseCase para obtener datos paginados
- [ ] Implementar estados reactivos en Page (signals)
- [ ] Conectar eventos del DataTable (pageChange, sortChange, searchChange)

#### Backend
- [ ] Crear `PageResponse<T>` DTO
- [ ] Agregar método `findAllPaginated` en Gateway
- [ ] Implementar query paginada en ReactiveRepository
- [ ] Implementar handler con validación de parámetros
- [ ] Agregar ruta `/paginated` en RouterRest

---

## ✅ Checklist de Código

Antes de cada commit, verificar:

- [ ] **Todo el código está en inglés (variables, funciones, clases, comentarios)**
- [ ] Código sigue convenciones de nomenclatura
- [ ] No hay `any` types (excepto casos justificados)
- [ ] Componentes usan `ChangeDetectionStrategy.OnPush`
- [ ] Componentes son standalone
- [ ] Imports organizados correctamente
- [ ] **NO hay archivos `index.ts` (barrel exports)**
- [ ] **Imports apuntan directamente al archivo específico**
- [ ] **Componentes soportan tema claro y oscuro (`.theme-light`)**
- [ ] **Todos los colores usan variables CSS (no hardcoded)**
- [ ] **Formularios tienen validaciones apropiadas por tipo de campo**
- [ ] **Todos los textos visibles están en `es.json` (i18n)**
- [ ] **Notificaciones usan mensajes del backend (`result.message`)**
- [ ] No hay console.log en producción
- [ ] Tests pasan
- [ ] No hay errores de linting
- [ ] Commit message sigue conventional commits

---

## 🔧 Configuración del Editor

### VS Code Extensions Recomendadas

- Angular Language Service
- Prettier
- ESLint
- GitLens
- Angular Snippets

### Settings Recomendados

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.preferences.importModuleSpecifier": "relative"
}
```

---

## 📚 Referencias

- [Angular Style Guide](https://angular.io/guide/styleguide)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/)
- [Clean Code - Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [Atomic Design - Brad Frost](https://bradfrost.com/blog/post/atomic-web-design/)
- [Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/)
