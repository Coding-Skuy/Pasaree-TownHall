# 10 — Lapak Aktif

> Varian 1. Definisi operasional untuk target, insentif, dan dasbor.

## 1. Definisi

**Lapak aktif bulan berjalan** = lapak berstatus `AKTIF` yang memenuhi seluruh syarat berikut dalam 30 hari terakhir:

1. Minimal 3 produk berstatus `TAYANG` dengan stok total > 0.
2. Minimal 1 pesanan `SELESAI` (bukan hanya DIPESAN/DIBAYAR).
3. Tingkat konfirmasi tepat waktu (≤ 2 jam) ≥ 80%.
4. Tanpa pelanggaran larangan mutlak yang belum dipulihkan.

Lapak yang gagal salah satu syarat menjadi `TIDAK_AKTIF_BULAN_INI` (label analitik, bukan status blokir) dan kehilangan slot promosi beranda.

## 2. Metrik Turunan

| Metrik | Rumus | Target Varian 1 |
|---|---|---|
| % lapak aktif | lapak aktif / total lapak AKTIF | ≥ 60% per bulan |
| Waktu kurasi median | median ajukan → tayang | ≤ 36 jam |
| Konfirmasi tepat waktu | sub-pesanan ≤ 2 jam / total | ≥ 90% |
| Selesai per lapak aktif | pesanan SELESAI / lapak aktif | ≥ 8/bulan |
| Retur bermasalah | retur disetujui bermasalah / selesai | ≤ 2% |
| Stok-0 kronis | produk 14 hari stok 0 / total tayang | ≤ 15% |

## 3. Sumber Data & Dasbor

- Sumber: event pesanan + snapshot stok harian + log kurasi (API web/mobile).
- Dasbor web admin: tren 12 minggu, daftar lapak berisiko (konfirmasi < 80%, stok-0 kronis, tanpa penjualan 21 hari).
- Notifikasi otomatis ke lapak: peringatan H-7 sebelum label non-aktif + saran aksi (restok, promo, ikut trip).

## 4. Tindak Lanjut

- Lapak non-aktif 2 bulan berturut-turut → review kurator: pembinaan, bekukan sementara, atau arsipkan produk tanpa penjualan.
- Lapak aktif 3 bulan berturut-turut + rating ≥ 4.5 → lencana `PRODUSEN_ANDALAN` + prioritas slot beranda + tarif promo even.
- Semua insentif tercatat dan dapat diaudit; tidak ada pengecualian manual tanpa tiket kurator.
