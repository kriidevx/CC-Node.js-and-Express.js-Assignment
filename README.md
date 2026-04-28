# 📅 Deadline Tracker API

**Bootcamp:** CC Web Dev Bootcamp
**Instructors:** [Kruthi Krishna](https://github.com/kriidevx) & [Bhavya Chawat](https://github.com/bhavyachawat)

---

## 🚀 How to Run

Make sure Node.js is installed, then:

```bash
npm install
npm run dev
```

Test all routes using your browser or Postman.

---

## 🎯 What You're Building

A REST API that helps college students track every assignment, lab record, viva, and submission deadline — built entirely with **Node.js and Express.js**.

You will build **5 routes** that together form a real, working backend API. By the end, hitting `GET /api/deadlines/urgent` and seeing only what's due in the next 24 hours will feel like actual magic.

This is not a toy project. This is the kind of API that powers real productivity apps.

---

## 📁 Folder Structure

```
YourName_USN/
└── DeadlineTrackerAPI/
    ├── .env
    ├── .gitignore
    ├── package.json
    ├── server.js
    └── README.md
```

---

## ⚙️ Setup (Do This First)

### Step 1 — Initialize the project

```bash
mkdir DeadlineTrackerAPI
cd DeadlineTrackerAPI
npm init -y
npm install express
```

### Step 2 — Create your files

```bash
touch server.js .env .gitignore
```

### Step 3 — Configure .env and .gitignore

**.env**
```
PORT=8080
```

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

## 🗃️ Your Fake Database

At the top of `server.js`, after your middleware, add this in-memory array.
This is your database for now — no MongoDB yet.

```js
let deadlines = [
  { id: 1, title: "CN Lab Record", subject: "CN", type: "lab", dueDate: "2025-02-10", submitted: false },
  { id: 2, title: "DAA Assignment 2", subject: "DAA", type: "assignment", dueDate: "2025-02-11", submitted: false },
  { id: 3, title: "DMS Viva", subject: "DMS", type: "viva", dueDate: "2025-02-15", submitted: false }
];
```

Each deadline has:
| Field | Type | Description |
|---|---|---|
| `id` | Number | Unique identifier |
| `title` | String | What the deadline is |
| `subject` | String | Which subject |
| `type` | String | `lab` / `assignment` / `viva` |
| `dueDate` | String | `"YYYY-MM-DD"` format |
| `submitted` | Boolean | Done or not |

---

## 📌 The 5 Routes You Need to Build

---

### Route 1 — GET all deadlines (with optional filter)

**Endpoint:** `GET /api/deadlines`
**Optional query:** `?type=lab` or `?type=viva` or `?type=assignment`

```js
app.get('/api/deadlines', (req, res) => {
  // your code here
});
```

**Expected output — GET /api/deadlines**
```json
[
  { "id": 1, "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": false },
  { "id": 2, "title": "DAA Assignment 2", "subject": "DAA", "type": "assignment", "dueDate": "2025-02-11", "submitted": false },
  { "id": 3, "title": "DMS Viva", "subject": "DMS", "type": "viva", "dueDate": "2025-02-15", "submitted": false }
]
```

**Expected output — GET /api/deadlines?type=lab**
```json
[
  { "id": 1, "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": false }
]
```

💡 **Hint:** Use `req.query.type` to check if a filter was passed. If yes, use `.filter()` on the array.

---

### Route 2 — GET one deadline by ID

**Endpoint:** `GET /api/deadlines/:id`

```js
app.get('/api/deadlines/:id', (req, res) => {
  // your code here
});
```

**Expected output — GET /api/deadlines/1**
```json
{ "id": 1, "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": false }
```

**Expected output — GET /api/deadlines/99**
```json
{ "message": "Deadline 99 not found" }
```

💡 **Hint:** Use `parseInt(req.params.id)` and `.find()`. Return 404 if not found.

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
app.post('/api/deadlines', (req, res) => {
  // your code here
});
```

**Expected output — 201 Created**
```json
{ "id": 4, "title": "IoT Mini Project", "subject": "IoT", "type": "assignment", "dueDate": "2025-02-20", "submitted": false }
```

**Expected output — if title is missing — 400 Bad Request**
```json
{ "message": "Title is required" }
```

💡 **Hint:** Destructure `req.body`. Validate that `title` exists. Set `submitted: false` by default. Push to the array and return 201.

---

### Route 4 — PUT update a deadline (mark submitted or edit)

**Endpoint:** `PUT /api/deadlines/:id`
**Body (JSON):**
```json
{
  "submitted": true
}
```

```js
app.put('/api/deadlines/:id', (req, res) => {
  // your code here
});
```

**Expected output — 200 OK**
```json
{ "id": 1, "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": true }
```

**Expected output — if not found**
```json
{ "message": "Deadline not found" }
```

💡 **Hint:** Find the deadline by ID. Update only the fields present in `req.body` using `Object.assign()` or direct assignment. Return the updated object.

---

### Route 5 — DELETE a deadline

**Endpoint:** `DELETE /api/deadlines/:id`

```js
app.delete('/api/deadlines/:id', (req, res) => {
  // your code here
});
```

**Expected output — 200 OK**
```json
{ "message": "Deadline deleted", "remaining": 2 }
```

**Expected output — if not found**
```json
{ "message": "Deadline not found" }
```

💡 **Hint:** Use `.some()` to check existence first. Then use `.filter()` to remove it. Return the count of remaining deadlines.

---

### 🌟 BONUS Route — GET urgent deadlines (due in next 24 hours)

**Endpoint:** `GET /api/deadlines/urgent`

This is the smart route. No input needed — the API figures out what's due soon.

```js
app.get('/api/deadlines/urgent', (req, res) => {
  // your code here
});
```

**Expected output**
```json
{
  "urgent": [
    { "id": 1, "title": "CN Lab Record", "subject": "CN", "type": "lab", "dueDate": "2025-02-10", "submitted": false }
  ],
  "count": 1
}
```

💡 **Hint:** Use `new Date()` for today. Filter deadlines where `dueDate` is within 24 hours from now and `submitted` is false.

⚠️ **Important:** This route must be placed BEFORE `/api/deadlines/:id` in your code — otherwise Express will treat `"urgent"` as an `:id` param.

---

## 🧪 Testing Checklist (use Postman or browser)

```
GET  /api/deadlines              → returns all 3 deadlines
GET  /api/deadlines?type=lab     → returns only lab deadlines
GET  /api/deadlines/1            → returns CN Lab Record
GET  /api/deadlines/99           → returns 404 message
POST /api/deadlines              → creates new deadline (201)
POST /api/deadlines (no title)   → returns 400 error
PUT  /api/deadlines/1            → marks as submitted
DELETE /api/deadlines/1          → removes deadline
GET  /api/deadlines/urgent       → returns deadlines due in 24hrs
```

---

## ✅ Submission Checklist

- [ ] Project initialised with `npm init -y`
- [ ] Express installed
- [ ] `.env` with PORT, `.gitignore` with node_modules and .env
- [ ] In-memory deadlines array with correct fields
- [ ] Logger middleware using `app.use()`
- [ ] `GET /api/deadlines` with optional `?type=` filter
- [ ] `GET /api/deadlines/:id` with 404 handling
- [ ] `POST /api/deadlines` with body validation
- [ ] `PUT /api/deadlines/:id` with update logic
- [ ] `DELETE /api/deadlines/:id` with 404 handling
- [ ] **BONUS:** `GET /api/deadlines/urgent` working correctly
- [ ] All routes tested in Postman

---

## 📚 Concepts Used (from session)

| Concept | Where You Use It |
|---|---|
| `npm init`, `npm install express` | Project setup |
| `.env` + `process.env.PORT` | Environment variables |
| `app.use(express.json())` | Middleware — parse request body |
| Custom logger middleware | `app.use((req, res, next) => {})` |
| In-memory array as fake DB | `let deadlines = [...]` |
| `req.query` | Filter by type |
| `req.params` | Get/update/delete by ID |
| `req.body` | POST and PUT data |
| `res.status().json()` | Proper HTTP responses |
| `app.listen()` | Start the server |

---

## 👤 Student Details

```
Name   :
USN    :
GitHub :
```

---

*Assignment by [Kruthi Krishna](https://github.com/kruthikrishna) & [Bhavya Chawat](https://github.com/bhavyachawat) · CC Web Dev Bootcamp*
