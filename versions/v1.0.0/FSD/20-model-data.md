> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 20 — Model Data

## Entitas Inti

Sumber isi lama: `katalog/10-model-data.md` yang dipindah dengan `git mv` ke file ini. Sumber kebenaran untuk KMP Kotlin dan Web Bun TypeScript. ID memakai ULID string. Waktu memakai ISO-8601 UTC. Uang memakai integer IDR tanpa sen.

```
Produsen 1-1 Lapak 1-* Produk 1-* VarianProduk 1-* SKUStok
Kategori 1-* Produk primer | Produk *-* Tag
Produk 1-* MediaProduk | Produk 1-* HargaRiwayat
Pesanan *-* VarianProduk via ItemPesanan
```

- Produsen: id ULID PK; nama 3 sampai 80; jenis enum PERORANGAN, UMKM, KOPERASI, DAPUR_BERSAMA; identitas_terverifikasi default false; kontak_wa E.164; dapur_bersama_id nullable relasi Pawonee atau Pedaree; dibuat_pada datetime.
- Lapak: id ULID PK; produsen_id FK unik 1 produsen 1 lapak aktif; nama_lapak 3 sampai 60 unik; slug unik pola `^[a-z0-9-]{3,60}$`; deskripsi 50 sampai 500; status enum AKTIF, BEKU, TUTUP; titik_serah_lumbung_id wajib; rekening_payout tersensor via skema Lumbung; dibuat_pada datetime.
- Kategori baku: pangan-segar, pangan-olahan, bumbu-dapur, minuman, kerajinan-pangan, paket-hampers. Flag butuh_izin_pangan dan butuh_kedaluwarsa true untuk pangan-segar, pangan-olahan, minuman.
- Produk: id ULID PK; lapak_id FK; judul 10 sampai 80 wajib memuat berat atau isi; kategori_kode FK primer; tag maksimal 2 dari daftar baku; deskripsi 50 sampai 2000; komposisi nullable wajib bila pangan; alergen wajib bila pangan boleh kosong berarti tanpa alergen; status enum DRAF, MENUNGGU_KURASI, PERLU_REVISI, TAYANG, NONAKTIF_STOK_0, TAKEDOWN, ARSIP; rating_rata 0 sampai 5 default 0; total_ulasan default 0.
- VarianProduk: id ULID PK; produk_id FK; nama_varian 2 sampai 40; sku 4 sampai 24 unik format `LPK-MBKD-0001`; harga_idr integer di atas 0 kelipatan 500; stok integer minimal 0; satuan enum g, ml, pcs, paket; berat_kirim_gram di atas 0 untuk ongkir trip; tanggal_produksi dan kedaluwarsa wajib pangan.
- MediaProduk: id ULID PK; produk_id FK; url https CDN; jenis enum FOTO, VIDEO; utama boolean tepat 1 per produk; foto utama minimal 800x800.
- HargaRiwayat audit: produk_id, varian_id, harga_baru, harga_lama, diubah_oleh, diubah_pada append-only; perubahan di atas 20 persen menandai kurasi ulang.
- Pendukung pesanan: Pesanan multi-lapak dengan sub-pesanan per lapak; ItemPesanan merujuk varian; status pesanan enum DRAF_KERANJANG, DIPESAN, DIBAYAR, DIKONFIRMASI_LAPAK, DISERAHKAN_KE_TRIP, DITERIMA_PEMBELI, SELESAI, DIBATALKAN, DIKEMBALIKAN.

## Contoh DTO

TypeScript Web Bun TS 5.9.x: ProdukStatus union; VarianProduk berisi id, produk_id, nama_varian, sku, harga_idr, stok, satuan, berat_kirim_gram, tanggal_produksi, kedaluwarsa; ProdukTayang berisi id, judul, kategori_kode, tag, deskripsi, lapak id nama slug, varian, media url jenis utama, rating_rata, total_ulasan.

Kotlin KMP shared: data class VarianProduk serializable berisi id, produkId, namaVarian, sku, hargaIdr Int, stok Int, satuan String, beratKirimGram Int, tanggalProduksi, kedaluwarsa nullable; data class PratinjauCheckout berisi subtotalIdr, ongkirIdr, biayaLayananIdr, totalIdr, kunciStokHingga.

## Aturan Angka

- Stok satu-satunya penentu keterbelian; stok 0 tidak dapat masuk keranjang. Harga tidak pernah float; semua kalkulasi komisi, ongkir, total integer IDR dibulatkan ke bawah per baris item.
- Slug lapak dan SKU tidak dapat diubah setelah produk pertama tayang. Soft-delete menjadi ARSIP untuk audit pesanan lama.
- Sinkronisasi offline memakai diperbarui_pada dan vektor versi per lapak; konflik stok dimenangkan server. Basis data operasional: DB pasaree.

## Batasan

Batasan dokumen ini: hanya definisi entitas, kunci, enum, dan aturan angka. Serialisasi JSON dan endpoint ada di `30-kontrak.md`. Perubahan skema butuh persetujuan Tech Lead dan migrasi teruji; perubahan skema wajib mengubah DTO Kotlin dan TS dalam satu rilis minor.
