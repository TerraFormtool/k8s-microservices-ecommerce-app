# ---------- Stage 1 : Build ----------
FROM maven:3.9.11-eclipse-temurin-21 AS builder

WORKDIR /workspace

# Copy complete repository
COPY . .

# Build only UI
WORKDIR /workspace/src/ui

RUN mvn clean package -DskipTests

# ---------- Stage 2 : Runtime ----------
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /workspace/ui/target/ui-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
