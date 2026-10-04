> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 10 — Pengguna Pasaree

## Daftar Peran

- Pembeli: menjelajah katalog, memesan via mobile utama atau etalase web baca, membayar via kasir, melacak pesanan, mengonfirmasi terima, memberi ulasan maksimal 7 hari. Kebutuhan: stok real-time, pratinjau server, trip Lumbung tersedia, timeline status.
- Produsen pemilik lapak: CRUD draf lapaknya, ajukan kurasi, isi stok, lihat komisi dan payout. Operator lapak: CRUD draf, isi stok, tanpa ubah rekening payout. Kebutuhan: dasbor 30 hari, daftar menunggu, saldo tertahan, autosave draf 10 detik.
- Kurator: setujui, revisi, atau tolak semua lapak; takedown; audit 90 hari. Kebutuhan: antrean FIFO prioritas pangan segar, umur antrean jam, alasan baku, snapshot konten.
- Admin: kelola kategori, komisi, takedown permanen. Kebutuhan: tren 12 minggu, daftar lapak berisiko, riwayat tarif append-only, audit trail 2 tahun.

## Hak Akses

- Pembeli hanya melihat katalog tayang dan pesanannya sendiri. Produsen hanya lapaknya. Kurator membaca semua lapak dan menulis keputusan kurasi. Admin penuh termasuk ubah komisi dengan pengumuman 14 hari.
- Auth terpisah per peran: JWT pembeli untuk mobile tidak dapat dipertukarkan dengan JWT lapak atau kurator untuk web. Token web lapak tidak disimpan di localStorage; SvelteKit memanggil API via server load dan actions. Klaim audien wajib tepat `pasaree` di semua peran.
- Mobile pembeli adalah satu-satunya alur bayar penuh; web hanya pratinjau etalase tanpa bayar. Tayang produk hanya di web; mobile tidak memiliki fungsi tayang.

## Batasan

Batasan dokumen ini: hanya peran, kebutuhan pandang, dan hak akses. Aturan bisnis rinci ada di BRD, langkah sistem ada di FSD. Di luar batas: peran sopir trip dan admin gudang yang diatur Lumbung-TownHall; peran pengolah dapur yang diatur Pawonee.
