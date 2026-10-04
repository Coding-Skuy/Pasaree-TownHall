> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# Pasaree-TownHall — Divisi Pasar (Marketplace & Commerce) PT ChefGenie

## Peran Pasaree

Pasaree adalah divisi pasar PT ChefGenie: lapak terkurasi produsen lokal, katalog dengan stok real-time, dan checkout transparan. Fokus Pasaree-first: produsen lokal lolos kurasi, pembeli mendapat harga jujur sebelum ongkir, lapak menerima payout adil setelah komisi transparan. Skala Varian 1: lapak terkurasi dengan minimal 3 produk tayang per lapak, konfirmasi pesanan maksimal 2 jam jam kerja, kurasi maksimal 2x24 jam kerja. Sukses = lapak aktif: minimal 60 persen lapak berstatus AKTIF memenuhi syarat tiap bulan, konfirmasi tepat waktu minimal 90 persen, retur bermasalah maksimal 2 persen. Monetisasi: komisi transparan 8 persen dari subtotal per sub-pesanan (paket hampers 10 persen, promo lapak baru 5 persen selama 30 hari), ditambah biaya layanan pembeli Rp2.000 per checkout; ongkir 100 persen diteruskan ke kas trip Lumbung. Basis data: DB pasaree (terpisah dari divisi lain). Autentikasi: JWT Bearer dengan klaim audien tepat `pasaree`; token lintas divisi tanpa audien pasaree ditolak 401; proksi ke Lumbung memakai token layanan internal, bukan token pengguna.

## Peta Versi Aktif

- Versi aktif: v1.0.0 (disetujui). Isi beku ada di `versions/v1.0.0/`.
- `versions/v1.0.0/CHANGELOG.md` — ringkasan versi awal.
- `versions/v1.0.0/BRD/` — kebutuhan bisnis BR-001 dan seterusnya.
- `versions/v1.0.0/PRD/` — pengguna dan kriteria US-001 dan seterusnya.
- `versions/v1.0.0/FRD/` — kebutuhan fungsional FR-001 dan seterusnya.
- `versions/v1.0.0/FSD/` — rancangan alur, model data Lapak, Produk, Varian, Media, dan kontrak API.
- `versions/v1.0.0/SNAPSHOT-ROADMAP.md` — salinan beku janji v1.0.0.
- Peta hidup lintas versi ada di `roadmap/`: `TIMELINE.md`, `MILESTONE.md`, `ROADMAP.md`.

## Cara Baca History

1. Mulai dari `versions/v1.0.0/CHANGELOG.md` untuk ringkasan versi.
2. Lanjut ke `versions/v1.0.0/BRD/00-ikhtisar.md` untuk konteks bisnis, lalu `PRD/10-pengguna.md` untuk peran.
3. Untuk janji waktu itu, baca `versions/v1.0.0/SNAPSHOT-ROADMAP.md` yang sudah dibekukan dan tidak diubah lagi.
4. Untuk kondisi terkini lintas versi, baca `roadmap/TIMELINE.md` dan `roadmap/MILESTONE.md`.
5. Riwayat perubahan antar versi dilacak lewat `git log` dan `CHANGELOG.md` tiap versi. File lama sengaja dihapus setelah dipindah dengan `git mv` agar tidak ada dua sumber kebenaran.

## TownHall Lain dan Pedoman Induk

Pedoman induk: https://github.com/Coding-Skuy/ChefGenie-TownHall.

Lima TownHall lain yang meniru pola template emas ini:

- https://github.com/Coding-Skuy/Lumbung-TownHall — distribusi trip dan keuangan payout; Pasaree memproksi trip dan mengeksekusi payout via Lumbung.
- https://github.com/Coding-Skuy/Pawonee-TownHall — dapur dan pengolahan, jalur verifikasi higiene produsen pangan.
- https://github.com/Coding-Skuy/Pedaree-TownHall — pengantar dan last-mile, mitra verifikasi dapur bersama.
- https://github.com/Coding-Skuy/TitipO-TownHall — titip dan kemitraan.
- https://github.com/Coding-Skuy/Titeny-TownHall — ketelitian dan audit mutu.

Pola yang ditiru: penamaan `versions/vX.Y.Z/BRD|PRD|FRD|FSD/`, file `NN-nama-kebab.md`, header versi satu baris, dan bagian Batasan di tiap file.

## Batasan

Batasan ruang lingkup repo ini: hanya lapak terkurasi, katalog produk, alur beli multi-lapak, komisi transparan, escrow kasir, dan kontrak API Pasaree. Di luar batas: armada, gudang, jadwal trip, dan kas payout yang menjadi milik Lumbung-TownHall dan hanya diakses via integrasi proksi; resep dapur milik Pawonee; routing last-mile milik Pedaree; skema titip milik TitipO; audit independen milik Titeny. Setiap lapak wajib mengisi `titik_serah_lumbung_id`; pengiriman di luar trip memerlukan persetujuan kurator dan catat manual.
