# UBC Postman

Postman workspace untuk testing dan eksplorasi
REST API **Universal Business Core (UBC)**.

Repository ini berisi collection, environment, dan konfigurasi Postman yang
digunakan untuk pengujian API UBC.

## Environment lokal

Pilih environment **UBC Local** di Postman, lalu isi **local value**
`platform_admin_token` dengan nilai `PLATFORM_ADMIN_TOKEN` dari backend.
Jangan masukkan nilai token tersebut ke initial/shared value atau commit Git.

Setelah environment aktif, collection `UBC_End_To_End` dan
`UBC_Platform_Admin` dapat dijalankan langsung dari Collection Runner tanpa
menambahkan parameter lewat terminal.

## Usage

Import atau buka repository ini menggunakan Postman, kemudian pilih
environment yang sesuai dan jalankan collection yang tersedia.

Collection mengikuti kontrak API UBC dan digunakan untuk pengujian endpoint,
response, assertion, serta flow antar resource.

`UBC_End_To_End` menjalankan seluruh flow bisnis, termasuk aktivasi Tenant agar
flow tetap mandiri. `UBC_Platform_Admin` adalah collection terpisah yang fokus
pada daftar, detail, aktivasi, suspend, dan reaktivasi Tenant.
