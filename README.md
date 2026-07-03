# Cloud To-Do App — AWS + Docker + CI/CD

A containerized Flask application deployed on AWS EC2 with a fully automated CI/CD pipeline using GitHub Actions.

## 🖥️ Live Demo
http://13.232.217.232
Live demo was hosted on AWS EC2 during development; infrastructure has been decommissioned to avoid ongoing cloud costs.

## 📐 Architecture

```
Developer
   │  git push
   ▼
GitHub Repository
   │  triggers
   ▼
GitHub Actions (CI/CD)
   │  1. Build Docker image
   │  2. Push image to Docker Hub
   ▼
Docker Hub (Image Registry)
   │  3. SSH into EC2 + pull latest image
   ▼
AWS EC2 (Ubuntu, Custom VPC)
   ├── Nginx (Reverse Proxy, Port 80)
   │        │
   │        ▼
   └── Docker Container (Flask App, Port 5000)
```

**Network setup:** Custom VPC → Public Subnet → Internet Gateway → Route Table → Security Group (ports 22, 80) → EC2 Instance

## 🛠️ Tech Stack

| Category         | Tools Used                          |
|-------------------|--------------------------------------|
| Application       | Python, Flask                       |
| Containerization  | Docker                              |
| Cloud Provider    | AWS (EC2, VPC, Subnets, IGW, Route Tables, Security Groups) |
| Web Server        | Nginx (reverse proxy)               |
| CI/CD             | GitHub Actions                      |
| Image Registry    | Docker Hub                          |
| Version Control   | Git & GitHub                        |

## 🚀 Features

- Add and delete to-do tasks via a simple web UI
- Fully containerized application (portable, reproducible builds)
- Custom AWS networking built from scratch (VPC, subnet, IGW, route table, security group)
- Nginx reverse proxy for clean URL access (no exposed app port)
- Automated CI/CD: every `git push` to `main` triggers build → push → deploy with zero manual steps

## ⚙️ CI/CD Pipeline

Defined in `.github/workflows/deploy.yml`. On every push to `main`:

1. **Checkout** — pulls the latest code
2. **Docker Login** — authenticates to Docker Hub using repository secrets
3. **Build & Push** — builds the Docker image and pushes it to Docker Hub
4. **Deploy to EC2** — SSHes into the EC2 instance, pulls the new image, stops/removes the old container, and starts the new one

All credentials (Docker Hub token, EC2 host, SSH key) are stored securely as **GitHub Actions Secrets** — never committed to the repository.

## 📦 Local Setup

```bash
# Clone the repository
git clone https://github.com/omkarbhosale-7/todo-app.git
cd todo-app

# Build the Docker image
docker build -t todo-app .

# Run the container
docker run -p 5000:5000 todo-app
```

Visit `http://localhost:5000` in your browser.

## ☁️ Manual AWS Deployment (Summary)

1. Created a custom VPC with a public subnet
2. Created and attached an Internet Gateway
3. Configured a route table (`0.0.0.0/0 → IGW`) and associated it with the subnet
4. Created a Security Group allowing inbound SSH (22) and HTTP (80)
5. Launched an EC2 instance (Ubuntu) inside the custom VPC/subnet
6. Installed Docker on EC2, pulled the image from Docker Hub, and ran the container
7. Installed and configured Nginx as a reverse proxy (port 80 → port 5000)

## 🔒 Security Notes

- SSH private keys and other secrets are excluded via `.gitignore` and stored only as GitHub Actions Secrets
- Security Group rules restrict inbound traffic to only required ports (22, 80)

## 📈 Future Improvements

- Add persistent storage using RDS or a mounted volume (currently in-memory)
- Add HTTPS via an SSL certificate (AWS ACM + Load Balancer)
- Migrate infrastructure provisioning to Terraform (Infrastructure as Code)
- Add CloudWatch monitoring and alerting
- Container orchestration with Kubernetes (EKS)

---

**Author:** Omkar Bhosale
**GitHub:** [github.com/omkarbhosale-7](https://github.com/omkarbhosale-7)
**Docker Hub:** [hub.docker.com/u/omkarbhosale07](https://hub.docker.com/u/omkarbhosale07)
