FROM eclipse-temurin:17-jre

WORKDIR /opt/i27

ARG JAR_SOURCE
COPY ${JAR_SOURCE} app.jar

EXPOSE 8761

ENTRYPOINT ["java", "-jar", "app.jar"]