# 📅 Deadline Tracker API

**Bootcamp:** CC Web Dev Bootcamp
**Instructors:** [Kruthi Krishna](https://github.com/kruthikrishna) & [Bhavya Chawat](https://github.com/bhavyachawat)

---

## 🚀 How to Run

Make sure Node.js is installed, then:

```bash
npm install
npm run dev
```

Test all routes using Postman.

---

## 🎯 What You're Building

A REST API that helps college students track every assignment, lab record, viva, and submission deadline — built with **Node.js**, **Express.js**, and **MongoDB**.

Last week you designed schemas. This week you bring them to life. By the end, hitting `GET /api/deadlines/urgent` and seeing only what's due in the next 24 hours pulled straight from a real database will feel like actual magic.

---

## 📁 Folder Structure

```
YourName_USN/
└── DeadlineTrackerAPI/
    ├── models/
    │   └── Deadline.js
    ├── .env
    ├── .gitignore
    ├── package.json
    └── server.js
```

---

## ⚙️ Setup (Do This First)

### Step 1 — Initialize the project

```bash
mkdir DeadlineTrackerAPI
cd DeadlineTrackerAPI
npm init -y
npm install express mongoose dotenv
```

### Step 2 — Create your files

```bash
mkdir models
touch server.js models/Deadline.js .env .gitignore
```

### Step 3 — Configure .env and .gitignore

**.env**
```
PORT=8080
MONGO_URI=your_mongodb_connection_string_here
```

> 💡 Go to [MongoDB Atlas](https://cloud.mongodb.com), create a free cluster, click **Connect → Drivers**, and copy your connection string. Replace `<password>` with your actual password.

**.gitignore**
```
node_modules/
.env
```

### Step 4 — Update package.json scripts

```json
"scripts": {
  "start": "node server.js",
  "dev": "node --watch --env-file=.env server.js"
}
```

---

## 🗃️ Your MongoDB Model

Create `models/Deadline.js`. This is your schema — just like you designed last week, but now it's real and connected to a database.

```js
const mongoose = require('mongoose');

const deadlineSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true
  },
  subject: {
    type: String,
    required: true
  },
  type: {
    type: String,
    enum: ['lab', 'assignment', 'viva'],
    required: true
  },
  dueDate: {
    type: Date,
    required: true
  },
  submitted: {
    type: Boolean,
    default: false
  }
});

module.exports = mongoose.model('Deadline', deadlineSchema);
```

Each deadline has:

| Field | Type | Description |
|---|---|---|
| `title` | String | What the deadline is |
| `subject` | String | Which subject |
| `type` | String | `lab` / `assignment` / `viva` |
| `dueDate` | Date | Due date |
| `submitted` | Boolean | Done or not (default: false) |

---

## 🔌 Connect to MongoDB in server.js

Start your `server.js` with this. Everything else goes below.

```js
const express = require('express');
const mongoose = require('mongoose');
require('dotenv').config();

const Deadline = require('./models/Deadline');

const app = express();
const PORT = process.env.PORT || 8080;

// Middleware
app.use(express.json());

// Logger middleware
app.use((req, res, next) => {
  console.log(req.method, req.url);
  next();
});

// Connect to MongoDB
mongoose.connect(process.env.MONGO_URI)
  .then(() => console.log('MongoDB connected'))
  .catch((err) => console.log('Connection error:', err));

// Your routes go here

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

## 📌 The 5 Routes You Need to Build

All routes go between the MongoDB connection and `app.listen()`.

---

### Route 1 — GET all deadlines (with optional filter)

**Endpoint:** `GET /api/deadlines`
**Optional query:** `?type=lab` or `?type=viva` or `?type=assignment`

```js
app.get('/api/deadlines', async (req, res) => {
  // your code here
});
```

**Expected output — GET /api/deadlines**
```json
[
  { "_id": "...", "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": false },
  { "_id": "...", "title": "DAA Assignment 2", "subject": "DAA", "type": "assignment", "dueDate": "2025-02-11", "submitted": false }
]
```

**Expected output — GET /api/deadlines?type=lab**
```json
[
  { "_id": "...", "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": false }
]
```

💡 **Hint:** Use `req.query.type` to check if a filter was passed. Build a `filter` object and pass it to `Deadline.find(filter)`. If no query, just use `Deadline.find({})`.

---

### Route 2 — GET one deadline by ID

**Endpoint:** `GET /api/deadlines/:id`

```js
app.get('/api/deadlines/:id', async (req, res) => {
  // your code here
});
```

**Expected output — GET /api/deadlines/validId**
```json
{ "_id": "...", "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": false }
```

**Expected output — ID not found**
```json
{ "message": "Deadline not found" }
```

💡 **Hint:** Use `Deadline.findById(req.params.id)`. If the result is `null`, return a 404 response. Wrap in `try/catch` — an invalid ID format will throw an error.

---

### Route 3 — POST a new deadline

**Endpoint:** `POST /api/deadlines`

**Body (JSON):**
```json
{
  "title": "IoT Mini Project",
  "subject": "IoT",
  "type": "assignment",
  "dueDate": "2025-02-20"
}
```

```js
app.post('/api/deadlines', async (req, res) => {
  // your code here
});
```

**Expected output — 201 Created**
```json
{ "_id": "...", "title": "IoT Mini Project", "subject": "IoT", "type": "assignment", "dueDate": "2025-02-20", "submitted": false }
```

**Expected output — if title is missing — 400 Bad Request**
```json
{ "message": "Title is required" }
```

💡 **Hint:** Destructure `req.body`. Check if `title` exists — if not, return 400. Then create `new Deadline({...req.body})` and call `.save()`. Return the saved document with status 201.

---

### Route 4 — PUT update a deadline

**Endpoint:** `PUT /api/deadlines/:id`

**Body (JSON):**
```json
{
  "submitted": true
}
```

```js
app.put('/api/deadlines/:id', async (req, res) => {
  // your code here
});
```

**Expected output — 200 OK**
```json
{ "_id": "...", "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": true }
```

**Expected output — if not found**
```json
{ "message": "Deadline not found" }
```

💡 **Hint:** Use `Deadline.findByIdAndUpdate(req.params.id, req.body, { new: true })`. The `{ new: true }` option returns the updated document instead of the old one.

---

### Route 5 — DELETE a deadline

**Endpoint:** `DELETE /api/deadlines/:id`

```js
app.delete('/api/deadlines/:id', async (req, res) => {
  // your code here
});
```

**Expected output — 200 OK**
```json
{ "message": "Deadline deleted successfully" }
```

**Expected output — if not found**
```json
{ "message": "Deadline not found" }
```

💡 **Hint:** Use `Deadline.findByIdAndDelete(req.params.id)`. If the result is `null`, the ID didn't exist — return 404.

---

### 🌟 BONUS Route — GET urgent deadlines (due in next 24 hours)

**Endpoint:** `GET /api/deadlines/urgent`

No input needed — the API figures out what's due soon on its own.

```js
app.get('/api/deadlines/urgent', async (req, res) => {
  // your code here
});
```

**Expected output**
```json
{
  "urgent": [
    { "_id": "...", "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": false }
  ],
  "count": 1
}
```

💡 **Hint:** Create `now` and `next24hrs` using `new Date()`. Use `Deadline.find()` with a query like:
```js
{ dueDate: { $gte: now, $lte: next24hrs }, submitted: false }
```

⚠️ **Important:** Place this route BEFORE `GET /api/deadlines/:id` — otherwise Express will treat the word `"urgent"` as an ID param and it will never work.

---

## 🧪 Testing Checklist (use Postman)

```
GET    /api/deadlines              → returns all deadlines from DB
GET    /api/deadlines?type=lab     → returns only lab deadlines
GET    /api/deadlines/urgent       → deadlines due in next 24 hours
GET    /api/deadlines/:id          → returns one deadline
GET    /api/deadlines/badId        → returns 404 message
POST   /api/deadlines              → creates and saves to MongoDB (201)
POST   /api/deadlines (no title)   → returns 400 error
PUT    /api/deadlines/:id          → marks as submitted
DELETE /api/deadlines/:id          → removes from DB
```

---

## ✅ Submission Checklist

- [ ] Project initialized with `npm init -y`
- [ ] `express`, `mongoose`, `dotenv` installed
- [ ] `.env` with `PORT` and `MONGO_URI`
- [ ] `.gitignore` with `node_modules/` and `.env`
- [ ] `models/Deadline.js` with correct Mongoose schema
- [ ] MongoDB connected successfully (`MongoDB connected` in terminal)
- [ ] Logger middleware using `app.use()`
- [ ] `GET /api/deadlines` with optional `?type=` filter
- [ ] `GET /api/deadlines/:id` with 404 handling
- [ ] `POST /api/deadlines` with body validation
- [ ] `PUT /api/deadlines/:id` using `findByIdAndUpdate`
- [ ] `DELETE /api/deadlines/:id` using `findByIdAndDelete`
- [ ] All routes wrapped in `try/catch`
- [ ] **BONUS:** `GET /api/deadlines/urgent` working correctly
- [ ] All routes tested in Postman

---

## 📚 Concepts Used

| Concept | Where You Use It |
|---|---|
| `npm install express mongoose dotenv` | Project setup |
| `.env` + `process.env.MONGO_URI` | Environment variables |
| `mongoose.connect()` | Connect to MongoDB Atlas |
| `mongoose.Schema` + `mongoose.model()` | Define your data structure |
| `app.use(express.json())` | Middleware — parse request body |
| Custom logger middleware | `app.use((req, res, next) => {})` |
| `req.query` | Filter by type |
| `req.params.id` | Get/update/delete by ID |
| `req.body` | POST and PUT data |
| `Model.find()` | Get all documents |
| `Model.findById()` | Get one document |
| `new Model().save()` | Create and save a document |
| `Model.findByIdAndUpdate()` | Update a document |
| `Model.findByIdAndDelete()` | Delete a document |
| `async/await` + `try/catch` | Handle async DB operations |
| `res.status().json()` | Proper HTTP responses |

---

## 👤 Student Details

```
Name   :
USN    :
GitHub :
```

---

*Assignment by [Kruthi Krishna](https://github.com/kruthikrishna) & [Bhavya Chawat](https://github.com/bhavyachawat) · CC Web Dev Bootcamp*

