# 40 — Mobile KMP (Beli)

> Varian 1. Target: Android 9+ dan iOS 16+. Framework: Kotlin Multiplatform + Compose Multiplatform + Navigation3. Tanpa desktop.

## 1. Struktur Proyek

```
pasaree-mobile/
  gradle/libs.versions.toml   # Kotlin 2.1.x, Compose 1.7.x, Navigation3 1.0.x, Ktor 3.x
  shared/                     # lihat produk/30-modul-KMP-bersama.md
  composeApp/                 # UI bersama
  androidApp/                 # entry Android (MainActivity, push FCM)
  iosApp/                     # entry iOS (SwiftUI host + push APNs)
```

Build desktop dimatikan: tidak ada modul `desktopApp`, tidak ada target `jvm(desktop)`.

## 2. Navigasi Navigation3

- Entry: `NavDisplay` tunggal dengan `rememberNavBackStack(Home)`.
- Rute type-safe serializable (Home, Cari, DetailProduk, Keranjang, Checkout, Bayar, StatusPesanan, PesananSaya, Ulasan).
- Transisi: fade + slide standar Material3. Back gesture Android dan swipe-back iOS didukung via Navigation3 defaults.
- Guard: rute Checkout/Bayar/PesananSaya memerlukan sesi; bila belum masuk, arahkan ke Masuk lalu kembali ke rute asal.

## 3. Layar Wajib Varian 1

1. Beranda: kategori, produk unggulan, trip terdekat.
2. Pencarian + filter (kategori, harga, rating, lapak).
3. Detail produk: foto, varian, stok real-time, tombol tambah, info lapak + skor fulfillment, ulasan.
4. Keranjang: grup per lapak, edit qty, pratinjau server.
5. Checkout: pilih trip + titik, catatan, voucher, rincian biaya server.
6. Bayar: metode VA/QRIS/e-wallet, hitung mundur 30 menit, retry idempoten.
7. Status pesanan + timeline + bukti trip + tombol konfirmasi terima/retur.
8. Ulasan: bintang + teks + foto.

## 4. Kinerja & Batas

- Cold start ≤ 2,5 dtk di perangkat menengah. Daftar katalog memakai lazy paging (20/halaman) + placeholder shimmer.
- Cache gambar Coil3 128 MB. Cache API katalog 5 menit, stok tidak di-cache > 30 detik.
- Ukuran APK/AAB target ≤ 35 MB (Android), IPA ≤ 45 MB (iOS) Varian 1.

## 5. Izin & Privasi

- Izin: notifikasi, kamera (hanya untuk foto ulasan). Lokasi opsional untuk titik trip terdekat, dengan opt-in.
- Data pembeli (nama, alamat titik, riwayat) tersimpan di akun server; hapus akun menghapus cache lokal + menandai anonimisasi server ≤ 30 hari.

## 6. Kriteria Selesai Varian 1

- [ ] Alur beli ujung-ke-ujung lolos di Android + iOS fisik.
- [ ] Checkout offline → antre → sinkron saat online (lihat `platform/60-offline-sinkron.md`).
- [ ] Tidak ada kode `expect` yang menyisakan `TODO`/`NotImplemented`.
