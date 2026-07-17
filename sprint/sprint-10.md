# Sprint 10 — Fitur Admin (Backend + Frontend)

**Fokus**: Role Admin bisa melihat semua lowongan, takedown lowongan, dan mengelola akun user.

**Demo di akhir sprint**: Login sebagai Admin → tab Jobs → daftar semua lowongan (aktif + nonaktif) → tombol "Takedown" pada lowongan → lowongan dinonaktifkan. Tab Accounts → daftar semua user/company → bisa lihat detail.

---

- [ ] **Task 1: [credora-api] Endpoint admin (list all jobs, takedown, list users)**
  - Acceptance: `GET /admin/jobs` (protected, ADMIN) — semua lowongan termasuk yang nonaktif, dengan data company. `PATCH /admin/jobs/:id/takedown` (protected, ADMIN) — set `isActive: false`. `GET /admin/users` (protected, ADMIN) — list semua user dengan role, email, name, createdAt. `DELETE /admin/users/:id` (protected, ADMIN) — soft delete atau hard delete user (beserta relasinya). Semua endpoint menolak akses dari role selain ADMIN (403).
  - Verify: (1) Login ADMIN → `GET /admin/jobs` → semua lowongan termasuk nonaktif, (2) `PATCH /admin/jobs/:id/takedown` → lowongan menjadi nonaktif, (3) `GET /admin/users` → list user, (4) Login USER → `GET /admin/jobs` → 403, (5) Prisma Studio → data berubah sesuai.
  - Files:
    - `credora-api/src/routes/admin.routes.js`
    - `credora-api/src/controllers/admin.controller.js`
    - `credora-api/src/index.js` (register route)

---

- [ ] **Task 2: [credora-app] AdminJobListScreen — daftar lowongan + takedown**
  - Acceptance: `AdminJobListScreen.js` memanggil `GET /admin/jobs` dan menampilkan semua lowongan. Setiap card menampilkan: posisi, nama company, status (aktif/nonaktif badge), tanggal posting. Lowongan aktif punya tombol "Takedown" → konfirmasi → call PATCH takedown → badge berubah menjadi nonaktif. Filter/tab: Semua, Aktif, Nonaktif. Pull-to-refresh.
  - Verify: (1) Login ADMIN → tab Jobs → semua lowongan muncul (termasuk yang sudah dinonaktifkan company), (2) Tap "Takedown" → konfirmasi → badge berubah merah/nonaktif, (3) Filter "Aktif" → hanya lowongan aktif, (4) Pull-to-refresh → data terbaru.
  - Files:
    - `credora-app/src/screens/admin/AdminJobListScreen.js`
    - `credora-app/src/services/adminService.js`
    - `credora-app/src/components/StatusBadge.js` (reuse)

---

- [ ] **Task 3: [credora-app] AdminAccountsScreen — kelola akun**
  - Acceptance: `AdminAccountsScreen.js` memanggil `GET /admin/users` dan menampilkan list user: nama, email, role (badge berwarna), tanggal registrasi. Filter berdasarkan role (All, User, Company, Admin). Tap user → detail info (bukan bisa edit — admin hanya melihat). Tombol "Hapus Akun" per user → konfirmasi → call DELETE → user hilang dari list. Tidak bisa hapus diri sendiri (admin yang login).
  - Verify: (1) Login ADMIN → tab Accounts → list user muncul, (2) Filter "Company" → hanya akun company, (3) Tap "Hapus Akun" → konfirmasi → user hilang, (4) Cek Prisma Studio → user terhapus, (5) Coba hapus akun admin sendiri → ditolak.
  - Files:
    - `credora-app/src/screens/admin/AdminAccountsScreen.js`
    - `credora-app/src/services/adminService.js`
    - `credora-app/src/navigation/AdminTabs.js` (finalize)
