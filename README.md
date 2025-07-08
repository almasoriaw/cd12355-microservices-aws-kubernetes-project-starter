# Coworking Space Service Extension

The Coworking Space Service provides APIs enabling users to request one-time tokens and administrators to authorize coworking space access. This project extends the service with an **analytics microservice**, deployed securely and scalably on AWS using Kubernetes.

---

## 📌 Project Context

You are viewing the deployment repository for the **Coworking Space Service**. This microservice exposes endpoints to generate reports on coworking space usage for business analysts.

As DevOps engineers, our role is to ensure the application:

- Is built and versioned consistently
- Is deployed reproducibly and securely
- Can be updated via an automated pipeline with minimal manual intervention

---

## 🛠️ Technologies & Tools

| Technology | Purpose |
|---|---|
| **Docker** | Containerizes the Flask-based analytics application for portability and reproducibility. |
| **AWS ECR (Elastic Container Registry)** | Hosts Docker images securely in a private registry. |
| **AWS CodeBuild** | Automates building Docker images and pushing to ECR as part of CI/CD. |
| **Amazon EKS (Elastic Kubernetes Service)** | Manages the Kubernetes cluster running microservices at scale. |
| **Kubernetes ConfigMap & Secret** | Manages environment configuration and sensitive credentials securely in the cluster. |
| **AWS CloudWatch** | Aggregates logs for application monitoring and debugging. |
Future use | **Helm (Bitnami PostgreSQL Chart)** | Simplifies database deployment with preconfigured Kubernetes resources. |

---

## 🚀 Deployment Architecture

The deployment process integrates these tools as follows:

1. **Build Phase**
   - Code changes are pushed to the repository.
   - **AWS CodeBuild** builds a new Docker image tagged with semantic versioning (e.g., `1.2.0`) or short commit hashes for traceability.
   - The built image is pushed to **Amazon ECR**.

2. **Deploy Phase**
   - Kubernetes manifests (`configmap.yaml`, `secret.yaml`, `coworking.yaml`) are updated to reference the new image tag and configuration if necessary.
   - ConfigMaps store plaintext configuration (e.g., DB host, username) and Secrets store sensitive data (e.g., DB password), injected into the Deployment via environment variables.
   - Deployment files are applied to the **Amazon EKS cluster**, triggering rolling updates managed by Kubernetes.

3. **Run & Monitor**
   - The application is exposed via a LoadBalancer service, and endpoints are accessible externally.
   - Logs are streamed to **AWS CloudWatch** for operational visibility.

---

## 🧩 Releasing New Builds

For experienced developers deploying changes:

1. **Update application code** with required changes and commit to the main branch (or relevant CI trigger branch).
2. **Verify CodeBuild pipeline completion** – ensuring the Docker image is built and pushed to ECR successfully.
3. **Update `coworking.yaml`** to reference the new image tag to trigger a rollout.
4. **Apply updated Kubernetes manifests:**

   ```bash
   kubectl apply -f configmap.yaml
   kubectl apply -f secret.yaml
   kubectl apply -f coworking.yaml
