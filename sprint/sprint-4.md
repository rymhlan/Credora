# Sprint 4 — Auth Frontend: Login + Register + AuthContext

**Fokus**: User bisa register dan login dari app, token disimpan di SecureStore, navigasi otomatis ke tab sesuai role.

**Demo di akhir sprint**: Buka Expo Go → Register akun baru (pilih role User) → otomatis masuk ke UserTabs. Tutup app → buka lagi → masih login (token persisted). Logout → kembali ke layar Login.

---

- [ ] **Task 1: [credora-app] AuthContext + SecureStore token management**
  - Acceptance: `AuthContext.js` menyediakan state `user`, `token`, `isLoading` dan fungsi `login(email, password)`, `register(email, password, name, role)`, `logout()`. Token disimpan/dihapus dari `expo-secure-store`. Saat app start, cek token di SecureStore → jika ada, panggil `GET /auth/me` untuk validasi → jika valid, set user + token. `authService.js` berisi fungsi yang memanggil endpoint `/auth/register`, `/auth/login`, `/auth/me` via axios.
  - Verify: Import AuthContext di App.js, pastikan tidak ada error saat render. Console.log state `user` saat mount — harusnya `null` (belum login).
  - Files:
    - `credora-app/src/context/AuthContext.js`
    - `credora-app/src/services/authService.js`
    - `credora-app/App.js`

---

- [ ] **Task 2: [credora-app] LoginScreen + RegisterScreen UI + integrasi auth**
  - Acceptance: `LoginScreen` punya input email, password, tombol Login, link ke Register. `RegisterScreen` punya input name, email, password, dropdown/selector role (User/Perusahaan), tombol Register. Keduanya memanggil fungsi dari AuthContext. Validasi dasar di frontend (email tidak kosong, password minimal 6 karakter). Tampilkan pesan error dari backend (misal "Email sudah terdaftar"). Loading indicator saat proses auth.
  - Verify: (1) `npx expo start` + backend Docker jalan, (2) Buka app → layar Login muncul, (3) Register user baru → sukses → masuk ke tab sesuai role, (4) Logout → Login ulang → sukses, (5) Coba login password salah → error message muncul.
  - Files:
    - `credora-app/src/screens/auth/LoginScreen.js`
    - `credora-app/src/screens/auth/RegisterScreen.js`
    - `credora-app/src/components/FormInput.js`

---

- [ ] **Task 3: [credora-app] Navigasi conditional berdasarkan auth state + role**
  - Acceptance: `RootNavigator.js` diubah: jika `isLoading` → SplashScreen/loading. Jika `user == null` → tampilkan AuthStack (Login, Register). Jika `user.role == 'USER'` → UserTabs. Jika `user.role == 'COMPANY'` → CompanyTabs. Jika `user.role == 'ADMIN'` → AdminTabs. Tidak ada tombol "back" dari tab ke login (user harus logout eksplisit). Logout menghapus token dan kembali ke LoginScreen.
  - Verify: (1) Fresh app → Login screen, (2) Login sebagai USER → UserTabs (tidak bisa back ke Login), (3) Logout → Login screen, (4) Login sebagai COMPANY → CompanyTabs, (5) Kill app → buka lagi → auto-login ke role terakhir.
  - Files:
    - `credora-app/src/navigation/RootNavigator.js`
    - `credora-app/App.js`
    - `credora-app/src/screens/auth/` (minor adjustments)
