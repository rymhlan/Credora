# Sprint 11 — Ekspor PDF CV (ATS-Compliant)

**Fokus**: Implementasi fitur ekspor CV ke PDF dengan format ATS-compliant sesuai kaidah PRD Bagian 3, menggunakan `expo-print` + `expo-sharing`.

**Demo di akhir sprint**: User buka detail lamaran → tap "Unduh PDF" → PDF ter-generate dan bisa di-share/simpan. PDF mengikuti format ATS: satu kolom, heading baku, tanpa tabel/ikon, teks bisa di-select.

---

- [ ] **Task 1: [credora-app] pdfGenerator.js — template HTML CV ATS-compliant**
  - Acceptance: File `src/utils/pdfGenerator.js` berisi fungsi `generateCVHTML(cvData)` yang menerima object CV dan mengembalikan string HTML template ATS-compliant. Kaidah yang WAJIB dipenuhi (PRD Bagian 3): (1) Satu kolom (single column, tanpa float/flex dua kolom), (2) Tanpa tabel, ikon, grafik, atau bar chart level skill, (3) Font standar (Arial/Calibri/Times New Roman), (4) Heading section baku: BIODATA, RINGKASAN, PENGALAMAN KERJA, PENDIDIKAN, KEAHLIAN, (5) Teks biasa semua — informasi tidak dikemas dalam elemen visual. Fungsi ini murni (pure function), tidak bergantung pada komponen UI, bisa di-test secara terpisah.
  - Verify: (1) Panggil `generateCVHTML(sampleData)` → hasilnya string HTML, (2) HTML tidak mengandung `<table>`, `<img>`, `<svg>`, `<i class="icon">` atau sejenisnya, (3) HTML mengandung heading `BIODATA`, `RINGKASAN`, `PENGALAMAN KERJA`, `PENDIDIKAN`, `KEAHLIAN`, (4) Tidak ada CSS `float`, `display: flex` untuk layout dua kolom, (5) Font-family merujuk ke Arial/Calibri/sans-serif.
  - Files:
    - `credora-app/src/utils/pdfGenerator.js`

---

- [ ] **Task 2: [credora-app] Fitur ekspor + share PDF dari detail lamaran**
  - Acceptance: Di `StatusScreen` → tap lamaran → detail lamaran → tombol "Unduh CV (PDF)". Tombol memanggil `expo-print` dengan HTML dari `generateCVHTML()` → menghasilkan file PDF → `expo-sharing` membuka share sheet (bisa simpan ke file, kirim via WhatsApp, dll). Loading indicator saat generate. Error handling jika gagal. PDF juga bisa di-generate dari `PreviewScreen` sebelum submit lamaran.
  - Verify: (1) Buka detail lamaran → tap "Unduh CV (PDF)" → share sheet muncul, (2) Simpan PDF → buka di PDF reader → teks bisa di-select/copy (bukan gambar), (3) Layout satu kolom di PDF reader, (4) Coba dari PreviewScreen (sebelum submit) → PDF juga berhasil, (5) Airplane mode → error handling (bukan crash — PDF harusnya tetap bisa karena lokal).
  - Files:
    - `credora-app/src/screens/user/StatusScreen.js` (add PDF button)
    - `credora-app/src/screens/user/PreviewScreen.js` (add PDF button)
    - `credora-app/src/utils/pdfGenerator.js` (import/use)

---

- [ ] **Task 3: [credora-app] Verifikasi format ATS — audit checklist terpisah**
  - Acceptance: Buat file `sprint/ats-verification-checklist.md` berisi tabel audit dengan kolom: Kaidah ATS, Status (✓/✗), Bukti. Setiap kaidah dari PRD Bagian 3 dicek satu per satu terhadap PDF yang dihasilkan: (1) Satu kolom — bukan dua kolom → bukti: screenshot PDF, (2) Tanpa tabel — tidak ada `<table>` di HTML dan tidak ada grid visual di PDF, (3) Tanpa ikon/grafik — tidak ada `<img>`, `<svg>`, bar chart, (4) Font standar — cek di PDF properties atau HTML source, (5) Heading baku — BIODATA, RINGKASAN, PENGALAMAN KERJA, PENDIDIKAN, KEAHLIAN ada di PDF, (6) Teks bisa di-copy — select teks di PDF reader → paste ke notepad → teks muncul (bukan garbled), (7) Output PDF berbasis teks — bukan screenshot/gambar.
  - Verify: Semua 7 baris checklist di `ats-verification-checklist.md` berstatus ✓. Jika ada ✗, task ini belum selesai — perbaiki `pdfGenerator.js` sampai semua ✓.
  - Files:
    - `sprint/ats-verification-checklist.md`
    - `credora-app/src/utils/pdfGenerator.js` (perbaikan jika perlu)
