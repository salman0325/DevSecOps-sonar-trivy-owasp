

# SonarQube + Jenkins + GitHub Actions Integration Guide (Multi-Branch)

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Step 1: Install SonarQube (Docker)](#step-1-install-sonarqube-docker)
3. [Step 2: Setup PostgreSQL Database](#step-2-setup-postgresql-database)
4. [Step 3: Configure SonarQube](#step-3-configure-sonarqube)
5. [Step 4: Create SonarQube Token](#step-4-create-sonarqube-token)
6. [Step 5: Configure Quality Gate](#step-5-configure-quality-gate)
7. [Step 6: Setup Jenkins with SonarQube](#step-6-setup-jenkins-with-sonarqube)
8. [Step 7: Jenkins Pipeline with Multi-Branch SonarQube Scan](#step-7-jenkins-pipeline-with-multi-branch-sonarqube-scan)
9. [Step 8: Setup GitHub Actions with SonarQube](#step-8-setup-github-actions-with-sonarqube)
10. [Step 9: Configure GitHub Webhook for SonarQube](#step-9-configure-github-webhook-for-sonarqube)
11. [Step 10: Additional Plugins and Tools](#step-10-additional-plugins-and-tools)
12. [References](#references)

---

## Prerequisites

* Docker installed (for SonarQube & PostgreSQL containers)
* Jenkins installed and running (with access to plugins)
* GitHub repository for your project
* Basic knowledge of CI/CD pipelines

---

## Step 1: Install SonarQube (Docker)

### 1.1 Start PostgreSQL container (required for SonarQube)

```bash
docker volume create sonarqube-postgres-data

docker run -d --name sonarqube-postgres \
  -e POSTGRES_USER=sonar \
  -e POSTGRES_PASSWORD=sonarpassword \
  -e POSTGRES_DB=sonarqube \
  -v sonarqube-postgres-data:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:13
```

### 1.2 Start SonarQube container connected to PostgreSQL

```bash
docker volume create sonarqube-data

docker run -d --name sonarqube -p 9000:9000 \
  -e SONARQUBE_JDBC_URL=jdbc:postgresql://host.docker.internal:5432/sonarqube \
  -e SONARQUBE_JDBC_USERNAME=sonar \
  -e SONARQUBE_JDBC_PASSWORD=sonarpassword \
  -v sonarqube-data:/opt/sonarqube/data \
  sonarqube:community
```

> **Note:** Replace `host.docker.internal` with your PostgreSQL host IP on Linux.

---

## Step 2: Setup PostgreSQL Database (Optional if using embedded DB)

* Use the above PostgreSQL docker container
* Ensure SonarQube connects via environment variables
* Persistent volume ensures data durability

---

## Step 3: Configure SonarQube

1. Open SonarQube UI: [http://localhost:9000](http://localhost:9000)
2. Login with default credentials:

   * Username: `admin`
   * Password: `admin`
3. Change admin password on first login (recommended)
4. Install necessary plugins (some might be pre-installed):

   * GitHub plugin
   * Jenkins plugin
   * SonarScanner CLI

---

## Step 4: Create SonarQube Token

1. In SonarQube UI, click your profile → **My Account** → **Security**
2. Generate a new token (e.g., `jenkins-token`)
3. Copy and save the token securely (you will use it in Jenkins & GitHub Actions)

---

## Step 5: Configure Quality Gate

1. Navigate to **Quality Gates** in SonarQube UI
2. Modify the default Quality Gate or create a new one for testing
3. Typical metrics to check (for testing, you might loosen them):

   * Code coverage > 50%
   * No new blockers or critical issues
4. Assign this Quality Gate to your projects or branches

---

## Step 6: Setup Jenkins with SonarQube

### 6.1 Install Jenkins Plugins

* SonarQube Scanner
* GitHub Integration Plugin
* Pipeline Plugin

### 6.2 Configure SonarQube Server in Jenkins

* Manage Jenkins → Configure System
* Add SonarQube installation with name (e.g., `SonarQube`)
* Add server URL (e.g., `http://localhost:9000`)
* Add authentication token (the one generated in Step 4)

---

## Step 7: Jenkins Pipeline with Multi-Branch SonarQube Scan

Example `Jenkinsfile`:

```groovy
pipeline {
    agent any
    environment {
        SONARQUBE_TOKEN = credentials('sonarqube-token-id') // Jenkins credential ID
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "sonar-scanner -Dsonar.projectKey=my-project -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=${SONARQUBE_TOKEN}"
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
```

* Use **Multibranch Pipeline job** in Jenkins for multi-branch support
* Configure Jenkins credentials with your SonarQube token

---

## Step 8: Setup GitHub Actions with SonarQube

Example `.github/workflows/sonar.yml`:

```yaml
name: SonarQube Scan

on:
  push:
    branches:
      - '**'

jobs:
  sonar:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Set up JDK 11
        uses: actions/setup-java@v3
        with:
          java-version: '11'

      - name: Cache SonarQube packages
        uses: actions/cache@v3
        with:
          path: ~/.sonar/cache
          key: ${{ runner.os }}-sonar

      - name: SonarQube Scan
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        run: |
          sonar-scanner \
            -Dsonar.projectKey=my-project \
            -Dsonar.organization=my-org \
            -Dsonar.sources=. \
            -Dsonar.host.url=http://your-sonarqube-url \
            -Dsonar.login=$SONAR_TOKEN
```

* Add `SONAR_TOKEN` secret in your GitHub repo secrets (the SonarQube token)
* Replace `your-sonarqube-url` with your actual URL

---

## Step 9: Configure GitHub Webhook for SonarQube

1. Go to your GitHub repository → Settings → Webhooks
2. Add webhook URL:

   ```
   http://<sonarqube-server>/sonarqube-webhook/
   ```
3. Content type: `application/json`
4. Select events: **Push events**
5. Save webhook

This allows SonarQube to receive push notifications and update status checks.

---

## Step 10: Additional Plugins and Tools

* **SonarQube Plugins:**

  * GitHub plugin (for PR decoration)
  * Jenkins plugin (for Jenkins integration)
  * Quality Gate Plugin (built-in)

* **Jenkins Plugins:**

  * SonarQube Scanner plugin
  * Pipeline plugin
  * GitHub Branch Source plugin (for Multibranch pipeline)

* **Others:**

  * Docker (if using containers)
  * Helm (for Kubernetes deployment)

---

# Summary

| Step | Task                           | Notes                              |
| ---- | ------------------------------ | ---------------------------------- |
| 1    | Install SonarQube & PostgreSQL | Use Docker with persistent storage |
| 2    | Configure SonarQube UI         | Change password, install plugins   |
| 3    | Create Token                   | For API & Jenkins/GitHub Actions   |
| 4    | Quality Gate Setup             | Adjust thresholds for testing      |
| 5    | Setup Jenkins                  | Add token, plugins, configure job  |
| 6    | Jenkinsfile for multi-branch   | Use `waitForQualityGate`           |
| 7    | GitHub Actions setup           | Use sonar-scanner CLI              |
| 8    | GitHub Webhook                 | For PR status updates              |

---

## References

* [SonarQube Official Docs](https://docs.sonarqube.org/latest/)
* [SonarQube Scanner CLI](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/)
* [Jenkins SonarQube Plugin](https://plugins.jenkins.io/sonar/)
* [GitHub Actions for SonarQube](https://github.com/SonarSource/sonarcloud-github-action)

---

