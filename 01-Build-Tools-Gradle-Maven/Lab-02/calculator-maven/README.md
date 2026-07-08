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
<img width="886" height="254" alt="image" src="https://github.com/user-attachments/assets/1ec49eaf-9b43-4ff2-bc65-50ffd331c43c" />


## Build the Project

Clean and package the application:

```bash
mvn clean package
```
<img width="862" height="269" alt="image" src="https://github.com/user-attachments/assets/a0ca2d4a-fa97-4091-9468-4074b2c0afd1" />


Or install it into the local Maven repository:

```bash
mvn clean install
```

## Run Unit Tests

```bash
mvn test
```
<img width="848" height="327" alt="image" src="https://github.com/user-attachments/assets/9b4d9d07-c080-41ba-bcf8-97149808a268" />


## Generated Artifact

After a successful build, Maven generates the JAR file inside the `target` directory.

Example:

```text
target/calculator.jar
```
<img width="879" height="83" alt="image" src="https://github.com/user-attachments/assets/0eaf64b8-ca38-4542-84c5-7dd09fcc04f9" />


## Run the Application

```bash
java -jar target/calculator.jar
```
<img width="868" height="176" alt="image" src="https://github.com/user-attachments/assets/434ca2ee-c08b-46c4-ae5d-ccb6c413e51f" />

## Verification

The application was successfully:

* Built using Maven.
* Tested using Maven Surefire.
* Packaged as a JAR file.
* Executed successfully from the generated artifact.

## Technologies Used

* Java
* Apache Maven
