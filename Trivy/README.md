

## What is Trivy?

**Trivy is an open-source vulnerability scanner** used to detect security issues in:

* Docker images
* Source code & dependencies
* Kubernetes YAML files
* Infrastructure as Code (Terraform, Helm)

---

## Why Trivy Exists?

Because container images and dependencies often contain **known vulnerabilities (CVEs)** that can reach production if not scanned early.

---

## What Trivy Scans

* OS packages (apk, apt, yum)
* Language dependencies (npm, pip, maven, go)
* Docker images
* Git repositories
* Kubernetes & Terraform files

---

## How Trivy Works (Internally)

1. Artifact Analyzer – reads image/code
2. Vulnerability Database – downloads CVE data
3. Matching Engine – matches packages with CVEs
4. Report Formatter – formats output
5. Security Report – final results

---

## Installation

### Install Trivy on Linux

```bash
sudo apt install wget -y
wget https://github.com/aquasecurity/trivy/releases/latest/download/trivy_0.50.0_Linux-64bit.deb
sudo dpkg -i trivy_0.50.0_Linux-64bit.deb
```

### Verify Installation

```bash
trivy --version
```

---

## Basic Trivy Commands

### Scan Docker Image

```bash
trivy image nginx:latest
```

### Scan Image with Only High & Critical

```bash
trivy image --severity HIGH,CRITICAL nginx:latest
```

### Scan Local Project (Filesystem)

```bash
trivy fs .
```

### Scan GitHub Repository (Remote)

```bash
trivy repo https://github.com/user/repo
```

### Scan Kubernetes YAML Files

```bash
trivy config .
```

### Scan Terraform Files

```bash
trivy config terraform/
```

---

## Exit Codes (Very Important)

```bash
trivy image --exit-code 1 --severity CRITICAL nginx:latest
```

* exit-code 0 → no issue
* exit-code 1 → vulnerability found (pipeline fails)

---

## Generate HTML Report

### Step 1: Download HTML Template

```bash
wget https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl
```

### Step 2: Generate HTML Output

```bash
trivy image nginx:latest --format template --template @html.tpl -o report.html
```

---

## How to Fix Trivy Issues

1. Update vulnerable packages
2. Upgrade base image
3. Use minimal images (alpine)
4. Remove unused dependencies
5. Accept risk (if no fix available)

---

## Jenkins Integration

### Jenkins Plugins Required

* Docker Pipeline
* Git

### Jenkins Pipeline Example

```groovy
stage('Trivy Scan') {
    steps {
        sh 'trivy image --exit-code 1 --severity CRITICAL myapp:latest'
    }
}
```

Pipeline fails if **critical vulnerabilities** are found.

---

## GitHub Actions Integration

### .github/workflows/trivy.yml

```yaml
name: Trivy Scan
on: [push]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aquasecurity/trivy-action@master
        with:
          scan-type: fs
          severity: CRITICAL
```

---

## Local vs Remote Scanning

### Local

```bash
trivy fs .
```

### Remote Repository

```bash
trivy repo https://github.com/user/repo
```

---

## Common Trivy Flags

| Flag             | Purpose             |
| ---------------- | ------------------- |
| --severity       | Filter severity     |
| --exit-code      | Fail pipeline       |
| --ignore-unfixed | Ignore unfixed CVEs |
| --format         | Output format       |
| -o               | Output file         |

---

## Trivy in DevSecOps

* Shift-left security
* Automated vulnerability scanning
* CI/CD blocking
* Compliance reporting

---

## Trivy vs SonarQube

* Trivy → Security vulnerabilities
* SonarQube → Code quality & bugs

---

## Interview One-Liner

> Trivy is an open-source vulnerability scanner used to detect security risks in containers, code, and infrastructure before production.

---

## Author

Prepared for DevSecOps interviews & real-world usage 🚀
