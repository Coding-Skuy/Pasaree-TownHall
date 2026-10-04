# 10 — Alur Beli

> Varian 1. Berlaku untuk Mobile KMP (pembeli) dan Web (lapak/admin + etalase baca). Pembayaran via kasir Pasaree, payout via Lumbung. Pengiriman fisik via trip Lumbung.

## 1. Peran

- **Pembeli:** menjelajah katalog, memesan via mobile (utama) atau web etalase.
- **Lapak/produsen:** konfirmasi pesanan, siapkan barang, serah ke trip.
- **Lumbung:** menyediakan trip (jadwal, rute, titik serah), mengantar, mencatat serah-terima.
- **Kasir Pasaree:** otorisasi bayar, tahan dana (escrow) hingga serah-terima, teruskan payout via Lumbung.

## 2. Status Pesanan

```
DRAF_KERANJANG → DIPESAN → DIBAYAR → DIKONFIRMASI_LAPAK → DISERAHKAN_KE_TRIP → DITERIMA_PEMBELI → SELESAI
Cabang: DIBATALKAN (dari DIPESAN/DIBAYAR/DIKONFIRMASI_LAPAK dengan aturan) | DIKEMBALIKAN (dari DITERIMA_PEMBELI ≤ 1×24 jam, khusus non-pangan segar)
```

## 3. Alur Langkah per Langkah

1. **Jelajah & keranjang (mobile utama).** Pembeli menambah varian dengan stok > 0. Keranjang tersimpan lokal + tersinkron ke akun (lihat `platform/60-offline-sinkron.md`). Validasi stok ulang saat checkout.
2. **Checkout.** Pembeli memilih: trip Lumbung (jadwal + titik ambil/antar), catatan per lapak, voucher bila ada. Sistem menghitung: subtotal + ongkir per trip + biaya layanan transparan. Komisi lapak tidak dibebankan ke pembeli (lihat `keuangan/10-komisi-transparan.md`).
3. **Bayar.** Kasir Pasaree memproses pembayaran. Batas bayar 30 menit untuk `DIPESAN`, lewat itu otomatis `DIBATALKAN` dan stok dikembalikan.
4. **Konfirmasi lapak.** Lapak wajib konfirmasi ≤ 2 jam jam kerja (07.00–19.00). Tanpa konfirmasi → otomatis batal + dana kembali penuh + penalti skor lapak.
5. **Siapkan & serah ke trip.** Lapak mengemas sesuai instruksi (label pesanan + tanggal produksi), menyerahkan pada trip yang dipilih. Kurir trip memindai serah-terima → status `DISERAHKAN_KE_TRIP`.
6. **Terima.** Pembeli mengonfirmasi terima di mobile (atau otomatis 2×24 jam setelah trip tiba). Dana escrow dilepas ke payout.
7. **Selesai & ulasan.** Pembeli dapat memberi ulasan 1–5 + foto ≤ 7 hari. Ulasan memengaruhi rating dan bobot pencarian.

## 4. Aturan Keranjang Multi-Lapak

- Satu checkout dapat memuat banyak lapak, tetapi dikelompokkan per lapak menjadi sub-pesanan dengan status masing-masing.
- Ongkir dihitung per trip, bukan per lapak. Jika sub-pesanan dibatalkan, ongkir tidak kembali kecuali seluruh checkout batal sebelum serah ke trip.
- Stok dikunci (reservasi) selama 30 menit pada status `DIPESAN`. Kegagalan bayar melepas kunci.

## 5. Pembatalan & Pengembalian

| Kondisi | Dana | Stok |
|---|---|---|
| Batal sebelum bayar | tidak ada gerakan dana | kunci dilepas |
| Batal setelah bayar, sebelum konfirmasi lapak | kembali 100% ≤ 1×24 jam | kembali |
| Batal oleh lapak setelah konfirmasi (stok/produksi gagal) | kembali 100% + voucher ongkir trip berikutnya | kembali |
| Batal oleh pembeli setelah konfirmasi | kembali subtotal − 10% penalti ke lapak, ongkir hangus bila sudah diserahkan ke trip | tidak kembali bila sudah diserahkan |
| Retur non-pangan segar ≤ 1×24 jam | kembali 100% setelah barang diterima lapak | bertambah bila layak jual |

Pangan segar tidak dapat diretur kecuali salah kirim/basi saat terima (bukti foto ≤ 3 jam).

## 6. Integrasi Trip Lumbung

- Pasaree membaca jadwal trip dari API Lumbung (distribusi): `trip_id, jadwal_berangkat, rute, titik_serah, kapasitas, ongkir`.
- Pasaree tidak membuat trip sendiri. Jika trip penuh/terlambat, pembeli memilih trip lain; lapak tidak boleh mengirim di luar trip tanpa persetujuan kurator + catat manual.
- Bukti serah-terima trip menjadi dasar pelepasan escrow. Sengketa mengacu pada log pindaian Lumbung.

## 7. Notifikasi Wajib

DIPESAN, DIBAYAR, DIKONFIRMASI_LAPAK, DISERAHKAN_KE_TRIP, DITERIMA_PEMBELI, SELESAI, DIBATALKAN — via push mobile + WhatsApp fallback untuk lapak. Kegagalan kirim notifikasi tidak membatalkan status, tetapi dicatat di audit.
