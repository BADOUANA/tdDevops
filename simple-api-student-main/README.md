# simple-api-devops

A multi-stage build in a Dockerfile is used to create a more efficient and smaller final Docker image. It involves using multiple `FROM` instructions to create intermediate images for different stages of the build process. Each stage can produce its own artifacts, and only the necessary artifacts are carried over to the final image, leaving behind unnecessary build dependencies. This helps reduce the size of the final image, which is especially important in production environments.

In the Dockerfile you provided, there are two stages: the "Build" stage and the "Run" stage. Let's break down each step:

### Build Stage:

1. `FROM maven:3.8.6-amazoncorretto-17 AS myapp-build`: This defines the first stage and uses the `maven:3.8.6-amazoncorretto-17` image, which is based on Amazon Corretto 17 and includes Maven.

2. `ENV MYAPP_HOME /opt/myapp`: This sets an environment variable `MYAPP_HOME` to `/opt/myapp`, specifying the home directory for the application.

3. `WORKDIR $MYAPP_HOME`: This sets the working directory for the build process to the value of `MYAPP_HOME`.

4. `COPY pom.xml .`: This copies the `pom.xml` file from the host (your local machine) to the current working directory in the container. This is typically where the project's Maven configuration resides.

5. `COPY src ./src`: This copies the source code (and other application files) from the host to the container's working directory. It's common to have a directory structure like `src` where the application's source code resides.

6. `RUN mvn package -DskipTests`: This runs the Maven `package` command, which compiles the source code, runs tests (skipped in this case due to `-DskipTests`), and packages the application into a JAR file. The JAR file will be located in the target directory within the container's working directory.

### Run Stage:

1. `FROM amazoncorretto:17`: This defines the second stage and uses the `amazoncorretto:17` image, which is based on Amazon Corretto 17 and serves as the base image for running the application.

2. `ENV MYAPP_HOME /opt/myapp`: This sets the `MYAPP_HOME` environment variable to the same value as in the build stage, ensuring consistency.

3. `WORKDIR $MYAPP_HOME`: This sets the working directory for the run stage to the value of `MYAPP_HOME`.

4. `COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar`: This copies the JAR file produced in the build stage from the `myapp-build` stage to the run stage. It places the JAR in the container's working directory and renames it to `myapp.jar`.

5. `ENTRYPOINT java -jar myapp.jar`: This sets the entry point command for the container. When the container is started, it will execute the Java command to run the JAR file `myapp.jar`, effectively starting the Spring Boot application.
