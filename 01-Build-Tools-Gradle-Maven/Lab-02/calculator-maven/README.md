# Maven Calculator Lab

## Overview

This lab demonstrates the basics of using Apache Maven to build and manage a simple Java application.

## Objectives

* Understand the Maven project structure.
* Build a Java application using Maven.
* Generate a JAR artifact.
* Execute the generated application.
* Verify that the application works correctly.

## Project Structure

```
calculator-maven/
├── pom.xml
├── src/
│   ├── main/java
│   └── test/java
└── target/
```

## Prerequisites

* Java JDK
* Apache Maven

Verify the installation:

```bash
java -version
mvn -version
```

## Build the Project

Clean and package the application:

```bash
mvn clean package
```

Or install it into the local Maven repository:

```bash
mvn clean install
```

## Run Unit Tests

```bash
mvn test
```

## Generated Artifact

After a successful build, Maven generates the JAR file inside the `target` directory.

Example:

```text
target/calculator.jar
```

## Run the Application

```bash
java -jar target/calculator.jar
```

## Verification

The application was successfully:

* Built using Maven.
* Tested using Maven Surefire.
* Packaged as a JAR file.
* Executed successfully from the generated artifact.

## Technologies Used

* Java
* Apache Maven

## Screenshots

Add screenshots of the following:

1. `mvn -version`
2. `mvn test`
3. `mvn clean package`
4. Generated JAR inside the `target` directory (`ls target`)
5. Running the application using:

```bash
java -jar target/calculator.jar
```

6. Successful application output after execution.
