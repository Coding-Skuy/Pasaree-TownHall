> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FRD 10 — Kebutuhan Fungsional

Dokumen ini menyatakan apa yang wajib dilakukan sistem, tanpa menyatakan cara implementasi.

## Lapak dan Kurasi

- FR-001 Sistem wajib mengelola lapak dengan aturan 1 produsen 1 lapak aktif, slug unik, status AKTIF, BEKU, TUTUP, dan titik_serah_lumbung_id wajib.
- FR-002 Sistem wajib menjalankan cek otomatis draf produk: judul, harga, stok, media, kategori, label pangan, dan duplikasi di bawah 85 persen kemiripan.
- FR-003 Sistem wajib mengelola antrean kurasi FIFO prioritas pangan segar dengan keputusan SETUJU, REVISI, TOLAK yang beraudit beserta snapshot konten.
- FR-004 Sistem wajib menegakkan status produk DRAF, MENUNGGU_KURASI, PERLU_REVISI, TAYANG, NONAKTIF_STOK_0, TAKEDOWN, ARSIP dengan transisi otomatis stok 0 dan kurasi ulang ringan.

## Katalog

- FR-101 Sistem wajib mengelola 6 kategori baku dan daftar tag baku maksimal 2 per produk.
- FR-102 Sistem wajib menolak tambah ke keranjang untuk varian stok 0 dan menampilkan stok real-time per varian.
- FR-103 Sistem wajib mencatat riwayat harga append-only dan menandai kurasi ulang ringan untuk kenaikan di atas 20 persen.
- FR-104 Sistem wajib memproksi jadwal trip Lumbung berisi trip_id, jadwal_berangkat, titik_serah, sisa kapasitas, ongkir dengan cache 60 detik dan flag basi.

## Beli dan Pesanan

- FR-201 Sistem wajib mengelompokkan checkout multi-lapak menjadi sub-pesanan per lapak dengan status masing-masing.
- FR-202 Sistem wajib mengunci stok reservasi 30 menit pada DIPESAN dan melepasnya saat batal atau gagal bayar.
- FR-203 Sistem wajib memproses bayar idempoten dengan kunci 24 jam tanpa tagihan ganda dan batas bayar 30 menit.
- FR-204 Sistem wajib mencatat timeline status DIPESAN, DIBAYAR, DIKONFIRMASI_LAPAK, DISERAHKAN_KE_TRIP, DITERIMA_PEMBELI, SELESAI, DIBATALKAN, DIKEMBALIKAN beserta bukti trip.
- FR-205 Sistem wajib mengelola ulasan 1 per produk per pesanan maksimal 7 hari setelah selesai.

## Keuangan

- FR-301 Sistem wajib menghitung komisi per baris integer IDR dibulatkan ke bawah: 8 persen default, 10 persen hampers, 5 persen promo lapak baru 30 hari.
- FR-302 Sistem wajib menahan dana escrow kasir hingga serah-terima trip lalu meneruskan payout via Lumbung T+1.
- FR-303 Sistem wajib menjalankan rekonsiliasi harian berisi pesanan_id, escrow_id, subtotal, komisi, ongkir, payout, status dengan ambang selisih Rp1.000.

## Lintas Segmen

- FR-401 Sistem wajib memakai ID ULID string, waktu ISO-8601 UTC, uang integer IDR, paginasi cursor, dan idempotency-key untuk bayar dan aksi antre.
- FR-402 Sistem wajib mencatat setiap aksi kurasi, stok, harga, dan pesanan dengan pembuat, waktu, dan versi untuk resolusi konflik server-menang.
- FR-403 Sistem wajib menegakkan hak peran: pembeli hanya pesanannya, produsen hanya lapaknya, kurator semua lapak untuk kurasi, admin penuh. JWT audien wajib `pasaree`; basis data wajib `pasaree`.

## Batasan

Batasan dokumen ini: hanya kebutuhan fungsional. Bahasa pemrograman, pustaka, basis data lokal, dan pola navigasi tidak diatur di sini dan hanya boleh muncul di FSD. Setiap kebutuhan di atas wajib punya uji penerimaan di PRD/30-kriteria.md.
