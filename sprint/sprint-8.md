# Sprint 8 — Preview CV + Status Lamaran (User)

**Fokus**: User bisa preview CV yang sudah diisi sebelum submit, dan melihat status semua lamarannya.

**Demo di akhir sprint**: User isi form CV → tap "Preview" → layar preview menampilkan CV dalam format ATS-friendly (satu kolom, heading baku) → user bisa "Kirim" dari preview. Tab Status → daftar semua lamaran user dengan status badge (Pending/Reviewed/Accepted/Rejected).

---

- [ ] **Task 1: [credora-app] PreviewScreen — preview CV format ATS di layar**
  - Acceptance: `PreviewScreen.js` menerima data CV (dari CVFormScreen via navigation params) dan menampilkan preview CV dalam format yang mengikuti kaidah ATS PRD Bagian 3: satu kolom, heading baku (BIODATA, RINGKASAN, PENGALAMAN KERJA, PENDIDIKAN, KEAHLIAN), tanpa tabel/ikon/grafik, font standar. Layout mirip dokumen (background putih, padding, border). Tombol "Kirim Lamaran" dan "Kembali Edit". "Kirim" memanggil POST /applications → sukses → navigasi ke StatusScreen.
  - Verify: (1) Isi form CV → Preview → layout satu kolom terlihat, (2) Heading BIODATA/RINGKASAN/PENGALAMAN KERJA/PENDIDIKAN/KEAHLIAN ada, (3) Tidak ada ikon atau tabel, (4) Tap "Kirim" → sukses → StatusScreen, (5) Tap "Kembali Edit" → kembali ke form dengan data utuh.
  - Files:
    - `credora-app/src/screens/user/PreviewScreen.js`
    - `credora-app/src/navigation/UserTabs.js` (add route)
    - `credora-app/src/services/applicationService.js`

---

- [ ] **Task 2: [credora-app] StatusScreen — daftar lamaran + status badge**
  - Acceptance: `StatusScreen.js` (tab Status di UserTabs) memanggil `GET /applications` dan menampilkan list lamaran user: posisi lowongan, nama perusahaan, tanggal melamar, status badge berwarna (PENDING=kuning, REVIEWED=biru, ACCEPTED=hijau, REJECTED=merah). Tap item → detail lamaran (CVData yang dikirim + status). Pull-to-refresh. Empty state "Belum ada lamaran". `StatusBadge.js` component reusable.
  - Verify: (1) User yang sudah submit lamaran → tab Status → list muncul, (2) Badge warna sesuai status, (3) Tap lamaran → detail muncul, (4) User baru (belum lamar) → empty state, (5) Pull-to-refresh → data reload.
  - Files:
    - `credora-app/src/screens/user/StatusScreen.js`
    - `credora-app/src/components/StatusBadge.js`
    - `credora-app/src/services/applicationService.js`

---

- [ ] **Task 3: [credora-api] Endpoint update status lamaran (untuk Company)**
  - Acceptance: `PATCH /applications/:id/status` (protected, role COMPANY) menerima `{ status }` (REVIEWED/ACCEPTED/REJECTED). Validasi: hanya company yang memiliki lowongan terkait bisa update status. Response mengembalikan application yang sudah diupdate. `GET /applications/job/:jobId` (protected, role COMPANY) — list semua lamaran untuk lowongan tertentu milik company tersebut.
  - Verify: (1) Login COMPANY → `GET /applications/job/:jobId` → list pelamar, (2) `PATCH /applications/:id/status` dengan `REVIEWED` → 200 + status updated, (3) Login COMPANY lain → coba update lamaran bukan miliknya → 403, (4) Login USER → coba update → 403, (5) Prisma Studio → status berubah.
  - Files:
    - `credora-api/src/routes/applications.routes.js`
    - `credora-api/src/controllers/applications.controller.js`
