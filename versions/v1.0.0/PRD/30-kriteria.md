> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 30 — Kriteria Keberhasilan

## Definisi Lapak Aktif

Sumber isi lama: `metrik/10-lapak-aktif.md` yang dipindah dengan `git mv` ke file ini. Lapak aktif bulan berjalan adalah lapak berstatus AKTIF yang memenuhi seluruh syarat berikut dalam 30 hari terakhir:

1. Minimal 3 produk berstatus TAYANG dengan stok total di atas 0.
2. Minimal 1 pesanan SELESAI, bukan hanya DIPESAN atau DIBAYAR.
3. Tingkat konfirmasi tepat waktu maksimal 2 jam minimal 80 persen.
4. Tanpa pelanggaran larangan mutlak yang belum dipulihkan.

Gagal salah satu syarat menjadi TIDAK_AKTIF_BULAN_INI sebagai label analitik bukan status blokir, dan kehilangan slot promosi beranda.

## Cerita Pengguna dan Acceptance

- US-001 Sebagai produsen saya mengajukan produk sehingga tayang maksimal 2x24 jam kerja. Acceptance: cek otomatis menolak draf gagal per kolom; kurator memutus setuju, revisi, atau tolak beralasan; produk disetujui muncul di katalog maksimal 5 menit.
- US-002 Sebagai pembeli saya checkout multi-lapak sehingga membayar satu total transparan. Acceptance: pratinjau server menampilkan subtotal, ongkir, biaya layanan; stok atau harga berubah mengembalikan 409 STOK_HABIS atau HARGA_BERUBAH dengan angka terbaru; bayar idempoten tanpa tagihan ganda.
- US-003 Sebagai lapak saya mengonfirmasi pesanan masuk sehingga fulfillment tepat waktu. Acceptance: batas konfirmasi maksimal 2 jam jam kerja; tolak setelah bayar memicu refund otomatis; serah trip memakai kode serah cocok pindaian kurir Lumbung.
- US-004 Sebagai pembeli saya menerima pesanan sehingga escrow dilepas adil. Acceptance: konfirmasi terima manual atau otomatis 2x24 jam; retur non-pangan segar maksimal 1x24 jam; pangan segar hanya untuk salah kirim atau basi dengan foto maksimal 3 jam.
- US-005 Sebagai kurator saya mengosongkan antrean sehingga katalog tetap jujur. Acceptance: antrean FIFO prioritas pangan segar; 50 item tanpa reload manual di web; setiap keputusan beraudit.
- US-006 Sebagai admin saya memantau lapak aktif sehingga target Varian 1 tercapai. Acceptance: persen lapak aktif minimal 60 persen; konfirmasi tepat waktu minimal 90 persen; selesai per lapak aktif minimal 8 per bulan; retur bermasalah maksimal 2 persen; stok-0 kronis maksimal 15 persen.

## Non-Goals v1.0.0

- Tanpa alur bayar di web; tanpa target desktop KMP; tanpa Electron dan Tauri; tanpa bayar offline; tanpa token layanan Lumbung di klien; tanpa perhitungan total di klien untuk keputusan bayar atau payout.

## Batasan

Batasan dokumen ini: hanya kriteria produk yang dapat diuji. Rincian teknis API dan skema ada di FSD. Klaim sukses tanpa skor bulanan lapak aktif, konfirmasi, selesai, retur, dan stok dinyatakan tidak berlaku. Lapak non-aktif 2 bulan berturut-turut masuk review kurator; lapak aktif 3 bulan berturut-turut dengan rating minimal 4.5 mendapat lencana PRODUSEN_ANDALAN.
