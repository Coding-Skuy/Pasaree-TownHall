# 30 — Modul KMP Bersama

> Varian 1. Lokasi: aplikasi mobile beli (Android+iOS). Pola: modul `shared` Kotlin Multiplatform dipakai oleh `composeApp` (Compose Multiplatform + Navigation3). Tanpa desktop.

## 1. Struktur Modul

```
shared/
  src/commonMain/
    model/        # DTO katalog, keranjang, pesanan, trip (kotlinx.serialization)
    domain/       # use-case: CariProduk, PratinjauCheckout, BuatPesanan, KonfirmasiTerima
    network/      # Ktor client, endpoint, paginasi cursor, retry + idempotency-key
    storage/      # SQLDelight: keranjang, cache katalog, antrean offline
    sync/         # pekerja sinkronisasi, resolusi konflik (server-menang untuk stok/harga)
    util/         # format IDR, format waktu, ULID, logger
  src/androidMain/  # DataStore, WorkManager scheduler
  src/iosMain/      # NSUserDefaults-backed settings, BGTask scheduler
composeApp/
  src/commonMain/ # UI Compose Multiplatform + Navigation3 graph
```

## 2. Dependensi Kunci (Varian 1, dikunci)

- Kotlin 2.1.x, Compose Multiplatform 1.7.x, Navigation3 1.0.x.
- Ktor 3.x (content-negotiation JSON), kotlinx.serialization 1.8.x, kotlinx.coroutines 1.9.x.
- SQLDelight 2.x untuk cache offline. DataStore untuk preferensi.
- Koin 4.x untuk DI di common code.

## 3. Tanggung Jawab per Lapisan

| Lapisan | Isi | Aturan |
|---|---|---|
| model | DTO persis kontrak `produk/20-*` | tidak ada logika bisnis; field `hargaIdr: Int` |
| domain | UseCase murni + `Result<DomainError>` | tanpa import Android/iOS API; dapat diuji unit 100% |
| network | `PasareeApi` interface + `KtorPasareeApi` | timeout 15 dtk, retry 2× untuk GET idempoten saja; POST bayar tidak auto-retry tanpa kunci idempotensi |
| storage | DAO keranjang, cache produk (TTL 5 mnt), antrean aksi offline | tulis lokal dulu, lalu sinkron (lihat `platform/60-offline-sinkron.md`) |
| sync | `SyncWorker` menarik delta via `diperbarui_pada` | konflik stok/harga: server menang; konflik qty keranjang: gabung max(lokal, server) |

## 4. Navigasi (Navigation3)

Graf Varian 1:

```
Beranda → Pencarian → DetailProduk({produkId}) → Keranjang → Checkout({tripId}) → Bayar({pesananId}) → StatusPesanan({pesananId})
Beranda → PesananSaya → DetailPesanan({pesananId}) → Ulasan
```

- Semua rute memakai type-safe route (`@Serializable data class DetailProduk(val produkId: String)`).
- State back-stack dipulihkan setelah rotasi dan process-death via `rememberNavBackStack`.
- Deep-link: `chefgenie://pasaree/produk/{id}` dan `https://pasaree.chefgenie.id/p/{id}` membuka DetailProduk.

## 5. Aturan UI Bersama

- Format harga: `Rp56.000` via `formatIdr()` tunggal di shared. Dilarang format manual di composeApp.
- Gambar via Coil3 + cache disk 128 MB. Placeholder kategori baku bila gagal muat.
- Aksesibilitas: label konten Indonesia untuk tombol beli, rating, dan status pesanan.
- Offline banner: tampil bila `sync.status == LURING`; tombol "Coba lagi" memicu `SyncWorker.now()`.

## 6. Pengujian Wajib

- Unit commonTest: kalkulasi total vs pratinjau server (klien tidak boleh dipercaya), parsing cursor, konflik sinkron.
- UI test: tambah-ke-keranjang → checkout → bayar (mock API), verifikasi idempotency-key terkirim.
- Matriks perangkat: Android 9+ dan iOS 16+. Tanpa target desktop/JVM — build desktop dimatikan di `gradle.properties` (`org.gradle.project.excludeDesktop=true` setara).
