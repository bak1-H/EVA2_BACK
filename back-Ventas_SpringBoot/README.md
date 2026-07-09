# Ventas — Springboot-API-REST

Microservicio Spring Boot para la gestión de ventas de Innovatech Chile.

## Endpoints (`/api/v1/ventas`)

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/v1/ventas` | Crear una venta |
| GET | `/api/v1/ventas` | Listar todas las ventas |
| GET | `/api/v1/ventas/{idVenta}` | Obtener una venta por ID |
| PUT | `/api/v1/ventas/{idVenta}` | Actualizar una venta |
| DELETE | `/api/v1/ventas/{idVenta}` | Eliminar una venta |

Documentación interactiva (Swagger/OpenAPI) en `/swagger-ui.html`.

## Stack

Java 17, Spring Boot 3.4.4, Spring Data JPA, MySQL (runtime) / H2 (test), Lombok, springdoc-openapi.

## Correr en local

```bash
cd Springboot-API-REST
./mvnw spring-boot:run
```

Requiere un MySQL accesible en `spring.datasource.url` (ver `src/main/resources/application.properties`) o levantarlo vía `docker-compose.yml` en la raíz del repo.

## Tests

```bash
./mvnw test
```

Usa el perfil `test` (`application-test.properties`) con H2 en memoria — no depende de una base de datos externa. Incluye pruebas de contexto (`SpringbootApiRestApplicationTests`) y pruebas unitarias de servicio con Mockito (`VentaServiceTest`).

## Contenedor

`Dockerfile` multietapa: build con Maven sobre `eclipse-temurin:17-jdk-alpine`, runtime sobre `eclipse-temurin:17-jre-alpine` corriendo como usuario no-root (`apiventas`).
