# Doctor Endpoints — Test Cases

Base URL: `http://{{host}}:{{port}}/LSMI/api`

---

## 1. Profile

### 1.1 GET /doctor/profile

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| PRF-001 | Fetch doctor profile | None | 200 | JSON object with `doctor_id`, `name`, `email`, `specialization`, `hospital_id`, `department_id` |

---

## 2. Schedule

### 2.1 GET /doctor/schedule/details

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| SCH-001 | Fetch doctor's schedule | None | 200 | JSON array of schedule objects with `id`, `doctor_id`, `day`, `start_time`, `end_time` |

### 2.2 POST /doctor/schedule/details

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| SCH-002 | Create a new schedule entry | `{ "doctor_id": 6, "day": "Monday", "start_time": "08:00", "end_time": "17:00", "hospital_id": 1, "department_id": 1 }` | 201 | `{ "message": "Schedule created", "schedule_id": <id> }` |

### 2.3 PUT /doctor/schedule/details

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| SCH-003 | Update an existing schedule entry | `{ "schedule_id": <id_from_SCH-002>, "start_time": "09:00", "end_time": "18:00" }` | 200 | `{ "message": "Schedule updated" }` |

### 2.4 DELETE /doctor/schedule/details

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| SCH-004 | Delete an existing schedule entry | `{ "schedule_id": <id_from_SCH-002> }` | 200 | `{ "message": "Schedule deleted" }` |

### 2.5 GET /doctor/get_affiliate_schedule

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| AFL-001 | Fetch affiliate schedule | None | 200 | JSON array of affiliate schedule entries with `doctor_name`, `hospital`, `department`, `day`, `start_time`, `end_time` |

---

## 3. Availability

### 3.1 POST /telemed/availability

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| AVL-001 | Set doctor as available | `{ "doctor_id": 6, "name": "Patrick", "hospital_id": 1, "department_id": 1, "is_active": true }` | 200 | `{ "message": "Availability set" }` |
| AVL-002 | Set doctor as unavailable | `{ "doctor_id": 6, "name": "Patrick", "hospital_id": 1, "department_id": 1, "is_active": false }` | 200 | `{ "message": "Availability updated", "is_active": false }` |

### 3.2 GET /telemed/availability/stream

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| AVL-003 | Stream availability by hospital & department | `hospital_id=1&department_id=1` | 200 | SSE stream (`text/event-stream`); first event contains current availability state |

### 3.3 GET /telemed/availability/

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| AVL-004 | Get all availabilities | None | 200 | JSON array of availability records with `doctor_id`, `name`, `hospital_id`, `department_id`, `is_active` |

### 3.4 DELETE /telemed/availability

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| AVL-005 | Invalidate a doctor's availability | `doctor_id=3` | 200 | `{ "message": "Availability invalidated" }` |

---

## 4. Lobby

### 4.1 GET /telemed/lobby/check

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| LBY-001 | Check lobby for hospital & department | `hospital_id=1&department_id=1` | 200 | JSON with `queue_count` and `waiting_patients` array |

### 4.2 GET /telemed/lobby/stream

| Test ID | Scenario | Input | Expected Status | Expected Response |
|---------|----------|-------|-----------------|-------------------|
| LBY-002 | Stream lobby for hospital & department | `hospital_id=1&department_id=1` | 200 | SSE stream (`text/event-stream`) with current queue state |

---

## 5. CRUD Lifecycle (Integration)

| Test ID | Scenario | Steps | Expected Outcome |
|---------|----------|-------|------------------|
| INT-001 | Schedule CRUD flow | 1. POST create → 2. GET verify present → 3. PUT update → 4. GET verify updated → 5. DELETE → 6. GET verify removed | Each step succeeds |
| INT-002 | Availability lifecycle | 1. POST set active → 2. GET all confirms present → 3. DELETE invalidate → 4. GET all confirms removed | Each step succeeds |