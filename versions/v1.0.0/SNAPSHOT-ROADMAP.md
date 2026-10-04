> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# SNAPSHOT-ROADMAP v1.0.0 — Salinan Beku

Salinan beku janji v1.0.0 pada 04 Okt 2026. Tidak diubah lagi. Perubahan masa depan dicatat di `roadmap/` living dan dirilis sebagai versi baru.

## Janji Beku

- Lapak terkurasi: 1 produsen 1 lapak aktif, verifikasi awal 1x24 jam kerja, tayang atau tolak beralasan 2x24 jam kerja, audit lapak tiap 90 hari.
- Katalog Varian 1: 6 kategori baku, stok real-time integer, harga IDR kelipatan Rp500, foto utama minimal 800x800, stok 0 otomatis non-tayang.
- Alur beli: keranjang multi-lapak per sub-pesanan, kunci stok 30 menit, batas bayar 30 menit, konfirmasi lapak maksimal 2 jam jam kerja, serah ke trip Lumbung, terima manual atau otomatis 2x24 jam, ulasan maksimal 7 hari.
- Keuangan: komisi 8 persen (hampers 10 persen, lapak baru 5 persen 30 hari), biaya layanan Rp2.000 per checkout, escrow kasir Pasaree, payout T+1 via Lumbung, rekonsiliasi harian.
- Target: lapak aktif minimal 60 persen per bulan, konfirmasi tepat waktu minimal 90 persen, selesai per lapak aktif minimal 8 per bulan, retur bermasalah maksimal 2 persen, waktu kurasi median maksimal 36 jam.
- Sistem: mobile KMP Android 9 dan iOS 16 tanpa desktop, web Bun 1.4 dan Svelte 5 tanpa Electron dan Tauri, backend tunggal pasaree-backend-service, DB pasaree, JWT audien pasaree, bayar tidak pernah offline.

## Sumber

- Dibekukan dari folder `lapak/`, `katalog/`, `produk/`, `platform/`, `keuangan/`, `metrik/` Varian 1 ditambah definisi lapak aktif. File lama sudah dipindah dengan `git mv` dan dihapus dari lokasi asal.

## Batasan

Batasan dokumen ini: hanya salinan janji saat v1.0.0 disetujui. Tidak menjadi acuan operasional terkini; acuan terkini ada di `roadmap/TIMELINE.md` dan `roadmap/MILESTONE.md`.
