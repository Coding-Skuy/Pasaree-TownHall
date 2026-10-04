# 00 — Piagam Kurasi Pasaree

> Varian 1 — Berlaku penuh. Bahasa Indonesia. Tanpa placeholder.

## 1. Kedudukan

Pasaree-TownHall adalah TownHall **Marketplace & Commerce** ChefGenie. Ruang lingkup: **lapak, katalog, produsen lokal**. Fulfillment fisik **wajib via trip Lumbung** (Lumbung-TownHall: distribusi & keuangan). Pasaree tidak membangun armada, gudang, atau kasir pembayaran sendiri.

## 2. Prinsip Kurasi

1. **Produsen lokal dulu.** Prioritas: produsen dalam radius layanan Lumbung, UMKM terdaftar, dapur Pawonee/Pedaree yang lolos higiene.
2. **Jujur katalog.** Foto asli, berat bersih tercantum, tanggal produksi/kedaluwarsa wajib untuk pangan segar dan olahan.
3. **Harga transparan.** Harga tampil = harga bayar sebelum ongkir dan komisi tertera terpisah. Dilarang mark-up tersembunyi.
4. **Keamanan pangan.** Produk pangan wajib mencantumkan izin (PIRT/BPOM sesuai kelas) dan label alergen.
5. **Keberlanjutan.** Kemasan sekali pakai dikurangi; insentif untuk kemasan guna-ulang dan ambil-di-lapak.

## 3. Kriteria Lapak

Lapak diterima jika memenuhi seluruh syarat berikut:

- Identitas produsen terverifikasi (KTP/NIB/komunitas, salah satu cukup + verifikasi luring oleh kurator).
- Minimal 3 produk aktif dengan foto asli, deskripsi 50–300 kata, dan stok awal tercatat.
- Menyetujui komisi transparan (lihat `keuangan/10-komisi-transparan.md`) dan payout via skema Lumbung.
- Menyetujui SLA fulfillment: konfirmasi pesanan ≤ 2 jam jam kerja, serah ke trip Lumbung sesuai jadwal trip.
- Skor higiene ≥ 80 untuk pangan (cek oleh tim Pawonee bila dapur bersama).

## 4. Kriteria Produk

- Judul jelas: nama + varian + berat/isi (contoh: "Sambal Bawang Mbok D — 150 g").
- Kategori tunggal primer + maksimal 2 tag sekunder (lihat `katalog/10-model-data.md`).
- Stok bilangan bulat ≥ 0, satuan baku (g, ml, pcs, paket). Stok 0 otomatis non-tayang.
- Harga dalam IDR, kelipatan Rp500. Diskon wajib mencantumkan harga awal dan periode.
- Media: minimal 1 foto 800×800 px, maksimal 6 foto + 1 video 30 detik. Dilarang foto stok/internet.
- Pangan wajib: komposisi, alergen, tanggal produksi, daya tahan, instruksi simpan.

## 5. Larangan Mutlak

1. Produk ilegal, berbahaya, atau klaim kesehatan tanpa izin.
2. Dropship dari luar ekosistem tanpa sepengetahuan kurator.
3. Duplikasi lapak untuk produk identik (satu produsen = satu lapak aktif).
4. Manipulasi ulasan, stok palsu, atau harga jebakan.
5. Transaksi di luar kasir Pasaree untuk pesanan yang berasal dari katalog.

Pelanggaran 1–2 berakibat takedown langsung + pembekuan 30 hari. Pelanggaran berulang berakibat tutup permanen.

## 6. Dewan Kurasi

- Ketua: pemilik TownHall Pasaree.
- Anggota: 1 kurator katalog, 1 perwakilan produsen (rotasi 3 bulan), 1 perwakilan Lumbung (jaminan fulfillment nyambung).
- Kuorum 2 dari 3. Banding maksimal 1 kali dalam 7 hari via tiket admin web.

## 7. SLA Kurasi

| Proses | SLA |
|---|---|
| Pendaftaran lapak → verifikasi awal | 1×24 jam kerja |
| Pengajuan produk → tayang/tolak beralasan | 2×24 jam kerja |
| Banding takedown | 3×24 jam kerja |
| Audit lapak aktif | tiap 90 hari |

## 8. Keterkaitan TownHall

- **Lumbung (distribusi):** jadwal trip, titik serah, ongkir per trip. Pasaree tidak menjanjikan SLA di luar trip yang dipublikasi Lumbung.
- **Lumbung (keuangan):** payout, potongan komisi, rekonsiliasi.
- **Pawonee/Pedaree:** produsen dapur bersama otomatis memenuhi syarat higiene bila sertifikat dapur masih berlaku.

## 9. Perubahan Piagam

Perubahan piagam wajib dicatat di CHANGELOG TownHall dengan nomor varian baru. Varian 1 ini adalah dasar tayang perdana.
