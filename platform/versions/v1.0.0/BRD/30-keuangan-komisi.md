> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 30 — Keuangan dan Komisi

## Konteks

Keuangan Pasaree hanya dari komisi transparan dan biaya layanan; kas dan payout dieksekusi via Lumbung. Sumber isi lama: `keuangan/10-komisi-transparan.md` yang dipindah dengan `git mv` ke file ini.

## Kebutuhan Bisnis

- BR-301 Tarif default 8 persen dari subtotal per sub-pesanan sebelum ongkir dan biaya layanan; kategori paket-hampers 10 persen; promo lapak baru 5 persen selama 30 hari pertama sejak produk pertama tayang secara otomatis. PPN atau beban pajak bila ada ditampilkan terpisah, bukan di dalam persen komisi.
- BR-302 Rumus per baris integer IDR dibulatkan ke bawah: komisi_baris sama dengan floor dari harga_idr dikali qty dikali tarif_persen dibagi 100; payout_lapak sama dengan subtotal dikurangi jumlah komisi_baris dikurangi penalti bila ada. Biaya layanan pembeli Rp2.000 per checkout masuk operasional Pasaree dan tidak mengurangi payout lapak. Ongkir 100 persen diteruskan ke kas trip Lumbung.
- BR-303 Aliran dana dikunci: pembeli bayar lalu kasir Pasaree menahan dana dengan escrow_id; serah-terima trip tercatat lalu escrow dilepas; Lumbung mengeksekusi payout ke rekening lapak T+1 hari kerja setelah dipotong komisi; retur atau refund diselesaikan dari escrow bila belum dilepas, bila sudah payout dipotong dari payout berikutnya dengan notifikasi.
- BR-304 Rekonsiliasi harian otomatis Pasaree dengan Lumbung berisi pesanan_id, escrow_id, subtotal, komisi, ongkir, payout, status. Selisih di atas Rp1.000 atau status menggantung di atas 2x24 jam menjadi tiket prioritas tinggi ke admin dan lapak diberi tahu. Laporan lapak harian, mingguan, bulanan dapat diunduh CSV; audit trail 2 tahun.
- BR-305 Perubahan tarif default memerlukan pengumuman 14 hari di web dan notifikasi ke semua lapak aktif. Tarif promo lapak baru tidak dapat diubah di tengah periode. Riwayat tarif tersimpan append-only berisi tarif_persen, berlaku_mulai, diubah_oleh, alasan.
- BR-306 Sanksi terkait komisi: transaksi luar kasir untuk pesanan katalog berpenalti 25 persen dari nilai transaksi dan bekukan 30 hari; manipulasi harga untuk mengakali komisi berakibat takedown dan tutup permanen pada pengulangan.

## Metrik

- Komisi tertagih per minggu, payout T+1 tepat waktu, selisih rekonsiliasi, penalti transaksi luar kasir. Struk menampilkan subtotal, ongkir, biaya layanan, total bayar, komisi per lapak, estimasi payout.

## Batasan

Batasan segmen ini: hanya tarif, escrow, payout via Lumbung, dan rekonsiliasi. Di luar batas: pembukuan PT induk, pajak, dan pinjaman. Harga tampil ke pembeli tidak dibebani komisi; halaman lapak menampilkan kalkulator komisi dan estimasi payout sebagai info transparansi.
