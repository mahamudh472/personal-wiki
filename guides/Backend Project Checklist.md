
# ✅ Backend Project Checklist (Reusable)

## 0️⃣ Pre-Development (DO NOT SKIP)

### 📌 Project Understanding

* [ ] Read **project vision** completely
* [ ] Review **Figma main flows** (not UI details)
* [ ] List **core user journeys** (happy path only)
* [ ] Identify **non-goals / out-of-scope**

### 📌 Stakeholders & Roles

* [ ] List all user roles
* [ ] Define permissions per role
* [ ] Decide: single role vs multiple roles per user

---

## 1️⃣ Requirements → Backend Contract

### 📌 Feature Breakdown

For each feature:

* [ ] What triggers it?
* [ ] Who can access it?
* [ ] Required inputs
* [ ] Expected outputs
* [ ] Failure cases

### 📌 API Ownership

* [ ] Which APIs are public?
* [ ] Which are internal/admin-only?
* [ ] Versioning strategy (`/v1/`, `/v2/` or not)

---

## 2️⃣ Data Modeling (Foundation)

### 📌 Models

* [ ] Define core entities
* [ ] Decide soft delete vs hard delete
* [ ] Add timestamps (`created_at`, `updated_at`)
* [ ] Decide nullable vs required fields
* [ ] Index frequently queried fields

### 📌 Relationships

* [ ] One-to-one
* [ ] One-to-many
* [ ] Many-to-many
* [ ] Cascade rules defined

> 🔴 If models are unclear → STOP. Everything else depends on this.

---

## 3️⃣ Business Rules (Write Before Code)

### 📌 Rules Documentation

For each feature:

* [ ] When is it allowed?
* [ ] When is it blocked?
* [ ] Edge cases
* [ ] Time-based behavior (expiry, cooldowns, renewals)

### 📌 State Management

* [ ] Explicit states (e.g., pending / active / expired)
* [ ] Allowed state transitions
* [ ] Invalid transitions handled

---

## 4️⃣ Architecture Decisions (Lock Early)

### 📌 Core Decisions

* [ ] Authentication method
* [ ] Authorization method
* [ ] Timezone handling
* [ ] Currency handling
* [ ] Async tasks needed?
* [ ] External services list

### 📌 Decision Log

* [ ] Why this approach?
* [ ] Alternatives rejected
* [ ] Assumptions made

> This saves you months later.

---

## 5️⃣ Service Layer Setup (Highly Recommended)

### 📌 Structure

* [ ] Services folder created
* [ ] One service = one responsibility
* [ ] No DB logic in views
* [ ] No external calls in views

Example:

```
services/
  users.py
  payments.py
  subscriptions.py
```

---

## 6️⃣ API Design (Before Implementation)

### 📌 API Contracts

For every endpoint:

* [ ] URL
* [ ] Method
* [ ] Request payload
* [ ] Response payload
* [ ] Error responses
* [ ] Permission required

### 📌 Consistency

* [ ] Naming conventions
* [ ] Pagination format
* [ ] Error format
* [ ] Status codes standard

---

## 7️⃣ Implementation Phase

### 📌 Coding Rules

* [ ] Thin views/controllers
* [ ] Business logic only in services
* [ ] Reusable helpers
* [ ] No magic numbers/strings
* [ ] Feature flags if needed

### 📌 Incremental Delivery

* [ ] Feature works end-to-end
* [ ] API tested
* [ ] Edge cases handled
* [ ] Logs added

---

## 8️⃣ Integration & External Services

### 📌 Third-Party APIs

* [ ] Timeout handling
* [ ] Retry logic
* [ ] Webhook verification
* [ ] Idempotency handling
* [ ] Failure recovery plan

---

## 9️⃣ Security & Reliability

### 📌 Security

* [ ] Input validation
* [ ] Permission checks everywhere
* [ ] Rate limiting
* [ ] Sensitive data protection
* [ ] Secrets not in code

### 📌 Reliability

* [ ] Graceful failure handling
* [ ] Transaction usage
* [ ] Rollback strategy

---

## 🔟 Testing Strategy (Realistic)

### 📌 Tests

* [ ] Core business rules tested
* [ ] Permission tests
* [ ] Payment flows tested
* [ ] Edge cases tested

> Don’t aim for 100% — aim for **critical path coverage**.

---

## 1️⃣1️⃣ Deployment Readiness

### 📌 Environment

* [ ] Environment variables documented
* [ ] Migrations tested
* [ ] Background workers running
* [ ] Health check endpoint

---

## 1️⃣2️⃣ Scope Control (Very Important)

### 📌 Change Policy

* [ ] New feature = new phase
* [ ] Minor change vs major change defined
* [ ] Verbal requests not accepted
* [ ] Everything written

---

## 1️⃣3️⃣ Weekly Backend Health Check

Ask yourself:

* [ ] Which files are growing too large?
* [ ] Any duplicated logic?
* [ ] Any unclear responsibility?
* [ ] Any TODO older than 2 weeks?

Refactor early. Always.
