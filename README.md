

# **EngageAI — Classroom Engagement Analytics System**

Comprehensive AI-powered platform for real-time student engagement tracking in classroom environments using computer vision, with live dashboards, analytics, and role-based management.

## **Table of Contents**
- Project Overview
- Architecture
- Prerequisites
- Installation & Setup
- Running All Services
- API Documentation
- WebSocket Events
- Demo Credentials
- Environment Configuration
- AI Model Integration
- Troubleshooting

---

## **Project Overview**

EngageAI is a full-stack application designed to:
- **Track** real-time student engagement during live classroom sessions
- **Analyze** engagement patterns with AI-powered computer vision
- **Visualize** live analytics on teacher/admin dashboards
- **Generate** comprehensive engagement reports
- **Manage** multiple classrooms, sessions, and student records

**Key Features**:
- ✅ Multi-role system (Admin, Teacher, Student)
- ✅ Real-time engagement scoring via PyTorch ML model
- ✅ Live WebSocket updates during sessions
- ✅ Session recording with engagement snapshots
- ✅ Anomaly detection (no face, multiple faces, camera blackout)
- ✅ JWT-based authentication
- ✅ MongoDB persistence
- ✅ Fully seeded demo database

---

## **Architecture**

```
Student Engagement Analysis/
├── EngageAI/                    ← React + Vite Frontend (Multi-role SPA)
│   ├── admin/                   ← Admin dashboard & management
│   ├── teacher/                 ← Teacher live session & analytics
│   ├── student/                 ← Student classroom interface
│   ├── app/                     ← Global layouts
│   ├── auth/                    ← Auth context & protected routes
│   └── services/                ← API client, WebSocket client
│
├── Backend/                     ← Node.js + Express REST API + Socket.io
│   ├── controllers/             ← Business logic (auth, sessions, analytics)
│   ├── models/                  ← MongoDB schemas (User, Session, Engagement, Classroom)
│   ├── routes/                  ← Express route handlers
│   ├── middleware/              ← Auth, validation, error handling
│   ├── sockets/                 ← Socket.io event handlers
│   └── utils/                   ← JWT, AI service client, DB seed, helpers
│
├── AIService/                   ← Python FastAPI Microservice
│   ├── main.py                  ← FastAPI endpoints (model inference)
│   ├── requirements.txt          ← Python dependencies
│   └── model/                   ← Trained ML model (PyTorch/ONNX/Keras)
│
└── Admin/, Student/, Teacher/   ← Legacy individual role frontends (deprecated)
```

---

## **Prerequisites**

| Component    | Version     | Link / Notes                                          |
|--------------|-------------|------------------------------------------------------|
| Node.js      | ≥ 18        | [nodejs.org](https://nodejs.org)                     |
| npm          | ≥ 9         | Bundled with Node.js                                 |
| MongoDB      | ≥ 6         | Local or [MongoDB Atlas](https://www.mongodb.com)    |
| Python       | ≥ 3.10      | [python.org](https://www.python.org)                 |
| pip          | latest      | Python package manager                               |
| Git          | latest      | For cloning the repo                                 |

---

## **Installation & Setup**

### **Step 1: Clone & Navigate**

```bash
git clone https://github.com/Hyphenant/your-repo.git
cd "Student Engagement Analysis"
```

### **Step 2: Start MongoDB**

**Option A — Local MongoDB:**
```bash
mongod
```

**Option B — MongoDB Atlas (Cloud):**
1. Create cluster at [atlas.mongodb.com](https://www.mongodb.com/cloud/atlas)
2. Get connection string
3. Update .env with `MONGODB_URI`

### **Step 3: Backend Setup**

```bash
cd Backend

# Install dependencies (if fresh clone)
npm install

# Configure environment
# Edit Backend/.env if needed (default settings work for local dev)

# Seed database with demo data
npm run seed

# Start development server (auto-restarts on file changes)
npm run dev
```

✅ **Backend ready**: `http://localhost:5000`

### **Step 4: AI Microservice Setup**

```bash
cd ../AIService

# Create Python virtual environment
python -m venv venv

# Activate venv
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# Install Python dependencies
pip install -r requirements.txt

# Place your trained model in AIService/model/
# Supported formats: .pt (PyTorch), .onnx, .h5 (Keras), .pkl (sklearn)
# See AIService/model/README.txt for details

# Start FastAPI server
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

✅ **AI Service ready**: `http://localhost:8000` | Docs: `http://localhost:8000/docs`

### **Step 5: Frontend Setup**

```bash
cd ../EngageAI

# Install dependencies (if fresh clone)
npm install

# Start Vite dev server
npm run dev
```

✅ **Frontend ready**: `http://localhost:5173`

---

## **Running All Services (Quick Reference)**

Open 4 terminals:

| Terminal | Command | Port |
|----------|---------|------|
| 1 | `mongod` | 27017 |
| 2 | `cd Backend && npm run dev` | 5000 |
| 3 | `cd AIService && venv\Scripts\activate && uvicorn main:app --port 8000` | 8000 |
| 4 | `cd EngageAI && npm run dev` | 5173 |

Then navigate to: **`http://localhost:5173`**

---

## **Demo Credentials**

After running `npm run seed` in Backend:

| Role    | Email                 | Password     | Access Level          |
|---------|----------------------|-------------|----------------------|
| Admin   | admin@school.edu     | admin123    | Full system access    |
| Teacher | kavya@school.edu     | teacher123  | Session mgmt + analytics |
| Teacher | arjun@school.edu     | teacher123  | Session mgmt + analytics |
| Student | aarav@school.edu     | student123  | Classroom participation |
| Student | ananya@school.edu    | student123  | Classroom participation |
| Student | (+ 5 more) | student123 | Classroom participation |

---

## **API Documentation**

### **Authentication Endpoints**

```
POST /api/auth/register
  Body: { name, email, password, role }
  Returns: { success, token, user }

POST /api/auth/login
  Body: { email, password, role }
  Returns: { success, token, user }

GET /api/auth/me
  Auth: JWT Token (Bearer)
  Returns: { success, user }
```

**Login Response Example:**
```json
{
  "success": true,
  "message": "Login successful.",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "_id": "507f1f77bcf86cd799439011",
      "name": "Ms. Kavya Sharma",
      "email": "kavya@school.edu",
      "role": "teacher"
    }
  }
}
```

### **Session Management**

```
POST /api/session/start
  Auth: ✓ (Teacher)
  Body: { subject, title, classroomId }
  Returns: { sessionId, startTime }

POST /api/session/end
  Auth: ✓ (Teacher)
  Body: { sessionId }
  Returns: { sessionId, endTime, summary }

GET /api/session/active
  Auth: ✓ (Student)
  Returns: { sessionId, subject, teacherName }

POST /api/session/join
  Auth: ✓ (Student)
  Body: { sessionId }
  Returns: { joined: true }

GET /api/session/:id
  Auth: ✓ (Teacher/Admin)
  Returns: { session details, participants, engagement data }
```

### **Engagement Tracking**

```
POST /api/engagement/update
  Auth: ✓ (Student)
  Content-Type: multipart/form-data
  Body: { file (image), sessionId }
  Returns: { engagementScore, flags, state }
```

### **Analytics Endpoints**

```
GET /api/analytics/session/:id
  Auth: ✓ (Teacher/Admin)
  Returns: { avgEngagement, trends, flagCount, studentBreakdown }

GET /api/analytics/student/:id
  Auth: ✓ (All)
  Returns: { attendance, avgEngagement, trends, sessions }

GET /api/analytics/class/:classroomId
  Auth: ✓ (Teacher/Admin)
  Returns: { classwide stats, top/bottom performers, trends }
```

---

## **WebSocket Events (Real-Time Updates)**

### **Client → Server Events**

| Event | Payload | Who | Purpose |
|-------|---------|-----|---------|
| `session:join_room` | `{ sessionId }` | Student | Join live session room |
| `teacher:join_session` | `{ sessionId }` | Teacher | Start monitoring |
| `session:leave_room` | `{ sessionId }` | Student | Leave session |
| `flag:no_face` | `{ sessionId }` | Student | Signal no face detected |
| `flag:multiple_faces` | `{ sessionId }` | Student | Signal multiple faces |
| `flag:camera_blackout` | `{ sessionId }` | Student | Signal camera off |
| `flag:long_inactivity` | `{ sessionId }` | Student | Signal inactivity |

### **Server → Client Broadcasts**

| Event | Payload | Recipients | When |
|-------|---------|------------|------|
| `session:started` | `{ sessionId, subject, title, startTime }` | Teacher | Session begins |
| `session:ended` | `{ sessionId, summary, avgEngagement }` | All in room | Session ends |
| `student:joined` | `{ sessionId, studentId, name }` | Teacher | Student joins |
| `student:connected` | `{ sessionId, studentId, name }` | Teacher | Camera/socket connected |
| `student:disconnected` | `{ sessionId, studentId, name }` | Teacher | Student leaves |
| `engagement:update` | *(see below)* | All in room | Real-time scoring (~1s intervals) |
| `student:flagged` | `{ studentId, name, flag, severity }` | All in room | Anomaly detected |

**Engagement Update Payload (Live Dashboard):**
```json
{
  "studentId": "507f1f77bcf86cd799439011",
  "studentName": "Aarav Patel",
  "engagementPercent": 78,
  "state": "Attentive",
  "confidence": 0.92,
  "statusColor": "green",
  "timestamp": "2026-02-24T10:30:45.123Z",
  "flags": {
    "noFace": false,
    "multipleFaces": false,
    "cameraBlackout": false,
    "longInactivity": false
  }
}
```

**Status Color Logic:**
- 🟢 `green` — Engagement ≥ 70% → **Engaged**
- 🟡 `yellow` — Engagement 40–69% → **Neutral**
- 🔴 `red` — Engagement < 40% → **Disengaged**

---

## **Environment Configuration**

### **.env**

```env
# Server
PORT=5000
NODE_ENV=development

# Database
MONGODB_URI=mongodb://localhost:27017/engageai

# Auth
JWT_SECRET=your_super_secret_key_change_in_production
JWT_EXPIRES_IN=7d

# External Services
AI_SERVICE_URL=http://localhost:8000

# CORS
FRONTEND_URL=http://localhost:5173
ALLOWED_ORIGINS=http://localhost:5173,http://localhost:3000
```

### **.env**

```env
# FastAPI
HOST=0.0.0.0
PORT=8000
DEBUG=true

# Model
MODEL_PATH=model/engagement_model.pt
MOCK_MODE=false
```

---

## **AI Model Integration**

### **Supported Model Formats**

Place your trained model file in model:

| Filename | Framework | Status |
|----------|-----------|--------|
| `engagement_model.pt` | PyTorch | ✅ Recommended |
| `engagement_model.onnx` | ONNX Runtime | ✅ Supported |
| `engagement_model.h5` | Keras/TensorFlow | ✅ Supported |
| `engagement_model.pkl` | scikit-learn | ✅ Supported |

### **Auto-Detection**

The AI Service automatically detects which model file is present and loads it.  
**No code changes required** — just drop the file and restart the service.

### **Mock Mode**

If **no model file** is found, the service activates **mock prediction mode**:
- Uses heuristics (frame brightness, edge density)
- Generates pseudo-random engagement scores
- Useful for testing without a trained model

---

## **Troubleshooting**

### **Backend Issues**

| Error | Solution |
|-------|----------|
| `EADDRINUSE: address already in use :5000` | Another process using port 5000. Run `lsof -i :5000` (Mac/Linux) or `netstat -ano \| findstr :5000` (Windows) |
| `MongooseError: Cannot connect to MongoDB` | Ensure `mongod` is running or MongoDB Atlas URI is correct in `.env` |
| `JWT_SECRET not found` | Add `JWT_SECRET` to .env |
| `AI_SERVICE_URL connection refused` | Ensure AI Service is running on port 8000 |

### **AI Service Issues**

| Error | Solution |
|-------|----------|
| `ModuleNotFoundError: No module named 'torch'` | Run `pip install -r requirements.txt` and activate venv |
| `FileNotFoundError: model/engagement_model.pt` | Place model file in model or enable mock mode |
| `Port 8000 already in use` | Change port: `uvicorn main:app --port 8001` |

### **Frontend Issues**

| Error | Solution |
|-------|----------|
| `CORS error when calling backend` | Verify `FRONTEND_URL` in .env |
| `Cannot GET /socket.io` | Ensure Backend is running and `Socket.io` is initialized |
| `Blank page on localhost:5173` | Run `npm install` and restart dev server |

### **Common Fixes**

```bash
# Clear all node modules and reinstall
rm -rf node_modules package-lock.json
npm install

# Restart Python venv
rm -rf venv
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt

# Reset MongoDB (DELETE ALL DATA)
mongo
use engageai
db.dropDatabase()
```

---

## **Production Deployment**

**Backend (Node.js):**
```bash
npm install
npm start  # Uses PORT from .env
```

**AI Service (Python):**
```bash
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4
```

**Frontend (React):**
```bash
npm run build
# Serve dist/ folder via static file server
```

**Database:** Use MongoDB Atlas (managed cloud service)

---

## **Additional Resources**

- **MongoDB Docs**: https://docs.mongodb.com
- **Express.js Docs**: https://expressjs.com
- **React Docs**: https://react.dev
- **PyTorch Docs**: https://pytorch.org/docs
- **Socket.io Docs**: https://socket.io/docs
