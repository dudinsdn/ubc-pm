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
