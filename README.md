# 🔐 API Security Risk Analyzer

> **Cybersecurity Internship — Task 3**  
> A read-only API Security Risk Analysis tool that audits public REST APIs against the OWASP API Security Top 10 (2023) and generates professional security reports.

![Security](https://img.shields.io/badge/Security-OWASP%20API%20Top%2010-red?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.11+-blue?style=for-the-badge&logo=python)
![React](https://img.shields.io/badge/React-18.x-61DAFB?style=for-the-badge&logo=react)
![FastAPI](https://img.shields.io/badge/FastAPI-0.110-009688?style=for-the-badge&logo=fastapi)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [APIs Tested](#-apis-tested)
- [Findings Summary](#-findings-summary)
- [Tools Used](#-tools-used)
- [Analysis Approach](#-analysis-approach)
- [Project Structure](#-project-structure)
- [Setup & Installation](#-setup--installation)
- [Sample Evidence](#-sample-evidence)
- [Ethical Disclosure](#-ethical-disclosure)

---

## 🔍 Project Overview

This project performs a **passive, read-only security audit** of two public demo REST APIs. The tool identifies common API vulnerabilities, classifies their severity, explains the business impact in plain language, and produces a downloadable security report — exactly as a professional AppSec consultant would deliver to a SaaS client.

The six security checks map directly to the **OWASP API Security Top 10 (2023)**:

| ID | Finding | Severity | OWASP Category |
|----|---------|----------|----------------|
| F-01 | Unauthenticated access to user data | 🔴 CRITICAL | API2:2023 |
| F-02 | Broken Object Level Authorization (BOLA) | 🟠 HIGH | API1:2023 |
| F-03 | Excessive data exposure in responses | 🟠 HIGH | API3:2023 |
| F-04 | Missing rate limiting on all endpoints | 🟡 MEDIUM | API4:2023 |
| F-05 | Absent HTTP security response headers | 🟡 MEDIUM | API8:2023 |
| F-06 | Auth token not enforced on user endpoint | 🟢 LOW | API2:2023 |

---

## 🎯 APIs Tested

### 1. JSONPlaceholder
- **URL:** https://jsonplaceholder.typicode.com
- **Purpose:** Free fake REST API for testing and prototyping
- **Checks:** F-01, F-02, F-03, F-04, F-05

### 2. ReqRes
- **URL:** https://reqres.in
- **Purpose:** Hosted REST API simulating real authentication flows
- **Checks:** F-04, F-05, F-06

---

## 📊 Findings Summary

```
Overall Risk Rating:  HIGH RISK

  CRITICAL    HIGH    MEDIUM    LOW
     1          2        2       1
```

---

## 🛠 Tools Used

| Tool | Purpose |
|------|---------|
| **Postman** | Primary API testing — send requests, inspect headers and responses |
| **Browser DevTools** | Network tab for live header and response inspection |
| **Python / FastAPI** | Backend analysis engine and REST endpoints |
| **httpx** | Async HTTP client for all security check requests |
| **Pydantic** | Typed data models for Finding and Report objects |
| **ReportLab** | PDF report generation |
| **python-docx** | Word document report generation |
| **React + Vite** | Frontend dashboard UI |
| **Tailwind CSS** | Component styling |

---

## 🔬 Analysis Approach

The analysis follows a **passive inspection methodology** aligned with OWASP API Security testing guidance. Every test is read-only — no data is modified, no exploits are attempted, no systems are disrupted.

### Phase 1 — Reconnaissance
- Review API documentation to understand intended behaviour and auth model
- Map all available endpoints and HTTP methods
- Identify resource ID patterns and authentication schemes

### Phase 2 — Unauthenticated Access (F-01)
```
Request:  GET /users   (no Authorization header)
Expected: 401 Unauthorized
Actual:   200 OK + full user data  →  CRITICAL finding
```

### Phase 3 — BOLA Authorization Test (F-02)
```
GET /users/1  →  200 OK  (Leanne Graham)
GET /users/2  →  200 OK  (Ervin Howell)
GET /users/3  →  200 OK  (Clementine Bauch)
No auth check between requests  →  HIGH finding
```

### Phase 4 — Response Field Audit (F-03)
- Fetch `GET /users/1` and inspect every response field
- Map each field to a documented consumer use case
- Flag `geo.lat`, `geo.lng`, `company.bs`, `company.catchPhrase`, `website` as over-exposed

### Phase 5 — Rate Limit Detection (F-04)
- Send exactly **5 sequential GET requests** with 200 ms gaps
- Check all responses for: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `Retry-After`
- All absent across all 5 responses → **MEDIUM finding**

### Phase 6 — Security Header Audit (F-05)
Open Postman → send `GET /users` → click **Response Headers** tab. Check for:

| Header | Expected Value | Found? |
|--------|---------------|--------|
| `X-Content-Type-Options` | `nosniff` | ❌ Missing |
| `X-Frame-Options` | `DENY` | ❌ Missing |
| `Strict-Transport-Security` | `max-age=31536000` | ❌ Missing |
| `Content-Security-Policy` | `default-src 'self'` | ❌ Missing |
| `Cache-Control` | `no-store` | ❌ Missing |

### Phase 7 — Token Enforcement (F-06)
```
Step 1:  POST /api/login  →  receive token "QpwL5tpe83ilfN2"
Step 2:  GET  /api/users  (no token in headers)  →  200 OK
Finding: token issued but never checked  →  LOW finding
```

### Phase 8 — Documentation
Every finding follows the **DEBA structure**:
- **D**escription — what the vulnerability is
- **E**vidence — actual HTTP request + response
- **B**usiness Impact — real-world consequences
- **A**ction — specific, implementable remediation steps

---

## 📁 Project Structure

```
api-security-risk-analyzer/
├── README.md
├── LICENSE
├── docker-compose.yml
│
├── backend/
│   ├── main.py                    # FastAPI app
│   ├── requirements.txt
│   ├── analyzer/
│   │   ├── engine.py              # Orchestrates all checks
│   │   ├── models.py              # Pydantic Finding + Report models
│   │   └── checks/
│   │       ├── auth_check.py      # F-01
│   │       ├── bola_check.py      # F-02
│   │       ├── data_exposure.py   # F-03
│   │       ├── rate_limit_check.py# F-04
│   │       ├── header_check.py    # F-05
│   │       └── token_check.py     # F-06
│   └── report/
│       └── generator.py           # PDF + DOCX output
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── api.js
│   │   └── components/
│   │       ├── Dashboard.jsx
│   │       ├── ApiSelector.jsx
│   │       ├── FindingsTable.jsx
│   │       ├── SeverityBadge.jsx
│   │       ├── ProgressTracker.jsx
│   │       └── ReportDownload.jsx
│   └── package.json
│
├── evidence/
│   ├── emails/
│   │   ├── F01_unauthenticated_access.eml
│   │   ├── F02_bola_enumeration.eml
│   │   ├── F03_excessive_data_exposure.eml
│   │   ├── F04_missing_rate_limiting.eml
│   │   ├── F05_missing_security_headers.eml
│   │   └── F06_token_not_enforced.eml
│   └── screenshots/
│       └── README.md
│
├── reports/
│   └── sample_report_template.md
│
└── docs/
    └── OWASP_mapping.md
```

---

## ⚙️ Setup & Installation

### Backend
```bash
cd backend
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

### Frontend
```bash
cd frontend
npm install
npm run dev
# Open http://localhost:3000
```

### Docker (single command)
```bash
docker-compose up --build
```

---

## 📧 Sample Evidence

The `evidence/emails/` directory contains sample finding notification emails — formatted exactly as a security analyst would send to a development team after each finding.

Each email includes:
- Finding ID and severity rating
- Affected API and endpoint
- Proof-of-concept HTTP request and response
- Business impact statement
- Prioritised remediation steps

---

## ⚖️ Ethical Disclosure

| Constraint | Status |
|-----------|--------|
| Only public demo APIs used | ✅ |
| All requests read-only | ✅ |
| Maximum 5 requests for rate-limit check | ✅ |
| No exploitation or bypass attempts | ✅ |
| No private or production systems accessed | ✅ |

> ❌ **This tool must NOT be used against any API without explicit written permission.** Unauthorized testing may violate the IT Act 2000 (India) and equivalent legislation in other jurisdictions.

---

## 👤 Author

**Utpal** — Cybersecurity Internship, Task 3  
Shambhunath Institute of Engineering & Technology (AKTU University)

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.
