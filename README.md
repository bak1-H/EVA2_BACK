# Innovatech Backend

Repositorio con los dos microservicios Spring Boot de Innovatech Chile, desplegados como servicios independientes en el mismo clúster ECS Fargate (`innovatech-cluster`):

| Microservicio | Directorio | Puerto interno | Path en el ALB |
|---|---|---|---|
| Ventas | `back-Ventas_SpringBoot/Springboot-API-REST` | 8080 | `/api/v1/ventas*` (y default junto al frontend) |
| Despachos | `back-Despachos_SpringBoot/Springboot-API-REST-DESPACHO` | 8080 | `/api/v1/despachos*` |

Cada microservicio tiene su propio `Dockerfile`, workflow de CI/CD y task definition de ECS — se despliegan por separado.

## Arquitectura

```
GitHub push → GitHub Actions → mvnw test → docker build → ECR
                                                  ↓
                              ECS Fargate (innovatech-cluster)
                              ├─ backend-service  (Ventas)
                              └─ despachos-service (Despachos)
                                                  ↓
                              RDS MySQL (innovatech-mysql, us-east-1)
```

Ambos servicios comparten la misma instancia RDS MySQL, con bases de datos separadas (`ventas_db`, `despacho_db`).

## Levantar en local

```bash
DB_HOST=<host-mysql-local> docker-compose up --build
```

Expone Ventas en `localhost:8081` y Despachos en `localhost:8082` (ver `docker-compose.yml`).

## Pipelines CI/CD

Cada microservicio tiene su propio workflow en `.github/workflows/`:

- **`deploy.yml`** (Ventas): checkout → JDK 17 → `mvnw test` (perfil H2 en memoria) → build & push a ECR (`innovatech-backend`) → force-new-deployment en `backend-service`.
- **`deploy-despachos.yml`** (Despachos): checkout → JDK 17 → `mvnw test` (perfil H2 en memoria) → build & push a ECR (`innovatech-despachos`) → registro de task definition + creación/actualización de infraestructura ECS (target group, regla ALB, servicio) si no existe.

## Secretos

Gestionados vía GitHub Secrets del repositorio: credenciales AWS (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`, `AWS_REGION`) y `DESPACHOS_DB_PASSWORD` para la contraseña de la base de datos usada en la task definition de Despachos.

## Repos relacionados

- Frontend: [EVA2_FRONT](https://github.com/bak1-H/EVA2_FRONT)
