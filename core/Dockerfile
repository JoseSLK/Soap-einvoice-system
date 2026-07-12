# Etapa 1: Build
FROM maven:3.9-eclipse-temurin-21-alpine AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src

# Si application.properties no existe (esta en .gitignore),
# usar el archivo de ejemplo como fallback
RUN if [ ! -f src/main/resources/application.properties ]; then \
      cp src/main/resources/application.properties.example src/main/resources/application.properties; \
    fi

RUN mvn clean package -DskipTests

# Etapa 2: Runtime slim
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# Crear usuario no-root (buena práctica)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Copiar el JAR
COPY --from=build /app/target/*.jar app.jar

# Puerto de tu app Spring Boot
EXPOSE 8080

# Limitar RAM para el free tier de Render (512 MB)
# Spring Boot + JVM se ajustan solos con estas flags
ENTRYPOINT ["java", "-XX:+UseContainerSupport", "-XX:MaxRAMPercentage=75.0", "-jar", "app.jar"]