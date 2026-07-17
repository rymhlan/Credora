# Sprint 1 — Backend Foundation: Docker + Prisma + Health Check

**Fokus**: Setup credora-api agar `docker compose up` berhasil menjalankan Express + PostgreSQL, Prisma ter-init, dan endpoint health-check merespons.

**Demo di akhir sprint**: Jalankan `docker compose up --build`, lalu `curl http://localhost:4000/health` mengembalikan `{ "status": "ok", "db": "connected" }`.

---

- [x] **Task 1: [credora-api] Inisialisasi project Node.js + Express + Prisma**
  - Acceptance: Folder `credora-api/` berisi `package.json` dengan dependency `express`, `cors`, `dotenv`, `@prisma/client`, dan devDependency `prisma`. File `src/index.js` ada dan bisa dijalankan (`node src/index.js` tanpa error). Prisma schema ter-init di `prisma/schema.prisma` dengan model `User` dasar (id, email, password, role, name, createdAt).
  - Verify: `cd credora-api && npm install && node src/index.js` — server listen di port 4000, lalu `curl http://localhost:4000/health` mengembalikan JSON `{ "status": "ok" }`.
  - Files:
    - `credora-api/package.json`
    - `credora-api/src/index.js`
    - `credora-api/prisma/schema.prisma`
    - `credora-api/.env`
    - `credora-api/.gitignore`

---

- [x] **Task 2: [credora-api] Dockerfile + docker-compose.yml + .dockerignore**
  - Acceptance: `Dockerfile` multi-stage ringan (node:20-alpine), `docker-compose.yml` mendefinisikan service `api` (port 4000) dan `postgres` (postgres:16-alpine, port 5432, volume `pgdata`), `.dockerignore` mengecualikan `node_modules`, `.env`, `.git`. Environment variable (`DATABASE_URL`, `JWT_SECRET`, `PORT`) dibaca dari `.env` via `env_file`, tidak di-hardcode.
  - Verify: `docker compose up --build` — kedua container (`api` dan `postgres`) running tanpa error di log, `curl http://localhost:4000/health` merespons `200 OK`.
  - Files:
    - `credora-api/Dockerfile`
    - `credora-api/docker-compose.yml`
    - `credora-api/.dockerignore`
    - `credora-api/.env`

---

- [x] **Task 3: [credora-api] Prisma migration + health-check endpoint konek ke DB**
  - Acceptance: Migration pertama (`init`) berhasil dijalankan via `docker compose exec api npx prisma migrate dev --name init`, tabel `User` terbentuk di PostgreSQL. Endpoint `GET /health` diperluas: selain `status: "ok"`, juga mengecek koneksi Prisma ke database dan mengembalikan `{ "status": "ok", "db": "connected" }`. Jika DB unreachable, mengembalikan `{ "status": "ok", "db": "disconnected" }`.
  - Verify: (1) `docker compose up --build`, (2) `docker compose exec api npx prisma migrate dev --name init` sukses, (3) `curl http://localhost:4000/health` → `{ "status": "ok", "db": "connected" }`, (4) Buka Prisma Studio (`docker compose exec api npx prisma studio`) dan pastikan tabel `User` terlihat.
  - Files:
    - `credora-api/src/index.js` (update health endpoint)
    - `credora-api/prisma/schema.prisma`
    - `credora-api/prisma/migrations/` (auto-generated)
