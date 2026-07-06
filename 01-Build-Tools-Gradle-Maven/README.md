# Lab 01 - Building and Packaging Java Application with Gradle

## 🎯 Objective

Learn how to build and package a Java application using Gradle by performing the following tasks:

- Install Gradle
- Clone the project repository
- Run unit tests
- Build the application
- Generate the JAR artifact
- Run the application
- Verify the application works correctly

---

## 📁 Repository

```bash
git clone https://github.com/Ibrahim-Adel15/calculator-gradle.git
```

---

## 🛠 Prerequisites

- Ubuntu (WSL)
- Java 17
- Gradle
- Git

---

## 🚀 Steps

### 1. Clone the repository

```bash
git clone https://github.com/Ibrahim-Adel15/calculator-gradle.git
cd calculator-gradle
```

### 2. Run Unit Tests

```bash
gradle test
```

### 3. Build the Application

```bash
gradle build
```

### 4. Verify Generated Artifact

```bash
ls build/libs
```

Expected output:

```text
calculator.jar
```

### 5. Run the Application

```bash
java -jar build/libs/calculator.jar
```

---

## 📦 Build Output

```
build/
└── libs/
    └── calculator.jar
```

---

## ✅ Result

- Unit tests passed successfully.
- JAR artifact generated.
- Application executed successfully.

---

## 📚 Skills Practiced

- Linux Commands
- Git
- Gradle
- Java
- Build Automation
- Software Packaging
