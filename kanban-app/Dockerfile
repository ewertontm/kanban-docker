# Build stage
FROM maven:3.9-eclipse-temurin-11-alpine AS build

# Set working directory
WORKDIR /workspace

# Copy dependency files first for better caching
COPY pom.xml /workspace/
RUN mvn dependency:go-offline -B

# Copy source code
COPY src /workspace/src

# Build application
RUN mvn clean package -DskipTests -B

# Runtime stage
FROM eclipse-temurin:11-jre-alpine

# Create non-root user
RUN addgroup -g 1001 -S app && adduser -S app -u 1001 -G app

# Install security updates and wget for health check
RUN apk update && apk upgrade && apk add --no-cache wget && rm -rf /var/cache/apk/*

# Set working directory
WORKDIR /app

# Copy jar from build stage
COPY --from=build /workspace/target/*.jar app.jar

# Change ownership to app user
RUN chown app:app app.jar

# Switch to non-root user
USER app

# Expose port
EXPOSE 8080

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:8080/actuator/health || exit 1

# Start application with optimized JVM settings for Java 11
ENTRYPOINT ["java", \
    "-XX:+UseContainerSupport", \
    "-XX:MaxRAMPercentage=75.0", \
    "-XshowSettings:vm", \
    "-jar", "app.jar"]