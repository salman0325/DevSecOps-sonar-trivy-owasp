### ✅ OWASP Full Form

**OWASP** = **Open Web Application Security Project**

Ye ek non-profit organization hai jo web application security ke liye free standards aur best practices deta hai.

Sabse famous document:
👉 **OWASP Top 10**

Ye web applications ke **top 10 security risks** batata hai.

---

# 🏆 OWASP Top 10 (Latest Major List)

Main aapko har ek ke liye bataunga:

* ❓ Why happens
* 💥 Damage
* 🛡 Prevention (Practical)

---

# 1️⃣ Broken Access Control

## ❓ Why Happens

* Role check nahi hota
* Direct URL access allowed
* IDOR (Insecure Direct Object Reference)
* Backend validation missing

Example:
User A dusre user ka data URL change karke access kar leta hai.

## 💥 Damage

* Unauthorized data access
* Admin privilege takeover
* Account hijacking

## 🛡 Prevention

✔ Backend me role-based access control
✔ Never trust frontend validation
✔ Use frameworks security features (Spring Security)
✔ Proper authorization middleware

---

# 2️⃣ Cryptographic Failures

(Pehle isko Sensitive Data Exposure kehte the)

## ❓ Why Happens

* Password plain text me store
* Weak hashing (MD5, SHA1)
* No HTTPS
* Hardcoded secret keys

## 💥 Damage

* Password leak
* Credit card theft
* Data breach

## 🛡 Prevention

✔ Use strong hashing (bcrypt, Argon2)
✔ Use HTTPS (TLS)
✔ Encrypt sensitive data
✔ Never hardcode secrets

---

# 3️⃣ Injection (SQL, Command, LDAP etc)

## ❓ Why Happens

User input directly query me use karna.

Example:

```sql
SELECT * FROM users WHERE id = ' " + userInput + " '
```

## 💥 Damage

* Database leak
* Data deletion
* Full system compromise

## 🛡 Prevention

✔ Use Prepared Statements
✔ Parameterized queries
✔ ORM (Hibernate, JPA)
✔ Input validation

---

# 4️⃣ Insecure Design

## ❓ Why Happens

* Security planning hi nahi
* No threat modeling
* Business logic flaws

Example:
Password reset me unlimited attempts.

## 💥 Damage

* Logic bypass
* Fraud
* Business loss

## 🛡 Prevention

✔ Threat modeling
✔ Secure SDLC
✔ Rate limiting
✔ Proper architecture planning

---

# 5️⃣ Security Misconfiguration

## ❓ Why Happens

* Default passwords
* Open ports
* Debug mode enabled
* Directory listing enabled

## 💥 Damage

* Server takeover
* Sensitive config leak

## 🛡 Prevention

✔ Disable default credentials
✔ Proper firewall
✔ Production me debug off
✔ Regular configuration review

---

# 6️⃣ Vulnerable & Outdated Components

## ❓ Why Happens

* Old libraries
* Known CVE vulnerabilities
* No dependency update

## 💥 Damage

* Remote code execution
* Full system hack

## 🛡 Prevention

✔ Regular dependency update
✔ Use dependency-check tools
✔ Patch management

---

# 7️⃣ Identification & Authentication Failures

## ❓ Why Happens

* Weak passwords allowed
* No MFA
* Session timeout nahi
* JWT validation missing

## 💥 Damage

* Account takeover
* Session hijacking

## 🛡 Prevention

✔ Strong password policy
✔ MFA enable
✔ Secure session management
✔ Secure cookies

---

# 8️⃣ Software & Data Integrity Failures

## ❓ Why Happens

* Unsigned software updates
* CI/CD compromise
* Using untrusted plugins

## 💥 Damage

* Supply chain attack
* Malware injection

## 🛡 Prevention

✔ Verify digital signatures
✔ Secure CI/CD
✔ Trusted repositories use karein

---

# 9️⃣ Security Logging & Monitoring Failures

## ❓ Why Happens

* Logs collect nahi hote
* Alert system nahi
* Monitoring disabled

## 💥 Damage

* Attack detect nahi hota
* Late incident response

## 🛡 Prevention

✔ Centralized logging
✔ SIEM tools
✔ Real-time alert system

---

# 🔟 Server-Side Request Forgery (SSRF)

## ❓ Why Happens

* Server external URL fetch karta hai without validation

Example:
Attacker internal AWS metadata access karta hai.

## 💥 Damage

* Internal network access
* Cloud credential leak

## 🛡 Prevention

✔ Whitelist URLs
✔ Block internal IP ranges
✔ Network segmentation

---

# 🧠 Practical Developer Checklist (For Spring Boot)

Since aap Spring Boot project use kar rahe ho:

✔ Use Spring Security
✔ Use JPA (avoid raw SQL)
✔ Store password with BCrypt
✔ Enable HTTPS
✔ Validate input (@Valid)
✔ Keep dependencies updated
✔ Disable actuator sensitive endpoints
✔ Use Docker secure configs
✔ Run SonarQube scan regularly

---

# 🎯 Real Attack Flow Example

Bank App me agar:

* No auth check → Broken Access Control
* Plain password → Cryptographic failure
* Raw SQL → Injection
* Debug open → Misconfiguration

To attacker:

1. SQL injection karega
2. Admin password nikal lega
3. Login karega
4. Funds transfer karega

