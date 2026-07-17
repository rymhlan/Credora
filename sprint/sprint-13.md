# Sprint 13 — Black Box Testing + Persiapan Build APK

**Fokus**: Eksekusi black box testing seluruh fitur, dokumentasi hasil, perbaiki bug yang ditemukan, dan persiapan build APK untuk demo sidang.

**Demo di akhir sprint**: Tabel black box testing lengkap dengan semua skenario berstatus Valid. APK preview build berhasil diinstall di device fisik dan semua fitur berfungsi.

---

- [ ] **Task 1: [credora-app] + [credora-api] Skenario Black Box Testing — eksekusi + dokumentasi**
  - Acceptance: File `sprint/blackbox-testing-results.md` berisi tabel skenario uji per fitur dengan kolom: No, Fitur, Skenario, Input, Ekspektasi, Hasil Aktual, Status (Valid/Tidak Valid). Minimal skenario yang diuji: (1) Register + Login per role, (2) Edit profil, (3) Browse + detail lowongan, (4) Isi form CV + validasi checklist, (5) Preview CV, (6) Submit lamaran, (7) Cek status lamaran, (8) Company post lowongan, (9) Company lihat + update status pelamar, (10) Admin takedown lowongan, (11) Admin kelola akun, (12) Ekspor PDF CV, (13) Logout. Setiap fitur minimal 2 skenario (happy path + error case).
  - Verify: Semua baris di tabel testing terisi lengkap (tidak ada kolom kosong). Semua status "Valid". Jika ada "Tidak Valid", bug harus dicatat dan diperbaiki sebelum task ini ditandai selesai.
  - Files:
    - `sprint/blackbox-testing-results.md`
    - (bug fix files sesuai temuan — tidak diketahui sebelumnya)

---

- [ ] **Task 2: [credora-api] Deploy backend ke Railway/Render**
  - Acceptance: Backend credora-api berhasil di-deploy ke layanan cloud (Railway atau Render) sehingga bisa diakses dari internet (bukan hanya localhost). URL production tercatat (misal `https://credora-api.up.railway.app`). Database PostgreSQL production sudah disetup (Railway Postgres atau Neon). Migration berhasil dijalankan di production. `GET /health` dari URL production mengembalikan `{ "status": "ok", "db": "connected" }`. Environment variable (`DATABASE_URL`, `JWT_SECRET`) sudah diset di dashboard hosting.
  - Verify: (1) `curl https://<production-url>/health` → `{ "status": "ok", "db": "connected" }`, (2) Register user baru via curl ke production URL → sukses, (3) Login → token valid, (4) `GET /jobs` → response (mungkin empty, perlu seed di production).
  - Files:
    - `credora-api/Dockerfile` (review/update jika perlu)
    - `credora-api/.env.example` (dokumentasi env vars needed)
    - `credora-app/src/services/api.js` (update baseURL ke production)

---

- [ ] **Task 3: [credora-app] Persiapan + build APK preview**
  - Acceptance: `app.json` / `eas.json` dikonfigurasi untuk build preview APK (profile "preview" di EAS). Semua aset (icon, splash screen) sudah ada. `baseURL` di `api.js` mengarah ke backend production yang sudah live. Build command `eas build -p android --profile preview` berhasil menghasilkan file APK. APK bisa diinstall di device fisik Android. Setelah install: register → login → browse lowongan → lamar → preview CV → export PDF → semua berfungsi dari APK.
  - Verify: (1) `eas build -p android --profile preview` selesai tanpa error, (2) Download APK → install di HP → app terbuka, (3) Register akun baru → login → fitur berjalan normal, (4) Eksport PDF → berhasil, (5) Semua role bisa login dan fitur sesuai.
  - Files:
    - `credora-app/app.json`
    - `credora-app/eas.json`
    - `credora-app/src/services/api.js`
    - `credora-app/assets/` (icon, splash)
