
# 🎓 Face + ID Card Attendance System

### 🚀 FastAPI • DeepFace • OpenCV • Tesseract • SQLite

A fully automated and secure attendance system that uses:

* **Face Recognition (DeepFace + Facenet Embeddings)**
* **Liveness Detection (Anti-Spoof using OpenCV)**
* **ID Card OCR Recognition (Tesseract OCR)**
* **FastAPI Backend**
* **SQLite Database**
* **Admin Dashboard (HTML + Bootstrap)**
* **Daily Duplicate Prevention**

## ⭐ Features

### 🔵 1. Face Recognition Attendance

* Converts face into **512-D embedding vectors** using **Facenet model (DeepFace)**.
* Uses **cosine similarity** to match with stored student data.
* Prevents duplicate attendance with date check.

### 🟠 2. Strong Liveness Detection (Anti-Spoof)

Stops cheating using:

* Printed photos
* Digital photos
* Screens
* Videos

Checks include:

* Motion difference
* Sharpness (Laplacian)
* Reflection detection
* Saturation levels
* Depth variation (Sobel)
* Optional eye detection

---

### 🟢 3. ID Card Attendance (OCR)

Uses **OpenCV + Tesseract** to extract:

* Roll number
* Name
* Branch

Includes:

* Text normalization
* Regex roll number detection
* Fuzzy text matching

Also verifies physical ID using:

* Texture
* Reflection/glare
* Color saturation

---

### 🟣 4. Duplicate Attendance Prevention

Attendance is allowed only once per day per student.

---

### 🟡 5. Admin Dashboard

Admin can:

* View all students & attendance
* Search by name/roll
* Filter by branch
* Delete attendance
* See timestamps and confidence scores

---

## 🏗️ Tech Stack

| Component              | Technology                 |
| ---------------------- | -------------------------- |
| **Backend API**        | FastAPI                    |
| **Face Recognition**   | DeepFace (Facenet Model)   |
| **Liveness Detection** | OpenCV                     |
| **OCR Engine**         | Tesseract OCR              |
| **Database**           | SQLite                     |
| **Frontend**           | HTML5, CSS3, Bootstrap, JS |
| **ORM**                | SQLAlchemy                 |
| **Logging**            | Internal audit logs        |

---

## 🧠 How Face Recognition Works

### Step 1 — Capture Image

Image (base64) → convert to NumPy array

```py
img_np = b64_to_image(image_b64)
```

### Step 2 — Liveness Detection

Two frames compared:

```py
motion_score = np.sum(diff) / 255
lap_var = cv2.Laplacian(gray, cv2.CV_64F).var()
```

If spoof → attendance blocked.

### Step 3 — Embedding Generation

DeepFace (Facenet):

```py
embedding = DeepFace.represent(
    img_path=img_np,
    model_name="Facenet",
    detector_backend="opencv",
)[0]["embedding"]
```

### Step 4 — Compare With Stored Encodings

```py
similarity = cosine_similarity(known, current)
```

If similarity ≥ threshold → mark attendance.

---

## 🪪 How ID Card OCR Works

### Step 1 — Preprocessing (OpenCV)

* Convert to grayscale
* Bilateral filter
* Adaptive threshold

### Step 2 — Extract Text (Tesseract)

```py
text = pytesseract.image_to_string(gray, config="--psm 7")
```

### Step 3 — Detect Roll Number

Regex pattern matching:

```py
re.findall(r"[a-z]{1,3}\d{2,6}[a-z0-9]{0,4}", text)
```

### Step 4 — Match With Database

* Direct roll match
* Fuzzy name/branch match
* Normalized text comparison

---

## 📁 Database Structure (SQLite)

### **1. users**

| Column        | Type      |
| ------------- | --------- |
| id            | INT       |
| full_name     | TEXT      |
| roll_no       | TEXT      |
| branch        | TEXT      |
| face_encoding | BLOB      |
| id_ocr_text   | TEXT      |
| created_at    | TIMESTAMP |

### **2. attendance**

| Column     | Type      |
| ---------- | --------- |
| id         | INT       |
| user_id    | INT       |
| status     | TEXT      |
| confidence | FLOAT     |
| timestamp  | TIMESTAMP |

### **3. audit_logs**

Tracks system actions.

---

## 🚀 Run Project Locally

### **1. Clone Repo**

```bash
git clone https://github.com/yourusername/yourrepo.git
cd yourrepo/backend
```

### **2. Create Virtual Environment**

```bash
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
```

### **3. Install Requirements**

```bash
pip install -r requirements.txt
```

### **4. Run FastAPI**

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### **5. Open API Docs**

```
http://localhost:8000/docs
```

---

## 📂 Folder Structure

```
backend/
 ├── app/
 │   ├── main.py
 │   ├── models.py
 │   ├── crud.py
 │   ├── schemas.py
 │   ├── database.py
 │   ├── utils/
 │   │   ├── face_utils.py
 │   │   ├── liveness_utils.py
 │   │   ├── ocr_utils.py
 │   └── routes/
 │       ├── attendance_routes.py
 │       └── admin_routes.py
 ├── attendance.db
 └── README.md
```

---

## ✔️ Security Features Summary

| Feature               | Protects From        |
| --------------------- | -------------------- |
| Liveness detection    | Photos, videos       |
| Reflection check      | Mobile screens       |
| Texture check         | Printed photos       |
| Daily attendance lock | Multiple attempts    |
| OCR validation        | Fake/invalid ID card |
| Audit logs            | Track misuse         |

---

