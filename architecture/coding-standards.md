# Atlas Platform - Estándares de Código 📝

## 📋 **Información General**

### Propósito del Documento

Este documento define los estándares de código obligatorios y recomendados para el desarrollo en **Atlas Platform**. Estos estándares garantizan consistencia, legibilidad y mantenibilidad del código, y fueron generados a partir del análisis del código base existente y las mejores prácticas del equipo.

**Audiencia**: Desarrolladores, Code Reviewers, DevOps  
**Última Actualización**: 2026-02-17  
**Estado**: Activo

---

## 🚨 **Estándares Obligatorios**

### 1. Nomenclatura

#### Variables y Funciones

```java
// ✅ CORRECTO - camelCase para variables y métodos
private Long organizationId;
private String visitorName;
private Boolean isActive;

public Mono<VisitRequest> findById(Long id) { ... }
public Mono<AuthToken> execute(AuthCredentials credentials) { ... }

// ❌ INCORRECTO
private Long OrganizationId;    // PascalCase en variable
private String visitor_name;     // snake_case en variable
public Mono<VisitRequest> Find_By_Id(Long id) { ... } // snake_case en método
```

#### Clases y Componentes

| Tipo | Patrón | Ejemplo Real del Proyecto |
|------|--------|---------------------------|
| Domain Model | `<Nombre>` (sin sufijo) | `Vehicle`, `VisitRequest`, `AuthUser`, `Zone` |
| Enum | `<Nombre>` descriptivo | `VisitStatus`, `VehicleType`, `RecurrenceType`, `OwnershipType` |
| Gateway (Interface) | `<Nombre>Repository` | `VisitRequestRepository`, `VehicleRepository`, `CompanyRepository` |
| Use Case | `<Nombre>UseCase` | `LoginUseCase`, `VisitRequestUseCase`, `VehicleUseCase` |
| Entity (BD) | `<Nombre>Entity` | `CompanyEntity`, `VehicleEntity`, `UserUnitEntity` |
| Reactive Repository | `<Nombre>ReactiveRepository` | `VisitRequestReactiveRepository`, `CompanyReactiveRepository` |
| Repository Adapter | `<Nombre>RepositoryAdapter` | `VisitRequestRepositoryAdapter`, `CompanyRepositoryAdapter` |
| Handler | `<Nombre>Handler` | `VisitHandler`, `VehicleHandler`, `CompanyHandler` |
| Router | `<Nombre>RouterRest` | `VisitRouterRest`, `VehicleRouterRest`, `ZoneRouterRest` |
| Request DTO | `<Nombre>Dto` | `VisitRequestDto`, `VisitApprovalDto` |
| Response DTO | `<Nombre>Response` | `VisitRequestResponse`, `ApiResponse<T>` |
| Exception | `<Nombre>Exception` | `BusinessException`, `NotFoundException`, `AuthenticationException` |
| Config | `<Nombre>Config` | `SecurityConfig`, `OpenApiConfig`, `R2dbcConfig` |
| Filter | `<Nombre>Filter` | `TenantFilter`, `JwtAuthenticationFilter` |

#### Archivos y Directorios

```bash
# ✅ CORRECTO - Estructura por feature en Atlas
domain/model/src/main/java/co/com/atlas/model/
├── visit/
│   ├── VisitRequest.java             # Modelo de dominio
│   ├── VisitStatus.java              # Enum
│   ├── VisitApproval.java            # Modelo relacionado
│   ├── RecurrenceType.java           # Enum
│   └── gateways/
│       ├── VisitRequestRepository.java   # Interface gateway
│       └── VisitApprovalRepository.java
├── vehicle/
│   ├── Vehicle.java
│   ├── VehicleType.java
│   └── gateways/
│       └── VehicleRepository.java
├── common/
│   ├── BusinessException.java
│   └── NotFoundException.java

# ❌ INCORRECTO - No mezclar entidades en un mismo paquete
domain/model/src/main/java/co/com/atlas/model/
├── models/           # ❌ No usar capa genérica
│   ├── Visit.java
│   ├── Vehicle.java  # ❌ Mezclado con Visit
│   └── Zone.java
```

#### Métodos

| Tipo | Patrón | Ejemplo Real |
|------|--------|-------------|
| Buscar uno | `findBy<Campo>` | `findById(Long id)`, `findByEmail(String email)` |
| Buscar varios | `findBy<Campo>` (retorna Flux) | `findByOrganizationId(Long orgId)`, `findByUnitId(Long unitId)` |
| Buscar pendientes | `findPendingBy<Contexto>` | `findPendingByOrganization(Long orgId)` |
| Buscar activos | `findActiveBy<Contexto>` | `findActiveByUnit(Long unitId)` |
| Verificar existencia | `existsBy<Campo>` | `existsByUserIdAndUnitId(Long userId, Long unitId)` |
| Guardar | `save` | `save(VisitRequest visitRequest)` |
| Eliminar | `delete` | `delete(Long id)` |
| Ejecutar (Use Case) | `execute` o verbo de dominio | `execute(AuthCredentials credentials)`, `create(VisitRequest request, Long userId)` |
| Mapeo a dominio | `toDomain` | `toDomain(VisitRequestEntity entity)` |
| Mapeo a entidad | `toEntity` | `toEntity(VisitRequest domain)` |
| Validación privada | `validate<Acción>` | `validateUserAndPassword(AuthUser user, String password)` |

### 2. Estructura de Código

#### Arquitectura Clean Architecture (Bancolombia)

```
atlas/
├── applications/
│   └── app-service/                    # Punto de entrada, ensamblaje de beans
├── domain/
│   ├── model/                          # Modelos, enums, gateways (interfaces)
│   └── usecase/                        # Lógica de negocio
└── infrastructure/
    ├── driven-adapters/
    │   ├── r2dbc-postgresql/           # Implementación BD (R2DBC + Flyway)
    │   └── notification/              # Adaptador de notificaciones
    ├── entry-points/
    │   └── reactive-web/              # API REST WebFlux (Handlers + Routers)
    └── helpers/
        ├── jwt-helper/                # Utilidades JWT
        └── tenant-context/            # Contexto multi-tenant
```

#### Flujo de Datos

```
API Request
    ↓
┌─────────────────────────────────────────────────────────────┐
│  ENTRY POINTS (reactive-web)                                │
│  ├── Handler (recibe request, valida formato, delega)       │
│  └── RouterRest (define rutas + documentación OpenAPI)      │
└─────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────┐
│  DOMAIN                                                      │
│  ├── Use Case (lógica de negocio, validaciones de dominio)  │
│  ├── Model (entidades de dominio - POJOs con Lombok)        │
│  └── Gateway Interface (contratos en model/<feature>/gateways/) │
└─────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────┐
│  INFRASTRUCTURE (driven-adapters)                            │
│  ├── RepositoryAdapter (implementa Gateway, contiene mapeo) │
│  ├── Entity (mapeo @Table con R2DBC)                        │
│  └── ReactiveRepository (Spring Data R2DBC)                 │
└─────────────────────────────────────────────────────────────┘
    ↓
MySQL 8.0 (R2DBC reactivo)
```

#### Restricciones de la Capa de Dominio (domain/)

**La capa de dominio DEBE mantenerse limpia de dependencias externas.**

| Prohibido en Dominio | Permitido en Dominio |
|---------------------|---------------------|
| ❌ `@Slf4j` (Lombok) | ✅ `@Getter`, `@Setter`, `@Builder`, `@RequiredArgsConstructor` |
| ❌ `@Component`, `@Service` (Spring) | ✅ `@AllArgsConstructor` |
| ❌ `org.slf4j.*` | ✅ `java.util.logging.Logger` (si necesario) |
| ❌ Imports de `infrastructure.*` | ✅ `reactor.core.publisher.Mono`, `Flux` |
| ❌ Imports de `r2dbc.*`, `api.*` | ✅ Interfaces Gateway en `model/<feature>/gateways/` |

#### Organización de Imports

```java
// ✅ CORRECTO - Orden de imports en Atlas
// 1. Imports del dominio (propios)
import co.com.atlas.model.visit.VisitRequest;
import co.com.atlas.model.visit.VisitStatus;
import co.com.atlas.model.visit.gateways.VisitRequestRepository;
import co.com.atlas.model.common.BusinessException;

// 2. Imports de Lombok
import lombok.RequiredArgsConstructor;

// 3. Imports de Reactor
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

// 4. Imports de Java estándar
import java.time.Instant;
import java.util.UUID;
```

#### Estructura de Modelos de Dominio

```java
// ✅ CORRECTO - Modelo de dominio en Atlas
package co.com.atlas.model.vehicle;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.Setter;

import java.time.Instant;

/**
 * Modelo de dominio para Vehículo.
 * Un vehículo pertenece a una vivienda (Unit) dentro de una organización.
 */
@Getter
@Setter
@AllArgsConstructor
@Builder(toBuilder = true)
public class Vehicle {
    private Long id;
    private Long unitId;
    private Long organizationId;
    private String plate;
    private VehicleType vehicleType;
    private String brand;
    private String model;
    private String color;
    private String ownerName;
    private Boolean isActive;
    private Long registeredBy;
    private String notes;
    private Instant createdAt;
    private Instant updatedAt;
    private Instant deletedAt;
}
```

**Reglas para modelos de dominio:**
- Siempre usar `@Getter`, `@Setter`, `@AllArgsConstructor`, `@Builder(toBuilder = true)`
- Usar `Long` para IDs (no `Integer`)
- Usar `Instant` para timestamps (`createdAt`, `updatedAt`, `deletedAt`)
- Usar enums propios para estados y tipos (`VisitStatus`, `VehicleType`)
- **NO** usar `@Data` en dominio (reservado para entities de infraestructura)
- Incluir Javadoc descriptivo en la clase

#### Estructura de Use Cases

```java
// ✅ CORRECTO - Use Case en Atlas
package co.com.atlas.usecase.visit;

import co.com.atlas.model.visit.VisitRequest;
import co.com.atlas.model.visit.VisitStatus;
import co.com.atlas.model.visit.gateways.VisitRequestRepository;
import co.com.atlas.model.common.BusinessException;
import co.com.atlas.model.common.NotFoundException;
import lombok.RequiredArgsConstructor;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

/**
 * Caso de uso para gestión de solicitudes de visita.
 */
@RequiredArgsConstructor
public class VisitRequestUseCase {
    
    private final VisitRequestRepository visitRequestRepository;
    // ... otros gateways inyectados
    
    public Mono<VisitRequest> create(VisitRequest request, Long requestedByUserId) {
        // 1. Validar existencia de entidades referenciadas
        // 2. Validar permisos del usuario
        // 3. Validar reglas de negocio
        // 4. Construir entidad y guardar
        return unitRepository.findById(request.getUnitId())
                .switchIfEmpty(Mono.error(new NotFoundException("Unit", request.getUnitId())))
                .flatMap(unit -> {
                    // Lógica de negocio...
                    VisitRequest newRequest = request.toBuilder()
                            .requestedBy(requestedByUserId)
                            .status(VisitStatus.PENDING)
                            .build();
                    return visitRequestRepository.save(newRequest);
                });
    }
}
```

**Reglas para Use Cases:**
- Solo `@RequiredArgsConstructor` — sin anotaciones de Spring
- Inyección de gateways (interfaces), nunca implementaciones concretas
- Retornar siempre `Mono<T>` o `Flux<T>` (programación reactiva)
- Validaciones de negocio en el use case, NO en handler ni adapter
- Usar `BusinessException` para errores de lógica de negocio
- Usar `NotFoundException` para entidades no encontradas
- Usar `.switchIfEmpty(Mono.error(...))` para manejar ausencia de datos

#### Estructura de Handlers (Entry Points)

```java
// ✅ CORRECTO - Handler en Atlas
package co.com.atlas.api.visit;

import co.com.atlas.api.common.dto.ApiResponse;
import co.com.atlas.api.visit.dto.VisitRequestDto;
import co.com.atlas.usecase.visit.VisitRequestUseCase;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.stereotype.Component;
import org.springframework.web.reactive.function.server.ServerRequest;
import org.springframework.web.reactive.function.server.ServerResponse;
import reactor.core.publisher.Mono;

@Component
@RequiredArgsConstructor
@Slf4j
public class VisitHandler {

    private final VisitRequestUseCase visitRequestUseCase;

    public Mono<ServerResponse> create(ServerRequest request) {
        return request.bodyToMono(VisitRequestDto.class)
                .flatMap(req -> {
                    // Mapear DTO → Dominio
                    VisitRequest visitRequest = VisitRequest.builder()
                            .unitId(req.getUnitId())
                            .visitorName(req.getVisitorName())
                            .build();
                    Long requestedBy = extractUserIdFromRequest(request);
                    return visitRequestUseCase.create(visitRequest, requestedBy);
                })
                .flatMap(this::buildSuccessResponse)
                .onErrorResume(BusinessException.class, 
                    e -> buildErrorResponse(e, HttpStatus.BAD_REQUEST, request.path()))
                .onErrorResume(NotFoundException.class, 
                    e -> buildErrorResponse(e, HttpStatus.NOT_FOUND, request.path()));
    }
}
```

**Reglas para Handlers:**
- `@Component`, `@RequiredArgsConstructor`, `@Slf4j`
- Inyectar Use Cases, NO repositorios directamente
- Recibir `ServerRequest`, retornar `Mono<ServerResponse>`
- Mapear DTO → Domain model antes de delegar al use case
- Usar `ApiResponse<T>` como envoltorio estándar de respuesta
- Manejar errores con `.onErrorResume()` para `BusinessException` y `NotFoundException`
- **NO** contener lógica de negocio

#### Estructura de Routers

```java
// ✅ CORRECTO - Router con documentación OpenAPI en Atlas
@Configuration
@Tag(name = "Visits", description = "Gestión de solicitudes de visita")
public class VisitRouterRest {

    @Bean
    @RouterOperations({
            @RouterOperation(
                    path = "/api/visits",
                    method = RequestMethod.POST,
                    beanClass = VisitHandler.class,
                    beanMethod = "create",
                    operation = @Operation(
                            operationId = "createVisitRequest",
                            summary = "Crear solicitud de visita",
                            tags = {"Visits"},
                            requestBody = @RequestBody(
                                    required = true,
                                    content = @Content(schema = @Schema(implementation = VisitRequestDto.class))
                            ),
                            responses = {
                                    @ApiResponse(responseCode = "200", description = "Solicitud creada"),
                                    @ApiResponse(responseCode = "400", description = "Error de validación")
                            }
                    )
            )
    })
    public RouterFunction<ServerResponse> visitRoutes(VisitHandler handler) {
        return route(POST("/api/visits"), handler::create)
                .andRoute(GET("/api/visits/{id}"), handler::getById);
    }
}
```

**Reglas para Routers:**
- `@Configuration` + `@Tag` (OpenAPI)
- Documentar TODOS los endpoints con `@RouterOperations`
- Usar `@Bean` para el método que retorna `RouterFunction<ServerResponse>`
- Prefijo de rutas: `/api/<feature>`
- Incluir `@Operation` con `operationId`, `summary`, `requestBody` y `responses`

#### Estructura de Entities (R2DBC)

```java
// ✅ CORRECTO - Entity de BD en Atlas
package co.com.atlas.r2dbc.company;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.Id;
import org.springframework.data.relational.core.mapping.Column;
import org.springframework.data.relational.core.mapping.Table;

import java.time.Instant;

@Data
@Builder(toBuilder = true)
@NoArgsConstructor
@AllArgsConstructor
@Table("company")
public class CompanyEntity {
    
    @Id
    private Long id;
    
    private String name;
    
    @Column("tax_id")
    private String taxId;
    
    @Column("is_active")
    private Boolean isActive;
    
    @Column("created_at")
    private Instant createdAt;
    
    @Column("updated_at")
    private Instant updatedAt;
    
    @Column("deleted_at")
    private Instant deletedAt;
}
```

**Reglas para Entities:**
- Usar `@Data`, `@Builder(toBuilder = true)`, `@NoArgsConstructor`, `@AllArgsConstructor`
- `@Table("nombre_tabla")` con el nombre exacto de la tabla en BD
- `@Id` para la clave primaria
- `@Column("nombre_columna")` para columnas que NO coinciden con el nombre del campo
- Mapeo de columnas: `snake_case` en BD → `camelCase` en Java
- Usar `Instant` para campos de timestamp

#### Estructura de Repository Adapters

```java
// ✅ CORRECTO - Adapter con mapeo inline en Atlas
@Repository
@RequiredArgsConstructor
public class VisitRequestRepositoryAdapter implements VisitRequestRepository {

    private final VisitRequestReactiveRepository repository;

    @Override
    public Mono<VisitRequest> findById(Long id) {
        return repository.findById(id)
                .map(this::toDomain);
    }

    @Override
    public Mono<VisitRequest> save(VisitRequest visitRequest) {
        VisitRequestEntity entity = toEntity(visitRequest);
        if (entity.getCreatedAt() == null) {
            entity.setCreatedAt(Instant.now());
        }
        entity.setUpdatedAt(Instant.now());
        return repository.save(entity)
                .map(this::toDomain);
    }

    // Mapeo inline (toDomain y toEntity como métodos privados del adapter)
    private VisitRequest toDomain(VisitRequestEntity entity) {
        return VisitRequest.builder()
                .id(entity.getId())
                .organizationId(entity.getOrganizationId())
                // ... mapeo de todos los campos
                .build();
    }

    private VisitRequestEntity toEntity(VisitRequest domain) {
        return VisitRequestEntity.builder()
                .id(domain.getId())
                .organizationId(domain.getOrganizationId())
                // ... mapeo de todos los campos
                .build();
    }
}
```

**Reglas para Adapters:**
- `@Repository`, `@RequiredArgsConstructor`
- Implementar la interfaz Gateway del dominio
- Incluir métodos `toDomain()` y `toEntity()` como métodos privados
- Asignar `createdAt` y `updatedAt` automáticamente en `save()`
- Inyectar SOLO su propio `ReactiveRepository`
- Si necesita datos de otra entidad, inyectar el **Adapter** de dicha entidad (NO su ReactiveRepository)

### 3. Manejo de Errores

#### Excepciones de Dominio

```java
// ✅ CORRECTO - BusinessException con código de error y HTTP status
public class BusinessException extends RuntimeException {
    private final String errorCode;
    private final int httpStatus;
    
    public BusinessException(String message) {
        super(message);           // httpStatus = 400 por defecto
        this.errorCode = "BUSINESS_ERROR";
        this.httpStatus = 400;
    }
    
    public BusinessException(String message, String errorCode) {
        super(message);
        this.errorCode = errorCode;
        this.httpStatus = 400;
    }
    
    public BusinessException(String message, String errorCode, int httpStatus) {
        super(message);
        this.errorCode = errorCode;
        this.httpStatus = httpStatus;
    }
}

// ✅ Uso en Use Cases
return Mono.error(new BusinessException(
    "No tienes permisos para crear visitas en esta unidad",
    "NO_PERMISSION", 403));

return Mono.error(new NotFoundException("Unit", request.getUnitId()));
```

#### Manejo Global de Errores

```java
// ✅ CORRECTO - GlobalErrorWebExceptionHandler captura TODAS las excepciones
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class GlobalErrorWebExceptionHandler implements WebExceptionHandler {
    // Captura BusinessException, NotFoundException, IllegalArgumentException
    // y retorna respuestas JSON estandarizadas
}
```

**Reglas de manejo de errores:**
- Crear excepciones de dominio para errores de negocio (`BusinessException`, `NotFoundException`)
- Incluir `errorCode` descriptivo (e.g., `"NO_PERMISSION"`, `"INVALID_DATE_RANGE"`)
- En Handlers: usar `.onErrorResume()` para captura local
- El `GlobalErrorWebExceptionHandler` captura errores no manejados
- **NUNCA** retornar excepciones sin envolver en `ApiResponse`

### 4. Comentarios y Documentación

```java
// ✅ CORRECTO - Javadoc en clases públicas
/**
 * Modelo de dominio para VisitRequest (Solicitudes de visita).
 */
@Getter
@Setter
@AllArgsConstructor
@Builder(toBuilder = true)
public class VisitRequest { ... }

// ✅ CORRECTO - Javadoc en métodos públicos de Use Case
/**
 * Crea una nueva solicitud de visita.
 */
public Mono<VisitRequest> create(VisitRequest request, Long requestedByUserId) { ... }

// ✅ CORRECTO - Javadoc en métodos gateway
/**
 * Busca una solicitud por ID.
 */
Mono<VisitRequest> findById(Long id);

// ✅ CORRECTO - Comentarios en lógica compleja
// Obtener la organización a usar (lastOrganizationId o primera activa)
Mono<Long> organizationIdMono = user.getLastOrganizationId() != null
        ? Mono.just(user.getLastOrganizationId())
        : userOrganizationRepository.findActiveByUserId(user.getId())
                .next()
                .map(uo -> uo.getOrganizationId())
                .defaultIfEmpty(0L);
```

**Reglas de documentación:**
- Javadoc obligatorio en: clases de dominio, interfaces gateway, métodos públicos de use cases
- Javadoc con `@Schema` obligatorio en DTOs de API (SpringDoc OpenAPI)
- Comentarios de línea para lógica de negocio no trivial
- Documentar restricciones y validaciones en use cases

---

## 💡 **Convenciones Recomendadas**

### 1. Organización de Archivos

```
domain/model/src/main/java/co/com/atlas/model/
├── auth/                          # Feature: Autenticación
│   ├── AuthUser.java
│   ├── AuthCredentials.java
│   ├── AuthToken.java
│   └── gateways/
│       ├── AuthUserRepository.java
│       └── JwtTokenGateway.java
├── visit/                         # Feature: Visitas
│   ├── VisitRequest.java
│   ├── VisitApproval.java
│   ├── VisitStatus.java
│   ├── RecurrenceType.java
│   ├── ApprovalAction.java
│   └── gateways/
│       ├── VisitRequestRepository.java
│       └── VisitApprovalRepository.java
├── common/                        # Excepciones y utilidades compartidas
│   ├── BusinessException.java
│   └── NotFoundException.java

domain/usecase/src/main/java/co/com/atlas/usecase/
├── auth/
│   ├── LoginUseCase.java
│   └── AuthenticationException.java
├── visit/
│   └── VisitRequestUseCase.java
├── vehicle/
│   └── VehicleUseCase.java

infrastructure/driven-adapters/r2dbc-postgresql/src/main/java/co/com/atlas/r2dbc/
├── company/                       # Paquete por entidad
│   ├── CompanyEntity.java
│   ├── CompanyReactiveRepository.java
│   └── CompanyRepositoryAdapter.java
├── visit/
│   ├── VisitRequestEntity.java
│   ├── VisitRequestReactiveRepository.java
│   └── VisitRequestRepositoryAdapter.java

infrastructure/entry-points/reactive-web/src/main/java/co/com/atlas/api/
├── visit/                         # Paquete por feature
│   ├── VisitHandler.java
│   ├── VisitRouterRest.java
│   └── dto/
│       ├── VisitRequestDto.java
│       └── VisitRequestResponse.java
├── common/
│   └── dto/
│       ├── ApiResponse.java
│       └── ErrorResponse.java
├── config/
│   ├── SecurityConfig.java
│   ├── JwtAuthenticationFilter.java
│   ├── TenantFilter.java
│   ├── GlobalErrorWebExceptionHandler.java
│   └── OpenApiConfig.java
```

### 2. Patrones de Código

#### Builder Pattern (con toBuilder)

```java
// ✅ RECOMENDADO - Usar toBuilder() para crear copias modificadas
VisitRequest newRequest = request.toBuilder()
        .requestedBy(requestedByUserId)
        .organizationId(unit.getOrganizationId())
        .status(VisitStatus.PENDING)
        .build();
```

#### Operadores Reactivos

```java
// ✅ RECOMENDADO - Encadenar operaciones reactivas correctamente
return repository.findById(id)
        .switchIfEmpty(Mono.error(new NotFoundException("Entity", id)))
        .flatMap(entity -> validatePermissions(entity, userId))
        .flatMap(entity -> processBusinessLogic(entity))
        .map(this::toDomain);

// ❌ INCORRECTO - Bloquear dentro de flujos reactivos
return repository.findById(id)
        .map(entity -> {
            // ❌ NUNCA block() dentro de un flujo reactivo
            var result = otherService.process(entity).block();
            return result;
        });
```

#### Respuesta API Estándar

```java
// ✅ RECOMENDADO - Usar ApiResponse como envoltorio
public static <T> ApiResponse<T> success(T data, String message) {
    return ApiResponse.<T>builder()
            .success(true)
            .status(200)
            .message(message)
            .data(data)
            .build();
}
```

### 3. Multi-Tenancy

```java
// ✅ CORRECTO - Uso del TenantContext
// En TenantFilter (se ejecuta automáticamente por request):
TenantContext.setOrganizationId(organizationId);
TenantContext.setUserId(userId);

// En adapters/use cases:
Long orgId = TenantContext.getOrganizationId();

// ⚠️ CRÍTICO - Siempre limpiar al final del request
TenantContext.clear();  // En doFinally() del filter
```

---

## 🔧 **Configuración de Herramientas**

### Lombok Configuration

```properties
# lombok.config (raíz del proyecto)
config.stopBubbling = true
lombok.addLombokGeneratedAnnotation = true
```

### Gradle Build Configuration

```gradle
// build.gradle - Versiones principales
ext {
    cleanArchitectureVersion = '4.0.5'
    springBootVersion = '4.0.1'
    sonarVersion = '7.2.2.6593'
    jacocoVersion = '0.8.14'
    pitestVersion = '1.19.0-rc.2'
    lombokVersion = '1.18.42'
}
```

### Java Toolchain

```gradle
// main.gradle
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(21)
    }
}
```

### SonarQube Configuration

```gradle
sonar {
    properties {
        property "sonar.sourceEncoding", "UTF-8"
        property "sonar.exclusions", "**/MainApplication.java"
        property "sonar.java.coveragePlugin", "jacoco"
        property "sonar.coverage.jacoco.xmlReportPaths", 
            "build/reports/jacocoMergedReport/jacocoMergedReport.xml"
        property "sonar.pitest.reportPaths", "build/reports/pitest/mutations.xml"
    }
}
```

### Scripts de Build

```bash
# Compilar proyecto completo
./gradlew build

# Ejecutar en desarrollo
./gradlew bootRun

# Ejecutar tests
./gradlew test

# Módulo específico
./gradlew :reactive-web:build
./gradlew :usecase:build
./gradlew :r2dbc-postgresql:build

# Producción
./gradlew build
java -jar applications/app-service/build/libs/AtlasPlatform.jar
```

---

## 🧪 **Estándares de Testing**

### Nomenclatura de Tests

```java
// ✅ CORRECTO - Tests con JUnit 5 + WebTestClient
@ContextConfiguration(classes = {RouterRest.class, Handler.class})
@WebFluxTest
class RouterRestTest {

    @Autowired
    private WebTestClient webTestClient;

    @Test
    void testListenGETUseCase() {
        webTestClient.get()
                .uri("/api/usecase/path")
                .accept(MediaType.APPLICATION_JSON)
                .exchange()
                .expectStatus().isOk()
                .expectBody(String.class)
                .value(userResponse -> {
                    Assertions.assertThat(userResponse).isEmpty();
                });
    }
}
```

### Herramientas de Testing

| Herramienta | Propósito | Configuración |
|-------------|-----------|---------------|
| **JUnit 5** | Framework de testing | `useJUnitPlatform()` |
| **WebTestClient** | Tests de API WebFlux | `@WebFluxTest` |
| **AssertJ** | Aserciones fluidas | `Assertions.assertThat()` |
| **Reactor Test** | Tests de flujos reactivos | `StepVerifier` |
| **BlockHound** | Detección de llamadas bloqueantes | `blockhound-junit-platform` |
| **JaCoCo** | Cobertura de código | Reporte XML + HTML |
| **PITest** | Mutation testing | `pitestVersion = '1.19.0-rc.2'` |

### Estructura de Tests

```java
// ✅ PATRÓN recomendado para tests reactivos
@Test
void shouldCreateVisitRequestSuccessfully() {
    // Arrange
    VisitRequest request = VisitRequest.builder()
            .unitId(1L)
            .visitorName("Juan Pérez")
            .build();

    // Act & Assert con StepVerifier
    StepVerifier.create(visitRequestUseCase.create(request, 1L))
            .assertNext(result -> {
                assertThat(result.getStatus()).isEqualTo(VisitStatus.PENDING);
                assertThat(result.getRequestedBy()).isEqualTo(1L);
            })
            .verifyComplete();
}
```

---

## 📊 **Métricas y Calidad**

### Umbrales de Calidad

| Métrica                     | Umbral Mínimo | Herramienta |
| --------------------------- | ------------- | ----------- |
| **Cobertura de Tests**      | ≥ 70%         | JaCoCo      |
| **Mutation Score**          | ≥ 60%         | PITest      |
| **Duplicación de Código**   | < 3%          | SonarQube   |
| **Code Smells**             | < 50          | SonarQube   |
| **Vulnerabilidades**        | 0 críticas    | SonarQube   |

### Code Review Checklist

- [ ] ✅ Nomenclatura sigue las convenciones del proyecto
- [ ] ✅ Modelo de dominio limpio (sin dependencias de infraestructura)
- [ ] ✅ Use Case contiene la lógica de negocio (no el handler ni adapter)
- [ ] ✅ Manejo de errores con `BusinessException` / `NotFoundException`
- [ ] ✅ Programación reactiva sin `.block()` ni operaciones bloqueantes
- [ ] ✅ MultiTenancy: datos filtrados por `organizationId`
- [ ] ✅ Router documentado con `@RouterOperations` (OpenAPI)
- [ ] ✅ Respuestas envueltas en `ApiResponse<T>`
- [ ] ✅ Sin código duplicado
- [ ] ✅ Javadoc en clases y métodos públicos
- [ ] ✅ Migración Flyway creada si hay cambios de esquema
- [ ] ✅ Tests cubren casos principales y de error

---

## 🚀 **Mejores Prácticas Específicas**

### Spring WebFlux (Reactivo)

- **NUNCA** usar `.block()` dentro de un flujo reactivo — causa deadlocks
- Usar `Mono<T>` para operaciones que retornan 0 o 1 resultado
- Usar `Flux<T>` para operaciones que retornan múltiples resultados
- Usar `.switchIfEmpty()` para manejar resultados vacíos
- Usar `.flatMap()` para operaciones asíncronas encadenadas
- Usar `.map()` solo para transformaciones síncronas
- Usar `.onErrorResume()` para manejo de errores por tipo
- Usar `TenantContext` en un `doFinally()` para limpiar contexto

### R2DBC y Base de Datos

- Usar `ReactiveCrudRepository` como base para repositorios
- Usar `@Query` con SQL nativo para consultas complejas
- Soft delete: filtrar por `deleted_at IS NULL` en consultas
- Convención de columnas: `snake_case` en BD → `camelCase` en Java (mapeo con `@Column`)
- IDs como `Long` (compatible con auto-increment de MySQL)
- Timestamps como `Instant` (UTC)

### Flyway (Migraciones)

- Formato de nombre: `V<N>__<descripción_en_snake_case>.sql`
- Cada migración es idempotente cuando sea posible
- **NUNCA** modificar una migración ya aplicada
- Crear nueva migración para cambios incrementales
- Revisar migraciones existentes ANTES de crear nuevas entidades

### JWT y Seguridad

- Tokens JWT contienen `userId`, `organizationId`, `roles` y `permissions`
- `JwtAuthenticationFilter` valida y extrae datos del token
- `TenantFilter` establece el contexto de organización
- `SecurityConfig` define endpoints públicos y protegidos
- Passwords hasheados con BCrypt

### Performance

- Activar `org.gradle.parallel=true` y `org.gradle.caching=true` para builds
- Usar `org.gradle.configuration-cache=true` para reducir tiempos de configuración
- En R2DBC: usar queries derivadas cuando sea posible (evitar SQL raw innecesario)
- Usar `Flux.collectList()` con precaución en conjuntos grandes de datos
- Filtrar datos en la query SQL, no en Java

### Seguridad

- Validar permisos de usuario en Use Cases ANTES de realizar operaciones
- Filtrar datos siempre por `organizationId` (multi-tenancy)
- Usar `@Order(Ordered.HIGHEST_PRECEDENCE)` para el handler global de errores
- No exponer detalles internos de errores al cliente
- Ejecutar contenedores Docker con usuario no-root (`USER appuser`)
- Variables sensibles (DB_PASSWORD, JWT_SECRET) como variables de entorno

---

## 🔄 **Proceso de Actualización**

### Cuándo Actualizar estos Estándares

1. **Cambios de tecnología** - Nuevas versiones de Spring Boot, Java o dependencias principales
2. **Lecciones aprendidas** - Problemas recurrentes identificados en code review
3. **Revisión trimestral** - Evaluación periódica de efectividad de los estándares
4. **Onboarding feedback** - Comentarios de nuevos desarrolladores

### Proponer Cambios

1. Crear issue/ticket describiendo la propuesta con justificación
2. Discutir en revisión de equipo
3. Implementar en una rama de prueba para validar el impacto
4. Actualizar este documento con los nuevos estándares
5. Comunicar cambios al equipo

---

## 📚 **Referencias y Recursos**

### Documentación Oficial

- [Spring Boot 4.x Reference](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Spring WebFlux Guide](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html)
- [Project Reactor Reference](https://projectreactor.io/docs/core/release/reference/)
- [Spring Data R2DBC](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/)
- [Clean Architecture - Bancolombia](https://medium.com/bancolombia-tech/clean-architecture-aislando-los-detalles-4f9530f35d7a)
- [Flyway Documentation](https://documentation.red-gate.com/flyway)
- [SpringDoc OpenAPI](https://springdoc.org/)
- [Lombok](https://projectlombok.org/)

### Herramientas del Proyecto

- **Build**: Gradle 9.2.1 + Clean Architecture Plugin 4.0.5
- **Calidad**: SonarQube 7.2.2
- **Cobertura**: JaCoCo 0.8.14
- **Mutation Testing**: PITest 1.19.0-rc.2
- **CI/CD**: Docker (eclipse-temurin:21-jdk-alpine)

---

**NOTA IMPORTANTE**: Estos estándares fueron generados analizando el código base existente del proyecto Atlas Platform (92 modelos de dominio, 24 use cases, 101 archivos de entry points, 84 archivos de driven adapters) y las prácticas actuales del equipo. Deben evolucionar con el proyecto y ser revisados periódicamente.

---

_Documento generado con Método Ceiba - Arquitecto_  
_Última actualización: 2026-02-17_  
_Versión: 1.0_
