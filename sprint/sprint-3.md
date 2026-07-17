# Sprint 3 — Auth Backend: Register + Login + JWT

**Fokus**: Endpoint register dan login di credora-api dengan hash bcrypt + JWT token, siap dikonsumsi frontend.

**Demo di akhir sprint**: `curl -X POST http://localhost:4000/auth/register` dengan body JSON → user tersimpan di DB dengan password ter-hash. `curl -X POST http://localhost:4000/auth/login` → mendapat JWT token. Token bisa dipakai untuk akses endpoint protected.

---

- [ ] **Task 1: [credora-api] Schema Prisma lengkap (User, Job, Application)**
  - Acceptance: `schema.prisma` mendefinisikan 3 model: `User` (id uuid, email unique, password, name, role enum USER/COMPANY/ADMIN, phone, address, education, skills String[], createdAt), `Job` (id uuid, companyId FK, position, description, salaryRange, requiredSkills String[], minEducation, location, createdAt, isActive Boolean default true), `Application` (id uuid, userId FK, jobId FK, cvData JSON, status enum PENDING/REVIEWED/ACCEPTED/REJECTED, createdAt). Relasi: User hasMany Job (sebagai company), User hasMany Application, Job hasMany Application. Migration berhasil tanpa error.
  - Verify: `docker compose up --build` → `docker compose exec api npx prisma migrate dev --name add_all_models` sukses → Prisma Studio menampilkan 3 tabel dengan kolom lengkap.
  - Files:
    - `credora-api/prisma/schema.prisma`
    - `credora-api/prisma/migrations/` (auto-generated)

---

- [ ] **Task 2: [credora-api] Endpoint POST /auth/register + POST /auth/login**
  - Acceptance: `POST /auth/register` menerima `{ email, password, name, role }`, validasi input (email format, password min 6 char, role harus salah satu dari USER/COMPANY/ADMIN), hash password dengan bcrypt, simpan ke DB, kembalikan `{ user: { id, email, name, role }, token }`. `POST /auth/login` menerima `{ email, password }`, cek email ada, cek password cocok, kembalikan `{ user: { id, email, name, role }, token }`. Error handling: 400 untuk input invalid, 401 untuk login gagal, 409 untuk email sudah terdaftar.
  - Verify: (1) `curl -X POST http://localhost:4000/auth/register -H "Content-Type: application/json" -d '{"email":"test@test.com","password":"123456","name":"Test","role":"USER"}'` → 201 + token, (2) Register ulang email sama → 409, (3) `curl -X POST http://localhost:4000/auth/login -H "Content-Type: application/json" -d '{"email":"test@test.com","password":"123456"}'` → 200 + token, (4) Login password salah → 401.
  - Files:
    - `credora-api/src/routes/auth.routes.js`
    - `credora-api/src/controllers/auth.controller.js`
    - `credora-api/src/index.js` (register route)

---

- [ ] **Task 3: [credora-api] Middleware JWT auth + role guard**
  - Acceptance: `authMiddleware.js` memverifikasi header `Authorization: Bearer <token>`, decode JWT, attach `req.user = { id, email, role }`. Jika token invalid/missing → 401. `roleMiddleware.js` menerima array role yang diizinkan, cek `req.user.role` — jika tidak sesuai → 403. Endpoint `GET /auth/me` (protected) mengembalikan data user dari token tanpa password. Endpoint ini berfungsi sebagai bukti middleware bekerja.
  - Verify: (1) `curl http://localhost:4000/auth/me` tanpa token → 401, (2) `curl http://localhost:4000/auth/me -H "Authorization: Bearer <token_dari_login>"` → 200 + user data tanpa password, (3) Buat endpoint test `GET /admin/test` yang butuh role ADMIN → akses dengan token USER → 403, akses dengan token ADMIN → 200.
  - Files:
    - `credora-api/src/middleware/authMiddleware.js`
    - `credora-api/src/middleware/roleMiddleware.js`
    - `credora-api/src/routes/auth.routes.js` (tambah /me)
    - `credora-api/src/controllers/auth.controller.js` (tambah getMe)
