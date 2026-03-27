# 🚀 CI/CD Pipeline Project using Jenkins, Docker & AWS EC2

## 📌 Project Overview

This project demonstrates a complete **CI/CD pipeline** where a Node.js application is:

* Built using Docker
* Pushed to Docker Hub
* Automatically deployed on AWS EC2 using Jenkins

---

## 🧠 Architecture

```
GitHub → Jenkins → Docker Build → Docker Hub → EC2 → Running App
```

---

## 🛠️ Tech Stack

* Node.js (Backend App)
* Docker (Containerization)
* Jenkins (CI/CD Automation)
* AWS EC2 (Hosting)
* Docker Hub (Image Registry)

---

## 📁 Project Structure

```
cicd-project/
 ├── app.js
 ├── package.json
 ├── Dockerfile
 ├── Jenkinsfile
 ├── screenshots/
 └── README.md
```

---

## ⚙️ Step 1: Create Node.js App

### app.js

```javascript
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Hello from CI/CD Pipeline 🚀");
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

---

### package.json

```json
{
  "name": "cicd-app",
  "version": "1.0.0",
  "description": "CI/CD Jenkins Docker Project",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

---

## 🐳 Step 2: Create Dockerfile

```Dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
```

---

## ☁️ Step 3: Launch AWS EC2 Instance

1. Go to AWS Console → EC2
2. Launch Ubuntu instance
3. Open ports:

   * 22 (SSH)
   * 8080 (Jenkins)
   * 3000 (App)

---

## 🔐 Step 4: Connect to EC2

```bash
ssh -i key.pem ubuntu@<EC2-IP>
```

---

## ⚙️ Step 5: Install Docker

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo usermod -aG docker ubuntu
```

---

## ⚙️ Step 6: Install Jenkins

```bash
sudo apt install openjdk-17-jdk -y

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
```

---

## 🔑 Step 7: Access Jenkins

```bash
http://<EC2-IP>:8080
```

Get admin password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## 🔐 Step 8: Configure Jenkins

* Install suggested plugins
* Add Docker Hub credentials:

  * Username
  * Password

---

## 🔁 Step 9: Create Jenkins Pipeline

### Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yourdockerhubusername/myapp"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/yourusername/your-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop myapp || true
                docker rm myapp || true
                docker run -d -p 3000:3000 --name myapp $DOCKER_IMAGE
                '''
            }
        }
    }
}
```

---

## 🔗 Step 10: Connect GitHub to Jenkins

1. Create new Pipeline job

2. Select:

   * Pipeline from SCM
   * Add GitHub repo URL

3. Add webhook in GitHub:

```
http://<EC2-IP>:8080/github-webhook/
```

---

## 🌐 Step 11: Access Application

```
http://<EC2-IP>:3000
```

---

## 🔄 How CI/CD Works

1. Push code to GitHub
2. Jenkins triggers automatically
3. Builds Docker image
4. Pushes to Docker Hub
5. Deploys container on EC2
6. App updates instantly

---

🖼️ 1. EC2 Instance Running

📁 File: screenshots/ec2-instance.png

![EC2 Instance](screenshots/ec2-instance.png)
🖼️ 2. Jenkins Dashboard

📁 File: screenshots/jenkins-dashboard.png

![Jenkins Dashboard](screenshots/jenkins-dashboard.png)
🖼️ 3. Jenkins Pipeline Success

📁 File: screenshots/jenkins-success.png

![Pipeline Success](screenshots/jenkins-success.png)
🖼️ 4. Docker Image in Docker Hub

📁 File: screenshots/docker-image.png

![Docker Image](screenshots/docker-image.png)
🖼️ 5. Running Container (EC2 Terminal)

📁 File: screenshots/docker-ps.png

![Docker Running](screenshots/docker-ps.png)
🖼️ 6. Application Output (Browser)

📁 File: screenshots/app-output.png

![App Output](screenshots/app-output.png)