# ---------- Build stage (Gradle) ----------
FROM eclipse-temurin:17-jdk-jammy AS builder
WORKDIR /app

# Copy Gradle wrapper and configuration first (caching layer)
COPY gradlew .
COPY gradle ./gradle
COPY build.gradle settings.gradle ./
RUN chmod +x gradlew && ./gradlew --no-daemon -q help > /dev/null 2>&1 || true

# Copy source code and build fat JAR
COPY src ./src
RUN ./gradlew bootJar --no-daemon -x test

# ---------- Runtime stage ----------
FROM eclipse-temurin:17-jre-jammy
WORKDIR /app

# Install curl for container health check
RUN apt-get update && apt-get install -y --no-install-recommends curl && rm -rf /var/lib/apt/lists/*

# Add a non-root user for security
RUN useradd -m -s /bin/bash spring
USER spring:spring

# Copy only the built jar
COPY --from=builder --chown=spring:spring /app/build/libs/app.jar app.jar

# Expose port
EXPOSE 8080

# Default run command
ENTRYPOINT ["java", "-jar", "app.jar"]
