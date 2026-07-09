# Despachos — Springboot-API-REST-DESPACHO

Microservicio Spring Boot para la gestión de despachos de Innovatech Chile.

## Endpoints (`/api/v1/despachos`)

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/v1/despachos` | Crear un despacho |
| GET | `/api/v1/despachos` | Listar todos los despachos |
| GET | `/api/v1/despachos/{idDespacho}` | Obtener un despacho por ID |
| PUT | `/api/v1/despachos/{idDespacho}` | Actualizar un despacho |
| DELETE | `/api/v1/despachos/{idDespacho}` | Eliminar un despacho |

Documentación interactiva (Swagger/OpenAPI) en `/swagger-ui.html`.

## Stack

Java 17, Spring Boot 3.4.4, Spring Data JPA, MySQL (runtime) / H2 (test), Lombok, springdoc-openapi.

## Correr en local

```bash
cd Springboot-API-REST-DESPACHO
./mvnw spring-boot:run
```

Requiere un MySQL accesible en `spring.datasource.url` (ver `src/main/resources/application.properties`) o levantarlo vía `docker-compose.yml` en la raíz del repo.

## Tests

```bash
./mvnw test
```

Usa el perfil `test` (`application-test.properties`) con H2 en memoria — no depende de una base de datos externa.

## Contenedor

`Dockerfile` multietapa: build con Maven sobre `eclipse-temurin:17-jdk`, runtime sobre `eclipse-temurin:17-jre` corriendo como usuario no-root (`apidespacho`).

## Despliegue

A diferencia de Ventas, el workflow de este servicio (`deploy-despachos.yml`) también provisiona la infraestructura ECS si no existe: task definition, target group (`tg-despachos`), regla de path routing en el ALB, y el servicio ECS. La contraseña de la base de datos se inyecta desde el secreto `DESPACHOS_DB_PASSWORD` de GitHub Actions.
