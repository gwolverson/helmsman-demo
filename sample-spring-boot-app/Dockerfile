FROM maven:3.8.3-openjdk-17 as MVNBUILDER

WORKDIR /build

COPY ./src ./src
COPY pom.xml .

RUN mvn clean package

FROM openjdk:17.0.2

WORKDIR /opt/app

COPY --from=MVNBUILDER /build/target/*.jar ./application.jar

CMD java -jar application.jar