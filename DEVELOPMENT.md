# Aetasaal 2.0 - Development Guide

## 📌 Overview
Aetasaal 2.0 is a web application divided into two segments:  
1. **Frontend** - Built with Angular (Node.js 10).  
2. **Backend** - Built with Koa.js (Node.js 12).  
The application runs on an **AWS EC2** instance for hosting.

---

## 🛠️ Prerequisites
Before starting development, ensure the following are installed:

- **NVM (Node Version Manager)**
- **Node.js 10** (for the frontend)
- **Node.js 12** (for the backend)
- **PostgreSQL** (for database management)
- **Git** (for version control)

---

## 🚀 Project Folder Structure
```
/taskflow-frontend  (Angular, Node.js 10)
/taskflow-backend   (Koa, Node.js 12)
```

---

## 🔄 Switching Node.js Versions Using NVM
### Install Required Node Versions
```sh
nvm install 10
nvm install 12
```

### Use Node.js 12 for Backend
```sh
cd taskflow-backend
nvm use 12
```

### Use Node.js 10 for Frontend
```sh
cd taskflow-frontend
nvm use 10
```

---

# 🏗️ Setting Up Development Environment

## 1️⃣ Backend Setup (Koa.js - Node.js 12)
### 📂 Install Dependencies
```sh
cd taskflow-backend
npm install
```

### 🛠️ Configure the Server
Edit `taskflow-backend/src/server.ts` and **comment out** the following line:
```javascript
app.use(mount('/', serve(frontendDir)));
```
This ensures the backend does not serve static frontend files during local development.

### 🛢️ Configure Database
Modify `taskflow-backend/src/config/env/development.json`:
```json
{
  "postgres": {
    "host": "localhost",
    "port": 5432,
    "username": "postgres",
    "password": "your_password",
    "database": "taskflow"
  }
}
```

### 🗄️ Install & Set Up PostgreSQL
1. Download **PostgreSQL**
2. Create a database:
```sh
psql -U postgres
CREATE DATABASE taskflow;
```
3. Run Migrations & Seeds:
```sh
npm run sqlz:createdb
npm run sqlz:migrate
npm run sqlz:seed
```

### 🚀 Start the Backend Server
```sh
nodemon server.js
```
The backend will run on **http://localhost:3000**.

---

## 2️⃣ Frontend Setup (Angular - Node.js 10)
### 📂 Install Dependencies
```sh
cd taskflow-frontend
npm install
```

### ⚙️ Update API URL in Frontend
Modify `taskflow-frontend\src\environments\environment.prod.ts`:
```typescript
export const environment = {
  apiBase: 'http://localhost:3000/api/',
  apiImage: 'http://localhost:3000/images/',
  downloadimage: 'http://localhost:3000/upload/'
};
```

### 🔄 Change the Server Port
Edit `taskflow-frontend/server.js` and set port to **4200**:
```javascript
const port = process.env.PORT || 4200;
```

### 🚀 Start the Angular Application
```sh
ng serve
```
The frontend will run on **http://localhost:4200**.

---
