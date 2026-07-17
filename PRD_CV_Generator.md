# PRD: Aplikasi Credora (Job Matching Platform)

## 1. Tujuan (Objective)

**Apa yang dibangun**: Aplikasi mobile Android bernama "Credora" dengan 3 role (User, Perusahaan, Admin), terdiri dari **dua project terpisah**: aplikasi mobile (React Native + Expo) dan backend API (Express + PostgreSQL, dijalankan lewat **Docker**). User menjelajah daftar lowongan, mengisi CV yang otomatis dihasilkan dalam **format ATS-compliant** dan tervalidasi sesuai spesifikasi lowongan, lalu mengirimkannya ke perusahaan. Perusahaan memposting lowongan dan mengelola pelamar. Admin memantau lowongan dan mengelola akun.

**Untuk siapa**: Mahasiswa tingkat akhir dan pencari kerja pemula (role User) sebagai pengguna utama sekaligus responden pengujian usability.

**Kenapa sekarang**: Kebutuhan skripsi (Rekayasa Perangkat Lunak) dengan syarat wajib 3 role dari dosen pembimbing, dievaluasi lewat System Usability Scale (SUS) khusus pada role User. Database menggunakan PostgreSQL, backend dikontainerisasi dengan Docker, dan CV yang dihasilkan wajib mengikuti format ATS-friendly.

**Definisi sukses**:
- Ketiga role berfungsi dalam satu aplikasi Android yang bisa dijalankan lewat Expo Go, terhubung ke backend API yang live (hosted via Docker container)
- CV yang dihasilkan aplikasi lolos kriteria format ATS-compliant (lihat Bagian 3)
- Seluruh skenario Black Box Testing berstatus valid/sesuai spesifikasi
- Skor SUS dari pengujian role User mencapai kategori *Acceptable* (grade B ke atas)

## 2. Arsitektur & Tech Stack

Proyek ini terdiri dari **dua repository terpisah**:

### A. `credora-app/` (Frontend — Mobile)
- React Native + Expo (managed workflow), JavaScript
- Navigasi: React Navigation (`@react-navigation/native`, stack + bottom tabs)
- HTTP client: `axios` untuk memanggil backend API
- PDF Export: `expo-print` + `expo-sharing` (mengikuti aturan format ATS di Bagian 3)
- Penyimpanan token login: `expo-secure-store`

### B. `credora-api/` (Backend — REST API, dikontainerisasi)
- Node.js + Express
- Database: **PostgreSQL**
  - **Development lokal**: dijalankan lewat **Docker Compose** (container `postgres`), tidak perlu install Postgres manual di laptop
  - **Production**: PostgreSQL terkelola (Neon/Railway Postgres addon — dipilih saat deploy), tetap diakses dari `credora-api` yang berjalan sebagai container
- ORM: **Prisma** (schema mudah dibaca, auto-generate query, auto-migration)
- Auth: JWT (`jsonwebtoken`) + hash password (`bcrypt`)
- **Containerization**: `Dockerfile` untuk image `credora-api`, `docker-compose.yml` untuk menjalankan API + Postgres sekaligus secara lokal
- Hosting: **Railway** atau **Render** (keduanya mendukung deploy langsung dari `Dockerfile`, free tier)

**Alur komunikasi**: `credora-app` → HTTP request (axios) → `credora-api` (container Docker, Express routes) → Prisma → PostgreSQL.

**Kenapa Docker**: menyeragamkan environment development supaya "jalan di laptop saya" tidak jadi masalah — Postgres dan backend selalu jalan dengan versi dan konfigurasi yang sama, baik di laptop kamu maupun saat di-deploy, tanpa perlu install Postgres/Node versi tertentu secara manual.

## 3. Format CV: ATS-Compliant (WAJIB)

Setiap CV yang dihasilkan aplikasi — baik saat preview di layar maupun saat diekspor ke PDF — **wajib** mengikuti kaidah ATS-friendly berikut. Ini bukan preferensi visual, tapi requirement fungsional karena CV yang tidak sesuai kaidah ini bisa gagal terbaca oleh sistem parsing perusahaan:

- **Satu kolom** (single column) — dilarang layout dua kolom kiri-kanan
- **Tanpa tabel, ikon, grafik, atau bar chart level skill** — semua informasi dalam bentuk teks biasa
- **Font standar** (contoh: Arial, Calibri, Times New Roman), tanpa font dekoratif
- **Heading section baku**: BIODATA, RINGKASAN, PENGALAMAN KERJA, PENDIDIKAN, KEAHLIAN (urutan bisa disesuaikan kebutuhan lowongan, tapi nama section tetap standar)
- **Output PDF berbasis teks**, bukan hasil render gambar/screenshot — supaya tetap bisa di-*copy-paste* dan di-parsing mesin
- Logic penyusunan template CV ATS ini ditempatkan di `credora-app/src/utils/pdfGenerator.js`, terpisah dari komponen UI, supaya aturan format bisa diaudit/ditest sendiri tanpa perlu render layar

## 4. Perintah (Commands)

### Frontend (`credora-app/`)
```bash
# Setup project baru
npx create-expo-app credora-app
cd credora-app

# Menjalankan aplikasi (scan QR dengan Expo Go)
npx expo start

# Install navigasi
npm install @react-navigation/native @react-navigation/stack @react-navigation/bottom-tabs
npx expo install react-native-screens react-native-safe-area-context

# Install HTTP client + penyimpanan token
npm install axios
npx expo install expo-secure-store

# Install fitur ekspor PDF
npx expo install expo-print expo-sharing

# Setup unit testing (opsional, untuk logic non-UI)
npm install --save-dev jest jest-expo @testing-library/react-native
npm test

# Build APK untuk demo sidang (kalau diperlukan)
eas build -p android --profile preview
```

### Backend (`credora-api/`) — via Docker
```bash
# Setup project baru
mkdir credora-api && cd credora-api
npm init -y
npm install express cors dotenv jsonwebtoken bcrypt
npm install prisma --save-dev
npm install @prisma/client
npx prisma init

# Jalankan API + Postgres sekaligus lewat Docker Compose (development)
docker compose up --build

# Jalankan migration di dalam container yang sedang berjalan
docker compose exec api npx prisma migrate dev --name init

# Melihat log container
docker compose logs -f api

# Menghentikan semua container
docker compose down

# Build image production (dipakai saat deploy ke Railway/Render)
docker build -t credora-api .

# Migration di environment production
npx prisma migrate deploy
```

## 5. Struktur Proyek (Project Structure)

```
credora-app/                      (REPO 1 — Frontend)
├── App.js                        → Entry point, root navigator
├── src/
│   ├── screens/
│   │   ├── auth/                 → LoginScreen, RegisterScreen, RoleSelectScreen
│   │   ├── user/                 → HomeScreen, JobDetailScreen, ProfileScreen,
│   │   │                           CVFormScreen, PreviewScreen, StatusScreen
│   │   ├── company/              → CompanyHomeScreen, PostJobScreen,
│   │   │                           ApplicantListScreen, ApplicantDetailScreen
│   │   └── admin/                → AdminJobListScreen, AdminAccountsScreen
│   ├── navigation/                → RootNavigator.js, UserTabs.js, CompanyTabs.js, AdminTabs.js
│   ├── components/                → JobCard.js, FormInput.js, StatusBadge.js, ChecklistItem.js
│   ├── services/                  → api.js (axios instance), authService.js, jobService.js,
│   │                                 applicationService.js
│   ├── context/                   → AuthContext.js, ProfileContext.js
│   ├── utils/                     → validation.js (logic checklist),
│   │                                 pdfGenerator.js (template CV ATS-compliant)
│   └── constants/                 → colors.js, roles.js
├── assets/                        → gambar, font
├── app.json                       → konfigurasi Expo
└── package.json

credora-api/                      (REPO 2 — Backend, dikontainerisasi)
├── src/
│   ├── index.js                  → Entry point Express, setup middleware
│   ├── routes/                   → auth.routes.js, jobs.routes.js, applications.routes.js,
│   │                                 users.routes.js
│   ├── controllers/               → auth.controller.js, jobs.controller.js,
│   │                                 applications.controller.js
│   ├── middleware/                 → authMiddleware.js (verifikasi JWT), roleMiddleware.js
│   └── utils/                      → validation.js (checklist kesesuaian, dipakai controller)
├── prisma/
│   ├── schema.prisma              → Definisi tabel: User, Job, Application
│   └── migrations/                → Auto-generated oleh Prisma
├── Dockerfile                      → Image untuk credora-api
├── docker-compose.yml              → Menjalankan service `api` + `postgres` sekaligus (dev)
├── .dockerignore
├── .env                            → DATABASE_URL, JWT_SECRET (JANGAN di-commit)
├── .gitignore
└── package.json
```

**Aturan penempatan**:
- Frontend tidak boleh memanggil Postgres/Prisma langsung — semua akses data lewat HTTP request ke backend (`src/services/`).
- Backend tidak boleh menaruh query Prisma langsung di file route — query harus lewat `controllers/`, supaya route tetap ringkas dan logic mudah ditest terpisah.
- Semua environment variable backend (`DATABASE_URL`, `JWT_SECRET`, `PORT`) didefinisikan di `.env` dan dirujuk lewat `docker-compose.yml`, tidak pernah di-hardcode di `Dockerfile`.

## 6. Gaya Penulisan Kode (Code Style)

**Umum (kedua repo)**: JavaScript, camelCase untuk variabel/fungsi, PascalCase untuk komponen/class, UPPER_SNAKE_CASE untuk konstanta, semua operasi async dibungkus `try/catch`.

**Frontend** — function component + hooks, satu komponen per file, style pakai `StyleSheet.create` di akhir file.

Contoh (`src/services/jobService.js`):
```javascript
import api from "./api";

export async function getAllJobs() {
  try {
    const response = await api.get("/jobs");
    return response.data;
  } catch (error) {
    console.error("Gagal mengambil daftar lowongan:", error);
    return [];
  }
}
```

**Backend** — satu resource = satu file route + satu file controller. Route hanya mendefinisikan path dan memanggil controller, tidak berisi logic.

Contoh (`prisma/schema.prisma` — potongan):
```prisma
model Job {
  id             String   @id @default(uuid())
  companyId      String
  position       String
  salaryRange    String
  requiredSkills String[]
  minEducation   String
  createdAt      DateTime @default(now())
  company        User     @relation(fields: [companyId], references: [id])
  applications   Application[]
}
```

Contoh (`Dockerfile` — credora-api):
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npx prisma generate
EXPOSE 4000
CMD ["node", "src/index.js"]
```

Contoh (`docker-compose.yml`):
```yaml
services:
  api:
    build: .
    ports:
      - "4000:4000"
    env_file: .env
    depends_on:
      - postgres
  postgres:
    image: postgres:16-alpine
    restart: always
    environment:
      POSTGRES_USER: credora
      POSTGRES_PASSWORD: credora
      POSTGRES_DB: credora_db
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

## 7. Strategi Pengujian (Testing Strategy)

Mengikuti Bab 3 skripsi, dengan tambahan lapis untuk backend dan format CV:

1. **Black Box Testing (utama)** — manual, dicatat dalam tabel skenario uji per fitur (login, upload lowongan, isi form CV, validasi checklist, preview, send, kelola pelamar, takedown lowongan). Kolom: input, ekspektasi, hasil aktual, status (valid/tidak valid).
2. **Validasi format ATS** — skenario uji khusus untuk memastikan PDF hasil ekspor memenuhi kaidah Bagian 3 (satu kolom, heading baku, teks bisa di-*copy-paste*, tanpa elemen grafis).
3. **Testing endpoint backend (opsional, ringan)** — pakai `supertest` untuk memastikan endpoint utama (`POST /auth/login`, `GET /jobs`, `POST /applications`) mengembalikan status code dan struktur response yang benar, dijalankan di dalam container Docker yang sama seperti production.
4. **Unit test (opsional, ringan)** — khusus fungsi murni di `utils/validation.js` (logic checklist kesesuaian skill/pendidikan), baik di sisi frontend maupun backend.
5. **Usability Testing (SUS)** — kuesioner 10 pernyataan ke responden role User setelah mencoba alur lengkap: pilih lowongan → isi CV → send.

Tidak menggunakan automated E2E testing (Detox, Appium, dll) — kompleksitas setup tidak sepadan dengan manfaat untuk skala skripsi solo.

## 8. Batasan (Boundaries)

**Selalu dilakukan:**
- Frontend memanggil data hanya lewat `src/services/` (axios ke backend), tidak pernah langsung ke database
- Backend memvalidasi input di controller sebelum query ke Prisma
- Backend dijalankan dan ditest lewat Docker (`docker compose up`), bukan `node` langsung di host, supaya environment selalu konsisten
- Template CV di `pdfGenerator.js` selalu diperiksa terhadap kaidah ATS di Bagian 3 sebelum dianggap selesai
- Simpan `DATABASE_URL` dan `JWT_SECRET` di `.env`, tidak pernah hardcode di kode maupun `Dockerfile`
- Ikuti struktur folder yang sudah disepakati di kedua repo
- Commit kode dalam potongan kecil dan sering, bukan sekali besar di akhir

**Tanya dulu sebelum lanjut:**
- Mengganti tech stack inti (misal pindah dari Postgres ke database lain, atau dari Express ke framework lain)
- Menambah dependency besar baru (state management library, dll)
- Mengubah schema Prisma yang sudah dipakai fitur lain (butuh migration baru, bisa berdampak ke data yang sudah ada)
- Mengubah struktur `Dockerfile`/`docker-compose.yml` yang memengaruhi cara deploy

**Jangan pernah:**
- Commit file `.env` (berisi `DATABASE_URL`, `JWT_SECRET`) ke repository publik — wajib ada di `.gitignore` dan `.dockerignore`
- Menghapus data hasil pengujian responden SUS tanpa backup
- Melewati validasi checklist demi mempercepat development
- Menyimpan password user dalam bentuk plain text (wajib di-hash dengan bcrypt)
- Membuat template CV yang melanggar kaidah ATS (dua kolom, ikon, tabel skill, dsb) meskipun secara visual terlihat lebih menarik

## 9. Success Criteria

- [ ] User bisa login, isi profil dasar, browse lowongan, isi CV (auto-prefill), lihat validasi checklist, preview, dan send
- [ ] CV yang dihasilkan memenuhi seluruh kaidah format ATS-compliant di Bagian 3
- [ ] Perusahaan bisa login, posting lowongan, lihat daftar pelamar, update status lamaran
- [ ] Admin bisa memantau lowongan aktif, takedown lowongan, kelola akun
- [ ] Backend API berjalan sebagai container Docker dan ter-deploy sehingga bisa diakses dari internet (bukan cuma localhost), sehingga app bisa dites dari HP fisik
- [ ] `docker compose up` berhasil menjalankan API + Postgres tanpa error di laptop manapun (bukti environment konsisten)
- [ ] Seluruh skenario Black Box Testing berstatus valid
- [ ] Skor SUS role User ≥ 68 (kategori Acceptable, grade B ke atas)
- [ ] Aplikasi mobile bisa dijalankan penuh lewat Expo Go tanpa custom native build

## 10. Open Questions

- Berapa lama sisa waktu sampai sidang? (menentukan realistis tidaknya fitur Profil Dasar reusable masuk MVP awal atau menyusul, mengingat sekarang ada 2 repo + Docker yang harus disiapkan)
- Apakah build APK final wajib untuk sidang, atau demo lewat Expo Go (yang terhubung ke backend live) sudah cukup?
- Berapa jumlah minimum responden SUS yang disyaratkan dosen pembimbing?
- Backend akan di-deploy ke Railway atau Render? (memengaruhi konfigurasi Dockerfile dan environment variable saat deploy)
- Apakah Docker Desktop sudah terpasang dan bisa jalan lancar di laptop kamu (Axioo Hype 5, RAM 16GB)? Docker cukup ringan untuk 1 container Postgres + 1 container Node, tapi worth dicek dulu sebelum sprint backend dimulai.
