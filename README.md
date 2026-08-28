# UBC Postman

Postman workspace untuk testing dan eksplorasi
REST API **Universal Business Core (UBC)**.

Repository ini berisi collection, environment, dan konfigurasi Postman yang
digunakan untuk pengujian API UBC.

## Environment lokal

Pilih environment **UBC Local** di Postman, lalu isi **local value**:

- `platform_admin_token` dengan nilai `PLATFORM_ADMIN_TOKEN` dari backend;
- `platform_operator_email` dengan email operator lokal;
- `platform_operator_password` dengan password operator lokal.

Jangan masukkan nilai rahasia tersebut ke initial/shared value atau commit Git.
Collection mencoba bootstrap operator secara idempoten, lalu login dan menyimpan
JWT platform sebagai collection variable `platform_access_token`. Token
bootstrap tidak digunakan untuk endpoint control plane lainnya.

Setelah environment aktif, tiga collection product boundary dapat dijalankan
langsung dari Collection Runner tanpa menambahkan parameter lewat terminal:

- `UBC_Business_App` untuk API Tenant, Auth/Membership, Core, Workshop,
  Laundry, dan kontrak offline-first;
- `UBC_Platform_Admin` untuk control plane online-first;
- `UBC_Customer_App` untuk identity linking, invitation/claim, login portal,
  dan data Customer read-only.

Setiap collection menyiapkan dependency minimumnya sendiri. Request setup yang
menyentuh boundary lain bukan coverage utama collection tersebut.

## Usage

Import atau buka repository ini menggunakan Postman, kemudian pilih
environment yang sesuai dan jalankan collection yang tersedia.

Collection mengikuti kontrak API UBC dan digunakan untuk pengujian endpoint,
response, assertion, serta flow antar resource.

`UBC_Business_App` menjalankan flow bisnis lengkap, termasuk Core, Auth,
Workshop, Laundry, idempotency, optimistic concurrency, negative cases,
incremental sync, dan cleanup berurutan. Bootstrap Platform, subscription, dan
aktivasi Tenant hanya berfungsi sebagai setup agar collection mandiri.

`UBC_Platform_Admin` mencakup bootstrap/login operator, daftar dan detail
Tenant, subscription plans, assignment/read/cancel subscription, lifecycle
Tenant, audit events, serta pemeriksaan access gate dari sisi Business App.

`UBC_Customer_App` mencakup setup Business/Customer/Transaction, direct identity
linking, invitation create/revoke/claim, replay protection, portal login dengan
JWT terpisah, isolasi token, daftar profil, dan transaksi read-only. Mutation
Customer tetap dilakukan oleh Owner sebagai setup Business App; Customer App
tidak memiliki endpoint mutation Customer.

## Security hardening scenarios

`UBC_Business_App` juga memuat skenario Tahap 5 untuk permission matrix,
perlindungan Owner terakhir, dan revocation token:

- `A10A Login Staff Tenant B` membuat token Staff yang terikat Membership.
- `A17` dan `A18` memastikan Staff mendapat `403` untuk operasi struktural.
- `A19` dan `A20` memastikan Owner terakhir tidak dapat didemote atau dicabut.
- `A12A` memastikan token Staff lama ditolak `401 access_revoked` setelah role
  berubah lewat `A12`.
- `S02`, `S03`, dan `S04` memeriksa payload 1 MiB serta CORS.
- `S01 Rate Limit Login` harus dijalankan sebagai request terisolasi dengan
  Collection Runner sebanyak 21 iterasi; iterasi ke-21 harus menghasilkan
  `429 rate_limit_exceeded` dan `Retry-After: 60`.

Pastikan `CORS_ALLOWED_ORIGINS` backend memuat nilai `cors_allowed_origin`
sebelum menjalankan `S04`. Skenario security yang memakai rate limit dijalankan
terpisah dari flow utama agar window 60 detik tidak mengganggu request setup.
