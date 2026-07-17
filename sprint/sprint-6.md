# Sprint 6 — Profil User + Daftar Lowongan (Frontend)

**Fokus**: User bisa lihat dan edit profil, serta browse daftar lowongan dari data asli di backend.

**Demo di akhir sprint**: Login sebagai User di Expo Go → tab Profile → isi/edit biodata (nama, HP, alamat, pendidikan, skills) → simpan → data tersimpan di DB. Tab Home → daftar lowongan dari database muncul sebagai card → tap card → layar detail lowongan.

---

- [ ] **Task 1: [credora-app] ProfileScreen — form edit profil user**
  - Acceptance: `ProfileScreen.js` menampilkan form: nama, nomor HP, alamat, pendidikan (dropdown/picker: SMA, D3, S1, S2, S3), skills (multi-input, bisa tambah/hapus skill). Saat mount, GET profil dari backend dan prefill form. Tombol "Simpan" memanggil PUT profil. Success → toast/alert "Profil berhasil disimpan". Error handling jika network gagal. Tombol "Logout" di bagian bawah.
  - Verify: (1) Login sebagai USER, (2) Buka tab Profile → form kosong/prefill dari DB, (3) Isi semua field → Simpan → alert sukses, (4) Kill app → buka lagi → Profile masih berisi data yang disimpan, (5) Cek Prisma Studio → data user updated.
  - Files:
    - `credora-app/src/screens/user/ProfileScreen.js`
    - `credora-app/src/services/userService.js`
    - `credora-app/src/components/FormInput.js`

---

- [ ] **Task 2: [credora-app] HomeScreen — daftar lowongan + JobCard component**
  - Acceptance: `HomeScreen.js` (User) memanggil `GET /jobs` saat mount, menampilkan list lowongan sebagai `JobCard` (posisi, nama perusahaan, lokasi, gaji). Pull-to-refresh untuk reload data. Loading indicator saat fetch. Empty state jika tidak ada lowongan. `JobCard.js` component reusable dengan styling rapi (bukan teks polos).
  - Verify: (1) Backend Docker jalan + ada seed data 5 lowongan, (2) Login sebagai USER → tab Home → 5 JobCard muncul, (3) Pull-to-refresh → data reload, (4) Matikan backend → buka Home → error handling (bukan crash).
  - Files:
    - `credora-app/src/screens/user/HomeScreen.js`
    - `credora-app/src/components/JobCard.js`
    - `credora-app/src/services/jobService.js`

---

- [ ] **Task 3: [credora-app] JobDetailScreen — detail lowongan**
  - Acceptance: Tap `JobCard` di HomeScreen → navigasi ke `JobDetailScreen.js` dengan parameter `jobId`. Screen memanggil `GET /jobs/:id` dan menampilkan: posisi, nama perusahaan, deskripsi lengkap, kisaran gaji, lokasi, skills yang dibutuhkan (sebagai tag/badge), pendidikan minimum, tanggal posting. Tombol "Lamar" di bawah (belum berfungsi, akan diimplementasi sprint berikutnya — tampilkan saja, onPress kosong atau alert "Fitur segera hadir").
  - Verify: (1) Login sebagai USER → Home → tap lowongan pertama, (2) Detail lengkap muncul dengan semua field, (3) Tombol "Lamar" terlihat, (4) Tekan back → kembali ke Home.
  - Files:
    - `credora-app/src/screens/user/JobDetailScreen.js`
    - `credora-app/src/navigation/UserTabs.js` (add stack for detail)
    - `credora-app/src/services/jobService.js`
