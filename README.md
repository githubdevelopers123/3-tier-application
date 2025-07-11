# 3-Tier React + Node + MongoDB App

## 🔧 Setup

```bash
git clone <repo>
cd 3-tier-app
docker-compose up --build


React: http://localhost:3000

API: http://localhost:5000/api/users

MongoDB: localhost:27017 (via mongo container)


Here’s a complete real-time practice setup for a 3-tier application using:

✅ React (frontend)

✅ Node.js (backend – Express)

✅ MongoDB (database)

✅ Folder Structure:
bash
Copy
Edit
3-tier-app/
├── backend/
│   ├── Dockerfile
│   ├── package.json
│   ├── server.js
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   ├── public/
│   ├── src/
│   │   ├── App.js
│   │   └── index.js
├── docker-compose.yml
└── README.md
🔷 Step-by-Step Guide:
🔹 1. backend/server.js
js
Copy
Edit
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());

const mongoUri = process.env.MONGO_URI || "mongodb://mongo:27017/testdb";
mongoose.connect(mongoUri, { useNewUrlParser: true, useUnifiedTopology: true });

const userSchema = new mongoose.Schema({ name: String });
const User = mongoose.model('User', userSchema);

app.get('/api/users', async (req, res) => {
  const users = await User.find();
  res.json(users);
});

app.post('/api/users', async (req, res) => {
  const user = new User({ name: req.body.name });
  await user.save();
  res.status(201).json(user);
});

app.listen(5000, () => console.log("Backend running on port 5000"));
🔹 2. backend/package.json
json
Copy
Edit
{
  "name": "backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^4.17.1",
    "mongoose": "^5.11.15"
  }
}
🔹 3. backend/Dockerfile
Dockerfile
Copy
Edit
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5000
CMD ["npm", "start"]
🔹 4. frontend/src/App.js
js
Copy
Edit
import React, { useEffect, useState } from 'react';

function App() {
  const [users, setUsers] = useState([]);
  const [name, setName] = useState('');

  const fetchUsers = async () => {
    const res = await fetch('/api/users');
    const data = await res.json();
    setUsers(data);
  };

  const addUser = async () => {
    await fetch('/api/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name })
    });
    setName('');
    fetchUsers();
  };

  useEffect(() => {
    fetchUsers();
  }, []);

  return (
    <div style={{ padding: 20 }}>
      <h1>React + Node + MongoDB App</h1>
      <input value={name} onChange={e => setName(e.target.value)} />
      <button onClick={addUser}>Add User</button>
      <ul>
        {users.map((u, i) => <li key={i}>{u.name}</li>)}
      </ul>
    </div>
  );
}

export default App;
🔹 5. frontend/package.json
json
Copy
Edit
{
  "name": "frontend",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^18.0.0",
    "react-dom": "^18.0.0",
    "react-scripts": "5.0.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build"
  }
}
🔹 6. frontend/Dockerfile
Dockerfile
Copy
Edit
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
🔹 7. docker-compose.yml
yaml
Copy
Edit
version: "3.8"

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    environment:
      - CHOKIDAR_USEPOLLING=true
    restart: always

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    depends_on:
      - mongo
    environment:
      - MONGO_URI=mongodb://mongo:27017/testdb
    restart: always

  mongo:
    image: mongo:5.0
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
    restart: always

volumes:
  mongodb_data:
🔹 8. README.md
markdown
Copy
Edit
# 3-Tier React + Node + MongoDB App

## 🔧 Setup

```bash
git clone <repo>
cd 3-tier-app
docker-compose up --build
React: http://localhost:3000

API: http://localhost:5000/api/users

MongoDB: localhost:27017 (via mongo container)
