# ---------- Stage 1 : Build ----------
FROM maven:3.9.11-eclipse-temurin-21 AS builder

WORKDIR /workspace

# Copy the entire repository (required because the project references sibling modules/files)
COPY . .

# Build the cart service
WORKDIR /workspace/src/cart

RUN mvn clean package -DskipTests

# ---------- Stage 2 : Runtime ----------
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /workspace/src/cart/target/carts-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
