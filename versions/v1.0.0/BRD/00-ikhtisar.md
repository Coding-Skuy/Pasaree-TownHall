> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 00 — Ikhtisar Pasaree

## Konteks

Divisi Pasaree adalah pasar PT ChefGenie. Tugasnya menjalankan lapak terkurasi produsen lokal, katalog dengan stok real-time, dan checkout transparan yang diantar via trip Lumbung. Prinsip Pasaree-first: produsen lokal dulu, katalog jujur, harga transparan, keamanan pangan, kemasan berkurang. Basis data tunggal: DB pasaree. Fulfillment fisik wajib via trip Lumbung; Pasaree tidak membangun armada, gudang, atau kas payout sendiri. Sumber isi lama: `lapak/00-piagam-kurasi.md` yang dipindah dengan `git mv` ke file ini.

## Kebutuhan Bisnis

- BR-001 Pasaree wajib menjalankan lapak terkurasi dengan aturan 1 produsen 1 lapak aktif; lapak diterima bila identitas terverifikasi, minimal 3 produk aktif, menyetujui komisi transparan dan payout via Lumbung, menyetujui SLA fulfillment, dan skor higiene minimal 80 untuk pangan.
- BR-002 Katalog wajib menampilkan stok real-time dan harga jujur: harga tampil adalah harga bayar sebelum ongkir, komisi tertera terpisah, dilarang mark-up tersembunyi. Stok 0 otomatis non-tayang.
- BR-003 Pendapatan Pasaree hanya komisi transparan: 8 persen dari subtotal per sub-pesanan, 10 persen untuk kategori paket-hampers, 5 persen promo lapak baru 30 hari. Ongkir 100 persen diteruskan ke kas trip Lumbung.
- BR-004 Sukses diukur sebagai lapak aktif: persen lapak aktif minimal 60 persen per bulan, konfirmasi tepat waktu minimal 90 persen, selesai per lapak aktif minimal 8 per bulan, retur bermasalah maksimal 2 persen, waktu kurasi median maksimal 36 jam.
- BR-005 SLA kurasi dikunci: pendaftaran lapak ke verifikasi awal 1x24 jam kerja; pengajuan produk ke tayang atau tolak beralasan 2x24 jam kerja; banding takedown 3x24 jam kerja; audit lapak aktif tiap 90 hari.
- BR-006 Setiap lapak wajib mengisi `titik_serah_lumbung_id`; pengiriman di luar trip memerlukan persetujuan kurator dan catat manual. Bukti serah-terima trip menjadi dasar pelepasan escrow.
- BR-007 Autentikasi dikunci: JWT Bearer dengan klaim audien tepat `pasaree`; token lintas divisi tanpa audien pasaree ditolak 401; proksi ke Lumbung memakai token layanan internal; bayar wajib memakai `idempotency-key`.

## Metrik

- Lapak aktif bulan berjalan, waktu kurasi median, konfirmasi tepat waktu, selesai per lapak, retur bermasalah, stok-0 kronis. Sumber: event pesanan, snapshot stok harian, log kurasi.

## Batasan

Batasan dokumen ini: hanya menyatakan kebutuhan bisnis dan angka ambang. Cara pemenuhan diatur di PRD, FRD, dan FSD. Di luar batas: penjadwalan trip, eksekusi payout, dan kas yang menjadi milik Lumbung-TownHall; verifikasi higiene dapur bersama milik Pawonee dan Pedaree; audit independen milik Titeny. Perubahan piagam wajib dicatat di CHANGELOG TownHall dengan nomor varian baru.
