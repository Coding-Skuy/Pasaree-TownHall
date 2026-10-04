# 10 — SOP Tayang Produk

> Varian 1. Pelaksana: produsen + kurator. Target: produk tayang ≤ 2×24 jam kerja.

## 1. Alur Ringkas

```
Daftar/aktif lapak → Isi draf produk → Cek otomatis → Review kurator → Tayang / Revisi → Audit 90 hari
```

## 2. Langkah Produsen (Web Lapak)

1. Masuk web lapak/admin (Bun + SvelteKit), pilih lapak aktif.
2. Klik **Tambah Produk**, isi:
   - Judul, kategori primer, tag, deskripsi, komposisi/alergen (bila pangan).
   - Varian + SKU per varian, harga IDR, stok awal, satuan, berat kirim (gram).
   - Foto/video sesuai piagam. Tanggal produksi & kedaluwarsa bila pangan.
3. Klik **Ajukan Kurasi**. Status menjadi `MENUNGGU_KURASI`. Draf tersimpan otomatis tiap 10 detik.
4. Jika dikembalikan (`PERLU_REVISI`), perbaiki sesuai catatan kurator maksimal 7 hari, lalu ajukan ulang. Lewat 7 hari draf diarsipkan.

## 3. Cek Otomatis (Sistem)

Berjalan saat produsen menekan Ajukan Kurasi:

- [ ] Judul 10–80 karakter, mengandung berat/isi.
- [ ] Harga kelipatan Rp500, > 0.
- [ ] Stok integer ≥ 0, berat kirim > 0.
- [ ] Foto ≥ 1, resolusi ≥ 800×800, ukuran ≤ 5 MB per berkas.
- [ ] Kategori valid dari daftar `katalog/10-model-data.md`.
- [ ] Pangan: komposisi + alergen + tanggal produksi + kedaluwarsa terisi.
- [ ] Duplikasi: kemiripan judul ≥ 85% dengan produk se-lapak ditolak.

Gagal cek otomatis = kembali ke draf dengan pesan kesalahan per kolom.

## 4. Langkah Kurator

1. Ambil antrean `MENUNGGU_KURASI` (FIFO, prioritas pangan segar).
2. Periksa: keaslian foto (reverse-check manual + metadata), kewajaran harga (±30% dari median kategori bila ada), kelengkapan label.
3. Keputusan:
   - **Setujui** → status `TAYANG`, produk muncul di katalog mobile + web ≤ 5 menit.
   - **Revisi** → pilih alasan baku + catatan bebas maksimal 280 karakter.
   - **Tolak** → hanya untuk pelanggaran larangan mutlak, wajib tautkan pasal piagam.
4. SLA: 2×24 jam kerja. Antrean > 50 item memicu notifikasi ke ketua TownHall.

## 5. Status Produk

`DRAF | MENUNGGU_KURASI | PERLU_REVISI | TAYANG | NONAKTIF_STOK_0 | TAKEDOWN | ARSIP`

- `NONAKTIF_STOK_0` otomatis oleh sistem, kembali `TAYANG` saat stok diisi (> 0) tanpa kurasi ulang bila tidak ada perubahan konten.
- Perubahan harga > 20% atau ganti foto utama pada produk `TAYANG` memicu kurasi ulang ringan (cek otomatis + 1 kurator).

## 6. Peran & Akses (Web)

| Peran | Hak |
|---|---|
| Produsen (pemilik lapak) | CRUD draf lapaknya, ajukan kurasi, isi stok, lihat komisi |
| Operator lapak | CRUD draf, isi stok, tanpa ubah rekening payout |
| Kurator | setujui/revisi/tolak semua lapak, takedown |
| Admin | kelola kategori, komisi, takedown permanen |

Mobile (pembeli) tidak memiliki fungsi tayang produk.

## 7. Bukti & Audit

- Setiap keputusan kurasi menyimpan: siapa, kapan, alasan, snapshot konten.
- Audit 90 hari: sistem menandai produk tanpa penjualan + tanpa update stok 90 hari → kurator memutuskan pertahankan/arsipkan.
- Metrik SOP dipantau di `metrik/10-lapak-aktif.md` (waktu kurasi median, % revisi).
