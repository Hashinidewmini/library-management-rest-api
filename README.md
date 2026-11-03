# Library Management REST API

A RESTful API for managing books in a library, built with **Spring Boot**, **Spring Data JPA**, and **MySQL**. It supports creating, viewing, updating, and deleting book records, with interactive API documentation through Swagger UI.

The application is also **containerized using Docker and Docker Compose**, allowing the Spring Boot API and MySQL database to run in separate containers.

## Tech Stack

- Java 21
- Spring Boot 4.1.1
- Spring Web MVC
- Spring Data JPA (Hibernate)
- MySQL 8.4
- Jakarta Bean Validation
- Springdoc OpenAPI / Swagger UI
- Maven Wrapper
- Docker and Docker Compose

## Features

- Create a book with an automatically generated ID
- Retrieve all books or a book by ID
- Update an existing book
- Delete a book by ID
- Validate required title and author fields and non-negative prices
- Explore and test endpoints with Swagger UI
- Run the application and MySQL database using Docker Compose
- Persist MySQL data using a Docker volume
- Configure database connections using environment variables

## API Endpoints

Base URL: `http://localhost:8080/api/books`

| Method | Endpoint | Description | Success Response |
| --- | --- | --- | --- |
| `GET` | `/api/books` | List all books | `200 OK` |
| `GET` | `/api/books/{id}` | Get a book by ID | `200 OK` |
| `POST` | `/api/books` | Create a new book | `201 Created` |
| `PUT` | `/api/books/{id}` | Update an existing book | `200 OK` |
| `DELETE` | `/api/books/{id}` | Delete a book | `204 No Content` |

Requests for missing book IDs return `404 Not Found` on ID-based endpoints.

## Example: Create a Book

**Request:** `POST http://localhost:8080/api/books`

```json
{
  "title": "Effective Java",
  "author": "Joshua Bloch",
  "isbn": "9780134685991",
  "price": 5400.0
}
```

Do not supply `id` when creating a new book: MySQL generates it automatically.

**Example response** (`201 Created`; actual ID may differ):

```json
{
  "id": 1,
  "title": "Effective Java",
  "author": "Joshua Bloch",
  "isbn": "9780134685991",
  "price": 5400.0
}
```

## Getting Started

You can run the application **with Docker** or **locally without Docker**.

### 1. Clone the Repository

```bash
git clone https://github.com/Hashinidewmini/library-management-rest-api.git
cd library-management-rest-api
```

## Option 1: Run with Docker (Recommended)

Docker Compose starts both the Spring Boot API and MySQL database.

### Prerequisites

- Docker Desktop (or Docker Engine with Docker Compose) installed and running
- JDK 21 to build the application's JAR locally
- Git

The project includes a Maven Wrapper, so a separate Maven installation is not required.

### Step 1: Configure Environment Variables

Create a `.env` file in the project root, based on `.env.example`:

```env
DB_ROOT_PASSWORD=your_mysql_password
```

Keep `.env` out of version control. It contains your database password.

### Step 2: Build the Spring Boot JAR

**Windows Command Prompt:**

```cmd
.\mvnw.cmd clean package -DskipTests
```

**macOS/Linux:**

```bash
./mvnw clean package -DskipTests
```

This creates an executable JAR in the `target/` directory. The current Dockerfile copies that JAR into the image, so this build step must happen first.

### Step 3: Build and Start the Containers

```bash
docker compose up --build -d
```

Docker Compose builds the Spring Boot API image, downloads the MySQL image if needed, creates a shared network and named volume, and starts the API after MySQL is healthy.

### Step 4: Verify the Containers

```bash
docker compose ps
```

Both services should be running:

- `api` — Spring Boot API, host port `8080`
- `db` — MySQL 8.4, host port `3307`

To inspect the application logs:

```bash
docker compose logs api
```

A successful startup includes `Started LibraryApiApplication`.

### Step 5: Test the API

Open Swagger UI at:

http://localhost:8080/swagger-ui.html

Or use Postman at:

`http://localhost:8080/api/books`

### Useful Docker Commands

| Command | Description |
| --- | --- |
| `docker compose up --build -d` | Build and start the services |
| `docker compose ps` | Check service status |
| `docker compose logs api` | View Spring Boot logs |
| `docker compose logs db` | View MySQL logs |
| `docker compose down` | Remove containers and network, keeping the volume |
| `docker compose up -d` | Restart using existing images |
| `docker images` | List local Docker images |
| `docker volume ls` | List Docker volumes |

### Database Persistence

The Compose configuration stores MySQL data in a named volume, `library-data` (usually displayed as `library-management-rest-api_library-data` when the project directory is named `library-management-rest-api`).

Data survives normal container removal and recreation:

```bash
docker compose down
docker compose up -d
```

**Warning:** `docker compose down -v` removes the volume too, deleting the books stored in the Docker database.

### Connect to Docker MySQL Using MySQL Workbench

| Setting | Value |
| --- | --- |
| Hostname | `127.0.0.1` |
| Port | `3307` |
| Username | `root` |
| Password | The value of `DB_ROOT_PASSWORD` in `.env` |
| Database | `library_db` |

Run:

```sql
SELECT * FROM library_db.book;
```

The Docker MySQL database is separate from the database installed directly on your computer, so books created in the local database will not appear here automatically.

## Option 2: Run Locally (Without Docker)

### Prerequisites

- JDK 21
- MySQL Server 8
- Git

### Step 1: Create the Database

In MySQL Workbench or the MySQL CLI:

```sql
CREATE DATABASE IF NOT EXISTS library_db;
```

### Step 2: Configure the Database Connection

In `src/main/resources/application.properties`, use:

```properties
spring.application.name=library-api
spring.datasource.url=jdbc:mysql://localhost:3306/library_db
spring.datasource.username=${DB_USERNAME:root}
spring.datasource.password=${DB_PASSWORD}
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

Set the local database password using an environment variable.

**Windows Command Prompt:**

```cmd
set "DB_PASSWORD=your_mysql_password"
```

If your MySQL username is not `root`, set `DB_USERNAME` too. Do not commit passwords to GitHub. `ddl-auto=update` is intended here for development, not production database migrations.

### Step 3: Run the Application

**Windows Command Prompt:**

```cmd
.\mvnw.cmd spring-boot:run
```

**macOS/Linux:**

```bash
./mvnw spring-boot:run
```

The application runs at http://localhost:8080 by default.

### Step 4: Open Swagger UI

Visit http://localhost:8080/swagger-ui/index.html and test the API using Swagger UI or Postman.

## Project Structure

```text
library-management-rest-api/
├── .mvn/
├── src/
│   ├── main/
│   │   ├── java/lk/ac/mrt/fit/library_api/
│   │   │   ├── controller/BookController.java
│   │   │   ├── model/Book.java
│   │   │   ├── repository/BookRepository.java
│   │   │   ├── service/BookService.java
│   │   │   └── LibraryApiApplication.java
│   │   └── resources/application.properties
│   └── test/
├── .dockerignore
├── .env.example
├── .gitignore
├── Dockerfile
├── compose.yaml
├── mvnw
├── mvnw.cmd
├── pom.xml
└── README.md
```

The local `.env` file and generated `target/` directory should remain untracked.

## Docker Architecture

The system runs as two Docker services on a shared Compose network:

**Spring Boot API (`api`)**
- Built from the project Dockerfile using the Eclipse Temurin Java 21 JRE
- Exposes port `8080`
- Connects to MySQL using the hostname `db`

**MySQL (`db`)**
- Uses the `mysql:8.4` image
- Listens on port `3306` inside Docker and port `3307` on the host
- Uses a named volume to persist database data
- Includes a health check

The API's database URL in Docker is:

```text
jdbc:mysql://db:3306/library_db
```

Docker Compose supplies this through `SPRING_DATASOURCE_URL`, overriding the local `application.properties` value without changing the Java code.

## Validation

- `title` and `author` cannot be blank.
- `price` must be zero or greater when provided.

## Notes

This learning project was developed for **IN3101 – Enterprise Application Development** laboratory exercises at the **University of Moratuwa**.

It demonstrates REST API development, layered architecture (controller → service → repository), JPA persistence, MySQL integration, API validation, Swagger documentation, Docker containerization, Docker Compose, environment-based configuration, and persistent database storage.

The project is intended for learning and local development. A production deployment would need additional security and deployment considerations.
