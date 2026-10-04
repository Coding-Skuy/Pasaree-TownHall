> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# CHANGELOG v1.0.0 — Versi Awal Pasaree

## Ringkasan Isi

v1.0.0 adalah versi awal TownHall Pasaree yang dibekukan sebagai template emas mengikuti Lumbung-TownHall. Seluruh isi lama dari folder `lapak/`, `katalog/`, `produk/`, `platform/`, `keuangan/`, dan `metrik/` dipecah dan dipindah dengan `git mv` ke struktur versi ini, lalu folder lama dihapus agar hanya ada satu sumber kebenaran.

## Isi per Direktori

- BRD: `00-ikhtisar.md` memuat kedudukan marketplace, prinsip kurasi, dan kebutuhan BR-001 dan seterusnya. `10-lapak-kurasi.md` memuat SOP tayang dan SLA kurasi. `20-katalog-produk.md` memuat kebutuhan katalog stok real-time dan kategori baku. `30-keuangan-komisi.md` memuat tarif 8 persen (hampers 10 persen), escrow, dan payout T+1 via Lumbung.
- PRD: `10-pengguna.md` memuat 4 peran: pembeli, produsen dan operator lapak, kurator, admin. `20-alur.md` memuat alur beli multi-lapak dari keranjang sampai selesai dan ulasan. `30-kriteria.md` memuat US-001 dan seterusnya, definisi lapak aktif, dan non-goals.
- FRD: `10-fungsional.md` memuat FR-001 dan seterusnya per segmen lapak, katalog, beli, keuangan, tanpa cara implementasi.
- FSD: `10-alur.md` memuat urutan sistem, modul KMP bersama, matriks KMP-vs-web, dan sinkron offline. `20-model-data.md` memuat entitas Produsen, Lapak, Produk, VarianProduk, MediaProduk, HargaRiwayat. `30-kontrak.md` memuat kontrak API mobile KMP dan web Bun, proksi trip Lumbung, event, dan galat baku dengan autentikasi JWT audien pasaree dan basis data pasaree.
- `SNAPSHOT-ROADMAP.md` memuat salinan beku janji Varian 1.

## Sumber Pemindahan

- `lapak/00-piagam-kurasi.md` menjadi BRD ikhtisar. `lapak/10-sop-tayang-produk.md` menjadi BRD lapak-kurasi.
- `katalog/10-model-data.md` menjadi FSD model-data. Aturan kategori dan tag diringkas ke BRD katalog-produk.
- `produk/10-alur-beli.md` menjadi PRD alur. `produk/20-kontrak-api-KMP-mobile.md` dan `produk/21-kontrak-api-web-bun.md` digabung menjadi FSD kontrak. `produk/30-modul-KMP-bersama.md` menjadi inti FSD alur.
- `platform/10-matriks-KMP-web.md`, `platform/40-mobile-KMP.md`, `platform/50-web-bun-svelte.md`, `platform/60-offline-sinkron.md` digabung menjadi FSD alur.
- `keuangan/10-komisi-transparan.md` menjadi BRD keuangan-komisi. `metrik/10-lapak-aktif.md` menjadi PRD kriteria.

## Batasan

Batasan versi ini: hanya lapak terkurasi, katalog Varian 1 enam kategori, alur beli multi-lapak via trip Lumbung, komisi 8 persen dan hampers 10 persen, payout via Lumbung, DB pasaree, JWT audien pasaree. Perubahan setelah ini wajib masuk v1.1.0 atau v2.0.0 dan dicatat di `roadmap/` living, bukan dengan mengubah file beku ini.
