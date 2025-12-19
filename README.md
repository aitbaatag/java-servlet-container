# Simple Web Container

A lightweight, custom Java-based web server that implements core servlet functionality to serve web applications without relying on external containers like Tomcat or Jetty.

## Overview

Simple Web Container is a minimalist web server built from scratch in Java. It provides:

- Custom HTTP request/response handling
- Servlet-based request routing
- Static file serving (HTML, CSS, JavaScript)
- Multi-threaded client connection handling
- Configuration-based servlet mapping

## Project Structure

```
Simple_Web_Container/
├── src/main/
│   ├── java/
│   │   ├── org/web/container/        # Core web server implementation
│   │   │   ├── SimpleWebContainer.java       # Main web server
│   │   │   ├── SocketHandler.java            # Client connection handler
│   │   │   ├── HttpServlet.java              # Abstract servlet base class
│   │   │   ├── HttpServletRequest.java       # HTTP request wrapper
│   │   │   ├── HttpServletResponse.java      # HTTP response wrapper
│   │   │   ├── FileServlet.java              # Static file handler
│   │   │   └── CustomServletContext.java     # Servlet context management
│   │   └── servlet/                  # Application servlets
│   │       ├── ServletHello.java             # Hello page servlet
│   │       └── ServletSingup.java            # Sign-up page servlet
│   ├── resources/
│   │   ├── config.properties         # Servlet routing configuration
│   │   └── webapp/                   # Static web assets
│   │       ├── HelloPage/            # Hello page resources
│   │       │   ├── hello.html
│   │       │   ├── style.css
│   │       │   └── script.js
│   │       └── SingUp/               # Sign-up page resources
│   │           ├── singup.html
│   │           ├── stylesSingUp.css
│   │           └── scriptSingUp.js
├── pom.xml                           # Maven configuration
├── Makefile                          # Build automation
└── README.md                         # This file
```

## Features

### Core Components

**SimpleWebContainer**: The main server class that:
- Listens on a configurable port (default: 8888)
- Accepts incoming client connections
- Routes requests to appropriate servlets based on URL patterns
- Manages servlet lifecycle

**SocketHandler**: Thread-based handler for:
- Parsing HTTP requests
- Routing to servlets
- Sending HTTP responses back to clients

**Servlet Framework**: Lightweight servlet implementation similar to standard Java servlets:
- `HttpServlet`: Base class for all servlets with `doGet()` and `doPost()` methods
- `HttpServletRequest`: Encapsulates HTTP request data
- `HttpServletResponse`: Manages HTTP response output

**FileServlet**: Serves static files from the webapp directory
- Handles CSS, JavaScript, and HTML files
- Streams file content to clients

## Configuration

Routes are configured in `src/main/resources/config.properties`:

```properties
/hello=servlet.ServletHello
/style.css=servlet.ServletHello
/script.js=servlet.ServletHello
/singup=servlet.ServletSingup
/stylesSingUp.css=servlet.ServletSingup
/scriptSingUp.js=servlet.ServletSingup
```

Each URL pattern is mapped to a servlet class. When a request comes in, the server matches the URL path to a configured route and delegates to the corresponding servlet.

## Building the Project

### Using Maven

```bash
mvn clean compile
```

### Using Make

```bash
# Compile all Java files
make all

# Run the server
make run

# Clean compiled files
make clean
```

## Running the Server

### Prerequisites
- Java 21 or higher
- Maven (optional, if using Maven build)

### Start the Server

```bash
# Using Maven
mvn clean compile exec:java -Dexec.mainClass="org.web.container.SimpleWebContainer"

# Or using Java directly (after compilation)
java -cp target/classes org.web.container.SimpleWebContainer
```

The server will start on `http://localhost:8888` by default.

### Access the Application

- **Hello Page**: `http://localhost:8888/hello`
- **Sign-up Page**: `http://localhost:8888/singup`

## How It Works

1. **Server Startup**: `SimpleWebContainer` creates a `ServerSocket` and listens for incoming connections on port 8888.

2. **Client Connection**: When a client connects, a new `SocketHandler` thread is spawned to handle that connection.

3. **Request Parsing**: `SocketHandler` reads the HTTP request and parses:
   - HTTP method (GET, POST, etc.)
   - Request path (URL)
   - Headers and body

4. **Servlet Routing**: The server looks up the request path in the configuration to find the corresponding servlet class.

5. **Servlet Processing**: The appropriate servlet's `doGet()` or `doPost()` method is invoked with:
   - `HttpServletRequest`: Contains request details (path, parameters, headers)
   - `HttpServletResponse`: Output stream for writing response data

6. **Response Delivery**: The response is written back to the client as an HTTP response.

## Example Servlet

```java
public class ServletHello extends HttpServlet {
    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) {
        String path = request.getPath();
        String fileName;
        
        if ("/hello".equals(path)) {
            fileName = "HelloPage/hello.html";
        } else if ("/style.css".equals(path)) {
            fileName = "HelloPage/style.css";
        }
        
        FileServlet fileServlet = new FileServlet(fileName, response.getOutPutstream());
        fileServlet.WriteToStream();
    }
}
```

## Technologies Used

- **Java 21**: Core language
- **Maven**: Dependency and build management
- **Make**: Build automation
- **Standard Java Networking**: Socket and ServerSocket APIs

## License

This project is open source and available for educational purposes.

---

**Note**: This project is intended for learning purposes to understand how web servers and servlet containers work. For production use, consider using established containers like Apache Tomcat, Jetty, or Undertow.
