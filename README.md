# DevSecOps Interview Guide (Beginner to Intermediate)


## What is DevSecOps?

DevSecOps is the practice of **integrating security into every stage of the DevOps lifecycle**. Security is automated and treated as a shared responsibility.

---

## Why DevSecOps is Important?

* Fix security issues early (shift-left)
* Reduce production risks
* Faster and safer releases
* Automation instead of manual security

---

## DevSecOps Lifecycle

1. Code
2. Build
3. Test
4. Scan (Security)
5. Deploy
6. Monitor

Security is added at **every stage**.

---

# Top 20 DevSecOps Interview Questions & Answers

## 1. What is DevSecOps?

**Answer:** DevSecOps means adding security into DevOps so applications are secure from the start.

## 2. DevOps vs DevSecOps?

**Answer:** DevOps focuses on speed and automation, DevSecOps adds security into the same process.

## 3. What is Shift-Left Security?

**Answer:** Performing security checks early in development instead of after deployment.

## 4. What is CI/CD in DevSecOps?

**Answer:** CI/CD automates building, testing, scanning, and deploying applications securely.

## 5. What is SAST?

**Answer:** Static Application Security Testing scans source code without running it.

## 6. What is DAST?

**Answer:** Dynamic Application Security Testing scans a running application.

## 7. What is SCA?

**Answer:** Software Composition Analysis scans third-party libraries for vulnerabilities.

## 8. What is a Vulnerability?

**Answer:** A weakness in software that attackers can exploit.

## 9. What is CVE?

**Answer:** CVE is a unique ID assigned to known security vulnerabilities.

## 10. What is Severity?

**Answer:** It shows how dangerous a vulnerability is (Low, Medium, High, Critical).

---

# DevSecOps Tools

## 11. What is SonarQube?

**Answer:** SonarQube analyzes source code for bugs, code smells, and security issues.

## 12. What is Trivy?

**Answer:** Trivy scans Docker images, code, and Kubernetes files for vulnerabilities.

## 13. SonarQube vs Trivy?

**Answer:** SonarQube checks code quality; Trivy checks security vulnerabilities.

## 14. What is a Quality Gate?

**Answer:** A rule in SonarQube that decides whether the build should pass or fail.

## 15. Can Quality Gate stop deployment?

**Answer:** Yes, if conditions fail, deployment is blocked.

---

# Container & Kubernetes Security

## 16. How do you secure Docker images?

**Answer:** Use minimal images and scan them using Trivy.

## 17. How do you secure Kubernetes?

**Answer:** Scan YAML files, use RBAC, and restrict permissions.

---

# Secrets & Configuration

## 18. What is Secret Management?

**Answer:** Securely storing passwords, tokens, and keys.

## 19. Why secrets should not be stored in Git?

**Answer:** Git repositories are shared and can expose sensitive data.

---

# Real-World & Scenario Questions

## 20. What happens if a critical vulnerability is found in pipeline?

**Answer:** The pipeline fails and deployment is blocked until fixed.

## 21. How do you implement DevSecOps in real projects?

**Answer:** By integrating SonarQube and Trivy into CI/CD pipelines and enforcing security policies.

---

## Common DevSecOps Tools

* Jenkins / GitHub Actions
* SonarQube
* Trivy
* Docker
* Kubernetes
* Vault

---

## Final Interview Tip

If asked **"How do you implement DevSecOps?"**, answer:

> We integrate automated security tools into CI/CD pipelines to detect vulnerabilities early and prevent insecure deployments.

---

### Author

Prepared for DevSecOps learners & interview pre
