# Dokumentasi Teknis & Panduan Pengembangan: Aplikasi OtoShield (Direktori Bengkel Transparan & Layanan Darurat)

Dokumen ini berisi cetak biru (*blueprint*) dan spesifikasi teknis lengkap untuk membangun aplikasi Android **OtoShield**, yang dirancang khusus untuk mengatasi masalah utama pengendara di Indonesia: kesulitan mencari bengkel terpercaya, ketakutan terkena getok harga, dan kebutuhan layanan darurat di jalan.

---

## 1. Analisis Kebutuhan Pengguna & *Pain Points* (Berdasarkan Data Medsos)
Berdasarkan hasil analisis keluhan dari platform digital (YouTube, X, TikTok), masalah utama yang dialami pengguna otomotif meliputi:
* **Krisis Kepercayaan:** Biaya perbaikan sering membengkak tanpa konfirmasi transparan dari bengkel.
* **Kepanikan Saat Darurat:** Kendaraan mogok di jalan tol atau malam hari membuat pengendara bingung mencari bantuan mekanik atau derek terpercaya.
* **Kurangnya Edukasi Perawatan:** Pengguna sering terlambat melakukan servis berkala karena tidak ada pengingat otomatis.

---

## 2. Fitur Utama & Spesifikasi Fungsional

### A. Fitur Utama (Core Features)
1. **SOS Emergency Roadside Assistance (Darurat 24/7):**
   * Tombol panik untuk memanggil layanan derek (*towing*) atau mekanik panggilan terdekat.
   * Pelacakan lokasi *real-time* berbasis GPS (Mirip sistem *ride-hailing*).
2. **Direktori Bengkel Terverifikasi & Transparan:**
   * Daftar bengkel rekanan dengan sistem rating jujur dari komunitas.
   * Transparansi daftar harga jasa (*labor fee*) dan kisaran suku cadang sebelum kendaraan diservis.
3. **Smart Cost Estimator (Estimator Biaya Cerdas):**
   * Fitur input gejala kerusakan (misal: "Rem bunyi berdecit", "Mesin getar saat AC nyala") untuk memunculkan kisaran estimasi biaya wajar di pasaran.
4. **Digital Service Book (Buku Servis Digital):**
   * Pengingat otomatis jadwal ganti oli, aki, dan kampas rem berdasarkan odometer/kilometer kendaraan.

---

## 3. Arsitektur Sistem & *Tech Stack*

### A. Frontend (Aplikasi Android)
* **Bahasa Pemrograman:** Kotlin
* **UI Toolkit:** Jetpack Compose (Modern declarative UI)
* **Arsitektur:** MVVM (Model-View-ViewModel) dengan Clean Architecture
* **Library Pendukung:** 
  * `Retrofit` / `Ktor` untuk komunikasi jaringan API.
  * `Google Maps SDK & Location Services` untuk peta dan pelacakan GPS.
  * `Room Database` untuk penyimpanan lokal (cache riwayat servis).

### B. Backend & Database
* **Server Framework:** Node.js (Express) atau Go (Golang) untuk performa cepat menangani *real-time request*.
* **Database Utama:** PostgreSQL (Relational database untuk data pengguna, bengkel, transaksi, dan riwayat).
* **Real-time Engine:** Firebase Realtime Database atau WebSockets (untuk fitur SOS & pelacakan posisi mekanik).
* **Cloud Storage:** Firebase Storage / AWS S3 (untuk menyimpan foto kerusakan kendaraan dan dokumen verifikasi bengkel).

---

## 4. Perancangan Skema Database (PostgreSQL / Relational)

Berikut adalah struktur tabel utama dalam database:

### Tabel `users`
* `id` (UUID, Primary Key)
* `name` (VARCHAR)
* `phone` (VARCHAR)
* `email` (VARCHAR)
* `created_at` (TIMESTAMP)

### Tabel `vehicles`
* `id` (UUID, Primary Key)
* `user_id` (UUID, Foreign Key ke `users`)
* `brand` (VARCHAR - e.g., Toyota, Honda)
* `model` (VARCHAR)
* `year` (INT)
* `license_plate` (VARCHAR)
* `current_kilometer` (INT)

### Tabel `workshops` (Bengkel)
* `id` (UUID, Primary Key)
* `name` (VARCHAR)
* `address` (TEXT)
* `latitude` (DECIMAL)
* `longitude` (DECIMAL)
* `phone` (VARCHAR)
* `is_verified` (BOOLEAN)
* `rating` (FLOAT)

### Tabel `emergency_requests` (SOS)
* `id` (UUID, Primary Key)
* `user_id` (UUID, Foreign Key)
* `workshop_id` (UUID, Foreign Key, nullable)
* `status` (ENUM: `PENDING`, `ACCEPTED`, `ON_THE_WAY`, `COMPLETED`, `CANCELLED`)
* `issue_description` (TEXT)
* `latitude` (DECIMAL)
* `longitude` (DECIMAL)
* `created_at` (TIMESTAMP)

---

## 5. Alur Pengguna (*User Flow*) Utama: Fitur SOS Darurat

1. Pengguna membuka aplikasi saat mobil/motor mengalami masalah di jalan.
2. Pengguna menekan tombol merah **"SOS Darurat"** di halaman utama.
3. Aplikasi mendeteksi lokasi GPS secara otomatis dan meminta pengguna mengisi deskripsi singkat kerusakan (contoh: "Ban bocor & mesin overheat").
4. Sistem mencarikan mekanik atau unit *towing* terdekat dalam radius 5 km.
5. Mekanik menerima pesanan, dan pengguna dapat melihat estimasi waktu kedatangan (*ETA*) serta perkiraan biaya awal secara transparan.
6. Setelah perbaikan selesai, pembayaran dilakukan secara digital dan pengguna dapat memberikan ulasan.

---

## 6. Rencana Pengembangan (Roadmap & Milestones)

* **Fase 1 (Bulan 1-2):** Desain UI/UX di Figma, perancangan skema database, dan *setup* arsitektur proyek Android (Kotlin).
* **Fase 2 (Bulan 3-4):** Pengembangan fitur Direktori Bengkel & Smart Cost Estimator, serta integrasi Google Maps API.
* **Fase 3 (Bulan 5):** Pengembangan backend untuk fitur SOS *Real-time* dan uji coba terbatas (*Closed Beta Testing*).
* **Fase 4 (Bulan 6):** Peluncuran publik (*Launch*) di Google Play Store dan evaluasi performa aplikasi.

---

## 7. Status Kesiapan Production (per Sesi 9)

> Ringkasan cepat untuk siapa pun yang membuka dokumen ini dan ingin tahu "apakah sudah bisa
> di-launch?" tanpa harus membaca seluruh catatan #memory di Bagian 8. Detail teknis lengkap tiap
> poin ada di Bagian 8, dicari lewat nomor item "BELUM DIKERJAKAN" yang dirujuk di bawah.

**Kesimpulan singkat: BELUM siap production.** Fondasi backend sudah cukup matang untuk tahap
staging (159 test otomatis PASS, termasuk test integrasi lewat Postgres sungguhan, plus
production-hardening dasar — lihat 🟢 di bawah), tapi ada beberapa *blocker* yang menyangkut uang
sungguhan dan aplikasi yang benar-benar dijalankan pengguna. **Tidak ada satu pun dari 5 blocker
lama yang hilang di Sesi 9** — pekerjaan sesi ini memperkuat backend, bukan menghilangkan
ketergantungan pada kredensial/lingkungan di luar sandbox.

### 🔴 Blocker — wajib selesai sebelum launch
1. **Kode Android belum pernah di-compile Gradle sungguhan.** Sandbox pengembangan tidak punya
   Android SDK, jadi 40+ file Kotlin (SOS, payment, upload foto, dll) baru divalidasi lewat baca
   manual + cek keseimbangan kurung `{}/()/[]`, BUKAN compiler asli. **Wajib** dibuka di Android
   Studio, sync Gradle, dan perbaiki error kompilasi jika ada. (Lihat item #1 di Bagian 8.)
2. **Payment gateway masih simulasi, bukan Midtrans sungguhan.** `createTransaction()` di
   `paymentGateway.js` mengembalikan respons berformat identik dengan Midtrans asli, tapi tidak
   pernah memanggil API mereka. Perlu isi `MIDTRANS_SERVER_KEY` asli & sambungkan ke API Midtrans
   sungguhan sebelum ada transaksi uang sungguhan. (Lihat item #3 di Bagian 8.)
3. **Driver cloud storage S3 belum pernah dites melawan bucket AWS/MinIO sungguhan** — baru dites
   dengan `aws-sdk-client-mock` (mock resmi AWS SDK, bukan bucket asli). Wajib divalidasi dengan
   kredensial & akses jaringan AWS sungguhan sebelum dipakai produksi. (Lihat item #4 di Bagian 8.)
4. **Docker & CI belum pernah benar-benar jalan end-to-end.** `docker compose up` (butuh image dari
   Docker Hub) dan `.github/workflows/backend-ci.yml` belum pernah divalidasi di lingkungan
   sungguhan — sandbox pengembangan tidak punya akses ke registry Docker Hub maupun GitHub Actions
   runner. **Update Sesi 9**: ditemukan `docker-compose.yml` sebelumnya menaruh nilai placeholder
   `.env.example` LANGSUNG sebagai `JWT_SECRET`/`ADMIN_API_KEY` sungguhan dengan `NODE_ENV=production`
   — sudah diperbaiki (sekarang wajib diisi lewat `.env` di root, lihat `.env.example` baru & item #2
   di Sesi 9 pada Bagian 8), dan `.github/workflows/backend-ci.yml` yang ternyata **hilang total** dari
   zip proyek (bukan cuma belum dites) sudah ditulis ulang. **Status validasi end-to-end tidak
   berubah**: keduanya tetap belum pernah benar-benar dijalankan di lingkungan sungguhan. (Lihat
   item #5 di Bagian 8.)
5. **`MAPS_API_KEY` masih placeholder** di `local.properties` — perlu diisi API key Google Cloud
   milik sendiri sebelum peta berfungsi di build produksi. (Lihat item #7 lama, SELESAI dari sisi
   kode di Sesi 3, tapi pengisian key aslinya di luar kendali sandbox.)

### 🟡 Perlu perhatian, tidak wajib blocker tapi sebaiknya dibereskan
- Dashboard admin/mekanik masih punya mode darurat `x-admin-key` (shared secret, satu key dipakai
  bersama semua operator) sebagai jalur alternatif dari login staf individual — jalur ini tidak
  tercatat di audit trail "siapa mengubah apa". **Belum disentuh di Sesi 9** (di luar fokus sesi ini).
- Helmet CSP default (`default-src 'self'`) dipasang di Sesi 9 dan sudah dicek KONSISTEN dengan
  dashboard `/mechanic` (tidak ada inline `<script>`/`<style>`, semua request same-origin) lewat
  `curl` — TAPI belum pernah dicek sungguhan di browser asli (DevTools console untuk CSP violation).
  Kemungkinan besar aman, tapi tetap sebaiknya dicek sekali di browser sebelum production.
- `express-rate-limit` pakai in-memory store (Sesi 9) — cukup untuk single-instance deployment, tapi
  kalau backend di-scale ke lebih dari satu instance di belakang load balancer, limit efektif akan
  jadi longgar (tiap instance punya hitungannya sendiri). Upgrade ke store bersama (mis.
  `rate-limit-redis`) baru diperlukan saat itu terjadi — lihat catatan di
  `middleware/rateLimit.middleware.js`.

### 🟢 Sudah solid
- Backend (Node.js/Express/PostgreSQL): **159 test otomatis PASS** (naik dari 137 — 22 test baru
  Sesi 9 murni untuk modul hardening, tidak ada test lama yang diubah), termasuk test integrasi
  sungguhan lewat Postgres asli untuk auth, kendaraan, direktori bengkel, SOS darurat, payment
  (jalur simulasi, termasuk webhook expire/cancel), estimator biaya, dan dashboard admin (ketiga
  role staf: ADMIN, MECHANIC, WORKSHOP_STAFF).
- **Production-hardening dasar (BARU Sesi 9)**: security header (`helmet`), rate limiting
  (`express-rate-limit` — umum semua `/api/*`, ketat khusus login/register, khusus anti-spam SOS),
  validasi environment variable saat boot yang gagal-cepat kalau `JWT_SECRET`/`ADMIN_API_KEY` kosong
  atau masih placeholder di production, CORS bisa dibatasi ke domain spesifik lewat `CORS_ORIGIN`,
  graceful shutdown (`SIGTERM`/`SIGINT` menutup koneksi HTTP + Socket.IO + pool Postgres dengan
  rapi), response compression, request logging (`morgan`). **Semua sudah dites nyata** (bukan cuma
  ditulis) — lihat detail & bukti di catatan Sesi 9 pada Bagian 8, bukan cuma unit test dengan mock.
- Kontrak API (nama field JSON, kode status HTTP, format data) sudah diverifikasi berkali-kali lewat
  `curl` sungguhan melawan server nyata — risiko utama yang tersisa di sisi Android murni soal
  sintaks/tipe Kotlin saat compile, bukan salah asumsi bentuk response backend.
- `npm audit`: 0 kerentanan (dicek ulang di Sesi 9 setelah menambah 4 dependency baru
  helmet/express-rate-limit/morgan/compression — tetap bersih).

---

## 8. #memory — Catatan Progres Pengembangan (oleh Claude)

> Bagian ini ditambahkan otomatis sebagai "memori" agar sesi pengerjaan berikutnya bisa
> langsung melanjutkan tanpa mengulang dari nol. File project lengkap ada di
> `otoshield-project.zip` (diperbarui terakhir di Sesi 9) dengan struktur:
> `otoshield/backend/` (Node.js+Express+PostgreSQL), `otoshield/android-app/` (Kotlin+Compose),
> `otoshield/docker-compose.yml`, dan `otoshield/.github/workflows/`.
> **Catatan zip:** `backend/node_modules/` SENGAJA tidak disertakan di zip (58MB, gampang
> dibuat ulang dengan `npm install`) — jalankan `npm install` dulu di `backend/` sebelum
> `npm test` atau boot server.

### Status: SUDAH SELESAI (Sesi 1)

**Backend (Node.js/Express/PostgreSQL) — fungsional, lolos `npm install` & syntax check:**
- `backend/src/db/schema.sql` — skema lengkap: users, vehicles, workshops, workshop_services, reviews, emergency_requests, cost_estimate_rules, service_history
- `backend/src/db/seed.sql` — data contoh 4 bengkel + 10 aturan kata kunci estimator
- Endpoint auth (register/login+JWT bcrypt), vehicles (CRUD, odometer, reminders servis, riwayat servis), workshops (list+radius Haversine, detail+harga transparan, review), emergency SOS (create+cari towing 5km+broadcast Socket.io), estimator biaya (keyword matching di `utils/estimatorRules.js`)
- Realtime: `backend/src/sockets/emergency.socket.js` (room per emergency ID, event lokasi mekanik)

**Android App (Kotlin+Jetpack Compose, MVVM+Clean Architecture) — 40 file Kotlin:**
- Layar selesai: **LoginScreen+RegisterScreen**, HomeScreen (+tombol logout), SosScreen+SosTrackingScreen
  (peta Google Maps **realtime via Socket.IO**, bukan polling lagi), WorkshopListScreen+WorkshopDetailScreen
  (harga transparan), CostEstimatorScreen, ServiceBookScreen (cache offline via Room + dialog tambah kendaraan)
- Data layer selesai: Retrofit ApiService+DTO lengkap, Room (AppDatabase, VehicleDao, ServiceHistoryDao),
  Repository pattern (Workshop/Estimator/Emergency/Vehicle), ServiceLocator manual DI, SessionManager (JWT),
  LocationHelper (FusedLocationProvider), AuthViewModel (login/register/logout), SocketManager
  (util/SocketManager.kt — wrapper Socket.IO client, event `emergency:updated` & `emergency:mechanic_location`)
- Alur navigasi: `NavGraph` cek `sessionManager.isLoggedIn()` untuk start destination
- `SosTrackingViewModel`: 1x fetch awal via REST → connect Socket.IO → dengarkan event realtime →
  polling 15 detik sebagai jaring pengaman

### Status: SUDAH SELESAI (Sesi 2 — sesi ini)

Fokus sesi ini: backend saja (item Android tetap butuh Android Studio sungguhan — lihat di bawah).
Semua yang dikerjakan di sesi ini **benar-benar dites jalan** di sandbox (bukan cuma ditulis):
`npm install` sukses, `node --check` semua file `.js` lolos, **18 unit test Jest PASS**, dan server
benar-benar di-boot + di-`curl` untuk verifikasi endpoint baru (bukan cuma baca kode).

1. **Upload foto kerusakan kendaraan** (Bagian 3.B blueprint) — SEKARANG ADA:
   - Kolom `photo_url` di `emergency_requests` (migrasi aditif: `ALTER TABLE ... ADD COLUMN IF NOT EXISTS`)
   - `backend/src/middleware/upload.middleware.js` (multer, simpan ke disk lokal `backend/uploads/emergency/`,
     validasi mimetype JPEG/PNG/WEBP, limit 5MB)
   - Endpoint baru: `POST /api/emergency/:id/photo` (multipart/form-data, field `photo`)
   - Disajikan statis lewat `/uploads` (lihat `server.js`)
   - **CATATAN JUJUR**: ini disk lokal, BUKAN Firebase Storage/S3 sungguhan seperti disebut blueprint
     Bagian 3.B, karena sandbox tidak punya kredensial cloud storage. Komentar di kode menjelaskan
     cara ganti ke S3/Firebase (ganti storage engine multer, controller tidak perlu berubah).
2. **Sistem pembayaran digital** (poin 6 user flow blueprint) — SEKARANG ADA (simulasi):
   - Tabel baru `payments` (user_id, emergency_request_id, workshop_id, amount, method, status, payment_reference, paid_at)
   - `backend/src/controllers/payments.controller.js` + `routes/payments.routes.js`:
     `POST /api/payments`, `GET /api/payments`, `GET /api/payments/:id`, `PUT /api/payments/:id/pay`
   - **CATATAN JUJUR**: `PUT /:id/pay` adalah simulasi/konfirmasi manual, BUKAN integrasi payment
     gateway sungguhan (Midtrans/Xendit dll) — sandbox tidak punya kredensial gateway apa pun.
     Komentar di kode menjelaskan cara ganti ke webhook gateway sungguhan.
3. **Dashboard mekanik/operator** (poin #2 "belum dikerjakan" sesi 1) — SEKARANG ADA:
   - Halaman statis di `/mechanic` (`backend/public/mechanic-dashboard/`: index.html, style.css, app.js —
     vanilla JS, tanpa framework/CDN eksternal, Socket.IO client di-serve otomatis oleh server sendiri)
   - Tema visual: "dispatch/control-room" (asphalt gelap + aksen oranye safety-signage, badge status
     ala rambu, indikator "live" berdenyut hanya saat request API berhasil)
   - Fitur: lihat semua SOS (filter status, auto-refresh 5 detik), ubah status + pilih bengkel bertugas,
     kirim update lokasi mekanik realtime lewat Socket.IO (event `emergency:location:update`)
   - Auth: `backend/src/middleware/admin.middleware.js` — shared secret `ADMIN_API_KEY` di header
     `x-admin-key`. **CATATAN JUJUR**: ini MVP, semua operator berbagi 1 key yang sama, TIDAK ADA
     akun/role per staf/bengkel dan TIDAK ADA audit trail siapa mengubah apa. Komentar di kode
     menjelaskan cara upgrade ke tabel `staff_accounts` + JWT seperti auth.middleware.js.
   - Endpoint pendukung: `GET /api/admin/emergency`, `PUT /api/admin/emergency/:id/status`,
     `GET /api/admin/workshops` (semua di `routes/admin.routes.js`, dilindungi `admin.middleware.js`)
4. **Unit test Jest** (poin #5 "belum dikerjakan" sesi 1, sebagian) — SEKARANG ADA:
   - `npm test` menjalankan 18 test, SEMUA PASS: `haversine.test.js` (4 test), `estimatorRules.test.js`
     (7 test — sekaligus butuh refactor `estimateCost` jadi pakai fungsi murni `matchRules()` yang
     dipisah dari query DB, agar bisa dites tanpa Postgres), `auth.middleware.test.js` (4 test),
     `admin.middleware.test.js` (3 test)
   - Sengaja belum ada test untuk controller yang butuh Postgres asli (butuh test-database/mocking pg
     yang lebih rumit — lihat item "belum dikerjakan" #4 di bawah)
5. **Docker** (poin #5 "belum dikerjakan" sesi 1, sebagian) — file SUDAH ADA tapi BELUM DITES:
   - `backend/Dockerfile`, `backend/.dockerignore`, `otoshield/docker-compose.yml`
     (backend + postgres:16-alpine, healthcheck, named volumes)
   - **CATATAN JUJUR**: sandbox TIDAK punya akses ke Docker Hub (`docker.io` dkk tidak ada di
     whitelist jaringan), jadi `docker build`/`docker compose up` BELUM PERNAH benar-benar dicoba.
     File hanya divalidasi secara manual (mengikuti pola docker-compose yang lazim, konsisten dengan
     `.env.example` & `schema.sql`). **Ini WAJIB dicoba jalan di mesin dengan internet normal
     sebelum dipakai serius** — kemungkinan ada typo/kesalahan kecil yang cuma ketahuan saat build.
6. **CI** (poin #5 "belum dikerjakan" sesi 1, sebagian) — file SUDAH ADA tapi BELUM DITES:
   - `.github/workflows/backend-ci.yml` (npm ci → node --check → npm test → npm audit)
   - **CATATAN JUJUR**: belum pernah jalan di runner GitHub Actions sungguhan (sandbox tidak
     terhubung ke GitHub Actions). YAML sudah divalidasi valid secara sintaks (`python3 -c
     "import yaml; yaml.safe_load(...)"`), dan setiap langkah di dalamnya sudah dicoba manual satu
     per satu di sandbox dan berhasil — tapi belum pernah dicoba sebagai satu kesatuan workflow.
7. **Dependency cleanup**: upgrade `multer` ke v2.x (v1.x punya kerentanan keamanan yang di-flag
   npm), hapus paket `uuid` yang ternyata dipasang tapi tidak pernah di-`require` di manapun
   (Postgres sendiri yang generate UUID lewat `uuid_generate_v4()`) — `npm audit` sekarang
   **0 kerentanan** (sebelumnya 1 moderate).
8. Sudah dicoba: boot server dengan `DATABASE_URL` palsu (server tetap bisa listen karena `pg.Pool`
   baru connect saat ada query — jadi smoke-test boot ini TIDAK memverifikasi query DB beneran
   jalan, hanya bahwa routing/middleware/require semua file tidak crash saat startup), lalu
   `curl` ke `/health`, `/`, `/mechanic/`, `/mechanic/app.js`, `/mechanic/style.css`, dan
   `/api/admin/emergency` (tanpa key & dengan key salah, harus 401) — semua sesuai ekspektasi.

### Status: SUDAH SELESAI (Sesi 3 — sesi ini)

**Temuan penting sesi ini yang mengubah asumsi sesi-sesi sebelumnya:** sandbox sekarang BISA
`apt-get install postgresql` (repo `archive.ubuntu.com`/`security.ubuntu.com` sudah cukup, tidak
butuh Docker Hub) — jadi untuk PERTAMA KALINYA seluruh alur backend dites melawan **Postgres
sungguhan**, bukan cuma `node --check` atau mock. Juga ditemukan `apt-get install docker.io`
BERHASIL dan `dockerd` BISA jalan (`docker info` sukses) — tapi `docker pull` tetap gagal 403
karena `registry-1.docker.io` tidak ada di whitelist jaringan, jadi `docker build` dari Dockerfile
manapun yang punya `FROM <image dari Docker Hub>` **masih belum bisa** divalidasi end-to-end.
Rekomendasi untuk sesi berikutnya: **selalu `apt-get install postgresql` di awal sesi** dan jalankan
migrasi+seed+curl sungguhan, jangan cuma andalkan mock lagi.

1. **Sistem login/role staf bengkel & mekanik** (item "belum dikerjakan" #2) — SEKARANG ADA & TERUJI PENUH:
   - Tabel `staff_accounts` (`workshop_id`, `role` ENUM ADMIN/WORKSHOP_STAFF/MECHANIC, `password_hash`)
   - `staff.controller.js`+`staff.routes.js`: `POST /api/staff/login`, `POST /api/staff`, `GET /api/staff`
   - `staff.middleware.js` (JWT staf, claim `type: 'staff'`) + `requireRole(...)`
   - `adminOrStaff.middleware.js`: hybrid, menerima `x-admin-key` LAMA *atau* token staf baru — dashboard
     `/mechanic` tetap kompatibel sambil menambah audit "siapa mengubah apa" untuk staf yang sudah login.
   - `auth.middleware.js` (user biasa) diperketat dengan claim `type: 'user'` agar token staf tidak bisa
     dipakai di endpoint user & sebaliknya — token lama tanpa claim `type` tetap diterima (kompatibilitas mundur).
   - **Dites end-to-end dengan Postgres sungguhan** lewat `curl`: bootstrap akun ADMIN pertama pakai
     `x-admin-key` lama → login staf → staf ADMIN buat staf MECHANIC baru → staf MECHANIC dites TIDAK
     bisa buat staf baru (403) → token user biasa dites TIDAK bisa akses dashboard admin (401) → token
     staf dites TIDAK bisa akses endpoint user (403). Semua sesuai ekspektasi.
   - Dashboard `/mechanic` (`index.html`+`app.js`+`style.css`) diupdate: form login staf (phone+password)
     jadi cara utama, admin key lama dipindah ke `<details>` "mode darurat" yang collapsed by default.
2. **Profil pengguna** (bagian dari item #8) — SEKARANG ADA & TERUJI PENUH:
   - `GET/PUT /api/auth/me` di `auth.controller.js`/`auth.routes.js`, protected `auth.middleware.js`
   - Dites end-to-end dengan Postgres sungguhan: register → get profile → update email → sukses.
3. **Manajemen kendaraan lebih lengkap** (bagian dari item #8) — SEKARANG ADA & TERUJI PENUH:
   - `PUT /api/vehicles/:id` (edit brand/model/year/license_plate) & `DELETE /api/vehicles/:id`
   - Dites end-to-end dengan Postgres sungguhan: tambah kendaraan → edit plat nomor → list → hapus →
     list lagi (kosong). Semua sesuai ekspektasi, termasuk 404 saat akses kendaraan bukan milik sendiri.
4. **Unit test controller dengan DB di-mock** (item #6, sebagian) — SEKARANG ADA:
   - `auth.controller.test.js`, `staff.controller.test.js`, `vehicles.controller.test.js` — pakai
     `jest.mock('../config/db')`. **CATATAN JUJUR**: ini mock (untuk kecepatan & isolasi test unit),
     BUKAN test integrasi otomatis. Validasi INTEGRASI sungguhan sudah dilakukan manual lewat `curl`
     (lihat poin di atas & "Temuan penting" di atas) tapi BELUM dituangkan jadi test otomatis
     (mis. `supertest` + Postgres sungguhan di `beforeAll`) — itu masih pekerjaan lanjutan yang jelas
     sekarang FEASIBLE di sandbox ini (dulu dianggap butuh "strategi test-database yang belum dipilih").
   - Total test naik dari 18 → **58 test, semua PASS** (`npm test`).
5. **Google Maps API key dari local.properties** (item #7) — SELESAI:
   - `android-app/app/build.gradle.kts` sekarang baca `MAPS_API_KEY` dari `local.properties` (fallback
     ke placeholder lama jika belum diisi, supaya project tetap bisa di-sync tanpa error).
   - `android-app/local.properties.example` (template + instruksi) dan `android-app/.gitignore` baru
     (sebelumnya belum ada sama sekali) agar `local.properties` tidak ke-commit.
6. **Layar profil & manajemen kendaraan di Android** (item #8, sisi Android) — SUDAH DITULIS:
   - `ui/screens/profile/ProfileScreen.kt` + `ProfileViewModel.kt` (lihat/edit nama/email/telepon,
     panggil `GET/PUT /api/auth/me`), route `Screen.Profile` di `NavGraph.kt`, tombol profil (ikon
     person) baru di `HomeScreen.kt` topbar.
   - `ServiceBookScreen.kt` ditambah menu 3-titik per kendaraan (Edit/Hapus), `EditVehicleDialog`
     (prefill dari data existing), dialog konfirmasi hapus. `ServiceBookViewModel.kt` +
     `VehicleRepository.kt` + `VehicleDao.kt` (`deleteById`) mendukung alur ini end-to-end ke API.
   - DTO baru: `UpdateProfileRequest`, `UpdateVehicleRequest`. `ApiService.kt` ditambah 4 endpoint
     (`getProfile`, `updateProfile`, `updateVehicle`, `deleteVehicle`).
   - **CATATAN JUJUR sama seperti Sesi 1/2**: kode ini BELUM pernah di-compile compiler Kotlin/Gradle
     sungguhan (masih item #1 di bawah — sandbox ini TETAP tidak punya Android SDK). Verifikasi sesi
     ini terbatas pada: cek keseimbangan kurung `{}`/`()` per file, cek semua field/method yang
     dipanggil benar-benar ada di kelas terkait (mis. `VehicleEntity.licensePlate`,
     `SessionManager.getBearerToken()`), dan cek semua ikon Compose yang dipakai sudah diimpor.
     Ini BUKAN pengganti build Gradle sungguhan.

### Status: SUDAH SELESAI (Sesi 4 — sesi ini)

**Konfirmasi ulang temuan Sesi 3:** `apt-get install postgresql postgresql-contrib` berhasil lagi di
awal sesi ini tanpa masalah. Seluruh pekerjaan di bawah dites melawan **Postgres sungguhan**
(`otoshield_db` untuk dev manual, `otoshield_test_db` terpisah untuk test otomatis), bukan mock.

1. **Refactor `server.js` → `app.js` + `server.js`** (prasyarat teknis untuk item 6) — `app.js` baru
   isinya murni Express app (routes/middleware, tanpa `http.createServer`/Socket.IO) supaya bisa
   di-`require` langsung oleh test integrasi tanpa bind port TCP sungguhan. `server.js` sekarang cuma
   bikin http server + Socket.IO + `app.set('io', ...)` + `listen()`. **Dites**: boot ulang server,
   curl semua endpoint utama (`/health`, `/`, `/mechanic`, `/api/workshops`, admin 401) — identik
   dengan sebelum refactor, dan 58 test lama tetap PASS.
2. **Integrasi payment gateway** (item "belum dikerjakan" #3) — SEKARANG ADA & TERUJI PENUH:
   - `src/services/paymentGateway.js`: pola gaya Midtrans. `verifySignatureKey()` adalah implementasi
     **sungguhan** (bukan simulasi) dari formula resmi Midtrans
     (`SHA512(order_id+status_code+gross_amount+ServerKey)`) pakai `crypto.timingSafeEqual`.
     **CATATAN JUJUR**: `createTransaction()` TIDAK memanggil API Midtrans sungguhan (sandbox tidak
     punya kredensial produksi & tidak ada akses jaringan ke domain Midtrans) — ia mengembalikan
     bentuk respons yang strukturnya identik dengan Snap API Midtrans asli, supaya sisi controller
     & Android app bisa dikembangkan melawan kontrak yang benar. Untuk pindah ke gateway sungguhan,
     ganti isi `createTransaction()` dengan HTTP call ke Midtrans — signature verification TIDAK perlu
     berubah sama sekali.
   - `payments.controller.js` diganti total: endpoint publik lama `PUT /:id/pay` (bisa dipanggil siapa
     saja untuk menandai pembayaran orang lain lunas — celah keamanan) **DIHAPUS**. Diganti
     `POST /api/payments/webhook/midtrans` (tanpa auth JWT — keamanan dari verifikasi signature) +
     `PUT /:id/confirm-cash` (khusus method CASH, ditolak 403 untuk VA/E-WALLET).
   - **10 unit test baru** (`paymentGateway.test.js`) — signature valid, signature dipalsukan, server
     key salah, order_id/amount diubah attacker, semua dites melawan SHA512 independen.
   - **Dites end-to-end dengan Postgres sungguhan** lewat `curl`: create payment → webhook signature
     valid → status PAID; webhook signature palsu → 403, status tetap PENDING; endpoint lama → 404;
     alur CASH (create → confirm-cash → PAID) sukses; confirm-cash pada VIRTUAL_ACCOUNT ditolak 403.
3. **Abstraksi cloud storage untuk foto** (item #4) — SEKARANG ADA & TERUJI:
   - `src/services/storage/{index,localDriver,s3Driver}.js`: driver dipilih lewat env `STORAGE_DRIVER`
     (`local` default — perilaku lama tidak berubah bagi siapa pun yang belum set env baru ini, atau
     `s3`). `upload.middleware.js` & `emergency.controller.js` (`uploadPhoto`) diupdate memakai
     abstraksi ini alih-alih hardcode `multer.diskStorage`.
   - **CATATAN JUJUR**: driver `s3.js` (pakai `@aws-sdk/client-s3`) BELUM pernah dites melawan bucket
     AWS S3 sungguhan (sandbox tidak punya kredensial AWS & tidak ada akses jaringan ke
     `*.amazonaws.com`). Yang SUDAH dites sungguhan (`s3Driver.test.js`, 7 test, pakai
     `aws-sdk-client-mock` — library resmi mock AWS SDK v3, bukan mock buatan sendiri):
     `PutObjectCommand` dipanggil dengan `Bucket`/`Key`/`Body`/`ContentType` yang benar, `getUrl()`
     mengembalikan format URL yang benar (termasuk custom endpoint untuk layanan S3-compatible seperti
     MinIO), dan error dari S3Client diteruskan sebagai exception ke controller. Upload ke bucket AWS
     sungguhan **wajib** dicoba di lingkungan dengan kredensial+akses jaringan AWS asli sebelum dipakai
     produksi.
   - Driver `local` (default, tidak berubah) **dites end-to-end dengan Postgres sungguhan** lewat
     `curl`: upload foto emergency → `photo_url` tersimpan di DB → file diunduh lewat static serving →
     dibandingkan byte-per-byte dengan file asli → identik.
   - **Bug ditemukan & diperbaiki** dalam proses ini: error dari `fileFilter` multer (mis. tipe file
     salah) sebelumnya jatuh ke error handler umum Express dan balas **500**, padahal seharusnya
     **400** (kesalahan input pengguna, bukan kegagalan server). Ini bug LAMA (sudah ada sejak Sesi 2,
     bukan regresi dari perubahan Sesi 4), ditemukan lewat test integrasi baru (lihat poin 4), dan
     diperbaiki di `emergency.routes.js` dengan wrapper yang menangkap error multer secara eksplisit.
4. **Test integrasi otomatis dengan Postgres sungguhan** (item #6) — SEKARANG ADA:
   - `otoshield_test_db` (database terpisah dari `otoshield_db` dev) dibuat manual lewat `psql` di
     sandbox sesi ini (perintah: lihat `src/test-helpers/integrationSetup.js`, butuh
     `ALTER USER otoshield CREATEDB` dulu). **CATATAN**: kalau dijalankan di mesin/CI lain, database
     ini perlu dibuat dulu secara manual juga — belum ada automasi pembuatan database-nya sendiri,
     hanya automasi schema DI DALAMNYA (`resetSchema()` drop+rebuild schema tiap file test jalan).
   - `src/test-helpers/integrationSetup.js`: override `DATABASE_URL` ke DB test SEBELUM `app.js`/
     `config/db.js` di-require, supaya pool `pg` yang dibuat memakai koneksi test, bukan dev.
   - 3 file test baru di `src/__tests__/integration/`, total **30 test, semua PASS**, betul-betul
     lewat HTTP (`supertest`) melawan Postgres sungguhan (bukan mock `pool.query` seperti test
     controller Sesi 3): `auth_vehicles.integration.test.js` (12 test — register/login/profil/CRUD
     kendaraan, termasuk cek kendaraan pengguna lain tidak bisa diakses), `workshops.integration.test.js`
     (7 test — listing, filter jarak/verified, review), `emergency_payments.integration.test.js`
     (11 test — SOS + upload foto + seluruh alur payment gateway/webhook dari poin 2).
   - **Total test proyek naik dari 58 → 105, semua PASS**, dijalankan 2x berturut-turut untuk
     konfirmasi tidak flaky (schema di-reset bersih tiap file test, jadi tidak saling bergantung urutan).

### Status: SUDAH SELESAI (Sesi 5 — sesi ini)

Fokus sesi ini: item #9 (kode Android untuk payment gateway, yang di Sesi 4 disebut "belum
disentuh"). `apt-get install postgresql postgresql-contrib` berhasil lagi di awal sesi, migrate+seed
jalan normal, **105 test lama tetap PASS** (dicek 2x, sebelum & sesudah eksplorasi sesi ini — sesi
ini tidak mengubah kode backend sama sekali, hanya membaca & meng-curl untuk verifikasi kontrak).

1. **Ditemukan saat mulai kerja**: catatan "belum dikerjakan" #9 di Sesi 4 mengira ada kode Android
   payment yang mungkin sudah usang ("cek `PaymentRepository.kt`/`ApiService.kt` kalau ada") - setelah
   di-`grep` ternyata **tidak ada kode payment sama sekali di sisi Android** sebelum sesi ini. Jadi
   pekerjaan sesi ini bukan "sinkronisasi" tapi menulis fitur baru dari nol mengikuti kontrak backend
   Sesi 4.
2. **File baru**: `data/remote/dto/PaymentDto.kt` (`CreatePaymentRequest`, `PaymentDto`,
   `CreatePaymentResponse`), `data/repository/PaymentRepository.kt`,
   `ui/screens/payment/PaymentViewModel.kt` + `PaymentScreen.kt` (pilih metode VA/E-WALLET/CASH →
   buat tagihan → untuk VA/E-WALLET tampilkan tombol buka `gateway_redirect_url` bila ada + tombol
   "Cek Status Terbaru" untuk polling manual setelah webhook; untuk CASH tombol "Konfirmasi Sudah
   Bayar Tunai" yang panggil `confirm-cash`).
3. **File diubah**: `ApiService.kt` (+4 endpoint: createPayment/getPayment/listPayments/confirmCash),
   `di/ServiceLocator.kt` (daftarkan `paymentRepository`), `navigation/Screen.kt` + `NavGraph.kt`
   (route baru `Screen.Payment`, path `payment/{emergencyId}?amount={amount}`),
   `ui/screens/sos/SosTrackingScreen.kt` (tombol "Bayar Sekarang" muncul saat status `COMPLETED`,
   navigasi ke `PaymentScreen` bawa `estimated_cost_max` sebagai jumlah default).
4. **Verifikasi kode Kotlin** (BUKAN pengganti build Gradle sungguhan - sandbox ini tetap tidak
   punya Android SDK, sama seperti sesi-sesi sebelumnya): cek keseimbangan kurung `{}/()/[]` di semua
   9 file yang disentuh (lolos), cek semua field DTO yang dipakai benar-benar ada & tipenya cocok
   (mis. `EmergencyRequestDto.estimated_cost_max: Int?`), cek pola smart-cast nullable-check yang
   dipakai (`if (x.field != null) { ...x.field... }`) konsisten dengan pola yang sudah "diterima" di
   `SosTrackingScreen.kt` sesi-sesi sebelumnya.
5. **Verifikasi kontrak API lewat curl sungguhan** (bagian paling penting sesi ini, berhasil
   menangkap 2 kesalahan asumsi SEBELUM jadi bug tersembunyi):
   - Boot server + Postgres asli, register user, buat emergency request, buat payment VA & CASH,
     `confirm-cash`, `GET`/list payment, coba `confirm-cash` pada payment VA (harus 403) - semua
     nama field JSON dikonfirmasi PERSIS cocok dengan `PaymentDto.kt`/`CreatePaymentResponse` punya
     Android (case-sensitive, termasuk `gateway_redirect_url`, `gateway_mode`).
   - **Bug ditemukan & diperbaiki SEBELUM dikirim**: draf pertama `PaymentScreen.kt` mengecek
     `gatewayMode == "sandbox"`, padahal nilai sungguhan dari `paymentGateway.js` adalah `"simulated"`
     (lihat konstanta `GATEWAY_MODE`) - salah tulis ini akan membuat label "mode uji coba" TIDAK
     PERNAH muncul ke pengguna. Sudah diperbaiki jadi `"simulated"`.
   - **Bug kedua ditemukan & diperbaiki**: draf pertama menaruh tombol "Cek Status Terbaru" DI DALAM
     blok `if (gatewayRedirectUrl != null)`. Hasil curl membuktikan bahwa dalam `GATEWAY_MODE ==
     'simulated'` (kondisi default sekarang, tanpa `MIDTRANS_SERVER_KEY`), `redirect_url` SELALU
     `null` untuk semua method non-CASH (lihat `createTransaction()` di `paymentGateway.js`) -
     draf pertama akan membuat pengguna macet tanpa cara memantau status sama sekali di mode
     default. Diperbaiki: tombol "Buka Halaman Pembayaran" hanya muncul kalau `redirect_url` ada,
     tombol "Cek Status Terbaru" SELALU muncul untuk status PENDING method non-CASH.
   - Dites juga alur webhook penuh: buat payment VA → hitung signature SHA512 sungguhan pakai dev
     server key → kirim ke `POST /api/payments/webhook/midtrans` → `GET` payment lagi → status
     berubah `PENDING` → `PAID`. Ini membuktikan `refreshStatus()` di `PaymentViewModel.kt` akan
     bekerja sesuai harapan begitu webhook gateway sungguhan terpasang nanti.
6. **CATATAN JUJUR**: seperti semua kode Kotlin lain di proyek ini, `PaymentScreen.kt`/
   `PaymentViewModel.kt`/dll BELUM PERNAH di-compile Gradle sungguhan. Yang membedakan sesi ini:
   kontrak API-nya (nama field, tipe, kapan null) sudah divalidasi SUNGGUHAN lewat curl melawan
   server nyata, jadi risiko yang tersisa murni di sisi sintaks/tipe Kotlin, bukan asumsi salah
   tentang bentuk response backend.

### Lanjutan Sesi 5: item #10 (upload foto kerusakan, sisi Android)

Dikerjakan setelah bagian payment di atas, dalam sesi yang sama.

1. **Ditemukan sebelum menulis kode**: catatan awal sesi ini ("EmergencyDto.kt sudah punya field
   photo_url") ternyata SALAH setelah dicek langsung ke file — `EmergencyRequestDto` belum punya
   field itu sama sekali. Ditambahkan `val photo_url: String?`. Pelajaran: jangan percaya catatan
   memory tanpa verifikasi ulang terhadap kode sungguhan, termasuk catatan yang ditulis sendiri.
2. **File baru**: `util/FileUtils.kt` (`uriToPhotoPart()` - salin `content://` URI ke file cache
   sementara lalu bungkus jadi `MultipartBody.Part`, field wajib bernama `"photo"`).
3. **File diubah**: `ApiService.kt` (+`uploadEmergencyPhoto` multipart), `EmergencyRepository.kt`
   (+`uploadPhoto()`), `EmergencyDto.kt` (+`photo_url`), `RetrofitInstance.kt`
   (+`resolveMediaUrl()` - lihat poin 4), `SosViewModel.kt`/`SosTrackingViewModel` (+state
   `isUploadingPhoto`/`photoUploadError` +fungsi `uploadPhoto()`), `SosTrackingScreen.kt` (+tombol
   "Tambahkan/Ganti Foto Kerusakan" pakai system Photo Picker
   `ActivityResultContracts.PickVisualMedia()` - tidak butuh izin runtime storage sama sekali di
   semua API level berkat backport di activity-compose 1.9.1 yang sudah dipakai proyek ini),
   `app/build.gradle.kts` (+dependency `io.coil-kt:coil-compose:2.6.0`, belum ada image-loading
   library sebelumnya di proyek).
4. **Bug ditemukan & diperbaiki SEBELUM dikirim** (lewat baca kode backend, bukan asumsi): draf
   awal berencana langsung memakai `photo_url` dari server sebagai URL gambar untuk Coil. Setelah
   dibaca, `localDriver.js` (default storage driver) ternyata mengembalikan PATH RELATIF
   (`/uploads/emergency/xxx.jpg`), sedangkan `s3Driver.js` mengembalikan URL ABSOLUT penuh
   (`https://...`) — dan path relatif itu di-serve Express di ROOT server
   (`app.use('/uploads', express.static(...))`), BUKAN di bawah `/api/`. Kalau langsung dipakai
   sebagai URL gambar tanpa penyesuaian, path relatif akan gagal dimuat Coil. Diperbaiki dengan
   `RetrofitInstance.resolveMediaUrl()`: kalau `photo_url` sudah `http(s)://` pakai apa adanya,
   kalau tidak maka digabung dengan host dari `BASE_API_URL` dikurangi suffix `"api/"`.
5. **Verifikasi lewat curl sungguhan terhadap server nyata** (bukan asumsi dari baca kode saja):
   - Upload file JPEG kecil (byte SOI/APP0/EOI valid) ke `POST /api/emergency/:id/photo` lewat
     multipart form field `photo` → berhasil, response berisi `photo_url` dengan bentuk PERSIS
     seperti diprediksi (`/uploads/emergency/{id}-{timestamp}.jpg`).
   - Upload file `text/plain` (bukan gambar) → dikonfirmasi balik `400` (BUKAN `500`) dengan pesan
     `"Format file tidak didukung. Gunakan JPEG, PNG, atau WEBP."` - mengonfirmasi wrapper multer
     Sesi 4 di `emergency.routes.js` bekerja seperti didokumentasikan, dan bahwa
     `EmergencyRepository.uploadPhoto()` akan menerima `Result.failure` dengan pesan yang aman
     ditampilkan ke pengguna (bukan wrapped as generic 500 error).
   - `GET` file statis di `http://localhost:4000{photo_url_relatif}` (di ROOT, bukan di bawah
     `/api/`) → `200 OK`, mengonfirmasi logika `resolveMediaUrl()` (strip `"api/"` dari
     `BASE_API_URL`, bukan strip seluruh path) sudah benar.
6. Balance check kurung `{}/()/[]` di semua file yang disentuh (`ApiService.kt`,
   `EmergencyDto.kt`, `RetrofitInstance.kt`, `EmergencyRepository.kt`, `FileUtils.kt`,
   `SosViewModel.kt`, `SosTrackingScreen.kt`) - satu "mismatch" ditemukan di `SosViewModel.kt` yang
   setelah ditelusuri ternyata FALSE POSITIVE: komentar docstring lama (bukan tulisan sesi ini)
   berisi penomoran `1) ... 2) ... 3) ... 4)` yang mengandung karakter `)` literal di teks komentar,
   bukan kode sungguhan. Kode aslinya sudah seimbang.
7. **CATATAN JUJUR (sama seperti bagian payment)**: kode Kotlin di atas tetap belum pernah
   di-compile Gradle sungguhan (tidak ada Android SDK di sandbox ini). Yang sudah tervalidasi
   sungguhan lewat curl: kontrak API (nama field, kode status HTTP, format path foto, lokasi
   static file). Yang BELUM tervalidasi sungguhan: sintaks Kotlin itu sendiri (pemeriksaan hanya
   sebatas baca manual + cek kurung), dan perilaku `ActivityResultContracts.PickVisualMedia` di
   perangkat/emulator sungguhan (tidak bisa dites di sandbox tanpa Android runtime).

### Status: BELUM DIKERJAKAN (lanjutkan di sini)

1. **Belum pernah di-build/compile.** Sandbox pengerjaan tidak punya Android SDK/Gradle maupun
   akses ke `dl.google.com`, jadi kode Kotlin (termasuk `ProfileScreen.kt`+`ProfileViewModel.kt` dan
   perubahan `ServiceBookScreen.kt` dari Sesi 3) belum divalidasi oleh compiler sungguhan — perlu
   dibuka di Android Studio, sync Gradle, dan diperbaiki jika ada error kompilasi. **Prioritaskan ini**
   sebelum menambah fitur Android baru. Tidak disentuh di Sesi 4 (fokus sesi ini backend saja).
2. ~~Sistem login/role staf bengkel & mekanik~~ — **SELESAI di Sesi 3**, lihat di atas.
3. ~~Integrasi payment gateway~~ — **SELESAI di Sesi 4**, lihat di atas. Sisa pekerjaan: kalau mau
   pindah dari simulasi ke Midtrans sungguhan, tinggal isi `MIDTRANS_SERVER_KEY` asli di env & ganti
   isi `createTransaction()` di `paymentGateway.js` dengan HTTP call ke API Midtrans (signature
   verification tidak perlu diubah). Juga belum ada test untuk skenario `transaction_status: 'expire'`
   / `'cancel'` di webhook (logic-nya ada di controller, tapi belum ada test integrasi spesifik untuk
   jalur itu).
4. ~~Cloud storage foto~~ — **Abstraksi & driver local SELESAI + TERUJI di Sesi 4**, lihat di atas.
   Sisa pekerjaan: driver `s3.js` masih perlu divalidasi melawan bucket AWS S3 (atau MinIO) sungguhan
   di lingkungan yang punya kredensial & akses jaringan AWS — belum bisa di sandbox chat ini.
5. **Verifikasi nyata Docker & CI** — masih sama seperti Sesi 3: `dockerd` bisa jalan di sandbox ini,
   tapi `docker pull`/`docker build FROM <image>` tetap gagal 403 karena registry Docker Hub tidak
   di-whitelist jaringan. `docker compose up` untuk stack ini (butuh image `node:20-alpine` &
   `postgres:16-alpine` dari Docker Hub) **masih belum bisa** divalidasi di sandbox chat ini — butuh
   mesin/CI dengan akses registry Docker Hub normal. `.github/workflows/backend-ci.yml` juga masih
   belum pernah jalan di runner GitHub Actions sungguhan. **Tidak dicoba ulang di Sesi 4** sesuai
   instruksi (tidak ada info baru bahwa ini sudah bisa).
6. ~~Test integrasi otomatis dengan Postgres sungguhan~~ — **SELESAI di Sesi 4**, lihat di atas (30
   test baru, total 105 test). Sisa pekerjaan kalau mau diperluas: belum ada test integrasi untuk
   `estimator.routes.js` (Smart Cost Estimator) atau `admin.routes.js` (dashboard mekanik/operator) —
   baru ada unit test murni untuk estimator (`estimatorRules.test.js`, sudah dari sesi-sesi awal) dan
   belum ada test SAMA SEKALI (mock atau integrasi) untuk `admin.controller.js`/`staff` end-to-end
   lewat dashboard `/mechanic`.
7. ~~Google Maps API key hardcode~~ — **SELESAI di Sesi 3**, lihat di atas. (Person masih perlu isi
   `MAPS_API_KEY` asli sendiri di `local.properties` miliknya — itu di luar kendali sandbox ini karena
   butuh akun Google Cloud pribadi.)
8. ~~Layar profil pengguna & manajemen kendaraan~~ — **Backend SELESAI & TERUJI PENUH di Sesi 3.**
   Kode Android (ProfileScreen, edit/hapus kendaraan) SUDAH DITULIS di Sesi 3 tapi seperti semua kode
   Kotlin lain di proyek ini, BELUM di-compile Gradle sungguhan — lihat item #1.
9. ~~Kode Android untuk payment gateway~~ — **SELESAI di Sesi 5**, lihat di atas. Tidak ada kode lama
   sama sekali, ditulis dari nol (`PaymentScreen`, `PaymentViewModel`, `PaymentRepository`,
   `PaymentDto`, route baru, tombol "Bayar Sekarang" di `SosTrackingScreen`). Kontrak API sudah
   diverifikasi lewat curl sungguhan melawan backend nyata. Sisa pekerjaan: sama seperti semua kode
   Kotlin lain di proyek ini, belum di-compile Gradle sungguhan — lihat item #1.
10. ~~Kode Android untuk upload foto kerusakan~~ — **SELESAI di Sesi 5**, lihat di atas
    ("Lanjutan Sesi 5: item #10"). Kontrak API (multipart field `photo`, path relatif vs absolut,
    kode error 400 untuk tipe file salah) sudah diverifikasi lewat curl sungguhan.

### Status: SUDAH SELESAI (Sesi 6 — sesi ini)

Fokus sesi ini: item #6 (test integrasi untuk `estimator.routes.js` & `admin.routes.js`, dicatat
"masih kosong" di akhir Sesi 5) — dipilih karena eksplisit ditandai sebagai prioritas paling feasible
di sandbox ini. `apt-get install postgresql postgresql-contrib` berhasil lagi (nodesource.sources
sempat bikin `apt-get update` gagal 403 — di-`mv` sementara ke `/tmp` sebelum update, tidak dihapus
permanen). Baseline dicek dulu SEBELUM menulis kode apa pun: 105 test lama tetap PASS (harus bikin
`otoshield_test_db` dulu secara manual — `ALTER USER otoshield CREATEDB` lalu `CREATE DATABASE`,
sesuai catatan Sesi 4 — sempat lupa di awal sesi ini dan 30 test integrasi lama gagal dengan pesan
"database does not exist", bukan bug kode).

1. **Test integrasi `estimator.routes.js`** (bagian dari item #6) — SEKARANG ADA:
   - File baru `src/__tests__/integration/estimator.integration.test.js`, **6 test**, insert
     `cost_estimate_rules` langsung lewat `pg` di `beforeAll` (pola sama dengan
     `workshops.integration.test.js` — bukan `seed.sql` penuh, supaya kata kunci & rentang biaya yang
     dites diketahui persis). Mencakup: 400 saat `symptom_text` kosong/kurang dari 3 karakter, 200
     dengan `matched:true` + kategori & rentang biaya benar untuk gejala dikenal, 200 dengan
     `matched:false` untuk gejala tak dikenal, dan kasus 2 gejala sekaligus (skor sama-sama 1.0)
     memastikan keduanya muncul (satu jadi hasil utama, satu jadi alternatif).
2. **Test integrasi `admin.routes.js`** (bagian dari item #6) — SEKARANG ADA:
   - File baru `src/__tests__/integration/admin.integration.test.js`, **19 test**. Mencakup DUA jalur
     auth yang didukung `adminOrStaff.middleware.js` (Sesi 3): shared key `x-admin-key` lama, DAN
     token staf JWT baru (login → dapat token → dipakai). Skenario yang dites lewat HTTP sungguhan
     (bukan cuma unit test middleware yang sudah ada sebelumnya): `GET/PUT /api/admin/emergency`
     tanpa key (401), key salah (401), key benar (200, join ke `users` tampil), filter `?status=`,
     update status tidak valid (400) dan valid (200, dikonfirmasi status benar-benar berubah di DB),
     `GET /api/admin/workshops`, bootstrap staf ADMIN pertama pakai key lama, staf ADMIN membuat staf
     MECHANIC baru pakai token (bukan key), staf MECHANIC ditolak (403) saat mencoba membuat staf atau
     lihat daftar staf, token user biasa ditolak (401) di dashboard admin, dan token staf ditolak (403)
     di endpoint user (`/api/auth/me`) — mengonfirmasi ulang lewat test otomatis apa yang Sesi 3 dulu
     hanya verifikasi manual lewat `curl`.
   - **Satu iterasi diperlukan**: draf pertama test "PUT tanpa auth -> status tidak berubah" mencoba
     verifikasi lewat `GET /api/emergency/:id` tanpa token — gagal karena endpoint itu sendiri butuh
     `auth.middleware.js` (401), jadi `check.body.status` adalah `undefined`, bukan `'PENDING'`.
     Diperbaiki dengan verifikasi lewat `GET /api/admin/emergency` (pakai `x-admin-key`) alih-alih.
3. **Total test proyek naik dari 105 → 129, semua PASS**, dijalankan 2x berturut-turut untuk
   konfirmasi tidak flaky (pola sama dengan Sesi 4).
4. **Temuan di luar rencana awal**: `npm install` bersih (bukan dari zip lama, `node_modules` memang
   tidak ikut ter-zip) memunculkan **3 kerentanan moderate BARU** di `npm audit` (lewat `qs`, dependency
   transitif `express`/`body-parser`) — padahal Sesi 2 mencatat "0 kerentanan" setelah cleanup. Ini
   BUKAN regresi dari perubahan kode proyek (tidak ada `package.json` yang diubah sesi ini), melainkan
   advisory baru yang terbit di database npm sejak Sesi 2 untuk versi `qs` yang sama yang sudah lama
   ter-pin via `^4.19.2` punya `express`. Dijalankan `npm audit fix` (tidak ada breaking change,
   hanya bump versi transitif) → **0 kerentanan lagi**, dan 129 test dicek ulang PASS setelah itu.
   **Pelajaran**: `npm audit` bisa berubah hasilnya antar sesi walau tidak ada kode yang disentuh —
   cek ulang tiap sesi baru, jangan asumsikan status "0 kerentanan" dari sesi lama masih berlaku.

### Status: SUDAH SELESAI (Sesi 7 — sesi ini)

Fokus sesi ini: dua kandidat yang eksplisit ditandai "jelas feasible di sandbox ini" di akhir catatan
Sesi 6 — test integrasi role `WORKSHOP_STAFF` dan skenario webhook Midtrans `expire`/`cancel`. Rutinitas
awal sesi diikuti persis sesuai saran Sesi 6: `npm install` bersih, `mv nodesource.sources` ke `/tmp`
sebelum `apt-get update && apt-get install -y postgresql postgresql-contrib` (berhasil lagi, konsisten
dengan 6 sesi sebelumnya), `CREATE USER otoshield` + `ALTER USER otoshield CREATEDB` + buat
`otoshield_db` & `otoshield_test_db` secara manual. Baseline dicek dulu SEBELUM menulis kode apa pun:
129 test lama tetap PASS, dan `npm audit` dijalankan di awal → **0 kerentanan**, tidak ada advisory baru
sejak `npm audit fix` Sesi 6 (beda dari Sesi 6 yang menemukan 3 advisory baru — kali ini bersih).

1. **Test integrasi role `WORKSHOP_STAFF`** (kandidat yang dicatat di akhir Sesi 6) — SEKARANG ADA:
   - Ditambahkan ke `admin.integration.test.js` (bukan file baru — konsisten dengan pola file itu yang
     sudah menggabungkan test dashboard admin + alur token staf sejak Sesi 6), di dalam `describe('Alur
     token staf ...')` yang sama dengan test `ADMIN`/`MECHANIC`, diletakkan setelah test "staf ADMIN GET
     daftar staf" supaya tidak mengganggu assertion `arrayContaining` di situ.
   - **5 test baru**: staf ADMIN membuat staf `WORKSHOP_STAFF` baru lewat token (201, `password_hash`
     tidak bocor), staf `WORKSHOP_STAFF` login lalu akses dashboard admin (200 — mengonfirmasi ulang
     bahwa `adminOrStaff.middleware.js` memang tidak membatasi role, sama seperti temuan `MECHANIC` di
     Sesi 6), staf `WORKSHOP_STAFF` (bukan ADMIN) ditolak 403 saat membuat staf baru, staf
     `WORKSHOP_STAFF` ditolak 403 saat GET daftar staf, dan token staf `WORKSHOP_STAFF` ditolak 403 di
     endpoint user biasa (`/api/auth/me`). Ketiga role (`ADMIN`, `MECHANIC`, `WORKSHOP_STAFF`) sekarang
     semuanya punya test end-to-end lewat Postgres sungguhan.
2. **Test integrasi webhook Midtrans `transaction_status: 'expire'`/`'cancel'`** (kandidat lain yang
   dicatat di akhir Sesi 6) — SEKARANG ADA:
   - Ditambahkan ke `emergency_payments.integration.test.js`, sub-`describe` baru di dalam blok
     `Payments`, diletakkan SETELAH test "GET /api/payments mengembalikan riwayat" (yang meng-assert
     `length).toBe(2)`) supaya pembayaran baru yang dibuat test ini tidak mengubah hitungan itu.
   - **3 test baru**: `expire` → status jadi `FAILED` & `paid_at` tetap `null`, `cancel` → status jadi
     `FAILED`, dan satu test tambahan di luar rencana awal — webhook `cancel` yang datang TERLAMBAT
     setelah pembayaran yang SAMA sudah `PAID` (dari test "signature VALID" sebelumnya di file yang
     sama) dites tidak boleh menurunkan status kembali ke `FAILED`. Ini memverifikasi guard
     `existing.rows[0].status !== 'PAID'` yang sudah ada di `payments.controller.js` sejak Sesi 4
     (logic-nya sudah benar, tapi belum pernah ada test yang membuktikannya secara eksplisit) — penting
     karena webhook gateway sungguhan bisa datang out-of-order/retry, dan pembayaran yang sudah lunas
     tidak boleh "anjlok" gara-gara webhook basi.
3. **Total test proyek naik dari 129 → 137, semua PASS**, dijalankan 2x berturut-turut untuk konfirmasi
   tidak flaky (pola sama dengan Sesi 4 & 6). `node --check` dijalankan ulang ke semua file `.js` di
   `backend/src/` (bukan cuma 2 file yang diubah) — semua lolos.
4. **Tidak ada perubahan** pada kode production (`controllers/`, `services/`, `middleware/`, `routes/`)
   sesi ini — murni penambahan test, sesuai lingkup yang diminta. `package.json` juga tidak disentuh.

### Sesi 8 — Verifikasi status kesiapan production (TIDAK ada perubahan kode/test)

**CATATAN JUJUR**: sesi ini BUKAN sesi coding. Person bertanya "apakah sudah selesai semua dan siap
untuk production?" — dijawab dengan membaca ulang Bagian 7 (status per Sesi 7) yang sudah ada di
dokumen, **TANPA** menjalankan ulang `npm test`, `npm audit`, atau menyentuh kode apa pun di sesi ini.
Jawabannya tidak berubah dari Sesi 7 karena tidak ada pekerjaan baru yang dilakukan di antara Sesi 7
dan pertanyaan ini.

**Jawaban singkat yang diberikan: BELUM siap production.** Ringkasan yang disampaikan ke person persis
mengikuti Bagian 7:
- 5 blocker 🔴 tetap terbuka, semuanya butuh lingkungan/kredensial DI LUAR sandbox chat ini: build
  Gradle sungguhan di Android Studio (item #1), kredensial `MIDTRANS_SERVER_KEY` produksi sungguhan
  (item #2), validasi driver S3 melawan bucket AWS/MinIO sungguhan (item #3), `docker compose up` +
  GitHub Actions runner sungguhan (item #4), dan `MAPS_API_KEY` Google Cloud milik sendiri (item #5).
- 1 catatan 🟡 tetap terbuka (bukan blocker): dashboard admin/mekanik masih punya jalur darurat
  `x-admin-key` (shared secret) yang tidak tercatat di audit trail "siapa mengubah apa".
- Bagian 🟢 (137 test PASS, kontrak API terverifikasi, `npm audit` 0 kerentanan) disampaikan apa
  adanya dari hasil Sesi 7, tanpa diverifikasi ulang di sesi ini.

**Pelajaran untuk sesi berikutnya**: kalau ditanya pertanyaan serupa ("sudah siap production?") tanpa
ada pekerjaan baru di antaranya, jawaban BOLEH langsung dari Bagian 7 tanpa perlu re-run test —
TAPI kalau ada jeda waktu yang signifikan sejak sesi terakhir, atau kalau person menyebut sudah
mengubah sesuatu di luar sandbox (mis. sudah isi `MIDTRANS_SERVER_KEY` asli), verifikasi ulang dulu
sebelum menjawab, jangan asumsikan Bagian 7 masih 100% akurat begitu saja.

### Status: SUDAH SELESAI (Sesi 9 — sesi ini)

**Konteks awal sesi**: person minta "lanjutkan hingga siap production". Rutinitas awal sesi diikuti
sama seperti sesi-sesi sebelumnya (`mv nodesource.sources`, `apt-get install postgresql`, buat user +
kedua database secara manual). Baseline dicek dulu: **137 test lama tetap PASS** (bukan 129 — baru
ketahuan di sesi ini bahwa kode di zip yang diupload sudah level Sesi 7, walau file
`otoshield_blueprint.md` DI DALAM zip itu sendiri masih versi Sesi 6/129-test; dokumen blueprint
terpisah yang diupload di sesi ini sudah sampai catatan Sesi 8). `npm audit` di awal → 0 kerentanan.

Karena daftar kandidat test integrasi "feasible di sandbox" sudah habis per Sesi 7 (dikonfirmasi
ulang di catatan Sesi 8), fokus sesi ini dialihkan ke **production-hardening backend** — sesuatu yang
belum pernah disentuh sama sekali dari Sesi 1, dan relevan langsung dengan permintaan "sampai siap
production" walau tidak menghilangkan 5 blocker eksternal yang sudah lama tercatat.

1. **Security headers, rate limiting, validasi environment, CORS, graceful shutdown** — SEKARANG ADA:
   - `helmet()` dipasang di `app.js` untuk header keamanan HTTP standar. Dicek CSP default
     (`default-src 'self'`) TIDAK merusak dashboard `/mechanic` (tidak ada inline script/style di
     situ, semua request same-origin) lewat `curl` — lihat 🟡 di Bagian 7 soal batasan ini (belum
     dicek di browser asli).
   - `express-rate-limit` (`middleware/rateLimit.middleware.js`): limiter umum semua `/api/*` (300
     req/15 menit/IP), limiter ketat khusus `POST /api/auth/login`, `/register`, `/api/staff/login`
     (20 percobaan/15 menit, `skipSuccessfulRequests` supaya login yang benar tidak ikut dihitung),
     limiter khusus `POST /api/emergency` (10 SOS baru/10 menit) untuk cegah spam permintaan darurat
     palsu. Otomatis jadi no-op (`next()` langsung) saat `NODE_ENV=test` supaya tidak mengganggu
     test integrasi yang sengaja memukul endpoint sama berkali-kali.
   - `src/config/validateEnv.js`: validasi `DATABASE_URL`/`JWT_SECRET`/`ADMIN_API_KEY` saat boot
     (`server.js`, BUKAN `app.js` — supaya test integrasi yang `require('./app')` langsung tidak ikut
     tervalidasi/exit). Wajib diisi (error kalau kosong), dan kalau masih nilai placeholder persis
     dari `.env.example` DENGAN `NODE_ENV=production` dianggap fatal (exit 1) — di development cuma
     `console.warn`. `JWT_SECRET` < 16 karakter kena perlakuan sama (fatal di production, warning di
     dev).
   - `src/config/corsOrigin.js`: origin CORS bisa dibatasi lewat `CORS_ORIGIN` di `.env` (daftar
     domain dipisah koma), dipakai KONSISTEN oleh `cors()` Express (`app.js`) maupun Socket.IO
     (`server.js`) — sebelumnya dua konfigurasi terpisah yang berpotensi tidak sinkron
     (`cors()` tanpa argumen vs `{ cors: { origin: '*' } }`).
   - Graceful shutdown di `server.js`: `SIGTERM`/`SIGINT` menutup `server.close()` → `io.close()` →
     `pool.end()` berurutan, dengan timeout paksa 10 detik kalau macet.
   - Tambahan kecil: `compression()` (gzip response), `morgan()` (request log, mati otomatis saat
     `NODE_ENV=test`), `app.set('trust proxy', 1)` (perlu untuk `req.ip`/rate-limit akurat di
     belakang reverse proxy).
   - **Semua di atas DITES NYATA, bukan cuma ditulis** (konsisten dengan budaya proyek ini): boot
     server sungguhan dengan `NODE_ENV=development`, `curl` cek header `Content-Security-Policy`/
     `Strict-Transport-Security`/`X-Frame-Options` muncul, dashboard `/mechanic` tetap balas 200,
     `kill -TERM` memicu log shutdown berurutan yang benar & proses keluar bersih; boot dengan
     berbagai kombinasi `NODE_ENV`/`JWT_SECRET` lewat `env -i ... node src/server.js` (dibungkus
     `timeout` supaya tidak menggantung sandbox) untuk 3 skenario validateEnv (kosong → exit 1,
     placeholder di production → exit 1, placeholder di development → boot dengan warning); 22
     percobaan login beruntun lewat `curl` mengonfirmasi request ke-21 & ke-22 balas `429` sesuai
     limit `authLimiter`. **Satu insight dari testing manual**: percobaan awal sempat salah desain
     (pakai `env -i` tapi lupa bahwa `dotenv.config()` di `server.js` akan mengisi variabel yang
     belum diset dari `backend/.env` yang sudah ada isinya lengkap — command pertama jadi tidak
     benar-benar menguji "variabel kosong" dan malah membuat server sungguhan `listen()` tanpa
     timeout, nyaris menghabiskan seluruh budget waktu tool sesi ini karena menggantung 300 detik).
     Diperbaiki dengan mengisi eksplisit SEMUA variabel relevan di tiap skenario `env -i` + selalu
     membungkus dengan `timeout`.
   - 22 test unit baru: `config/validateEnv.test.js` (12 test, fungsi murni `checkEnv()` — sengaja
     TIDAK mengetes `validateEnvOrExit()` yang memanggil `process.exit` asli, karena akan mematikan
     proses Jest itu sendiri; perilaku exit sungguhan divalidasi manual seperti di atas),
     `config/corsOrigin.test.js` (7 test), `middleware/rateLimit.middleware.test.js` (3 test —
     hanya menguji perilaku no-op saat `NODE_ENV=test`, yaitu kondisi sungguhan saat `npm test`
     berjalan; perilaku 429 sungguhan divalidasi manual seperti di atas, bukan lewat Jest, karena
     memaksa `NODE_ENV` bukan `'test'` di tengah proses Jest yang sama berisiko mengubah perilaku
     semua test lain yang jalan `--runInBand` di proses itu).
   - Dependency baru: `helmet`, `express-rate-limit`, `morgan`, `compression`. `npm audit` dicek
     ulang setelahnya → tetap 0 kerentanan.
   - `.env.example` (backend) ditambah dokumentasi `CORS_ORIGIN`.

2. **Bug ditemukan & diperbaiki di `docker-compose.yml`** (di luar rencana awal sesi ini) — file itu
   sebelumnya menaruh **nilai placeholder PERSIS dari `.env.example`** (`change_this_to_a_long_random_secret`,
   dst.) langsung sebagai `JWT_SECRET`/`ADMIN_API_KEY` sungguhan DENGAN `NODE_ENV=production` — kalau
   benar-benar dijalankan apa adanya, backend yang baru (item #1 di atas) akan LANGSUNG menolak boot
   (`validateEnvOrExit` menganggap ini fatal). Ini justru perilaku yang benar (menangkap kesalahan
   konfigurasi asli), tapi berarti file compose-nya sendiri yang wajib diperbaiki dulu:
   - Ditambahkan `otoshield/.env.example` (root, BEDA dengan `backend/.env.example` — dipakai Docker
     Compose untuk substitusi `${VAR}`, otomatis dibaca dari file `.env` di folder yang sama).
   - `docker-compose.yml` diubah untuk membaca `POSTGRES_PASSWORD`/`JWT_SECRET`/`ADMIN_API_KEY`/
     `CORS_ORIGIN` lewat `${VAR:?pesan error}` — `docker compose up` akan GAGAL START dengan pesan
     jelas kalau variabel wajib belum diisi di `.env` root, jaring pengaman tambahan di level Compose
     sebelum bahkan sampai ke `validateEnv.js` di dalam container.
   - **CATATAN JUJUR**: tetap belum bisa divalidasi `docker compose up` sungguhan di sandbox ini
     (masih 403 ke `registry-1.docker.io`, sama seperti sesi-sesi sebelumnya) — hanya sintaks YAML
     yang dicek (`python3 -c "import yaml; yaml.safe_load(...)"`) dan logika substitusi env
     ditelusuri manual, BUKAN dijalankan sungguhan lewat `docker compose config`/`up`.
3. **Temuan tidak terduga: `.github/workflows/backend-ci.yml` HILANG TOTAL dari zip** — README &
   `otoshield_blueprint.md` sejak Sesi 2 mencatat file ini "SUDAH ADA tapi BELUM DITES", tapi
   `unzip -l otoshield-project.zip | grep github` di sesi ini mengonfirmasi folder `.github/` sama
   sekali tidak ada di dalam zip yang diupload (bukan cuma lupa di-extract) — kemungkinan besar
   terlewat di proses re-zip salah satu sesi sebelumnya (dotfolder di root kadang tidak ikut
   ter-include tergantung cara zip dibuat, walau `backend/.env` yang juga dotfile ikut ter-zip, jadi
   penyebab pastinya tidak diketahui). Ditulis ulang dari nol mengikuti persis deskripsi pipeline
   yang sudah lama didokumentasikan ("npm ci -> node --check -> npm test -> npm audit"), ditambah
   service Postgres (dibutuhkan sejak Sesi 4 untuk test integrasi, belum ada di deskripsi lama karena
   waktu itu semua test masih mock). **CATATAN JUJUR**: sama seperti sebelumnya, TETAP belum pernah
   dicoba di runner GitHub Actions sungguhan — hanya sintaks YAML yang tervalidasi & tiap perintah di
   dalamnya sudah dicoba manual satu per satu di sandbox ini.
4. **Ditambahkan `.gitignore` di root proyek** (di luar rencana awal, ditemukan saat memeriksa risiko
   kebocoran secret) — sebelumnya TIDAK ADA `.gitignore` sama sekali, artinya `.env`/`local.properties`
   (kalau proyek ini di-`git init` & di-commit apa adanya) berisiko ikut ter-commit berikut rahasia
   sungguhan di dalamnya. `backend/.dockerignore` sudah lama ada tapi itu hanya melindungi image
   Docker, bukan riwayat Git.
5. **Migrasi & seed database `otoshield_db` (dev, bukan test) dijalankan & diverifikasi end-to-end**
   sungguhan lewat `curl` (register → login → login password salah balas 401 bukan 500 → list
   bengkel) — sekalian mengonfirmasi semua middleware baru (helmet, rate limit, CORS) tidak merusak
   alur normal, bukan cuma lolos test otomatis dengan DB test yang skema-nya dibuat programatik oleh
   `integrationSetup.js`.
6. **Total test proyek naik dari 137 → 159, semua PASS**, dijalankan 2x berturut-turut untuk
   konfirmasi tidak flaky. `node --check` ke semua file `.js` di `backend/src/` — semua lolos.
7. **Tidak ada perubahan** pada logic bisnis inti (controllers CRUD, estimator, payment gateway,
   socket realtime) — murni lapisan hardening di sekeliling `app.js`/`server.js`/routing, plus
   perbaikan file konfigurasi (`docker-compose.yml`, `.gitignore`, CI) yang tidak menyentuh kode
   `src/` yang sudah ada sebelumnya kecuali penambahan baris `import`/middleware di `app.js`,
   `server.js`, `auth.routes.js`, `staff.routes.js`, `emergency.routes.js`.

### Cara Melanjutkan
Unggah ulang `otoshield-project.zip` ke sesi baru, minta Claude meng-ekstraknya ke `/home/claude/`,
jalankan `npm install` di `backend/` (node_modules tidak ikut di zip). **Kalau `apt-get update` gagal
403 karena `nodesource.sources`**: `mv /etc/apt/sources.list.d/nodesource.sources /tmp/` dulu sebelum
`apt-get update && apt-get install -y postgresql postgresql-contrib` (terbukti berhasil 9 sesi
berturut-turut setelah itu) — Node.js sendiri sudah terpasang di sandbox, repo nodesource itu cuma
untuk apt-get, jadi aman dilepas sementara. Lalu rujuk daftar "BELUM DIKERJAKAN"/blocker di Bagian 7.
Untuk test integrasi otomatis, ingat perlu `ALTER USER otoshield CREATEDB` dulu sebelum bisa
`CREATE DATABASE otoshield_test_db` (lihat `test-helpers/integrationSetup.js`) — **cek ini SEBELUM
`npm test`**, jangan asumsikan baseline masih hijau tanpa langkah ini. Untuk boot server manual
(`npm run dev`/`node src/server.js`), sekarang WAJIB `cp .env.example .env` lalu isi `JWT_SECRET`/
`ADMIN_API_KEY` dengan nilai acak sungguhan (bukan placeholder) — `validateEnv.js` (Sesi 9) akan
langsung menolak boot di `NODE_ENV=production` kalau lupa, dan sekadar warning di `NODE_ENV=development`.
Item #1 (build Android di Android Studio) dan sebagian item #4 (`docker compose up`/GitHub Actions
runner yang butuh image dari Docker Hub/registry Actions) tetap butuh lingkungan di luar sandbox chat
ini — jangan buang waktu mencoba lagi kecuali ada info baru bahwa itu sudah bisa. **PENTING (pelajaran
Sesi 5, masih berlaku, HAMPIR TERULANG di Sesi 9)**: setiap layanan yang di-`start` (postgres,
`node src/server.js`, dll) TIDAK bertahan antar pemanggilan tool terpisah di sandbox ini — filesystem
persisten, tapi proses background tidak. Jalankan `service postgresql start` DI DALAM perintah shell
yang sama dengan `node src/server.js` & `curl` verifikasi (satu pemanggilan tool bash, banyak perintah
dipisah `&&`/baris baru). **TAMBAHAN pelajaran Sesi 9**: kalau menguji skenario env var lewat
`env -i ... node src/server.js` untuk memvalidasi `validateEnv.js`, SELALU bungkus dengan `timeout`
DAN isi eksplisit semua env var yang relevan di command itu — `require('dotenv').config()` di
`server.js` akan diam-diam mengisi variabel yang tidak diset dari `backend/.env` yang sudah ada di
sandbox, sehingga skenario "variabel kosong" yang dimaksud bisa gagal jadi kosong sungguhan dan malah
membuat server benar-benar `listen()` tanpa batas waktu.

Prioritas berikutnya — kandidat test integrasi tambahan & production-hardening dasar sudah **HABIS
per Sesi 9** untuk yang jelas feasible tanpa kredensial eksternal. Yang tersisa murni butuh
lingkungan/kredensial di luar sandbox chat ini:
- Item #4 (validasi driver `s3.js` melawan bucket AWS S3/MinIO sungguhan) — butuh kredensial cloud.
- Item #1 (build Android di Android Studio) — butuh Android SDK.
- Item #4 (`docker compose up`, GitHub Actions runner sungguhan) — butuh akses registry Docker Hub /
  GitHub Actions runner asli (file-nya sendiri sudah diperbaiki/ditulis ulang di Sesi 9, tinggal
  divalidasi jalan).
- Isi `MIDTRANS_SERVER_KEY` & `MAPS_API_KEY` asli — butuh kredensial produksi milik sendiri.
- 🟡 audit trail untuk jalur `x-admin-key` (lihat Bagian 7) — belum disentuh, murni soal desain
  (tabel log baru + tulis ke situ tiap kali endpoint admin dipanggil lewat jalur shared-key), feasible
  di sandbox ini kalau prioritas berikutnya ingin ke arah situ.
- Opsional (bukan gap yang secara eksplisit tercatat sebelumnya, disebut lagi dari Sesi 8): test
  integrasi untuk endpoint `workshops` sisi menulis (`POST`/review) di luar yang sudah ada, dan
  dashboard `/mechanic` belum pernah dites lewat headless browser (Puppeteer tersedia di sandbox ini
  tapi belum pernah dicoba) — ini juga akan menjadi cara paling meyakinkan untuk memverifikasi
  CSP `helmet` (Sesi 9) benar-benar tidak memblokir apa pun di browser sungguhan, bukan cuma
  ditelusuri manual lewat pembacaan kode seperti di Sesi 9.

**Rutinitas awal sesi yang disarankan (tetap berlaku)**: setelah `npm install` & setup DB test,
jalankan `npm audit` sekali sebelum mulai kerja, supaya kerentanan baru (kalau ada) tertangkap di
awal, bukan ketemu tidak sengaja di tengah kerja lain.


