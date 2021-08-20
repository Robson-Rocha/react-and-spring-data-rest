FROM openjdk:8-jdk-alpine
ARG JAR_FILE=target/react-and-spring-data-rest-*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]