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

Setelah environment aktif, collection `UBC_End_To_End` dan
`UBC_Platform_Admin` dapat dijalankan langsung dari Collection Runner tanpa
menambahkan parameter lewat terminal.

## Usage

Import atau buka repository ini menggunakan Postman, kemudian pilih
environment yang sesuai dan jalankan collection yang tersedia.

Collection mengikuti kontrak API UBC dan digunakan untuk pengujian endpoint,
response, assertion, serta flow antar resource.

`UBC_Postgres_N2N_Runner` adalah collection baru yang ringkas untuk smoke
test lintas produk pada composition root PostgreSQL: bootstrap/login Platform,
register dan aktivasi Tenant, membuat Business/Customer/Transaction, kemudian
invitation/claim dan pembacaan data melalui Customer Portal. Jalankan collection
ini secara utuh dan berurutan dengan environment `UBC Local`.

`UBC_End_To_End` menjalankan seluruh flow bisnis, termasuk assignment
subscription dan aktivasi Tenant agar flow tetap mandiri. Collection ini juga
mencakup Customer Portal tahap 1 dan onboarding 2A: identity linking,
invitation/claim single-use, login dengan JWT portal terpisah, daftar profil
tertaut, riwayat transaksi read-only, pemisahan token, isolasi lintas Tenant,
idempotency/conflict, revoke/replay protection, dan penyembunyian data
soft-delete.
`UBC_Platform_Admin` adalah collection terpisah yang fokus pada daftar,
detail, subscription, aktivasi, suspend, dan reaktivasi Tenant.
