# URL Shortener

A lightweight URL shortener built with plain Java, H2 Database, and a simple HTML/CSS/JavaScript frontend. The application lets anonymous users create generated short links, and lets logged-in users create custom short codes.

## Features

- Shorten long URLs into 6-character generated short codes.
- Redirect short URLs back to their original URLs.
- Track access counts in the database.
- Login with a default demo user.
- Create custom short URLs after login.
- Serve the web UI and API from the same Java HTTP server.
- Store data in an embedded H2 database.
- Run unit tests with JUnit 5 and Mockito.

## Tech Stack

- Java 17
- Maven
- H2 Database
- JDBC
- Java built-in `com.sun.net.httpserver.HttpServer`
- SLF4J Simple Logger
- HTML, CSS, and vanilla JavaScript
- JUnit 5 and Mockito

## Project Structure

```text
URL-Shortener/
+-- src/
|   +-- main/
|   |   +-- java/com/urlshortener/
|   |   |   +-- Main.java
|   |   |   +-- MainSimple.java
|   |   |   +-- database/
|   |   |   +-- server/
|   |   |   +-- utils/
|   |   +-- resources/database/schema.sql
|   |   +-- web/
|   |       +-- index.html
|   |       +-- css/style.css
|   |       +-- js/
|   +-- test/java/com/urlshortener/
+-- pom.xml
+-- run.bat
+-- run-simple.bat
+-- download-deps.ps1
+-- README.md
```

## Prerequisites

- Java 17 or later
- Maven 3.6 or later

Check your installed versions:

```bash
java -version
mvn -version
```

## Run The Application

From the project folder:

```bash
mvn clean compile
mvn exec:java
```

Open the application in your browser:

```text
http://localhost:3331
```

The default Maven entry point is:

```text
com.urlshortener.Main
```

This version uses H2 and stores data in local database files such as `urlshortener.mv.db`.

## Run With The Batch Script

On Windows, you can also use:

```bat
run.bat
```

The project also includes an in-memory version:

```bat
run-simple.bat
```

`MainSimple` uses `SimpleDatabaseManager`, so data is lost when the application stops.

## Run Tests

```bash
mvn test
```

Run a specific test class:

```bash
mvn test -Dtest=DatabaseManagerTest
```

## Default Login

Use these demo credentials to access custom URL creation:

```text
Username: admin
Password: password
```

The default user is created automatically when the H2 database tables are initialized.

## API Endpoints

### Create A Generated Short URL

```http
POST /shorten
Content-Type: application/x-www-form-urlencoded

url=https://example.com/very/long/link
```

Example:

```bash
curl -X POST http://localhost:3331/shorten ^
  -H "Content-Type: application/x-www-form-urlencoded" ^
  -d "url=https://www.example.com/very-long-url"
```

Successful response:

```json
{
  "success": true,
  "shortUrl": "http://localhost:3331/Ab12Cd",
  "originalUrl": "https://www.example.com/very-long-url"
}
```

### Login

```http
POST /login
Content-Type: application/x-www-form-urlencoded

username=admin&password=password
```

JSON login requests are also accepted by `AuthHandler`.

### Create A Custom Short URL

```http
POST /custom
Content-Type: application/x-www-form-urlencoded

url=https://example.com&customCode=mycustom&username=admin
```

Example:

```bash
curl -X POST http://localhost:3331/custom ^
  -H "Content-Type: application/x-www-form-urlencoded" ^
  -d "url=https://www.google.com&customCode=test123&username=admin"
```

Custom codes must be 3 to 20 alphanumeric characters.

### Redirect

Visit any generated or custom short URL:

```text
http://localhost:3331/test123
```

If the code exists, the server returns a `302` redirect to the original URL.

## Database

The H2 database is configured in `DatabaseManager`:

```text
jdbc:h2:./urlshortener;AUTO_SERVER=TRUE
```

Tables created by the application:

- `urls` stores generated short URLs.
- `users` stores login users.
- `custom_urls` stores user-created custom short URLs.

The schema is also available in:

```text
src/main/resources/database/schema.sql
```

## Frontend

The web UI is served from:

```text
src/main/web
```

Main frontend files:

- `index.html` contains the URL shortening and custom URL forms.
- `js/main.js` handles short URL and custom URL requests.
- `js/auth.js` handles login and stores the current user in `sessionStorage`.
- `css/style.css` contains the page styling.

## Notes

- URLs must start with `http://` or `https://`.
- Generated short codes are 6 alphanumeric characters.
- Custom short codes are checked against both generated and custom URL tables.
- This project is intentionally built without backend or frontend frameworks.
- The default password hashing is SHA-256 for demo purposes; production systems should use a stronger password hashing algorithm with salt.
