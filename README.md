# Spring Boot Okta OAuth2 Application

A modern Spring Boot application demonstrating OAuth2 authentication and authorization using Okta (Auth0) as the identity provider. Features a beautiful glassmorphism UI with Thymeleaf templates and TailwindCSS.

## Overview

This application showcases a complete OAuth2/OIDC authentication flow with:
- Secure login via Okta
- Custom logout handling with redirect to Okta
- User profile display with claims
- Modern, responsive UI with glassmorphism design
- Spring Security integration

## Features

- 🔐 **OAuth2 Authentication** - Secure login with Okta/Auth0
- 👤 **User Profile Display** - Shows authenticated user's name, email, and avatar
- 🚪 **Custom Logout** - Properly redirects to Okta logout endpoint
- 🎨 **Modern UI** - Glassmorphism design with TailwindCSS
- 🔒 **Spring Security** - Industry-standard security configuration
- 📱 **Responsive Design** - Works on all device sizes

## Technology Stack

- **Java 21**
- **Spring Boot 3.5.11**
  - Spring Web
  - Spring Security
  - OAuth2 Client
- **Okta Spring Boot Starter 3.0.5**
- **Thymeleaf** - Server-side template engine
- **TailwindCSS** - Modern CSS framework
- **Maven** - Build tool
- **spring-dotenv** - Environment variable management

## Prerequisites

- Java 21 or higher
- Maven 3.6+
- Okta account (or Auth0 account)

## Project Structure

```
spring-okta/
├── src/
│   ├── main/
│   │   ├── java/io/github/nzuwera/springsecurity/okta/
│   │   │   ├── SpringOktaApplication.java      # Main application
│   │   │   ├── config/
│   │   │   │   └── SecurityConfig.java         # Security configuration
│   │   │   └── controller/
│   │   │       └── HomeController.java         # Home page controller
│   │   └── resources/
│   │       ├── application.yaml                # Application configuration
│   │       ├── static/css/
│   │       │   └── styles.css                  # Additional styles
│   │       └── templates/
│   │           └── index.html                  # Main template
│   └── test/
├── .env                                         # Environment variables (not in repo)
├── pom.xml                                      # Maven configuration
└── README.md                                    # This file
```

## Getting Started

### 1. Create Okta Application

If you don't have an Okta developer account, create one at [developer.okta.com](https://developer.okta.com/) or [auth0.com](https://auth0.com/).

**Using Okta CLI:**
```bash
okta register
okta apps create
```

Select **Web** > **Okta Spring Boot Starter** and accept the default redirect URIs.

**Manual Setup:**
1. Create a new application in Okta/Auth0 dashboard
2. Choose **Web Application**
3. Set the following URLs:
   - **Login redirect URI**: `http://localhost:3000/login/oauth2/code/okta`
   - **Logout redirect URI**: `http://localhost:3000`
4. Note your Domain, Client ID, and Client Secret

### 2. Configure Environment Variables

Create a `.env` file in the project root:

```env
OKTA_DOMAIN=your-domain.okta.com
OKTA_CLIENT_ID=your-client-id
OKTA_CLIENT_SECRET=your-client-secret
```

**Example for Auth0:**
```env
OKTA_DOMAIN=dev-example.eu.auth0.com
OKTA_CLIENT_ID=abc123xyz...
OKTA_CLIENT_SECRET=secret123...
```

### 3. Build and Run

```bash
# Build the application
./mvnw clean package

# Run the application
./mvnw spring-boot:run
```

**Or using Maven directly:**
```bash
mvn clean package
mvn spring-boot:run
```

The application will start on **http://localhost:3000**

### 4. Access the Application

Open your browser and navigate to:
```
http://localhost:3000
```

You'll see the login page. Click **"Continue with Okta"** to authenticate.

## Application Configuration

### application.yaml

```yaml
spring:
  application:
    name: spring-okta

okta:
  oauth2:
    issuer: https://${OKTA_DOMAIN}/
    client-id: ${OKTA_CLIENT_ID}
    client-secret: ${OKTA_CLIENT_SECRET}

server:
  port: 3000
```

**Note:** The issuer URL format depends on your provider:
- **Auth0**: `https://${OKTA_DOMAIN}/`
- **Okta**: `https://${OKTA_DOMAIN}/oauth2/default`

## Security Configuration

The `SecurityConfig.java` (src/main/java/io/github/nzuwera/springsecurity/okta/config/SecurityConfig.java:15) provides:

- **Public access** to `/` and `/images/**`
- **Authenticated access** for all other requests
- **OAuth2 login** configuration
- **Custom logout handler** that redirects to Okta's logout endpoint

### Custom Logout Handler

The application implements a custom logout handler that:
1. Logs out the user from the Spring application
2. Redirects to Okta's logout endpoint
3. Returns the user to the application's base URL

```java
private LogoutHandler logoutHandler() {
    return (request, response, authentication) -> {
        try {
            String baseUrl = ServletUriComponentsBuilder
                .fromCurrentContextPath()
                .build()
                .toUriString();
            response.sendRedirect(
                issuer + "v2/logout?client_id=" + clientId + "&returnTo=" + baseUrl
            );
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    };
}
```

## Endpoints

| Endpoint | Method | Description | Authentication |
|----------|--------|-------------|----------------|
| `/` | GET | Home page - shows login or profile | Public |
| `/oauth2/authorization/okta` | GET | Initiates OAuth2 login | Public |
| `/logout` | GET/POST | Logs out user and redirects to Okta | Authenticated |

## User Interface

The application features a modern glassmorphism design with:

- **Animated blobs** - Decorative background elements
- **Glass cards** - Frosted glass effect with backdrop blur
- **Gradient buttons** - Smooth hover animations
- **Avatar display** - User profile picture with gradient ring
- **Responsive layout** - Mobile-first design

### UI Components

**Login Page:**
- Glassmorphic card with login button
- Animated fade-in effects
- Okta branding

**Profile Page:**
- User avatar with gradient ring
- User name and email display
- Authentication status badge
- Logout buttons (GET and POST methods)

## Development

### Key Files

**SecurityConfig.java** (src/main/java/io/github/nzuwera/springsecurity/okta/config/SecurityConfig.java)
- Configures Spring Security
- Sets up OAuth2 login
- Implements custom logout handler

**HomeController.java** (src/main/java/io/github/nzuwera/springsecurity/okta/controller/HomeController.java)
- Handles home page routing
- Extracts OIDC user claims
- Passes profile data to template

**index.html** (src/main/resources/templates/index.html)
- Thymeleaf template with Spring Security integration
- Conditional rendering for authenticated/unauthenticated states
- TailwindCSS styling

## Troubleshooting

### Common Issues

**1. Port Already in Use**
```
Error: Web server failed to start. Port 3000 was already in use.
```
**Solution:** Change the port in `application.yaml`:
```yaml
server:
  port: 8080
```
Don't forget to update your Okta redirect URIs!

**2. Redirect URI Mismatch**
```
Error: redirect_uri_mismatch
```
**Solution:** Ensure your Okta application has the correct redirect URI:
- `http://localhost:3000/login/oauth2/code/okta`

**3. Invalid Issuer**
```
Error: Invalid issuer
```
**Solution:**
- For Auth0: Use `https://${OKTA_DOMAIN}/`
- For Okta: Use `https://${OKTA_DOMAIN}/oauth2/default`

**4. Environment Variables Not Loaded**
```
Error: Could not resolve placeholder 'OKTA_DOMAIN'
```
**Solution:** Ensure `.env` file exists in project root with correct values.

## Testing

Run the tests:
```bash
./mvnw test
```

## Building for Production

```bash
./mvnw clean package
java -jar target/spring-okta-0.0.1-SNAPSHOT.jar
```

**Important:** Ensure production environment variables are set:
- `OKTA_DOMAIN`
- `OKTA_CLIENT_ID`
- `OKTA_CLIENT_SECRET`

## Reference Documentation

For further reference, please consider the following sections:

* [Official Apache Maven documentation](https://maven.apache.org/guides/index.html)
* [Spring Boot Maven Plugin Reference Guide](https://docs.spring.io/spring-boot/3.5.11/maven-plugin)
* [OAuth2 Client](https://docs.spring.io/spring-boot/3.5.11/reference/web/spring-security.html#web.security.oauth2.client)
* [Okta Spring Boot documentation](https://github.com/okta/okta-spring-boot#readme)
* [Thymeleaf](https://docs.spring.io/spring-boot/3.5.11/reference/web/servlet.html#web.servlet.spring-mvc.template-engines)
* [Spring Web](https://docs.spring.io/spring-boot/3.5.11/reference/web/servlet.html)

### Guides

* [Okta-Hosted Login Page Example](https://github.com/okta/samples-java-spring/tree/master/okta-hosted-login)
* [Custom Login Page Example](https://github.com/okta/samples-java-spring/tree/master/custom-login)
* [Okta Spring Security Resource Server Example](https://github.com/okta/samples-java-spring/tree/master/resource-server)
* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)

## License

This project is provided as-is for educational and demonstration purposes.

## Contributing

Feel free to submit issues and enhancement requests!

## Author

**nzuwera** - [GitHub Profile](https://github.com/nzuwera)

---

Built with ❤️ using Spring Boot and Okta
