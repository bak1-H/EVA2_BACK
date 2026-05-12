
# ETAPA 1: Builder (Compilación)
FROM eclipse-temurin:17-jdk-alpine AS builder
WORKDIR /app
# 1. Copiar el wrapper de Maven y dependencias primero para cachear capa
COPY .mvn/ .mvn
COPY mvnw pom.xml ./

RUN tr -d '\r' < mvnw > mvnw_unix && \
    mv mvnw_unix mvnw && \
    chmod +x mvnw
    
# Descargar dependencias 
RUN ./mvnw dependency:go-offline -B
# 2. Copiar código fuente y compilar
COPY src ./src
RUN ./mvnw clean package -DskipTests -B

# ETAPA 2: Runtime (Ejecución)
# Usamos JRE en lugar de JDK para reducir el tamaño (de ~500MB a ~150MB)
FROM eclipse-temurin:17-jre-alpine
# Crear un usuario de sistema para no ejecutar como root (Seguridad EC2)
RUN addgroup -S apiventas && adduser -S apiventas -G apiventas
USER apiventas:apiventas

WORKDIR /app

# Puerto interno por defecto del contenedor
ENV SERVER_PORT=8081

# Copiar solo el JAR resultante desde la etapa anterior
# Buscamos el jar que no sea el '-sources.jar' o '-javadoc.jar'
COPY --from=builder /app/target/*.jar app.jar

# Puerto estándar de Spring Boot
EXPOSE 8081
# Usamos ENTRYPOINT para permitir pasar opciones de JVM desde variables de entorno (JAVA_OPTS)
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
