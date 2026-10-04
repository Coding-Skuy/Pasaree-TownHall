> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 20 — Katalog dan Produk

## Konteks

Segmen katalog mencakup kategori baku, aturan judul dan media, harga dan stok real-time, serta keterkaitan trip Lumbung. Disintesis dari `katalog/10-model-data.md` (model pindah ke `FSD/20-model-data.md`) dan piagam kurasi.

## Kebutuhan Bisnis

- BR-201 Kategori primer dikunci enam: pangan-segar, pangan-olahan, bumbu-dapur, minuman, kerajinan-pangan, paket-hampers. Produsen tidak dapat menambah kategori; tag sekunder maksimal 2 dari daftar baku: pedas, manis, vegan, gluten-free, frozen, siap-saji, kemasan-ulang, edisi-musiman.
- BR-202 Setiap produk pangan wajib mencantumkan komposisi, alergen, tanggal produksi, daya tahan, instruksi simpan, dan izin PIRT atau BPOM sesuai kelas. Judul wajib memuat berat atau isi, contoh Sambal Bawang Mbok D 150 g.
- BR-203 Stok adalah satu-satunya penentu keterbelian: stok bilangan bulat minimal 0 dengan satuan baku g, ml, pcs, paket; stok 0 otomatis non-tayang dan tidak dapat masuk keranjang. Harga integer IDR kelipatan Rp500; diskon wajib mencantumkan harga awal dan periode; perubahan harga di atas 20 persen tercatat di riwayat dan memicu kurasi ulang ringan.
- BR-204 Media dikunci: minimal 1 foto 800x800 px, maksimal 6 foto dan 1 video 30 detik; dilarang foto stok atau internet. Tepat 1 media utama per produk.
- BR-205 Katalog menampilkan stok real-time milik Pasaree dan ketersediaan trip real-time dari Lumbung: setiap produk tayang membawa daftar trip tersedia berisi trip_id, jadwal_berangkat, dan ongkir_idr yang diproksi dari Lumbung dengan cache 60 detik. Bila Lumbung tidak merespons, kembalikan cache terakhir dengan flag basi true; checkout tanpa trip valid diblokir.
- BR-206 Slug lapak dan SKU tidak dapat diubah setelah produk pertama tayang. Produk dihapus menjadi ARSIP; data dipertahankan untuk audit pesanan lama.

## Metrik

- Stok-0 kronis maksimal 15 persen dari total tayang. Produk 14 hari stok 0 masuk daftar risiko. Ulasan memengaruhi rating dan bobot pencarian.

## Batasan

Batasan segmen ini: hanya aturan katalog, harga, stok, dan media. Di luar batas: eksekusi trip dan ongkir yang menjadi milik Lumbung; penilaian rasa, keamanan pangan, dan keaslian merek yang tetap diputus kurator manusia. Skema teknis ada di `FSD/20-model-data.md`.
