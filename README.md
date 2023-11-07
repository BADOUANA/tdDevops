# tdDevops

How to publish an image:

1. tag image 
```
docker tag my-database badou237/devops-database:1.0
```
2. push image
```
docker push badou237/devops-backend:1.0
```
# GitHub Action

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

# Setup Quality Gate

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

1. `ansible all -i inventories/setup.yml -m ping`
    - Command: This Ansible command uses the "ping" module to check if all hosts in the inventory are reachable.
    - `-i inventories/setup.yml`: Specifies the inventory file where host information is defined.
    - `-m ping`: Invokes the "ping" module, which sends a test ping to the hosts to check if they are responding.

2. `ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become`
    - Command: This Ansible command uses the "yum" module to remove the "httpd" package from all hosts and uses privilege escalation with "--become."
    - `-i inventories/setup.yml`: Specifies the inventory file.
    - `-m yum`: Invokes the "yum" module for package management.
    - `-a "name=httpd state=absent"`: Specifies the arguments for the "yum" module, indicating to remove the "httpd" package.
    - `--become`: Enables privilege escalation, allowing Ansible to run tasks with administrative privileges (e.g., using "sudo").

3. `ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"`
    - Command: This Ansible command uses the "setup" module to gather facts about all hosts and filters the output to display distribution-related information.
    - `-i inventories/setup.yml`: Specifies the inventory file.
    - `-m setup`: Invokes the "setup" module, which collects system information from the hosts.
    - `-a "filter=ansible_distribution*"`: Uses the "filter" argument to limit the output to facts related to the distribution (e.g., OS name, version, etc.).

4. `ansible-playbook -i inventories/setup.yml playbook.yml`
    - Command: This command runs an Ansible playbook named "playbook.yml" using the inventory file "inventories/setup.yml."
    - `ansible-playbook`: The "ansible-playbook" command is used to execute Ansible playbooks.
    - `-i inventories/setup.yml`: Specifies the inventory file to use when executing the playbook.
    - `playbook.yml`: Indicates the name of the Ansible playbook to run.


create a docker role
```
ansible-galaxy init roles/docker
```