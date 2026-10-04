> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 10 — Alur Sistem

## Urutan Modul Bersama dan Permukaan

Sumber isi lama: `produk/30-modul-KMP-bersama.md` yang dipindah dengan `git mv` ke file ini, digabung dengan `platform/10-matriks-KMP-web.md`, `platform/40-mobile-KMP.md`, `platform/50-web-bun-svelte.md`, dan `platform/60-offline-sinkron.md`.

### Struktur Modul KMP

```
shared/
  src/commonMain/
    model/        # DTO katalog, keranjang, pesanan, trip (kotlinx.serialization)
    domain/       # use-case: CariProduk, PratinjauCheckout, BuatPesanan, KonfirmasiTerima
    network/      # Ktor client, endpoint, paginasi cursor, retry + idempotency-key
    storage/      # SQLDelight: keranjang, cache katalog, antrean offline
    sync/         # pekerja sinkronisasi, resolusi konflik server-menang untuk stok dan harga
    util/         # format IDR, format waktu, ULID, logger
  src/androidMain/  # DataStore, WorkManager scheduler
  src/iosMain/      # NSUserDefaults-backed settings, BGTask scheduler
composeApp/
  src/commonMain/ # UI Compose Multiplatform + Navigation3 graph
```

Dependensi dikunci: Kotlin 2.1.x, Compose Multiplatform 1.7.x, Navigation3 1.0.x, Ktor 3.x, kotlinx.serialization 1.8.x, kotlinx.coroutines 1.9.x, SQLDelight 2.x, Koin 4.x. Tanpa modul desktop dan tanpa target JVM-desktop.

### Matriks KMP vs Web

Mobile KMP milik pembeli: jelajah katalog utama, detail dan ulasan, keranjang checkout bayar satu-satunya, lacak dan konfirmasi terima. Web Bun milik lapak dan admin: etalase baca, tayang produk satu-satunya, tulis stok dan harga, konfirmasi dan serah-trip lapak, antrean kurasi dan takedown, ringkasan komisi dan payout. Aturan anti-duplikasi: total, ongkir, dan biaya layanan hanya dihitung server; model katalog tunggal menghasilkan DTO Kotlin dan TS dalam satu rilis minor; auth terpisah per peran dan token tidak dapat dipertukarkan; trip Lumbung hanya dibaca proksi.

### Layar Mobile dan Web

- Mobile Navigation3: Beranda, Pencarian, DetailProduk, Keranjang, Checkout, Bayar, StatusPesanan, PesananSaya, Ulasan. Rute type-safe serializable; back-stack pulih setelah rotasi dan process-death; deep-link `chefgenie://pasaree/produk/{id}` dan `https://pasaree.chefgenie.id/p/{id}`. Cold start maksimal 2,5 detik; paging 20 per halaman; Coil3 128 MB; stok tidak di-cache di atas 30 detik.
- Web SvelteKit: rute publik `p/[id]` dan `lapak/[slug]` SSR etalase; rute lapak `dasbor`, `produk`, `stok`, `pesanan-masuk`; rute kurator `kurasi`, `takedown`; rute admin `kategori`, `komisi`, `audit`. Semua panggilan API lewat `+page.server.ts` dan `+server.ts`; JWT tidak di localStorage; peran dijaga di `hooks.server.ts`. Stack dikunci: Bun 1.4.x, Svelte 5, SvelteKit 2, TypeScript 5.9.x, Vite 6; tanpa Electron dan Tauri.

## Urutan Offline dan Sinkron

- Mobile offline-first; web draf-tahan; kebenaran akhir selalu server. Bayar tidak pernah offline: tombol bayar nonaktif saat luring.
- SQLDelight tabel: cache_produk berisi id, payload_json, diperbarui_pada, ttl; keranjang berisi varian_id, qty, harga_snapshot_idr, diperbarui_pada; antrean_aksi berisi id, jenis, payload_json, idempotency_key, percobaan, dibuat_pada. Jenis antrean: TAMBAH_KERANJANG, CHECKOUT, KONFIRMASI_TERIMA, ULASAN.
- Online memicu SyncWorker FIFO satu per satu. Server memvalidasi stok, harga, trip terkini; berubah mengembalikan 409 dengan angka terbaru dan item antrean menjadi BUTUH_TINJAUAN. Gagal jaringan memakai backoff 5 detik, 30 detik, 5 menit maksimal 24 jam lalu kedaluwarsa.
- Resolusi konflik: stok dan harga dimenangkan server; qty keranjang digabung max lokal server lalu pratinjau ulang; produk takedown dihapus dari keranjang; trip penuh meminta pilih trip lain dengan kunci stok diperpanjang 15 menit; ulasan ganda diabaikan via kunci idempotensi.
- Web: draf autosave localStorage 10 detik dan beforeunload; aksi gagal ke IndexedDB outbox dengan tombol kirim ulang. Indikator: LURING, MENYINKRONKAN dengan n antre, TERKINI, BUTUH_TINJAUAN.

## Batasan

Batasan dokumen ini: hanya urutan sistem, modul, matriks permukaan, dan aturan sinkron. Formula bisnis ada di BRD keuangan-komisi dan FSD model data serta kontrak. Di luar batas: desain visual dan merek. Target mutu: unit commonTest 100 persen untuk use-case domain; UI test tambah-ke-keranjang sampai bayar dengan mock API; matriks perangkat Android 9 dan iOS 16; tidak ada blok `expect` yang belum diimplementasikan.
