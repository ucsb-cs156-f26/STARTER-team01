# STARTER-team01

Instructions: <https://ucsb-cs156.github.io/f26/lab/team01.html>

TODO: change heading above to your repo name, e.g. `# team01-f26-03`

TODO: Add a link to the deployed Dokku app for your team here, e.g.

Deployments:

* Prod: <https://team01.dokku-03.cs.ucsb.edu>
* QA: <https://team01-qa.dokku-03.cs.ucsb.edu>

TODO: Fill in this table with correct information. 

| Table                     | Name         | Github Id |
|---------------------------|--------------|-----------|
| UCSBDiningCommonsMenuItem |              |           |
| UCSBOrganization          |              |           |
| RecommendationRequest     |              |           |
| MenuItemReview            |              |           |
| HelpRequest               |              |           |
| Articles                  |              |           |

Remember though, that in spite of these initial  assignments, it is still
a team project.  Please help other team members to finish their work
after completing your own.

# Java 25 setup with SDKMAN

This project follows the course instructions for Java 25.0.4, using the
recommended `25.0.4-tem` distribution via SDKMAN, with Maven 3.9.16
(also provided by the included Maven Wrapper, `./mvnw`).
See the [course software installation instructions](https://ucsb-cs156.github.io/f26/info/software.html)
for details on installing SDKMAN, Java and Maven.

If you use SDKMAN, the setup is:

```bash
sdk install java 25.0.4-tem
sdk install maven 3.9.16
sdk env install
java -version
mvn --version
```

The project includes an `.sdkmanrc` file so that the correct Java version is
selected automatically when SDKMAN is present (run `sdk env` in this
directory to select it).  Every `mvn` command below can also be run as
`./mvnw`, which downloads and uses the pinned Maven version without a
separate Maven install.

Both `java -version` and `mvn --version` should report Java 25.0.4 before you
run any of the commands below; with an older Java you may see confusing build
errors (for example, JaCoCo or Pitest complaining about an
`Unsupported class file major version`, or dozens of `cannot find symbol`
errors for Lombok-generated methods).

There is no frontend in this starter (only the Spring Boot backend and its
login page), so Node and npm are not needed for team01.
See [docs/versions.md](docs/versions.md) for the list of files to update
when changing the Java or Maven version.

# Brief overview of starter code 

TODO: remove this header and content of this section before submitting.
However leave the section `# Overview of application` and its content 
intact.

The starter code is a Spring Boot backend with working CRUD endpoints
(POST, GET all, GET by id, PUT, DELETE) for a few example database tables
such as `UCSBDates`, `UCSBDiningCommons` and `Restaurants`, plus the
infrastructure those need (OAuth login, an H2 database on localhost,
Postgres on Dokku, Liquibase migrations, Swagger, JaCoCo and Pitest).
The only frontend is the login page; you interact with the API through
Swagger.

You can use this code as a basis to:
* Add the backend code for your team's six new tables *in stages* as
  suggested in the issues (doing that in "one giant pull request" is
  *not* recommended), using the existing tables as examples.

# Overview of application

When complete, this application will have the following features:

* An endpoint to POST each entity type to the database
* An endpoint to GET each entity type from the database
* An endpoint to PUT each entity type in the database
* An endpoint to DELETE each entity type from the database
* An endpoint to GET a list of all entity types from the database
# Setup before running application

Before running the application for the first time,
you need to do the steps documented in [`docs/oauth.md`](docs/oauth.md).

Otherwise, when you try to login for the first time, you 
will likely see an error such as:

<img src="https://user-images.githubusercontent.com/1119017/149858436-c9baa238-a4f7-4c52-b995-0ed8bee97487.png" alt="Authorization Error; Error 401: invalid_client; The OAuth client was not found." width="400"/>

# Getting Started on localhost
* Start up the backend with:
  ``` 
  mvn spring-boot:run
  ```

Then, the app should be available on <http://localhost:8080>

If it doesn't work at first, e.g. you have a blank page on  <http://localhost:8080>, give it a minute and a few page refreshes.  Sometimes it takes a moment for everything to settle in.


# Getting Started on Dokku

See: [/docs/dokku.md](/docs/dokku.md)

# Accessing swagger

To access the swagger API endpoints, use:

* <http://localhost:8080/swagger-ui/index.html>

Or add `/swagger-ui/index.html` to the URL of your dokku deployment.

# SQL Database access

On localhost:
* The SQL database is an H2 database and the data is stored in a file under `target`
* Each time you do `mvn clean` the database is completely rebuilt from scratch
* You can access the database console via a special route, <http://localhost:8080/h2-console>
* For more info, see [docs/h2-database.md](/docs/h2-database.md)

On Dokku, follow instructions for Dokku databases:
* <https://ucsb-cs156.github.io/topics/dokku/postgres_database.html>

# Testing

## Unit Tests

* To run all unit tests, use: `mvn test`
* To run only the tests from `FooTests.java` use: `mvn test -Dtest=FooTests`

Unit tests are any methods labelled with the `@Test` annotation that are under the `/src/test/java` hierarchy, and have file names that end in `Test` or `Tests`

## Integration Tests

To run only the integration tests, use:
```
INTEGRATION=true mvn test-compile failsafe:integration-test
```

To run only the integration tests *and* see the tests run as you run them,
use:

```
INTEGRATION=true HEADLESS=false mvn test-compile failsafe:integration-test
```

To run a particular integration test (e.g. only `HomePageWebIT.java`) use `-Dit.test=ClassName`, for example:

```
INTEGRATION=true mvn test-compile failsafe:integration-test -Dit.test=HomePageWebIT
```

or to see it run live:
```
INTEGRATION=true HEADLESS=false mvn test-compile failsafe:integration-test -Dit.test=HomePageWebIT
```

Integration tests are any methods labelled with `@Test` annotation, that are under the `/src/test/java` hierarchy, and have names starting with `IT` (specifically capital I, capital T).

By convention, we are putting Integration tests (the ones that run with Playwright) under the package `src/test/java/edu/ucsb/cs156/example/web`.

Unless you want a particular integration test to *also* be run when you type `mvn test`, do *not* use the suffixes `Test` or `Tests` for the filename.

Note that while `mvn test` is typically sufficient to run tests, we have found that if you haven't compiled the test code yet, running `mvn failsafe:integration-test` may not actually run any of the tests.


## Partial pitest runs

This repo has support for partial pitest runs

For example, to run pitest on just one class, use:

```
mvn pitest:mutationCoverage -DtargetClasses=edu.ucsb.cs156.example.controllers.RestaurantsController
```

To run pitest on just one package, use:

```
mvn pitest:mutationCoverage -DtargetClasses=edu.ucsb.cs156.example.controllers.\*
```

To run full mutation test coverage, as usual, use:

```
mvn pitest:mutationCoverage
```
