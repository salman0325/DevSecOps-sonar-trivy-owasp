## ✅ 1) Prerequisites

✔ A running Kubernetes cluster (min 1.24+)
✔ Helm 3 installed
✔ StorageClass configured (e.g., gp2/gp3 on AWS)
✔ Ingress Controller deployed (NGINX/Traefik)
✔ kubectl configured to connect to cluster

---

## ✅ 2) Namespace & Helm Repo Add

```bash
kubectl create namespace sonarqube
helm repo add sonarqube https://SonarSource.github.io/helm-chart-sonarqube
helm repo update
```

Ye official Helm repo se charts laega. ([docs.sonarsource.com][1])

---

## ✅ 3) Create Secrets for Database

Production me alag PostgreSQL use karo (managed DB / StatefulSet).

### Example secret (PostgreSQL creds)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: sonar-db-secret
  namespace: sonarqube
type: Opaque
data:
  postgresql-password: $(echo -n "StrongDBPass123" | base64)
  postgresql-username: $(echo -n "sonar" | base64)
  postgresql-database: $(echo -n "sonarqube" | base64)
```

Apply:

```bash
kubectl apply -f sonar-db-secret.yaml
```

---

## ✅ 4) Helm Values File (values‑prod.yaml)

Create a file called **values‑prod.yaml** with this content:

```yaml
# values-prod.yaml

postgresql:
  enabled: false          # We supply our own DB via secret
  host: your-postgres.host.com
  port: 5432
  existingSecret: sonar-db-secret
  existingSecretUsernameKey: postgresql-username
  existingSecretPasswordKey: postgresql-password
  existingSecretDatabaseKey: postgresql-database

persistence:
  enabled: true           # Enable persistent data
  storageClass: gp2       # Change as per your cloud/storageclass
  accessMode: ReadWriteOnce
  size: 50Gi

service:
  type: ClusterIP         # Normally internal
  port: 9000

ingress:
  enabled: true
  ingressClassName: nginx
  hosts:
    - host: sonar.example.com
      paths:
        - path: /
          pathType: Prefix
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/proxy-body-size: "64m"
  tls:
    - hosts:
        - sonar.example.com
      secretName: sonar-tls-secret

resources:
  requests:
    cpu: "1000m"
    memory: "2Gi"
  limits:
    cpu: "2000m"
    memory: "4Gi"
```

✔ `postgresql.enabled: false` means external DB (recommended).
✔ Persistent storage for Sonar indexes + data enabled.
✔ Ingress host and TLS configured. ([docs.sonarsource.com][1])

---

## ✅ 5) Deploy SonarQube with Helm

```bash
helm upgrade --install sonarqube sonarqube/sonarqube \
  -n sonarqube \
  -f values-prod.yaml
```

Ye SonarQube ko chart + values ke sath deploy karega.

---

## ✅ 6) PostgreSQL Deployment (Optional)

Agar aap khud cluster me Postgres chahte ho, to ek example simple Deployment/StatefulSet use kar sakte ho:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgresql
  namespace: sonarqube
spec:
  selector:
    matchLabels:
      app: postgresql
  serviceName: "postgresql"
  replicas: 1
  template:
    metadata:
      labels:
        app: postgresql
    spec:
      containers:
        - name: postgres
          image: postgres:15
          env:
            - name: POSTGRES_DB
              value: sonarqube
            - name: POSTGRES_USER
              valueFrom:
                secretKeyRef:
                  name: sonar-db-secret
                  key: postgresql-username
            - name: POSTGRES_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: sonar-db-secret
                  key: postgresql-password
          ports:
            - containerPort: 5432
```

Apply:

```bash
kubectl apply -f postgres-statefulset.yaml
```

Aur `values-prod.yaml` me host ko `postgresql.default.svc.cluster.local` set karo.

---

## ✅ 7) Ingress TLS Secret

Create TLS secret:

```bash
kubectl create secret tls sonar-tls-secret \
  --cert=./tls.crt --key=./tls.key \
  -n sonarqube
```

Use real cert (Let’s Encrypt) in production.

---

## 🎯 Access SonarQube

Baad me browser me visit karo:

```
https://sonar.example.com
```

Default login (first install):

```
Username: admin
Password: admin
```

Aur phir prompt pe strong password set karo.

---

