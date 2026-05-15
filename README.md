# Full-Stack Blog Application Deployment on AWS EC2 (Ubuntu)

This project demonstrates deployment of a full-stack blog application (React frontend + Node.js/Express backend + MongoDB) on an AWS EC2 instance. The application allows users to create, view, update, and delete blog posts through a web interface.

---

## Architecture

```
User → Browser → EC2 (Node/Express Server)
                      ├── React Build (Frontend)
                      └── API (Backend)
                      ↓
                   MongoDB Database
```

---

## Step 1: Launch EC2 Instance

- OS: Ubuntu
- Configure Security Group:
  - SSH (22) → Your IP
  - HTTP (80) → 0.0.0.0/0 (optional)
  - App Port (5000) → 0.0.0.0/0

---

## Step 2: Connect to EC2

```bash
chmod 400 your-key.pem
ssh -i your-key.pem ubuntu@<public-ip>
```

---

## Step 3: Install Dependencies

```bash
sudo apt update
sudo apt install nodejs npm git -y
```

Install PM2 (process manager):

```bash
sudo npm install -g pm2
```

---

## Step 4: Clone Project

```bash
git clone https://github.com/vedbabar/Blog-app-LP-2.git
cd Blog-app-LP-2
```

---

## Step 5: Setup Backend (Express)

```bash
cd backend
npm install
```

Create `.env` file:

```bash
nano .env
```

Example:

```
MONGO_URI=mongodb+srv://USERNAME:PASSWORD@CLUSTER.mongodb.net/blogdb?retryWrites=true&w=majority
JWT_SECRET=your_super_secret_jwt_key_min_32_chars
PORT=5000
```

---

## Step 6: Setup Frontend (React)

```bash
cd ../frontend
npm install
npm run build
```

This creates a `build/` folder.

---


## Step 7: Run Application

```bash
cd ../backend
pm2 start server.js
pm2 save
```

Check status:

```bash
pm2 list
```

---

## Step 8: Access Application

Open in browser:

```
http://<public-ip>:5000
```

---

## Step 9: Remote Updates

### Update Backend

```bash
cd backend
git pull
pm2 restart all
```

### Update Frontend

```bash
cd frontend
git pull
npm run build
pm2 restart all
```

---

## Features

- Create blog posts  
- View blog posts  
- Update blog posts  
- Delete blog posts  

---

## Conclusion

The full-stack blog application is successfully deployed on an AWS EC2 instance. The backend serves both API endpoints and the React frontend. The application is publicly accessible and supports remote updates using SSH.

---

## ✅ Step 9 — Access Your App

Open your browser and navigate to:

```
http://<YOUR_EC2_PUBLIC_IP>:5000
```

---

## 🧰 Common PM2 Commands

| Command | Description |
|---|---|
| `pm2 status` | Check running processes |
| `pm2 logs task-manager-api` | View live logs |
| `pm2 restart task-manager-api` | Restart the app |
| `pm2 stop task-manager-api` | Stop the app |
| `pm2 delete task-manager-api` | Remove from PM2 list |

---

## 🔄 Updating the App After Changes

```bash
# Pull latest code
git pull origin main

# Rebuild frontend if any frontend changes were made
cd frontend && npm run build && cd ..

# Restart the backend server
pm2 restart task-manager-api
```

---

## 🧱 Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 19, Vite, Axios, Lucide React |
| Backend | Node.js, Express 4, Mongoose |
| Database | MongoDB (Atlas recommended) |
| Deployment | AWS EC2 (Ubuntu), PM2 |

---

## 📋 Prerequisites

- An **AWS Account**
- A **MongoDB Atlas** cluster (or self-hosted MongoDB)
- Your project pushed to a **GitHub repository**
- Basic familiarity with the Linux terminal
