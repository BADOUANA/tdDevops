# Database

The -e flag is used in Docker and other containerization tools to set environment variables for a container. When you use the -e flag, you can provide key-value pairs to specify the environment variables that the container should have access to.

Here's how it works:

1. Setting Environment Variables: When you run a container, you can use the -e flag followed by a key-value pair to set an environment variable. For example:
    ```java
    docker run -e MY_VARIABLE=example_value my_image
   
   //In this command, the -e flag is used to set the environment variable MY_VARIABLE with the value example_value inside the container.
    ```

2. Accessing Environment Variables: Inside the running container, the environment variable you set using -e is available to the application or processes running within the container. It allows the application to read and use these variables as if they were set in the system's environment.

3. Multiple Environment Variables: You can specify multiple environment variables by including multiple -e flags in your docker run command:

    ```java
    docker run -e VAR1=value1 -e VAR2=value2 my_image
   
   //This sets VAR1 and VAR2 as environment variables in the container.
    ```
    The -e flag is a convenient way to configure the runtime environment for your containerized applications, and it is especially useful when working with images and containers that expect specific environment variables for proper functioning. It allows you to customize the container's behavior without modifying the application's source code or configuration files.