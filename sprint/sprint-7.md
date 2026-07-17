# Sprint 7 — Form CV + Validasi Checklist (Backend + Frontend)

**Fokus**: User bisa mengisi form CV dari detail lowongan, data profil auto-prefill, validasi checklist kesesuaian skill/pendidikan terhadap syarat lowongan.

**Demo di akhir sprint**: User tap "Lamar" di detail lowongan → form CV muncul (biodata prefill dari profil) → isi pengalaman kerja + ringkasan → checklist validasi muncul (✓ pendidikan sesuai, ✗ kurang skill X) → user bisa submit hanya jika checklist terpenuhi atau acknowledge kekurangan.

---

- [ ] **Task 1: [credora-api] Endpoint lamaran (POST + GET /applications)**
  - Acceptance: `POST /applications` (protected, role USER) menerima `{ jobId, cvData }` di mana `cvData` berupa JSON `{ biodata: { name, phone, address, education }, summary, experience, skills }`. Validasi: jobId harus valid dan job harus aktif, user belum pernah melamar job yang sama. Simpan ke DB dengan status `PENDING`. `GET /applications` (protected, USER) mengembalikan semua lamaran milik user yang login beserta info lowongan (posisi, perusahaan). `GET /applications/:id` (protected) — detail satu lamaran.
  - Verify: (1) Login USER → `POST /applications` dengan jobId valid + cvData → 201, (2) POST ulang job sama → 409 "Sudah melamar", (3) `GET /applications` → array berisi lamaran, (4) POST dengan jobId invalid → 404, (5) Cek Prisma Studio → data Application tersimpan.
  - Files:
    - `credora-api/src/routes/applications.routes.js`
    - `credora-api/src/controllers/applications.controller.js`
    - `credora-api/src/index.js` (register route)

---

- [ ] **Task 2: [credora-app] CVFormScreen — form isi CV dengan auto-prefill**
  - Acceptance: Dari `JobDetailScreen` tap "Lamar" → navigasi ke `CVFormScreen.js` dengan parameter `jobId`. Form terdiri dari section: Biodata (name, phone, address, education — auto-prefill dari profil user), Ringkasan (textarea), Pengalaman Kerja (multi-entry: posisi, perusahaan, durasi — bisa tambah/hapus), Keahlian (multi-input, prefill dari profil). Jika profil belum lengkap, tampilkan pesan "Lengkapi profil dulu" dengan link ke ProfileScreen.
  - Verify: (1) Login USER yang sudah isi profil, (2) Buka lowongan → Lamar → CVFormScreen, (3) Biodata + skills sudah prefill, (4) Bisa tambah pengalaman kerja (posisi, perusahaan, durasi), (5) Bisa edit semua field, (6) Login USER tanpa profil → pesan "Lengkapi profil".
  - Files:
    - `credora-app/src/screens/user/CVFormScreen.js`
    - `credora-app/src/navigation/UserTabs.js` (add route)
    - `credora-app/src/services/userService.js`

---

- [ ] **Task 3: [credora-app] Validasi checklist kesesuaian + logic submit**
  - Acceptance: Di bawah form CV (atau sebagai step terpisah), tampilkan checklist validasi: (1) Pendidikan ≥ minimum yang diminta lowongan → ✓ atau ✗, (2) Setiap required skill dari lowongan dicek terhadap skills user → ✓ match atau ✗ kurang. Logic validasi di `src/utils/validation.js` (fungsi murni, testable). Komponen `ChecklistItem.js` menampilkan ikon ✓/✗ + teks. Jika semua terpenuhi, tombol "Kirim Lamaran" aktif. Jika ada yang kurang, tampilkan warning tapi user tetap bisa submit (dengan konfirmasi). Submit memanggil `POST /applications` → sukses → navigasi ke StatusScreen.
  - Verify: (1) User dengan pendidikan S1 melamar job minEducation S1 → ✓, (2) User tanpa skill "React" melamar job yang butuh "React" → ✗, (3) Semua match → tombol aktif langsung, (4) Ada mismatch → warning + konfirmasi → submit → sukses → StatusScreen, (5) Cek Prisma Studio → Application tersimpan.
  - Files:
    - `credora-app/src/utils/validation.js`
    - `credora-app/src/components/ChecklistItem.js`
    - `credora-app/src/screens/user/CVFormScreen.js` (integrate checklist + submit)
    - `credora-app/src/services/applicationService.js`
