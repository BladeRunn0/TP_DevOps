# Build
FROM maven:3.8.6-amazoncorretto-17 AS backendstudent-build
ENV BACKENDSTUDENT_HOME /opt/backendstudent
WORKDIR $BACKENDSTUDENT_HOME
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# Run
FROM amazoncorretto:17
ENV BACKENDSTUDENT_HOME /opt/backendstudent
WORKDIR $BACKENDSTUDENT_HOME
COPY --from=backendstudent-build $BACKENDSTUDENT_HOME/target/*.jar $BACKENDSTUDENT_HOME/backendstudent.jar

ENTRYPOINT java -jar backendstudent.jar
