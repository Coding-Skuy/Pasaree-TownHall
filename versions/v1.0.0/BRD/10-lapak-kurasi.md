> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 10 — Lapak dan Kurasi

## Konteks

Segmen lapak mencakup pendaftaran lapak, SOP tayang produk, dewan kurasi, dan penegakan larangan. Sumber isi lama: `lapak/10-sop-tayang-produk.md` yang dipindah dengan `git mv` ke file ini, ditambah piagam kurasi dari `BRD/00-ikhtisar.md`.

## Kebutuhan Bisnis

- BR-101 Setiap produk wajib melewati alur draf, cek otomatis, review kurator, lalu tayang atau revisi: daftar atau aktifkan lapak, isi draf produk, cek otomatis saat Ajukan Kurasi, review kurator FIFO prioritas pangan segar, tayang maksimal 5 menit setelah disetujui atau revisi maksimal 7 hari atau arsip.
- BR-102 Cek otomatis wajib menolak draf yang gagal: judul 10 sampai 80 karakter mengandung berat atau isi; harga kelipatan Rp500 di atas 0; stok integer minimal 0 dan berat kirim di atas 0; foto minimal 1 resolusi minimal 800x800 maksimal 5 MB; kategori valid; pangan wajib komposisi, alergen, tanggal produksi, kedaluwarsa; kemiripan judul sesama lapak di bawah 85 persen.
- BR-103 Kurator wajib memutus dengan alasan baku: setuju menjadi TAYANG; revisi dengan alasan baku dan catatan maksimal 280 karakter; tolak hanya untuk larangan mutlak dengan tautan pasal piagam. Setiap keputusan menyimpan siapa, kapan, alasan, dan snapshot konten.
- BR-104 Status produk dikunci: DRAF, MENUNGGU_KURASI, PERLU_REVISI, TAYANG, NONAKTIF_STOK_0, TAKEDOWN, ARSIP. NONAKTIF_STOK_0 otomatis kembali TAYANG saat stok diisi tanpa kurasi ulang bila konten tidak berubah. Perubahan harga di atas 20 persen atau ganti foto utama memicu kurasi ulang ringan.
- BR-105 Larangan mutlak ditegakkan: produk ilegal dan klaim kesehatan tanpa izin; dropship luar ekosistem tanpa sepengetahuan kurator; duplikasi lapak produk identik; manipulasi ulasan, stok palsu, harga jebakan; transaksi luar kasir untuk pesanan katalog. Pelanggaran butir 1 sampai 2 berakibat takedown langsung dan bekukan 30 hari; berulang berakibat tutup permanen.
- BR-106 Dewan kurasi beranggotakan ketua pemilik TownHall, 1 kurator katalog, 1 perwakilan produsen rotasi 3 bulan, 1 perwakilan Lumbung. Kuorum 2 dari 3. Banding maksimal 1 kali dalam 7 hari via tiket admin web.

## Metrik

- Waktu kurasi median ajukan ke tayang maksimal 36 jam. Persen revisi per 100 pengajuan. Antrean di atas 50 item memicu notifikasi ke ketua TownHall.

## Batasan

Batasan segmen ini: hanya dari pendaftaran lapak sampai produk tayang dan audit 90 hari. Di luar batas: penetapan jadwal trip, penagihan ongkir, dan implementasi aplikasi. Produk tanpa penjualan dan tanpa update stok 90 hari diputuskan kurator pertahankan atau arsipkan; metrik dipantau di `PRD/30-kriteria.md`.
