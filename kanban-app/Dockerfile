# Build stage
FROM maven:3.9.6-openjdk-17-slim AS build

# Create non-root user for build
RUN groupadd -r maven && useradd -r -g maven maven

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
FROM openjdk:17-jre-slim

# Create non-root user
RUN groupadd -r app && useradd -r -g app app

# Install security updates
RUN apt-get update && apt-get upgrade -y && rm -rf /var/lib/apt/lists/*

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
  CMD curl -f http://localhost:8080/actuator/health || exit 1

# Start application with optimized JVM settings
ENTRYPOINT ["java", \
    "-XX:+UnlockExperimentalVMOptions", \
    "-XX:+UseCGroupMemoryLimitForHeap", \
    "-XX:MaxRAMFraction=1", \
    "-XshowSettings:vm", \
    "-jar", "app.jar"]