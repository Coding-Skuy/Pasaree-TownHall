> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# TIMELINE Pasaree — Garis Waktu Hidup Lintas Versi

Dokumen living: diperbarui tiap ada versi baru. Salinan beku v1.0.0 ada di `versions/v1.0.0/SNAPSHOT-ROADMAP.md` dan tidak diubah lagi.

## Garis Waktu

- 04 Okt 2026 — Varian 1 berlaku penuh. Piagam kurasi, SOP tayang, model data katalog, alur beli multi-lapak, kontrak API mobile dan web, matriks KMP-vs-web, dan komisi transparan 8 persen disetujui sebagai dasar tayang perdana. Sumber isi lama: `lapak/`, `katalog/`, `produk/`, `platform/`, `keuangan/`, `metrik/`.
- 06 Okt 2026 — v1.0.0 TownHall disetujui. Struktur versi BRD, PRD, FRD, FSD dibekukan sebagai template emas mengikuti Lumbung-TownHall. Aturan: lapak terkurasi 1 produsen 1 lapak aktif, katalog stok real-time dengan ID ULID dan uang integer IDR, komisi 8 persen (hampers 10 persen), payout T+1 via Lumbung, DB pasaree, JWT audien pasaree.
- Minggu 1 sampai 4 tayang perdana — Operasi kecil. Target: 20 lapak terverifikasi, 60 produk tayang, waktu kurasi median maksimal 36 jam, konfirmasi tepat waktu minimal 80 persen. Jalur kritis: trip Lumbung tersedia untuk setiap checkout, escrow kasir menahan dana hingga serah-terima.
- Bulan 2 sampai 3 — Penguatan. Target lapak aktif minimal 60 persen per bulan, selesai per lapak aktif minimal 8 per bulan, stok-0 kronis maksimal 15 persen. Kurator mengaudit lapak tiap 90 hari; lapak non-aktif 2 bulan berturut-turut masuk review pembinaan, bekukan, atau arsipkan.
- Bulan 4 — Kesiapan skala. Syarat: retur bermasalah maksimal 2 persen, konfirmasi tepat waktu minimal 90 persen, rekonsiliasi escrow harian tanpa selisih di atas Rp1.000 menggantung lebih dari 2x24 jam. Lapak aktif 3 bulan berturut-turut dengan rating minimal 4.5 mendapat lencana PRODUSEN_ANDALAN.
- Setelah Varian 1 — Skala dan versi berikutnya. Rencana rinci menunjuk `ROADMAP.md` untuk v1.1.0 dan v2.0.0.

## Keterkaitan Versi

- v1.0.0 menjadi acuan awal. Perubahan jadwal pada versi baru dicatat di sini dengan tanggal dan nomor versi, tanpa mengubah snapshot beku.

## Batasan

Batasan dokumen ini: hanya mencatat tonggak waktu dan fase. Detail kebutuhan tetap di `versions/v1.0.0/BRD/`, detail kriteria lulus di `versions/v1.0.0/PRD/30-kriteria.md`, dan detail janji beku di `SNAPSHOT-ROADMAP.md`. Dokumen ini tidak mengatur tarif komisi, status pesanan, atau kontrak API.
