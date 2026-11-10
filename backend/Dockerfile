# ---------- Build stage (Gradle) ----------
FROM eclipse-temurin:17-jdk-alpine AS builder
WORKDIR /app

# Copy Gradle wrapper and config first (layer cache)
COPY gradlew .
COPY gradle ./gradle
COPY build.gradle settings.gradle ./
RUN chmod +x gradlew && ./gradlew dependencies --no-daemon -q 2>/dev/null || true

# Copy source and build fat JAR
COPY src ./src
RUN ./gradlew bootJar --no-daemon -x test

# ---------- Runtime stage ----------
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Add a non-root user
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# Copy only the built jar
COPY --from=builder /app/target/*.jar app.jar

# Expose port
EXPOSE 8080

# Default run command
ENTRYPOINT ["java","-jar","app.jar"]
