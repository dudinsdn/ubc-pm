# UBC Postman

Postman workspace untuk testing dan eksplorasi
REST API **Universal Business Core (UBC)**.

Repository ini berisi collection, environment, dan konfigurasi Postman yang
digunakan untuk pengujian API UBC.

## Usage

Import atau buka repository ini menggunakan Postman, kemudian pilih
environment yang sesuai dan jalankan collection yang tersedia.

Collection mengikuti kontrak API UBC dan digunakan untuk pengujian endpoint,
response, assertion, serta flow antar resource.

Untuk menjalankan `UBC_End_To_End`, isi collection variable
`platform_admin_token` dengan nilai yang sama seperti `PLATFORM_ADMIN_TOKEN`
pada backend. Collection mendaftarkan Tenant sebagai `pending`, mengaktifkannya
melalui `/platform/tenants/{tenant_id}/status`, lalu menguji activation gate
untuk status `pending` dan `suspended`.
