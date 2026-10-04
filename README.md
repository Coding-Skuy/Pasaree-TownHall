# Pasaree-TownHall

> TownHall **Marketplace & Commerce** ChefGenie — lapak, katalog, produsen lokal. Fulfillment via trip Lumbung.

## 1. Peran

- **Menjual:** pendaftaran lapak, kurasi, tayang produk, stok & harga (Web lapak/admin).
- **Membeli:** jelajah katalog, keranjang, checkout, bayar, lacak, ulasan (Mobile beli).
- **Tidak dilakukan di sini:** armada, gudang, jadwal trip, kas/payout — seluruhnya milik **Lumbung-TownHall** dan diakses via integrasi proksi.
- **Dapur bersama** (Pawonee/Pedaree) menjadi jalur verifikasi higiene produsen pangan.

## 2. Peta Folder

```
lapak/      00-piagam-kurasi.md         Prinsip, kriteria, larangan, SLA kurasi
            10-sop-tayang-produk.md     SOP draf → tayang, peran, status
katalog/    10-model-data.md            Skema Lapak/Produk/Varian/Media + DTO Kotlin & TS
produk/     10-alur-beli.md             Status pesanan, checkout multi-lapak, retur
            20-kontrak-api-KMP-mobile.md REST untuk mobile (katalog, keranjang, bayar, trip proksi)
            21-kontrak-api-web-bun.md   REST untuk web (draf, stok, pesanan masuk, kurasi)
            30-modul-KMP-bersama.md     Struktur shared/domain/network/storage/sync
platform/   10-matriks-KMP-web.md       Pembagian mobile vs web + aturan anti-duplikasi
            40-mobile-KMP.md            Android+iOS, Compose Multiplatform + Navigation3
            50-web-bun-svelte.md        Bun 1.4.x + Svelte 5 + SvelteKit 2 + TS 5.9.x
            60-offline-sinkron.md       Offline-first, antrean idempoten, konflik server-menang
keuangan/   10-komisi-transparan.md     Tarif 8% (hampers 10%), escrow, payout via Lumbung
metrik/     10-lapak-aktif.md           Definisi lapak aktif + target Varian 1
```

## 3. Stack (dikunci Varian 1)

- **Mobile beli:** Kotlin Multiplatform (Kotlin 2.1.x) + Compose Multiplatform 1.7.x + Navigation3 1.0.x. Target **Android 9+ dan iOS 16+**. Tanpa desktop (tidak ada modul desktopApp/target JVM-desktop).
- **Web lapak/admin:** Bun 1.4.x + Svelte 5 + SvelteKit 2 + TypeScript 5.9.x. Tanpa desktop (tanpa Electron/Tauri; via browser).
- **Kontrak bersama:** ID ULID string, waktu ISO-8601 UTC, uang integer IDR, paginasi cursor, idempotency-key untuk bayar dan aksi antre.
- **Offline:** SQLDelight + SyncWorker (mobile), autosave + outbox IndexedDB (web). Bayar tidak pernah offline.

## 4. Tautan Lumbung (Distribusi & Keuangan)

Pasaree membaca dan memproksi, tidak memiliki sendiri:

- **Distribusi (trip):** `GET /trip` proksi — `trip_id, jadwal_berangkat, rute, titik_serah, kapasitas, ongkir`. Serah-terima pindaian kurir menjadi dasar lepas escrow. Lihat `produk/10-alur-beli.md` §6 dan `produk/20-kontrak-api-KMP-mobile.md` §5.
- **Keuangan (kas/payout):** escrow kasir Pasaree → payout T+1 via Lumbung setelah dipotong komisi transparan. Rekonsiliasi harian. Lihat `keuangan/10-komisi-transparan.md` §3–4.
- **Titik serah:** setiap lapak wajib mengisi `titik_serah_lumbung_id` (lihat `katalog/10-model-data.md` §2.2). Pengiriman di luar trip memerlukan persetujuan kurator + catat manual.

## 5. Mulai Cepat

1. Baca `lapak/00-piagam-kurasi.md` lalu `lapak/10-sop-tayang-produk.md`.
2. Pahami data di `katalog/10-model-data.md`, alur uang di `keuangan/10-komisi-transparan.md`.
3. Mobile: `platform/40-mobile-KMP.md` + `produk/30-modul-KMP-bersama.md` + `produk/20-kontrak-api-KMP-mobile.md`.
4. Web: `platform/50-web-bun-svelte.md` + `produk/21-kontrak-api-web-bun.md`.
5. Sinkron: `platform/60-offline-sinkron.md`. Target: `metrik/10-lapak-aktif.md`.

Varian 1 — Indonesia penuh, tanpa placeholder.
