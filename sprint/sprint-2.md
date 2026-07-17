# Sprint 2 — Frontend Foundation: Expo + Navigasi Skeleton + Koneksi API

**Fokus**: Setup credora-app dengan Expo, buat navigasi skeleton per role (User, Perusahaan, Admin), dan pastikan app bisa terhubung ke credora-api yang jalan di Docker.

**Demo di akhir sprint**: Buka app di Expo Go → lihat layar login placeholder → pilih role → masuk ke tab navigator sesuai role → layar Home menampilkan data dari endpoint `/health` backend (bukti koneksi sukses).

---

- [ ] **Task 1: [credora-app] Inisialisasi Expo project + install dependensi inti**
  - Acceptance: Folder `credora-app/` berisi project Expo yang bisa dijalankan dengan `npx expo start`. Dependensi navigasi (`@react-navigation/native`, `@react-navigation/stack`, `@react-navigation/bottom-tabs`, `react-native-screens`, `react-native-safe-area-context`) dan `axios`, `expo-secure-store` sudah terinstall. Struktur folder `src/screens/`, `src/navigation/`, `src/services/`, `src/components/`, `src/context/`, `src/utils/`, `src/constants/` sudah dibuat (boleh kosong atau berisi placeholder).
  - Verify: `cd credora-app && npx expo start` — QR code muncul, scan di Expo Go tampil layar default tanpa error.
  - Files:
    - `credora-app/package.json`
    - `credora-app/App.js`
    - `credora-app/app.json`
    - `credora-app/src/` (folder structure)

---

- [ ] **Task 2: [credora-app] Navigasi skeleton per role (stack + bottom tabs)**
  - Acceptance: `RootNavigator.js` mengatur stack awal (Login → RoleSelect → Tab per role). `UserTabs.js` punya tab: Home, Status, Profile. `CompanyTabs.js` punya tab: Home, Post Job, Profile. `AdminTabs.js` punya tab: Jobs, Accounts. Setiap tab mengarah ke screen placeholder (teks nama screen di tengah layar). Navigasi bisa berpindah antar screen tanpa crash.
  - Verify: `npx expo start` → di Expo Go, tekan tombol "User" di RoleSelect → masuk UserTabs → bisa pindah antar tab Home/Status/Profile. Kembali, pilih "Company" → masuk CompanyTabs. Pilih "Admin" → masuk AdminTabs.
  - Files:
    - `credora-app/src/navigation/RootNavigator.js`
    - `credora-app/src/navigation/UserTabs.js`
    - `credora-app/src/navigation/CompanyTabs.js`
    - `credora-app/src/navigation/AdminTabs.js`
    - `credora-app/App.js`

---

- [ ] **Task 3: [credora-app] Axios instance + koneksi ke backend Docker**
  - Acceptance: File `src/services/api.js` berisi axios instance dengan `baseURL` yang mengarah ke `http://<IP_LOKAL>:4000` (bukan `localhost`, karena device/emulator tidak mengenali `localhost` host). Layar `HomeScreen` (User) memanggil `GET /health` saat mount dan menampilkan respons (`status` dan `db`) di layar. Ada catatan/komentar di kode tentang cara mengganti IP saat pindah jaringan.
  - Verify: (1) Pastikan `docker compose up` di credora-api sudah jalan, (2) `npx expo start` di credora-app, (3) buka Expo Go → pilih User → tab Home → layar menampilkan `"API status: ok, DB: connected"`.
  - Files:
    - `credora-app/src/services/api.js`
    - `credora-app/src/screens/user/HomeScreen.js`
    - `credora-app/src/constants/colors.js`
