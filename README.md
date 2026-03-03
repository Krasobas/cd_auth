# cd_auth

> Authentication & user management microservice for the CheckDev ecosystem.

Built with **Spring Boot**, **Spring Security**, and **Spring Data JPA** — `cd_auth` handles authentication, registration, user profiles, photo storage, and role-based access control. Designed to integrate seamlessly with other CheckDev modules via REST API.

---

## Features

- **Authentication & Registration** — secure login and signup flows
- **OAuth2 Support** — via `OAuthCall` and `UserDetailsDefinition`
- **Role-Based Access Control** — flexible roles and permissions (`Role`, `RoleService`, `SecurityConfig`)
- **User Profiles** — full CRUD for profile management
- **Photo Storage** — upload and retrieve profile images
- **CORS Handling** — cross-origin request filtering out of the box
- **Database Migrations** — powered by Liquibase

---

## Tech Stack

| Layer              | Technology                        |
| ------------------ | --------------------------------- |
| Language           | Java 17+                          |
| Framework          | Spring Boot                       |
| Security           | Spring Security + OAuth2          |
| ORM                | Spring Data JPA (Hibernate)       |
| Migrations         | Liquibase                         |
| Build              | Maven                             |
| CI/CD              | Jenkins                           |
| Service Discovery  | Eureka                            |
| Logging            | Logback                           |

---

## Project Structure

```
ru.checkdev.auth/
├── AuthSrv.java                        # Application entry point
├── config/
│   └── SecurityConfig.java             # Security configuration
├── domain/                             # JPA entities
│   ├── Photo.java
│   ├── Profile.java
│   └── Role.java
├── dto/                                # Data Transfer Objects
│   ├── ProfileDTO.java
│   └── ProfileTgDTO.java
├── filter/
│   └── CorsFilter.java                # CORS filter
├── repository/                         # Spring Data repositories
│   ├── PersonRepository.java
│   ├── PhotoRepository.java
│   └── RoleRepository.java
├── service/                            # Business logic
│   ├── PersonService.java
│   ├── PhotoService.java
│   ├── ProfileService.java
│   └── RoleService.java
├── util/                               # Helpers
│   ├── OAuthCall.java
│   ├── PhotoMainClass.java
│   └── UserDetailsDefinition.java
└── web/controller/                     # REST controllers
    ├── AuthController.java
    ├── DownloadController.java
    ├── PersonController.java
    └── ProfileController.java
```

---

## API Reference

| Method | Endpoint           | Description              |
| ------ | ------------------ | ------------------------ |
| POST   | `/auth/login`      | Authenticate a user      |
| POST   | `/auth/register`   | Register a new user      |
| GET    | `/profile/{id}`    | Get user profile         |
| PUT    | `/profile/{id}`    | Update user profile      |
| GET    | `/photo/{id}`      | Get profile photo        |
| POST   | `/photo/upload`    | Upload a new photo       |

> **Note:** Exact paths may vary depending on controller annotations.

---

## Database

- **ORM:** Hibernate (via Spring Data JPA)
- **Migrations:** Liquibase — `src/main/resources/db/db.changelog-master.xml`
- **Core tables:** `profile`, `role`, `photo`

---

## Getting Started

### Prerequisites

- Java 17+
- Maven 3.8+
- PostgreSQL

### Configuration

Edit `src/main/resources/application.properties`:

```properties
spring.application.name=auth
server.port=9010

# Database
spring.datasource.url=jdbc:postgresql://localhost:5432/cd_auth
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.jpa.hibernate.ddl-auto=validate
spring.liquibase.change-log=classpath:db/db.changelog-master.xml

# Default admin credentials
spring.security.user.name=admin
spring.security.user.password=admin

# Eureka
eureka.client.service-url.defaultZone=http://localhost:9009/eureka
```

### Build & Run

```bash
# Build
mvn clean package

# Run
java -jar target/cd_auth-0.0.1-SNAPSHOT.jar

# Or simply
mvn spring-boot:run
```

The service will be available at **http://localhost:9010**

---

## Docker

```dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/cd_auth-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 9010
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```bash
docker build -t checkdev-auth .
docker run -p 9010:9010 checkdev-auth
```

---

## Eureka Integration

To register with the Eureka Server (e.g. `cd_eureka` on port `9009`):

```properties
eureka.client.register-with-eureka=true
eureka.client.fetch-registry=true
eureka.client.service-url.defaultZone=http://localhost:9009/eureka
```

---

## CI/CD

The included `Jenkinsfile` defines the pipeline:

1. **Build** — `mvn clean package`
2. **Test** — run unit tests
3. **Deploy** — publish artifact (e.g. to Docker Registry or remote server)