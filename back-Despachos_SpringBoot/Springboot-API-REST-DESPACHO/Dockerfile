# ─── STAGE 1: Build ──────────────────────────────────────────
FROM eclipse-temurin:17-jdk-alpine AS builder
WORKDIR /app

# Copiar wrapper y pom primero (cache de dependencias)
COPY mvnw pom.xml ./
COPY .mvn .mvn
RUN chmod +x mvnw && ./mvnw dependency:go-offline -q

# Copiar fuentes y compilar
COPY src ./src
RUN ./mvnw package -DskipTests -q

# ─── STAGE 2: Runtime ────────────────────────────────────────
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Usuario no root (buena práctica de seguridad)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Copiar solo el JAR desde el stage anterior
COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8081

ENTRYPOINT ["java", "-jar", "app.jar"]
