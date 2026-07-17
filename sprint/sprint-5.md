# Sprint 5 — Profil User + Daftar Lowongan (Backend)

**Fokus**: Endpoint profil user (update biodata) dan CRUD lowongan di backend, siap dikonsumsi frontend.

**Demo di akhir sprint**: Via curl — user bisa update profil (name, phone, address, education, skills), company bisa create/read lowongan, semua user bisa list lowongan aktif.

---

- [ ] **Task 1: [credora-api] Endpoint profil user (GET + PUT /users/profile)**
  - Acceptance: `GET /users/profile` (protected, semua role) mengembalikan data profil user yang login (dari token), tanpa password. `PUT /users/profile` (protected) menerima `{ name, phone, address, education, skills }` dan mengupdate data di DB. Validasi: name tidak boleh kosong. Skills berupa array string. Response mengembalikan data user yang sudah diupdate.
  - Verify: (1) Login sebagai USER → dapat token, (2) `curl GET /users/profile` dengan token → data user, (3) `curl PUT /users/profile` update name + skills → 200 + data updated, (4) GET lagi → data berubah, (5) Cek di Prisma Studio → data di DB sesuai.
  - Files:
    - `credora-api/src/routes/users.routes.js`
    - `credora-api/src/controllers/users.controller.js`
    - `credora-api/src/index.js` (register route)

---

- [ ] **Task 2: [credora-api] Endpoint CRUD lowongan (jobs)**
  - Acceptance: `POST /jobs` (protected, role COMPANY) — buat lowongan baru `{ position, description, salaryRange, requiredSkills, minEducation, location }`, `companyId` dari token. `GET /jobs` (public) — list semua lowongan aktif (`isActive: true`) dengan data company (name). `GET /jobs/:id` (public) — detail satu lowongan. `PUT /jobs/:id` (protected, role COMPANY, hanya pemilik) — update lowongan. `DELETE /jobs/:id` atau `PATCH /jobs/:id/deactivate` (protected, COMPANY pemilik atau ADMIN) — soft delete (set `isActive: false`).
  - Verify: (1) Register + login sebagai COMPANY, (2) `POST /jobs` → 201 + job data, (3) `GET /jobs` → array berisi lowongan yang baru dibuat, (4) `GET /jobs/:id` → detail lowongan, (5) Login sebagai USER → `POST /jobs` → 403, (6) Login sebagai ADMIN → deactivate job → `GET /jobs` tidak menampilkan job tersebut.
  - Files:
    - `credora-api/src/routes/jobs.routes.js`
    - `credora-api/src/controllers/jobs.controller.js`
    - `credora-api/src/index.js` (register route)

---

- [ ] **Task 3: [credora-api] Seed data lowongan untuk testing**
  - Acceptance: File `prisma/seed.js` membuat: 1 akun ADMIN, 2 akun COMPANY (dengan nama perusahaan berbeda), 5 lowongan aktif dengan variasi posisi/skills/pendidikan, 2 akun USER. Script bisa dijalankan via `docker compose exec api npx prisma db seed`. Jika data sudah ada (email duplicate), skip tanpa error (upsert). Seed script terdaftar di `package.json` field `prisma.seed`.
  - Verify: (1) `docker compose exec api npx prisma db seed` → sukses tanpa error, (2) Prisma Studio → 5+ user, 5 job terlihat, (3) `curl GET /jobs` → 5 lowongan muncul dengan data company, (4) Jalankan seed ulang → tidak duplicate error.
  - Files:
    - `credora-api/prisma/seed.js`
    - `credora-api/package.json` (tambah prisma.seed config)
