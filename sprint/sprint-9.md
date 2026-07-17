# Sprint 9 — Fitur Perusahaan (Frontend)

**Fokus**: Role Company bisa post lowongan, lihat daftar pelamar, dan update status lamaran dari app.

**Demo di akhir sprint**: Login sebagai Company → tab Home → lihat lowongan yang sudah dipost → tab Post Job → buat lowongan baru → kembali ke Home → lowongan baru muncul. Tap lowongan → lihat daftar pelamar → tap pelamar → lihat detail CV → update status ke "Reviewed".

---

- [ ] **Task 1: [credora-app] CompanyHomeScreen — daftar lowongan milik company**
  - Acceptance: `CompanyHomeScreen.js` memanggil `GET /jobs?companyId=<id>` (atau endpoint khusus company) dan menampilkan lowongan milik company yang login. Setiap card menampilkan: posisi, jumlah pelamar, status aktif/nonaktif, tanggal posting. Tombol "Nonaktifkan" per lowongan (call PATCH deactivate). Pull-to-refresh. Empty state "Belum ada lowongan — buat yang pertama!".
  - Verify: (1) Login COMPANY yang punya 2 lowongan dari seed → 2 card muncul, (2) Tap "Nonaktifkan" → lowongan hilang dari list (atau badge berubah), (3) Login COMPANY baru (belum post) → empty state.
  - Files:
    - `credora-app/src/screens/company/CompanyHomeScreen.js`
    - `credora-app/src/services/jobService.js` (add getMyJobs)
    - `credora-app/src/components/JobCard.js` (reuse/extend)

---

- [ ] **Task 2: [credora-app] PostJobScreen — form posting lowongan baru**
  - Acceptance: `PostJobScreen.js` menampilkan form: posisi, deskripsi (textarea), kisaran gaji, lokasi, pendidikan minimum (dropdown), skills yang dibutuhkan (multi-input). Tombol "Posting Lowongan" memanggil `POST /jobs` dengan token. Sukses → alert + navigasi ke CompanyHomeScreen (lowongan baru muncul). Validasi frontend: posisi dan minimal 1 skill wajib diisi.
  - Verify: (1) Login COMPANY → tab Post Job → isi semua field → Posting → sukses, (2) Kembali ke Home → lowongan baru ada di list, (3) Submit tanpa posisi → error validasi, (4) Login USER → tab Post Job tidak tersedia (karena beda navigator).
  - Files:
    - `credora-app/src/screens/company/PostJobScreen.js`
    - `credora-app/src/services/jobService.js` (add createJob)
    - `credora-app/src/components/FormInput.js` (reuse)

---

- [ ] **Task 3: [credora-app] ApplicantListScreen + ApplicantDetailScreen**
  - Acceptance: Tap lowongan di CompanyHomeScreen → `ApplicantListScreen.js` menampilkan daftar pelamar (nama, pendidikan, tanggal melamar, status badge). Tap pelamar → `ApplicantDetailScreen.js` menampilkan CV lengkap (format yang sama dengan PreviewScreen — ATS layout) + dropdown/tombol untuk update status (Reviewed/Accepted/Rejected) yang memanggil `PATCH /applications/:id/status`. Sukses → badge berubah saat kembali ke list.
  - Verify: (1) Login COMPANY → tap lowongan yang punya pelamar → list muncul, (2) Tap pelamar → CV detail terlihat (satu kolom, heading baku), (3) Update status ke "Accepted" → kembali ke list → badge berubah hijau, (4) Lowongan tanpa pelamar → empty state.
  - Files:
    - `credora-app/src/screens/company/ApplicantListScreen.js`
    - `credora-app/src/screens/company/ApplicantDetailScreen.js`
    - `credora-app/src/navigation/CompanyTabs.js` (add stack routes)
    - `credora-app/src/services/applicationService.js`
