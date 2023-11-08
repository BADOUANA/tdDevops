# tdDevops



- [Readme backend](https://github.com/BADOUANA/tdDevops/blob/7b178235ae2ea39e5aabfd498b3e1275b398991e/simple-api-student-main/README.md)
- [Readme database](https://github.com/BADOUANA/tdDevops/blob/7b178235ae2ea39e5aabfd498b3e1275b398991e/tp1/tp/README.md)

How to publish an image:

1. tag image 
```
docker tag my-database username_docker/image_namee:tag
```
2. push image
```
docker push username_docker/devops-backend:1.0
```
# GitHub Action


## Introduction

GitHub Actions, an online service that allows you to create continuous integration and continuous delivery (CI/CD) pipelines for your software applications. It is a powerful tool for automating various development and deployment tasks.
## 1. Setup GitHub Actions

GitHub Actions is a tool that enables the creation of CI/CD pipelines to test and deliver software applications. Before using GitHub Actions, you need to set up a GitHub repository for your project and push your code to it. This is a necessary step to integrate CI/CD with your GitHub repository.

## 2. Build and Test Your Application

The core functionality of CI is to build and test your application automatically. For Java projects using Maven, the command `mvn clean verify` is typically used to build and run tests. This command ensures that your code is built from scratch, and unit tests and integration tests are executed.

### What is `testcontainers`?

`testcontainers` is a set of Java libraries that allow you to run Docker containers while testing your application. It's particularly useful for integration tests that require a database. In the provided example, a PostgreSQL container is used for database testing.

## 3. GitHub Actions Configurations

To set up GitHub Actions for your project, you need to create a workflow file named `main.yml` in the `.github/workflows` directory of your repository. This YAML file describes the steps to be executed in your CI/CD pipeline. Here is a sample `main.yml` file:

```yaml
name: CI devops 2023
on:
  push:
    branches: # Define branches to trigger the CI
  pull_request:

jobs:
  test-backend:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v2.5.0
      - name: Set up JDK 17
        # TODO: Define JDK setup
      - name: Build and test with Maven
        run: # TODO: Define build and test command
```

## 4. Continuous Delivery (CD)

Continuous Delivery involves deploying your application automatically when there is a commit to the main branch. To achieve this, you need to build Docker images within your GitHub Actions pipeline. You also need to securely handle Docker Hub credentials, which can be stored as GitHub secrets.

## 5. Quality Gate

Quality Gate ensures code quality and security by analyzing your code. In this report, SonarCloud is used to perform code analysis. To set up SonarCloud, you need to:

- Register for a free-tier account on SonarCloud.
- Create an organization on SonarCloud.
- Obtain the project key and organization key.

Then, configure your GitHub Actions pipeline to use SonarCloud for code analysis. This involves modifying the Maven build and test command to include SonarCloud parameters:

```bash
mvn -B verify sonar:sonar -Dsonar.projectKey=devops-2023 -Dsonar.organization=devops-school -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }} --file ./simple-api/pom.xml
```
Testcontainers is an open-source Java library that simplifies and streamlines integration testing by providing lightweight, disposable containers for various external services and databases. It allows developers and testers to write automated tests for applications that rely on external dependencies, such as databases, message brokers, or other services, without the need to set up and manage these services manually.

Key features and benefits of Testcontainers include:

1. **Ephemeral Containers**: Testcontainers creates containers for your integration tests, and these containers are automatically managed during the test's lifecycle. They are started when the test begins and stopped when it ends, ensuring that each test runs in an isolated environment.

2. **Database Containers**: Testcontainers supports a wide range of database containers, including PostgreSQL, MySQL, Oracle, SQL Server, and more. You can easily spin up a database container for your test, perform database operations, and then discard the container when the test finishes.

3. **Custom Containers**: Besides databases, Testcontainers allows you to define custom containers for other services like RabbitMQ, Kafka, Redis, or any Docker container you require for your tests.

4. **Java API**: Testcontainers provides a convenient Java API that integrates with popular testing frameworks like JUnit and TestNG. You can define container configurations, start and stop containers, and access container information programmatically in your test code.

5. **Environment Agnostic**: Testcontainers works on various platforms and environments, including local development machines, CI/CD pipelines, and cloud-based services. It promotes consistent and reproducible testing across different environments.

6. **Docker Compose Support**: Testcontainers can also work with Docker Compose files, allowing you to define multi-container test environments for more complex scenarios.

Here's an example of how you might use Testcontainers in a Java test:


In this example, a PostgreSQL container is started before the test and automatically stopped when the test finishes. Testcontainers makes it easy to write integration tests that run against real, isolated containers, ensuring a more realistic testing environment.

## Setup Quality Gate

Having a permissive Cross-Origin Resource Sharing policy is security-sensitive. It has led in the past to the following vulnerabilities:

CVE-2018-0269
CVE-2017-14460
Same origin policy in browsers prevents, by default and for security-reasons, a javascript frontend to perform a cross-origin HTTP request to a resource that has a different origin (domain, protocol, or port) from its own. The requested target can append additional HTTP headers in response, called CORS, that act like directives for the browser and change the access control policy / relax the same origin policy.

**My quality gate rate**
- The Student controller code at E
- The Department controller code at E
- The Greeting controller code at E


I propose this where i tried to upgrade the quality:
``` java
package fr.takima.training.simpleapi.controller;

import fr.takima.training.simpleapi.entity.Student;
import fr.takima.training.simpleapi.service.StudentService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.net.URI;
import java.util.List;
import java.util.Optional;

@RestController
@CrossOrigin
@RequestMapping("/students")
public class StudentController {
private final StudentService studentService;

    @Autowired
    public StudentController(StudentService studentService) {
        this.studentService = studentService;
    }

    @CrossOrigin(origins = "https://your-allowed-domain.com", methods = {RequestMethod.GET, RequestMethod.POST, RequestMethod.PUT, RequestMethod.DELETE}, allowedHeaders = {"Content-Type"})
    @GetMapping("/")
    public ResponseEntity<List<Student>> getStudents() {
        List<Student> students = studentService.getAll();
        return ResponseEntity.ok(students);
    }

    @GetMapping("/{id}")
    public ResponseEntity<Object> getStudentById(@PathVariable(name = "id") long id) {
        Optional<Student> studentOptional = Optional.ofNullable(studentService.getStudentById(id));
        return studentOptional.map(ResponseEntity::ok)
                .orElseGet(() -> ResponseEntity.notFound().build());
    }

    @PostMapping
    public ResponseEntity<Object> addStudent(@RequestBody Student student) {
        try {
            Student savedStudent = studentService.addStudent(student);
            URI location = ServletUriComponentsBuilder.fromCurrentRequest()
                    .path("/{id}")
                    .buildAndExpand(savedStudent.getId())
                    .toUri();
            return ResponseEntity.created(location).build();
        } catch (IllegalArgumentException e) {
            return ResponseEntity.badRequest().build();
        }
    }

    @PutMapping("/{id}")
    public ResponseEntity<Object> updateStudent(@RequestBody Student student, @PathVariable(name = "id") long id) {
        Optional<Student> studentOptional = Optional.ofNullable(studentService.getStudentById(id));
        return studentOptional.map(existingStudent -> {
            student.setId(id);
            studentService.addStudent(student);
            return ResponseEntity.ok(student);
        }).orElseGet(() -> ResponseEntity.notFound().build());
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Object> removeStudent(@PathVariable(name = "id") long id) {
        Optional<Student> studentOptional = Optional.ofNullable(studentService.getStudentById(id));
        return studentOptional.map(student -> {
            studentService.removeStudentById(id);
            return ResponseEntity.ok().build();
        }).orElseGet(() -> ResponseEntity.notFound().build());
    }
}
```


```java 
import org.springframework.web.bind.annotation.*;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.CrossOrigin;
import java.util.Optional;

@RestController
@RequestMapping(value = "/departments")
public class DepartmentController {
private final DepartmentService departmentService;
private final StudentService studentService;

    @Autowired
    public DepartmentController(DepartmentService departmentService, StudentService studentService) {
        this.departmentService = departmentService;
        this.studentService = studentService;
    }

    @CrossOrigin(origins = "https://your-allowed-domain.com", methods = {RequestMethod.GET, RequestMethod.POST, RequestMethod.PUT, RequestMethod.DELETE}, allowedHeaders = {"Content-Type"})
    @GetMapping
    public ResponseEntity<List<Department>> getAllDepartments() {
        List<Department> departments = departmentService.getAllDepartments();
        return ResponseEntity.ok(departments);
    }
    //...
}
```

```java
package fr.takima.training.simpleapi.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.CrossOrigin;

import java.util.concurrent.atomic.AtomicLong;

@RestController
@RequestMapping(value = "/departments")
public class GreetingController {
    private static final String TEMPLATE = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @CrossOrigin(origins = "https://your-allowed-domain.com", methods = {RequestMethod.GET, RequestMethod.POST, RequestMethod.PUT, RequestMethod.DELETE}, allowedHeaders = {"Content-Type"})
    @GetMapping("/")
    public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
        return new Greeting(counter.incrementAndGet(), String.format(TEMPLATE, name));
    }

    record Greeting(long id, String content) { }
}
```
# Ansible 

## Introduction

Ansible is an open-source automation tool that simplifies application deployment, configuration management, and task automation. It is agentless and uses SSH to communicate with remote servers. The report will cover the following key areas:

1. Inventories
2. Facts
3. Playbooks
4. Roles
5. Application Deployment
6. Frontend Configuration
7. Continuous Deployment

## 1. Inventories

Inventories in Ansible are used to define and group sets of hosts. Hosts are organized into groups, making it easier to manage and target specific sets of servers based on their roles or functionalities. By default, Ansible's inventory is located at `/etc/ansible/hosts`, but you can create a project-specific inventory file. Here's how to create a custom inventory file:

```yaml
all:
 vars:
   ansible_user: centos
   ansible_ssh_private_key_file: /path/to/private/key
 children:
   prod:
     hosts: hostname or IP
```

You can test your inventory using the `ping` command:

```bash
ansible all -i inventories/setup.yml -m ping
```

## 2. Facts

Facts in Ansible are predefined variables that provide information about the remote hosts. These facts are prefixed by `ansible_` and can be accessed to gather information about the target system. For example, you can use the `setup` module to get information about the OS distribution:

```bash
ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
```

## 3. Playbooks

Playbooks in Ansible are YAML files that define a set of tasks to be executed on remote hosts. They are used to automate tasks and configuration management. Here's an example of a simple playbook:

```yaml
- hosts: all
  gather_facts: false
  become: true

  tasks:
   - name: Test connection
     ping:
```

You can execute a playbook using the `ansible-playbook` command, and you can also check the syntax of your playbook before executing it:

```bash
ansible-playbook -i inventories/setup.yml playbook.yml
ansible-playbook -i inventories/setup.yml playbook.yml --syntax-check
```

## 4. Roles

Roles in Ansible provide a way to organize and structure your playbooks. They allow you to encapsulate and reuse sets of tasks, making your playbooks more modular and maintainable. Here's how to create a role:

```bash
ansible-galaxy init roles/docker
```

Roles typically have directories like `tasks`, `handlers`, `templates`, etc., and you can include them in your playbooks to make your code cleaner and more organized.

## 5. Application Deployment

To deploy your application using Ansible, you can create specific roles for each part of your application. You can use the `docker_container` module to start Docker containers for your application components. Here's an example of a `docker_container` task:

```yaml
- name: Run HTTPD
  docker_container:
    name: httpd
    image: cedric/my-httpd:1.0
```

You should have roles for installing Docker, creating a network, launching a database, launching your app, and launching a proxy. You can configure environment variables for your app and database tasks.

## 6. Frontend Configuration

In addition to deploying your application's backend, you should also configure your frontend. You can customize your HTTPD server to act as a proxy, redirecting requests from the frontend to the API. This ensures that your entire application stack works seamlessly.

## 7. Continuous Deployment

To achieve continuous deployment, you can configure GitHub Actions to automatically deploy your application when you release it on the production branch of your GitHub repository. This involves creating a workflow that uses Ansible to update your application on your server. You can use a Docker image for running Ansible and encrypt sensitive data using a vault.

This approach ensures that every code release is automatically deployed to your server, allowing for a full CI/CD pipeline.

We change our playbook to this, check every tasks to see the configuration.
```yaml
- hosts: all
  gather_facts: false
  become: yes

  # Install Docker
  roles:
    - docker
    - network
    - db
    - backend
    - httpd
```

