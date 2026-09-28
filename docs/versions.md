# Updating Versions of Java and/or Maven

This starter is backend only (Spring Boot); there is no frontend, so there
is no Node/npm version to maintain.

## Updating the Java version

When updating the version of Java used, the following places need to be adjusted:

* The "Java 25 setup with SDKMAN" section of `README.md`
* `<java.version>` in `pom.xml`
* `.sdkmanrc` (the full SDKMAN identifier, e.g. `java=25.0.4-tem`; used by `sdk env` locally)
* `.java-version` (the major version only, e.g. `25`; read by `actions/setup-java` in the
  workflows under `.github/workflows` *and* by the shared reusable workflows in
  [ucsb-cs156/workflows](https://github.com/ucsb-cs156/workflows), which cannot parse the
  `25.0.4-tem` form, so keep this file in the plain-number form)
* `system.properties`
* `Dockerfile` used for deploying on Dokku (both the `maven:...-eclipse-temurin-NN-...` build
  image and the `eclipse-temurin:NN-jre-...` runtime image)
* `.github/copilot-instructions.md`

Also check the course-wide values `jdk_distribution`, `java_version` and `maven_version`
in the term website's `_config.yml` (e.g. `ucsb-cs156/f26`), which the lab instructions
and installation pages use.

## Updating the Maven version

* The "Java 25 setup with SDKMAN" section of `README.md`
* `.mvn/wrapper/maven-wrapper.properties` (`distributionUrl`), used by `./mvnw`
* `Dockerfile` (the `maven:` build image tag)

## Lombok and Java 23+

Starting with JDK 23, `javac` no longer runs annotation processors (such as Lombok) that it
merely finds on the classpath.  The `maven-compiler-plugin` configuration in `pom.xml`
(`<proc>full</proc>`) turns that back on; without it every Lombok-generated symbol
(`log`, `builder()`, getters, ...) produces a `cannot find symbol` error.
