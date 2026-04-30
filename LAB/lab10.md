# Experiment 10

## Working with SonarQube

---

# Introduction

## What is SonarQube?

SonarQube is an open-source platform for continuous inspection of code quality. It performs static code analysis to detect:

* Bugs
* Code smells
* Security vulnerabilities

---

## How SonarQube Solves the Problem

* **Continuous Inspection** → Scans code on every commit
* **Quality Gates** → Defines pass/fail criteria
* **Technical Debt** → Measures effort to fix issues
* **Multi-language Support** → Supports 20+ languages
* **Visual Dashboard** → Displays code metrics

---

## Key Terms

| Term           | Meaning                            |
| -------------- | ---------------------------------- |
| Quality Gate   | Criteria to pass before deployment |
| Bug            | Code that may break                |
| Vulnerability  | Security issue                     |
| Code Smell     | Poorly written code                |
| Technical Debt | Time required to fix issues        |
| Coverage       | % of tested code                   |
| Duplication    | Repeated code                      |

---

# Practical

---

## Step 1: Create `docker-compose.yml`

```yaml id="compose"
version: '3.8'

services:
  sonar-db:
    image: postgres:13
    container_name: sonar-db
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonarqube
    volumes:
      - sonar-db-data:/var/lib/postgresql/data
    networks:
      - sonarqube-lab

  sonarqube:
    image: sonarqube:lts-community
    container_name: sonarqube
    ports:
      - "9000:9000"
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://sonar-db:5432/sonarqube
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonar-data:/opt/sonarqube/data
      - sonar-extensions:/opt/sonarqube/extensions
    depends_on:
      - sonar-db
    networks:
      - sonarqube-lab

volumes:
  sonar-db-data:
  sonar-data:
  sonar-extensions:

networks:
  sonarqube-lab:
    driver: bridge
```

📸 Screenshot:
`![Docker Compose File](../screenshots/lab10/Screenshot%20(710).png)

---

## Step 2: Start SonarQube

```bash id="start"
docker-compose up -d
```

📸 Screenshot:
`![Docker Compose Up](../screenshots/lab10/Screenshot%20(710).png)

---

## Step 3: Verify Using Logs

```bash id="logs"
docker-compose logs -f sonarqube
```

---

## Step 4: Login to SonarQube

👉 Open in browser:
http://localhost:9000

* Username: `admin`
* Password: `admin`

📸 Screenshot:
`![Login](../screenshots/lab10/Screenshot%202026-04-30%20232121.png)

---

## Step 5: Home Page

📸 Screenshot:
`![Homepage](../screenshots/lab10/Screenshot%202026-04-30%20232136.png)

---

## Step 6: Create Sample Java App

```bash id="mkdir"
mkdir -p sample-java-app/src/main/java/com/example
cd sample-java-app
```


## Step 7: Create `Calculator.java`

```java id="java"
package com.example;

public class Calculator {

    public int divide(int a, int b) {
        return a / b;
    }

    public int add(int a, int b) {
        int result = a + b;
        int unused = 100;
        return result;
    }

    public String getUser(String userId) {
        String query = "SELECT * FROM users WHERE id = " + userId;
        return query;
    }

    public int multiply(int a, int b) {
        int result = 0;
        for (int i = 0; i < b; i++) {
            result = result + a;
        }
        return result;
    }

    public int multiplyAlt(int a, int b) {
        int result = 0;
        for (int i = 0; i < b; i++) {
            result = result + a;
        }
        return result;
    }

    public String getName(String name) {
        return name.toUpperCase();
    }

    public void riskyOperation() {
        try {
            int x = 10 / 0;
        } catch (Exception e) {
        }
    }
}
```

📸 Screenshot:
`![Java File](../screenshots/lab10/Screenshot%202026-04-30%20232232.png)

---

## Step 8: Create `pom.xml`

```xml id="pom"
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>sample-app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <properties>
    <sonar.projectKey>sample-java-app</sonar.projectKey>
    <sonar.host.url>http://localhost:9000</sonar.host.url>
    <sonar.login>YOUR_TOKEN_HERE</sonar.login>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.2</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.sonarsource.scanner.maven</groupId>
        <artifactId>sonar-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```


## Step 9: Generate Token

* Go to: **Profile → My Account → Security**
* Create token: `scanner-token`
---

## Step 10: Run Scanner

```bash id="scanner"
mvn sonar:sonar -Dsonar.login=YOUR_TOKEN
```

📸 Screenshot:
`![Scanner Run](../screenshots/lab10/Screenshot%20(707).png)

---

## Step 11: Verify Build Success

📸 Screenshot:
`![Build Success](../screenshots/lab10/Screenshot%202026-04-30%20232343.png)

---

## Step 12: View Dashboard

👉 Open:
http://localhost:9000/dashboard?id=sample-java-app

📸 Screenshot:
`![Dashboard](../screenshots/lab10/Screenshot%20(706).png)
📸 Screenshot:
`![Dashboard](../screenshots/lab10/Screenshot%202026-04-20%20114053.png)
📸 Screenshot:
`![Report](../screenshots/lab10/Screenshot%202026-04-20%20103310.png)
📸 Screenshot:
`![Report](../screenshots/lab10/Screenshot%202026-04-18%20151951.png)
---

## Step 13: View Detailed Report

* Click issues
* View exact line and reason

📸 Screenshot:
`![Report](../screenshots/lab10/Screenshot%202026-04-18%20151930.png)

---

# Conclusion

SonarQube helps in maintaining code quality by detecting bugs, vulnerabilities, and improving maintainability through continuous inspection.

---

# Key Takeaways

* Continuous code inspection
* Improves code quality
* Detects bugs & vulnerabilities
* Provides visual reports
* Integrates with CI/CD pipelines

---
