# Library Management REST API

A RESTful API for managing books in a library, built with **Spring Boot**, **Spring Data JPA**, and **MySQL**. It supports creating, viewing, updating, and deleting book records, with interactive API documentation through Swagger UI.

## Tech Stack

- Java 21
- Spring Boot 4.1.1
- Spring Web MVC
- Spring Data JPA (Hibernate)
- MySQL 8
- Jakarta Bean Validation
- Springdoc OpenAPI / Swagger UI
- Maven Wrapper

## Features

- Create a book with an automatically generated ID
- Retrieve all books or a book by ID
- Update an existing book
- Delete a book by ID
- Validate required title and author fields and non-negative prices
- Explore and test endpoints with Swagger UI

## API Endpoints

Base URL: `http://localhost:8080/api/books`

| Method | Endpoint | Description | Success response |
| --- | --- | --- | --- |
| `GET` | `/api/books` | List all books | `200 OK` |
| `GET` | `/api/books/{id}` | Get a book by ID | `200 OK` |
| `POST` | `/api/books` | Create a new book | `201 Created` |
| `PUT` | `/api/books/{id}` | Update an existing book | `200 OK` |
| `DELETE` | `/api/books/{id}` | Delete a book | `204 No Content` |

Requests for missing book IDs return `404 Not Found` on the ID-based endpoints.

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

### Prerequisites

- JDK 21
- MySQL Server 8
- Git (to clone the repository)

The project includes a Maven Wrapper, so installing Maven separately is optional.

### 1. Clone the Repository

```bash
git clone https://github.com/Hashinidewmini/library-management-rest-api.git
cd library-management-rest-api
```


### 2. Create the Database

In MySQL Workbench or the MySQL CLI, run:

```sql
CREATE DATABASE IF NOT EXISTS library_db;
```

### 3. Configure the Database Connection

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

Set your own database password as a local environment variable. **Never commit passwords to GitHub.** For example, in Windows Command Prompt:

```cmd
set "DB_PASSWORD=your_mysql_password"
```

If your MySQL username is not `root`, also set `DB_USERNAME`. The `ddl-auto=update` setting is intended for local development, not production schema migrations.

### 4. Run the Application

**Windows Command Prompt:**

```cmd
.\mvnw.cmd spring-boot:run
```

**macOS / Linux:**

```bash
./mvnw spring-boot:run
```

The API runs at `http://localhost:8080` by default.

### 5. Open Swagger UI

Visit:

`http://localhost:8080/swagger-ui/index.html`

Use Swagger UI or Postman to test the endpoints.

## Project Structure

```text
src/
├── main/
│   ├── java/lk/ac/mrt/fit/library_api/
│   │   ├── controller/BookController.java
│   │   ├── model/Book.java
│   │   ├── repository/BookRepository.java
│   │   ├── service/BookService.java
│   │   └── LibraryApiApplication.java
│   └── resources/application.properties
└── test/
```

## Validation

- `title` and `author` cannot be blank.
- `price` must be zero or greater when provided.

## Notes

This is a learning project demonstrating Spring Boot REST APIs, a layered architecture (controller → service → repository), JPA persistence, and MySQL integration.
