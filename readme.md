# Java Library Remediation Proof of Concept

This is a simple Maven-based Java project demonstrating library remediation with specific versions and custom repositories.

## Prerequisites

- Java 21
- Maven

## Project Details

- **Group ID**: `com.edw`
- **Artifact ID**: `java-lib-remediation-poc`
- **Version**: `1.0-SNAPSHOT`

## Build and Package

To build the project and generate the executable JAR file, run the following command from the root directory:

```bash
mvn clean package -s settings.xml
```

The `-s settings.xml` flag ensures that the build uses the custom repository configurations defined in the project's settings file.

## Running the Application

After a successful build, you can run the application using:

```bash
java -cp target/java-lib-remediation-poc-1.0-SNAPSHOT.jar com.edw.Main
```

Expected Output:
```text
Hello World!
```
