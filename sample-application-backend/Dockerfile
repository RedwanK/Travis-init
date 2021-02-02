# Build
FROM maven:3.6.3-jdk-11 AS build
ENV MYAPP_HOME /opt/backend
WORKDIR $MYAPP_HOME
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# Run
FROM openjdk:11-jre
ENV MYAPP_HOME /opt/backend
WORKDIR $MYAPP_HOME
COPY --from=build $MYAPP_HOME/target/*.jar $MYAPP_HOME/app.jar

EXPOSE 8080

ENTRYPOINT java -jar app.jar