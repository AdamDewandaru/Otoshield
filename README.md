# OtoShield — Direktori Bengkel Transparan & Layanan Darurat

Proyek ini berisi implementasi awal (MVP scaffold) dari blueprint `otoshield_blueprint.md`:
aplikasi Android untuk membantu pengendara di Indonesia menemukan bengkel terpercaya,
mendapat estimasi biaya servis yang wajar, dan memanggil bantuan darurat di jalan.

## Struktur Proyek

```
otoshield/
├── backend/        # REST API (Node.js/Express) + PostgreSQL + Socket.io realtime
├── android-app/     # Aplikasi Android (Kotlin + Jetpack Compose, MVVM)
└── docs/             # Dokumentasi tambahan
```

## Status Implementasi

### Backend — SUDAH BERFUNGSI (lolos `npm install`, `node --check`, dan **18 unit test Jest, semua PASS**)
- Skema PostgreSQL lengkap: `users`, `vehicles`, `workshops`, `workshop_services`,
  `reviews`, `emergency_requests` (+ kolom `photo_url`), `cost_estimate_rules`,
  `service_history`, `payments`
- Data contoh (`seed.sql`) untuk 4 bengkel + kata kunci estimator
- REST API:
  - `POST /api/auth/register`, `POST /api/auth/login`
  - `GET/POST /api/vehicles`, `PUT /api/vehicles/:id/odometer`,
    `GET /api/vehicles/:id/reminders`, `GET/POST /api/vehicles/:id/history`
  - `GET /api/workshops` (filter radius pakai formula Haversine), `GET /api/workshops/:id`,
    `POST /api/workshops/:id/reviews`
  - `POST /api/emergency` (SOS, otomatis cari bengkel/towing radius 5km),
    `GET /api/emergency/:id`, `PUT /api/emergency/:id/status`,
    `POST /api/emergency/:id/photo` (upload foto kerusakan, multipart/form-data)
  - `POST /api/estimator/estimate` (Smart Cost Estimator berbasis kata kunci)
  - `POST /api/payments`, `GET /api/payments`, `GET /api/payments/:id`,
    `PUT /api/payments/:id/pay` (pembayaran digital — **simulasi**, lihat catatan di kode)
  - `GET /api/admin/emergency`, `PUT /api/admin/emergency/:id/status`, `GET /api/admin/workshops`
    (dilindungi header `x-admin-key`, dipakai oleh dashboard mekanik/operator)
- Socket.io untuk update posisi mekanik & status SOS secara realtime
- **Dashboard mekanik/operator** statis di `/mechanic` (HTML/CSS/JS, tanpa framework)
- **Production hardening (Sesi 9)**: security headers (`helmet`), rate limiting
  (`express-rate-limit` — umum + khusus login + khusus SOS), validasi environment variable saat
  boot (gagal-cepat kalau secret kosong/masih placeholder di production), CORS bisa dibatasi ke
  domain tertentu lewat `CORS_ORIGIN`, graceful shutdown (`SIGTERM`/`SIGINT`), response
  compression, request logging (`morgan`)
- Unit & integration test: `npm test` (Jest) — **159 test PASS**, meng-cover haversine, Smart Cost
  Estimator, seluruh middleware (auth, admin, staff, rate limit, validasi env), dan alur end-to-end
  (Postgres sungguhan) untuk auth, kendaraan, bengkel, SOS, payment (termasuk webhook expire/cancel),
  estimator, dan dashboard admin (role ADMIN/MECHANIC/WORKSHOP_STAFF)
- Dockerfile + `docker-compose.yml` (backend + PostgreSQL) — **ditulis tapi belum di-build**
  karena sandbox tidak punya akses ke Docker Hub (lihat catatan di masing-masing file)
- CI: `.github/workflows/backend-ci.yml` (npm ci, syntax check, test, audit)

### Android App — SCAFFOLD LENGKAP + AUTH + REALTIME (belum di-build/compile — lihat catatan di bawah)
- Arsitektur MVVM + Clean Architecture sesuai blueprint
- 5 alur utama: **Login/Register**, SOS Darurat (+ peta tracking realtime via Socket.IO),
  Direktori Bengkel (+ harga transparan), Smart Cost Estimator, Buku Servis Digital (cache offline via Room)
- Retrofit untuk komunikasi API, Room untuk cache lokal, Socket.IO client untuk update status/posisi
  mekanik realtime (dengan polling 15 detik sebagai jaring pengaman), manual Service Locator (DI)

## Cara Menjalankan Backend

```bash
cd backend
cp .env.example .env        # WAJIB isi JWT_SECRET & ADMIN_API_KEY dengan nilai acak sungguhan -
                             # server menolak boot di production kalau masih nilai contoh (lihat
                             # src/config/validateEnv.js), dan sekadar warning di development.
npm install
npm run migrate             # membuat tabel dari schema.sql
npm run seed                # (opsional) isi data contoh
npm test                    # jalankan unit test (tidak butuh koneksi database)
npm run dev                 # jalan di http://localhost:4000
```

Butuh PostgreSQL berjalan lokal (atau ganti `DATABASE_URL` ke instance cloud, misal Supabase/Neon/RDS).
Isi `CORS_ORIGIN` di `.env` dengan domain frontend/app resmi (dipisah koma kalau lebih dari satu)
sebelum deploy ke produksi — kosong/`*` berarti izinkan semua origin, cocok untuk development/emulator
Android tapi sebaiknya dibatasi di production (lihat `src/config/corsOrigin.js`).

Dashboard mekanik/operator tersedia di `http://localhost:4000/mechanic` setelah backend jalan —
masukkan nilai `ADMIN_API_KEY` dari `.env` di kolom "Admin API Key".

### Menjalankan dengan Docker (belum ditest — lihat catatan di docker-compose.yml)

```bash
cp .env.example .env        # di root proyek (BEDA dengan backend/.env) - isi JWT_SECRET,
                             # ADMIN_API_KEY, POSTGRES_PASSWORD dengan nilai acak sungguhan;
                             # docker compose akan MENOLAK START kalau ini kosong
docker compose up -d
docker compose exec backend npm run migrate
docker compose exec backend npm run seed
```

## Cara Membuka Android App

1. Buka folder `android-app/` di Android Studio (Koala atau lebih baru, disarankan).
2. Tambahkan API key Google Maps di `app/build.gradle.kts`
   (`manifestPlaceholders["MAPS_API_KEY"]`).
3. Sesuaikan `BASE_API_URL` di `app/build.gradle.kts`:
   - Emulator Android → `http://10.0.2.2:4000/api/` (default, otomatis mengarah ke localhost backend)
   - HP fisik → ganti ke IP lokal komputer, misal `http://192.168.1.10:4000/api/`
4. Sync Gradle, lalu jalankan di emulator/device.

## PENTING — Batasan Pekerjaan Sejauh Ini

Proyek ini dikerjakan di lingkungan sandbox tanpa Android SDK/Gradle terpasang dan tanpa akses
ke Maven Google (`dl.google.com`), sehingga kode Android **belum pernah dikompilasi/di-build**
secara otomatis oleh asisten. Kode sudah ditulis mengikuti sintaks Kotlin/Compose yang benar dan
sudah diperiksa secara manual (brace/paren balance, referensi antar file), tapi tetap perlu:
- Dibuka & di-sync di Android Studio untuk memverifikasi build sukses
- Diuji di emulator/device sungguhan

## Belum Dikerjakan (Roadmap Lanjutan)
- Build & perbaiki error kompilasi Android App di Android Studio sungguhan (prioritas utama —
  lihat bagian "PENTING" di atas)
- Sistem login/role khusus staf bengkel/mekanik (saat ini dashboard `/mechanic` masih pakai
  satu shared `ADMIN_API_KEY` untuk semua operator — lihat catatan di `admin.middleware.js`)
- Integrasi payment gateway sungguhan (Midtrans/Xendit dll — saat ini `/api/payments` masih
  simulasi, lihat catatan di `payments.controller.js`)
- Ganti storage foto kerusakan kendaraan dari disk lokal ke Firebase Storage/S3 (lihat catatan
  di `upload.middleware.js` — interface controller sudah didesain agar gampang diganti)
- Verifikasi nyata Dockerfile & docker-compose.yml (`docker build`/`docker compose up`) di mesin
  dengan akses internet normal
- Verifikasi nyata `.github/workflows/backend-ci.yml` dengan push ke repo GitHub
- Layar profil pengguna (edit nama/email/telepon) di Android App
- Google Maps API key asli (placeholder masih ada di `android-app/app/build.gradle.kts`)
