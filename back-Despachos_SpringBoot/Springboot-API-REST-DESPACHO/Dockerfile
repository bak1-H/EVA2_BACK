# ---- Etapa de compilación ----
FROM eclipse-temurin:17-jdk AS builder
WORKDIR /app
COPY mvnw pom.xml ./
COPY .mvn .mvn
RUN chmod +x mvnw && ./mvnw dependency:go-offline -B
COPY src src
RUN ./mvnw package -DskipTests -B

# ---- Etapa de ejecución ----
FROM eclipse-temurin:17-jre
RUN groupadd -r apidespacho && useradd -r -g apidespacho apidespacho
USER apidespacho:apidespacho
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8082
ENTRYPOINT ["java", "-jar", "app.jar"]
