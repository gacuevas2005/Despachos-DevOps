# STAGE 1: Build (Construcción)
FROM maven:3.9-eclipse-temurin-17-alpine AS build
WORKDIR /app

# Copiamos solo el pom.xml primero para aprovechar el caché de capas de Docker
COPY pom.xml .
RUN mvn dependency:go-offline

# Copiamos el código fuente y compilamos
COPY src ./src
RUN mvn clean package -DskipTests

# STAGE 2: Runtime (Ejecución segura)
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# SEGURIDAD: Crear un usuario no root para evitar escalada de privilegios
RUN addgroup -S innovatech && adduser -S gcuevas -G innovatech
USER gcuevas

# RENDIMIENTO: Copiamos solo el artefacto final de la etapa anterior (Capa limpia)
COPY --from=build /app/target/*.jar app.jar

# Configuración de red
EXPOSE 8091
ENTRYPOINT ["java", "-jar", "app.jar"]