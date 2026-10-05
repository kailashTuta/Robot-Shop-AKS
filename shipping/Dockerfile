FROM maven:3.9-eclipse-temurin-17 AS build

WORKDIR /opt/shipping

COPY pom.xml ./
RUN mvn -B dependency:go-offline
COPY src /opt/shipping/src/
RUN mvn -B -DskipTests package

FROM eclipse-temurin:17-jre-jammy

EXPOSE 8080

WORKDIR /opt/shipping

ENV CART_ENDPOINT=cart:8080 \
    DB_HOST=mysql

RUN apt-get update \
    && apt-get install -y --no-install-recommends curl \
    && rm -rf /var/lib/apt/lists/*

COPY --from=build /opt/shipping/target/shipping-1.0.jar ./shipping.jar

CMD ["java", "-Xmn256m", "-Xmx768m", "-jar", "shipping.jar"]
