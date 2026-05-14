# 📋 Nexus Tasks — MERN Task Manager

A full-stack Task Manager built with **MongoDB, Express, React, and Node.js**, deployed on a single **AWS EC2** instance. The Express backend serves the compiled React frontend as static files — no Nginx required.

---

## 🗂️ Project Structure

```
task-manager/
├── backend/
│   ├── models/
│   │   └── Task.js
│   ├── index.js
│   ├── package.json
│   └── .env          ← create this manually
└── frontend/
    ├── src/
    ├── .env          ← edit this before building
    ├── package.json
    └── vite.config.js
```

---

## ☁️ Step 1 — Launch & Configure EC2 Instance

1. Go to **AWS Console → EC2 → Launch Instance**
2. Choose the following settings:

| Setting | Value |
|---|---|
| OS | Ubuntu 22.04 LTS or 24.04 LTS |
| Instance Type | `t2.micro` (Free Tier eligible) |
| Key Pair | Create & download a `.pem` file |

3. Under **Network Settings → Security Group**, add these inbound rules:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | Anywhere (`0.0.0.0/0`) |
| HTTP | 80 | Anywhere |
| HTTPS | 443 | Anywhere |
| Custom TCP | **5000** | Anywhere (`0.0.0.0/0`) |

> ⚠️ **Port 5000** is required for the app to be accessible from the browser.

4. SSH into your instance from your local terminal:

```bash
chmod 400 your-key.pem
ssh -i "your-key.pem" ubuntu@<YOUR_EC2_PUBLIC_IP>
```

---

## 🛠️ Step 2 — Install Server Dependencies

Run the following commands on your EC2 instance:

```bash
# Update package lists
sudo apt update && sudo apt upgrade -y

# Install Git
sudo apt install git -y

# Install Node.js via NVM (Node Version Manager)
sudo apt install -y nodejs

sudo apt install npm 

# Verify installation
node -v
npm -v

sudo npm install -g pm2
```

---

## 📥 Step 3 — Clone the Repository

```bash
git clone https://github.com/your-username/task-manager.git
cd task-manager
```

---

## ⚙️ Step 4 — Configure Environment Variables

### Backend `.env`

Create the environment file inside the `backend/` folder:

```bash
cd backend
nano .env
```

Add the following content:

```env
PORT=5000
MONGODB_URI=mongodb+srv://<your-username>:<your-password>@cluster.mongodb.net/taskmanager
```

> 💡 **MongoDB Atlas** is recommended. Make sure your cluster allows connections from `0.0.0.0/0` (or your EC2's IP) under **Network Access**.

---

## 🖥️ Step 5 — Configure & Build the Frontend

### Edit Frontend `.env` (Before Building!)

Before building the React app, update the API URL to point to your EC2 instance:

```bash
cd ../frontend
nano .env
```

Change the value to your EC2 public IP:

```env
# Local development
# VITE_API_URL=http://localhost:5000/api/tasks

# AWS EC2 Production — replace with your actual EC2 Public IPv4
VITE_API_URL=http://<YOUR_EC2_PUBLIC_IP>:5000/api/tasks
```

> ⚠️ If you skip this step, the frontend will try to call `localhost` from the user's browser and fail to reach your server.

### Build the Frontend

```bash
npm install
npm run build
```

This generates the `frontend/dist/` folder that Express will serve.

---

## 🔌 Step 6 — Install Backend Dependencies

```bash
cd ../backend
npm install
```

### Verify Express Version

This project uses **Express v4**. Check your version:

```bash
npm list express
```

If you see Express v5 installed (which causes a `PathError` with wildcard routes), downgrade it:

```bash
npm uninstall express
npm install express@4
```

---

## 📝 Step 7 — Verify `backend/index.js` Deployment Code

Ensure the bottom of `backend/index.js` contains the static file serving block **before** `app.listen`:

```js
const path = require('path');

// Serve React frontend static files
app.use(express.static(path.join(__dirname, '../frontend/dist')));

// Catch-all route — serves index.html for any unmatched route (supports React Router)
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '../frontend/dist', 'index.html'));
});

// Bind to 0.0.0.0 so EC2 accepts external connections
app.listen(PORT, '0.0.0.0', () => {
  console.log(`Server running on port ${PORT}`);
});
```

> ⚠️ **Express v5 Note:** If you are using Express v5, replace `app.get('*', ...)` with:
> ```js
> app.get(/.*/, (req, res) => { ... });
> ```
> Express v5 does not support bare wildcard `*` — it throws a `PathError: Missing parameter name`.

---

## 🚀 Step 8 — Run with PM2 (Process Manager)

PM2 keeps your app running after you close the SSH session and restarts it automatically on server reboot.

```bash
# Install PM2 globally
sudo npm install -g pm2

# Start the backend server
pm2 start index.js --name "task-manager-api"

# Check that the process is running
pm2 status

# Generate startup script (run the command PM2 outputs)
pm2 startup

# Save the current process list
pm2 save
```

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
