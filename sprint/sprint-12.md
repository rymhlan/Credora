# Sprint 12 — Polish UI + Konsistensi Visual

**Fokus**: Merapikan tampilan seluruh app — warna konsisten, typography, spacing, loading states, error states, empty states. App harus terasa polished, bukan prototype.

**Demo di akhir sprint**: Semua layar terlihat konsisten (warna, font, spacing). Setiap layar punya loading state, error state, dan empty state yang rapi. Tidak ada teks placeholder "Lorem ipsum" atau styling default React Native yang tersisa.

---

- [ ] **Task 1: [credora-app] Design system — colors, typography, spacing constants**
  - Acceptance: `src/constants/colors.js` berisi palet warna lengkap (primary, secondary, success, warning, danger, background, text, border, card — light mode). `src/constants/typography.js` berisi ukuran font (heading, subheading, body, caption) dan font weight. `src/constants/spacing.js` berisi padding/margin standar (xs, sm, md, lg, xl). Semua screen dan component diupdate untuk menggunakan constants ini — tidak ada hardcoded color/size di screen files.
  - Verify: (1) Grep codebase: tidak ada hardcoded hex color di file screen (kecuali di constants), (2) Ganti primary color di `colors.js` → semua screen berubah warna, (3) Visual: semua layar punya warna yang harmonis dan konsisten.
  - Files:
    - `credora-app/src/constants/colors.js`
    - `credora-app/src/constants/typography.js`
    - `credora-app/src/constants/spacing.js`
    - `credora-app/src/constants/roles.js`

---

- [ ] **Task 2: [credora-app] Polish screen User flow (Home → Detail → CV → Preview → Status)**
  - Acceptance: Semua screen alur User dipoles: (1) HomeScreen — JobCard dengan shadow, border-radius, spacing rapi, (2) JobDetailScreen — layout bersih, skills sebagai pill/chip, tombol CTA menonjol, (3) CVFormScreen — section divider, label jelas, spacing antar input konsisten, (4) PreviewScreen — layout dokumen formal (border, padding, background putih), (5) StatusScreen — card lamaran rapi, badge berwarna. Loading spinner saat fetch. Pull-to-refresh visual feedback. Error state: ikon + teks + tombol retry. Empty state: ikon + teks informatif.
  - Verify: (1) Walkthrough manual seluruh alur User dari Login → Home → Detail → CV → Preview → Submit → Status, (2) Setiap screen punya loading → content/error/empty, (3) Tidak ada teks/styling placeholder, (4) Smooth transitions antar screen.
  - Files:
    - `credora-app/src/screens/user/HomeScreen.js`
    - `credora-app/src/screens/user/JobDetailScreen.js`
    - `credora-app/src/screens/user/CVFormScreen.js`
    - `credora-app/src/screens/user/PreviewScreen.js`
    - `credora-app/src/screens/user/StatusScreen.js`

---

- [ ] **Task 3: [credora-app] Polish screen Company + Admin + Auth**
  - Acceptance: Layar-layar berikut dipoles dengan standar visual yang sama dengan User flow: (1) LoginScreen + RegisterScreen — branding Credora, layout center, input styled, (2) CompanyHomeScreen + PostJobScreen + ApplicantList/Detail — card konsisten, form rapi, (3) AdminJobListScreen + AdminAccountsScreen — filter tab styled, badge konsisten. Semua screen punya loading/error/empty state.
  - Verify: (1) Login sebagai setiap role → semua screen terlihat polished dan konsisten, (2) Tidak ada screen yang masih pakai styling default/placeholder, (3) Navigasi smooth tanpa glitch visual.
  - Files:
    - `credora-app/src/screens/auth/LoginScreen.js`
    - `credora-app/src/screens/auth/RegisterScreen.js`
    - `credora-app/src/screens/company/` (all screens)
    - `credora-app/src/screens/admin/` (all screens)
