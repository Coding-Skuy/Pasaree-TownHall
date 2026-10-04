> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# MILESTONE Pasaree — Status Living

Legenda status: todo berarti belum mulai, doing berarti sedang berjalan, done berarti selesai terverifikasi.

## Milestone v1.0.0

- M-001 Piagam kurasi berlaku dan dewan kurasi terbentuk — status: done. Bukti: `versions/v1.0.0/BRD/00-ikhtisar.md` dan `BRD/10-lapak-kurasi.md` memuat kriteria, larangan, dan SLA 1x24 jam verifikasi lapak serta 2x24 jam tayang produk.
- M-002 Katalog Varian 1 tayang dengan stok real-time — status: doing. Target: 60 produk tayang lintas 6 kategori baku, ID ULID, uang integer IDR, stok 0 otomatis non-tayang. Bukti: `versions/v1.0.0/FSD/20-model-data.md`.
- M-003 Alur beli multi-lapak ujung-ke-ujung berjalan — status: doing. Target: checkout multi-lapak dikelompokkan per sub-pesanan, kunci stok 30 menit, bayar 30 menit, konfirmasi lapak maksimal 2 jam. Bukti: `versions/v1.0.0/PRD/20-alur.md`.
- M-004 Kontrak API mobile dan web dibekukan — status: doing. Target: mobile KMP membaca katalog dan membayar idempoten; web lapak menulis draf, stok, dan pesanan masuk via proksi server. Bukti: `versions/v1.0.0/FSD/30-kontrak.md`.
- M-005 Komisi transparan dan escrow via Lumbung berjalan — status: doing. Bukti: tarif 8 persen (hampers 10 persen), payout T+1, rekonsiliasi harian tanpa selisih di atas Rp1.000. Acuan: `versions/v1.0.0/BRD/30-keuangan-komisi.md`.
- M-006 Lapak aktif minimal 60 persen per bulan — status: todo. Syarat ukur: 3 produk tayang, 1 pesanan SELESAI, konfirmasi tepat waktu minimal 80 persen. Acuan: `versions/v1.0.0/PRD/30-kriteria.md`.
- M-007 JWT audien pasaree ditegakkan di semua backend — status: doing. Bukti: token tanpa audien pasaree ditolak 401; proksi Lumbung memakai token layanan internal. Basis data terpisah `pasaree`.
- M-008 Audit lapak 90 hari pertama — status: todo. Syarat: produk tanpa penjualan dan tanpa update stok 90 hari diputuskan pertahankan atau arsipkan oleh kurator.

## Aturan Pembaruan

- Status diubah hanya oleh pemilik TownHall Pasaree dengan bukti tanggal. Milestone yang sudah done tidak dihapus, hanya ditambah catatan verifikasi.

## Batasan

Batasan dokumen ini: hanya status milestone dan bukti ringkas. Rincian angka ada di `versions/v1.0.0/BRD/` dan `versions/v1.0.0/PRD/30-kriteria.md`. Dokumen ini tidak mengubah janji beku v1.0.0.
