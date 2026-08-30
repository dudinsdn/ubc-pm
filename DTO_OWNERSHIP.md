# DTO ownership untuk Postman collections

Collection utama dipisahkan berdasarkan product boundary, dan setiap request
ditata menurut DTO/API adapter pemilik kontraknya. Flow lintas boundary menjadi
integration runner khusus.

## Target struktur

- `UBC_Business_App`: auth, core, workshop, laundry, offline-sync, security
- `UBC_Platform_Admin`: platform-auth, tenant-lifecycle, subscriptions, audit
- `UBC_Customer_App`: identity-link, invitation, portal-auth, portal-read
- `UBC_Operations`: livez, readyz, metrics milik adapter API dan tanpa DTO produk

## Aturan migrasi

1. DTO tetap berada di adapter pemiliknya.
2. Setup lintas boundary hanya berada di integration runner.
3. Nama request memakai resource/DTO, bukan urutan global.
4. Secret tidak boleh masuk initial/shared value atau commit.
5. Migrasi bertahap: inventaris, folder DTO, runner integrasi, lint/live validation.
6. Endpoint Operations tidak dimasukkan ke collection produk karena tidak
   memiliki DTO atau perimeter token Business, Platform, maupun Portal.
7. Folder `UBC_Integration_Runner` mengikuti fase dependency-safe; lintas DTO
   hanya diperbolehkan di runner ini untuk kebutuhan setup integrasi.
