# Copilot Instructions for the Starter Team 01 Repository

## Context

This is a Spring Boot backend web application (Java 25, Spring Boot 3.5).
There is no frontend beyond the OAuth login page; the API is used through
Swagger at `/swagger-ui/index.html`.

We use JUnit 5 for backend unit tests, JaCoCo for coverage and Pitest for
mutation testing.  Both must be at 100% for code under `src/main/java`
(see the exclusions in `pom.xml`).

## Code Standards

Always reference these instructions first and fall back to search or bash
commands only when you encounter unexpected information that does not match
the info here.

### Required Before Each Commit

- Use `mvn test` to run the backend tests
- Use `mvn test jacoco:report` to generate a code coverage report and ensure that the coverage is 100%
- Use `mvn pitest:mutationCoverage` to ensure that all mutations are killed and that the mutation testing score is at 100%
- Code is formatted with google-java-format via the `git-code-format-maven-plugin`
  (a pre-commit hook is installed by `mvn` automatically; `mvn git-code-format:format-code` formats everything)

## Key Guidelines

1. Follow Spring Boot best practices and idiomatic patterns for Java code
2. Maintain existing code structure and organization: entities in `entities/`,
   repositories in `repositories/`, controllers in `controllers/`, Liquibase
   migration files in `src/main/resources/db/migration/changes/`
3. Every new database table needs an `@Entity`, a `@Repository`, a Liquibase
   change file (registered in `changelog-master.json`), a controller that
   extends `ApiController`, and controller tests that extend `ControllerTestCase`

## Working Effectively

### Toolchain (Java 25 via SDKMAN)

```bash
# Install SDKMAN, then:
sdk install java 25.0.4-tem
sdk install maven 3.9.16
sdk env            # selects the Java version from .sdkmanrc
java -version      # must report 25.0.4
mvn --version      # must report Maven 3.9.16 and Java 25
```

`./mvnw` can be used in place of `mvn`; it downloads the pinned Maven version.
On Ubuntu without SDKMAN, `sudo apt-get install -y openjdk-25-jdk` also works
if `JAVA_HOME` is set to the matching `/usr/lib/jvm/java-25-openjdk-*` path.

### Build and Test Commands

```bash
# Backend unit tests - NEVER CANCEL: takes about 3 minutes. Set timeout to 10+ minutes.
mvn test

# Coverage and mutation testing
mvn test jacoco:report            # report in target/site/jacoco/index.html
mvn pitest:mutationCoverage       # report in target/pit-reports/index.html

# Backend integration tests (Playwright) - NEVER CANCEL: takes about 2 minutes.
INTEGRATION=true mvn test-compile failsafe:integration-test

# Production build (what Dokku runs) - NEVER CANCEL: takes about 2 minutes.
PRODUCTION=true mvn -DskipTests clean dependency:list install
```

### Running the Application

- Copy `.env.SAMPLE` to `.env`: `cp .env.SAMPLE .env`
- OAuth setup is required for login functionality (see `docs/oauth.md`)
- Start the backend with `mvn spring-boot:run`; it runs on http://localhost:8080
- H2 database console: http://localhost:8080/h2-console
- Swagger API docs: http://localhost:8080/swagger-ui/index.html

## Architecture and Technology Stack

- **Framework**: Spring Boot 3.5 on Java 25
- **Database**: H2 (development), PostgreSQL (production on Dokku), Liquibase migrations
- **Authentication**: Google OAuth 2.0
- **API Documentation**: Swagger/OpenAPI (springdoc)
- **Testing**: JUnit 5, Mockito, MockMvc, Playwright (integration), JaCoCo, Pitest
- **Build Tool**: Maven (Maven Wrapper included)

## Known Issues and Workarounds

- **Java Version**: Must use Java 25. If Lombok-generated symbols (`log`, `builder()`,
  getters) are reported as `cannot find symbol`, the compiler is not running annotation
  processors; `pom.xml` sets `<proc>full</proc>` on `maven-compiler-plugin` for this.
- **Integration Test Failures**: Playwright driver creation errors are expected in some CI environments
- **Long Build Times**: Initial Maven builds download many dependencies (3+ minutes is normal)
- **Database Reset**: `mvn clean` completely rebuilds the H2 database from scratch

## Directory Structure

```
.
├── .github/workflows/          # CI/CD pipelines
├── docs/                       # Documentation
├── src/main/java/              # Java source code
├── src/main/resources/         # application*.properties, Liquibase migrations
├── src/test/java/              # Java unit and integration tests
├── .env.SAMPLE                 # Environment template
├── .sdkmanrc                   # Java version for SDKMAN (25.0.4-tem)
├── .java-version               # Java major version for GitHub Actions (25)
└── pom.xml                     # Maven configuration
```

## Before a Pull Request

1. `mvn test` (NEVER CANCEL)
2. `mvn test jacoco:report` and `mvn pitest:mutationCoverage` show 100%
3. `PRODUCTION=true mvn -DskipTests clean install` succeeds
4. Manual smoke test: start the backend and exercise the new endpoints in Swagger
