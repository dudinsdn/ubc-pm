# UBC Postman

Postman workspace untuk testing dan eksplorasi
REST API **Universal Business Core (UBC)**.

Repository ini berisi collection, environment, dan konfigurasi Postman yang
digunakan untuk pengujian API UBC.

Peta ownership DTO dan aturan migrasi collection ada di
[`DTO_OWNERSHIP.md`](DTO_OWNERSHIP.md). Migrasi folder DTO dilakukan bertahap
agar runner integrasi yang sudah tervalidasi tidak rusak.

## Environment lokal

Pilih environment **UBC Local** di Postman, lalu isi **local value**:

- `platform_admin_token` dengan nilai `PLATFORM_ADMIN_TOKEN` dari backend;
- `platform_operator_email` dengan email operator lokal;
- `platform_operator_password` dengan password operator lokal.

Jangan masukkan nilai rahasia tersebut ke initial/shared value atau commit Git.
Collection mencoba bootstrap operator secara idempoten, lalu login dan menyimpan
JWT platform sebagai collection variable `platform_access_token`. Token
bootstrap tidak digunakan untuk endpoint control plane lainnya.

Setelah environment aktif, tiga collection product boundary dapat dibuka di
Collection Runner. Setup lintas boundary dijalankan melalui
`UBC_Integration_Runner`:

- `UBC_Business_App` untuk DTO Auth/Membership, Core, Workshop, Laundry,
  offline-first, dan security;
- `UBC_Platform_Admin` untuk DTO platform-auth, tenant lifecycle, subscription,
  dan audit;
- `UBC_Customer_App` untuk DTO identity-link, invitation, portal auth, dan
  portal read-only;
- `UBC_Operations` untuk liveness, readiness, dan metrics milik adapter API;
- `UBC_Integration_Runner` untuk flow berurutan yang membutuhkan setup
  lintas boundary.

Request di dalam ketiga collection tersebut kini dikelompokkan berdasarkan
ownership DTO (`auth`, `core`, `subscriptions`, `identity-link`, dan seterusnya).
`UBC_Integration_Runner` menyimpan flow end-to-end berurutan yang membutuhkan
setup lintas boundary. Runner dibagi menjadi folder fase dependency-safe:
onboarding/activation, Core flow, offline sync, Laundry, Workshop cross-tenant,
negative contracts, Customer Portal, cleanup, lalu Auth/security. Jalankan runner ini untuk
validasi perjalanan lengkap, bukan sebagai pengganti collection DTO.

Collection produk hanya memuat endpoint milik DTO/API boundary-nya. Setup yang
menyentuh boundary lain dijalankan melalui `UBC_Integration_Runner` atau fixture
yang disediakan secara eksplisit.

## Usage

Import atau buka repository ini menggunakan Postman, kemudian pilih
environment yang sesuai dan jalankan collection yang tersedia.

Collection mengikuti kontrak API UBC dan digunakan untuk pengujian endpoint,
response, assertion, serta flow antar resource.

Untuk sementara, tiga product collection berisi negative/error-response yang
dapat berjalan tanpa fixture: invalid payload/credentials dan missing token pada
DTO masing-masing. Happy path, optimistic concurrency, cross-tenant, entitlement,
dan lifecycle ber-state tetap berada di `UBC_Integration_Runner`.

Product collection tidak membuat atau membagikan resource lintas boundary;
karena itu setiap run tidak bergantung pada collection variables dari run lain.

`UBC_Operations` berdiri sendiri agar endpoint operasional yang tidak memiliki
DTO produk tidak tercampur ke Business, Platform, Customer, atau integration flow.

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
