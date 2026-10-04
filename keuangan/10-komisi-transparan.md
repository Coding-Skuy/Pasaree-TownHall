# 10 — Komisi Transparan

> Varian 1. Komisi dibebankan ke lapak, bukan pembeli. Kas dan payout dieksekusi via Lumbung (keuangan).

## 1. Tarif Varian 1

- Tarif default: **8%** dari subtotal per sub-pesanan (sebelum ongkir dan biaya layanan).
- Kategori `paket-hampers`: 10% (kemas + kurasi tambahan).
- Promo pembuka lapak baru: 5% selama 30 hari pertama sejak produk pertama tayang, otomatis.
- PPN/beban pajak bila ada ditampilkan terpisah, bukan di dalam persen komisi.

Rumus per baris (integer IDR, bulatkan ke bawah):

```
komisi_baris = floor(harga_idr * qty * tarif_persen / 100)
payout_lapak = subtotal - sum(komisi_baris) - penalti (bila ada)
```

Biaya layanan pembeli (Rp2.000 per checkout Varian 1) masuk ke operasional Pasaree, bukan mengurangi payout lapak. Ongkir 100% diteruskan ke kas trip Lumbung.

## 2. Tampil di Produk & Checkout

- Halaman lapak (web): kalkulator komisi + estimasi payout per varian.
- Checkout pembeli (mobile): menampilkan "Lapak menerima RpX setelah komisi 8%" sebagai info transparansi, tanpa menambah total bayar.
- Struk: subtotal, ongkir, biaya layanan, total bayar, komisi per lapak, estimasi payout.

## 3. Aliran Dana (Escrow via Lumbung)

1. Pembeli bayar → kasir Pasaree menahan dana (`escrow_id`).
2. Serah-terima trip tercatat → escrow dilepas.
3. Lumbung mengeksekusi payout ke rekening lapak T+1 (hari kerja) setelah dipotong komisi.
4. Retur/refund diselesaikan dari escrow bila belum dilepas; bila sudah payout, dipotong dari payout berikutnya + notifikasi.

## 4. Rekonsiliasi

- Rekonsiliasi harian otomatis Pasaree ↔ Lumbung: `pesanan_id, escrow_id, subtotal, komisi, ongkir, payout, status`.
- Selisih > Rp1.000 atau status menggantung > 2×24 jam → tiket rekonsiliasi prioritas tinggi ke admin + lapak diberi tahu.
- Laporan lapak (web): harian, mingguan, bulanan — unduh CSV. Audit trail 2 tahun.

## 5. Perubahan Tarif

- Perubahan tarif default memerlukan pengumuman 14 hari di web + notifikasi ke semua lapak aktif.
- Tarif promo lapak baru tidak dapat diubah di tengah periode berjalan.
- Riwayat tarif tersimpan append-only: `tarif_persen, berlaku_mulai, diubah_oleh, alasan`.

## 6. Sanksi Terkait Komisi

- Transaksi luar kasir untuk pesanan katalog: penalti 25% dari nilai transaksi + pembekuan 30 hari.
- Manipulasi harga untuk mengakali komisi: takedown + tutup permanen pada pengulangan.
