# 10 — Model Data Katalog

> Varian 1. Sumber kebenaran untuk KMP (Kotlin) dan Web Bun (TypeScript). ID memakai ULID string. Waktu memakai ISO-8601 UTC. Uang memakai integer IDR (minor unit = rupiah, tanpa sen).

## 1. Entitas & Relasi

```
Produsen 1—1 Lapak 1—* Produk 1—* VarianProduk 1—* SKUStok
Kategori 1—* Produk (primer) | Produk *—* Tag
Produk 1—* MediaProduk | Produk 1—* HargaRiwayat
Pesanan *—* VarianProduk via ItemPesanan (didefinisikan di produk/10-alur-beli.md)
```

## 2. Tabel Inti

### 2.1 Produsen

| Kolom | Tipe | Wajib | Keterangan |
|---|---|---|---|
| id | string ULID | ya | PK |
| nama | string 3–80 | ya | |
| jenis | enum: `PERORANGAN\|UMKM\|KOPERASI\|DAPUR_BERSAMA` | ya | |
| identitas_terverifikasi | boolean | ya | default false |
| kontak_wa | string E.164 | ya | |
| dapur_bersama_id | string nullable | tidak | relasi ke Pawonee/Pedaree bila ada |
| dibuat_pada | datetime | ya | |

### 2.2 Lapak

| Kolom | Tipe | Wajib | Keterangan |
|---|---|---|---|
| id | string ULID | ya | PK |
| produsen_id | FK Produsen | ya | unik (1 produsen = 1 lapak aktif) |
| nama_lapak | string 3–60 | ya | unik |
| slug | string | ya | unik, `^[a-z0-9-]{3,60}$` |
| deskripsi | string 50–500 | ya | |
| status | enum: `AKTIF\|BEKU\|TUTUP` | ya | default AKTIF setelah verifikasi |
| titik_serah_lumbung_id | string | ya | titik serah pada trip Lumbung |
| rekening_payout | string tersensor | ya | dikelola via skema Lumbung |
| dibuat_pada | datetime | ya | |

### 2.3 Kategori

Kategori baku Varian 1 (tidak dapat ditambah produsen):

`pangan-segar | pangan-olahan | bumbu-dapur | minuman | kerajinan-pangan | paket-hampers`

| Kolom | Tipe | Keterangan |
|---|---|---|
| kode | string slug PK | salah satu di atas |
| nama | string | |
| butuh_izin_pangan | boolean | true untuk pangan-segar, pangan-olahan, minuman |
| butuh_kedaluwarsa | boolean | true untuk pangan-segar, pangan-olahan, minuman |

### 2.4 Produk

| Kolom | Tipe | Wajib | Keterangan |
|---|---|---|---|
| id | string ULID | ya | PK |
| lapak_id | FK Lapak | ya | |
| judul | string 10–80 | ya | wajib memuat berat/isi |
| kategori_kode | FK Kategori | ya | primer |
| tag | string[] | tidak | maks 2, dari daftar baku |
| deskripsi | string 50–2000 | ya | |
| komposisi | string nullable | kondisional | wajib bila kategori pangan |
| alergen | string[] | kondisional | wajib bila pangan (boleh `[]` = tanpa alergen) |
| status | enum: `DRAF\|MENUNGGU_KURASI\|PERLU_REVISI\|TAYANG\|NONAKTIF_STOK_0\|TAKEDOWN\|ARSIP` | ya | |
| rating_rata | float 0–5 | ya | default 0, dihitung sistem |
| total_ulasan | integer | ya | default 0 |
| dibuat_pada / diperbarui_pada | datetime | ya | |

Daftar tag baku: `pedas | manis | vegan | gluten-free | frozen | siap-saji | kemasan-ulang | edisi-musiman`.

### 2.5 VarianProduk

| Kolom | Tipe | Wajib | Keterangan |
|---|---|---|---|
| id | string ULID | ya | PK |
| produk_id | FK Produk | ya | |
| nama_varian | string 2–40 | ya | mis. "Original 150 g" |
| sku | string 4–24 unik | ya | format `LPK-XXXX-####` |
| harga_idr | integer > 0 kelipatan 500 | ya | |
| stok | integer ≥ 0 | ya | |
| satuan | enum: `g\|ml\|pcs\|paket` | ya | |
| berat_kirim_gram | integer > 0 | ya | untuk ongkir trip |
| tanggal_produksi | date nullable | kondisional | wajib pangan |
| kedaluwarsa | date nullable | kondisional | wajib pangan |

### 2.6 MediaProduk

| Kolom | Tipe | Keterangan |
|---|---|---|
| id ULID | PK | |
| produk_id | FK | |
| url | string https | CDN |
| jenis | enum `FOTO\|VIDEO` | |
| utama | boolean | tepat 1 utama per produk |
| lebar / tinggi | integer | foto utama ≥ 800×800 |

### 2.7 HargaRiwayat (audit)

| Kolom | Keterangan |
|---|---|
| produk_id + varian_id + harga_baru + harga_lama + diubah_oleh + diubah_pada | append-only, perubahan > 20% menandai kurasi ulang |

## 3. Contoh DTO

TypeScript (Web Bun, TS 5.9.x):

```ts
export type ProdukStatus = 'DRAF' | 'MENUNGGU_KURASI' | 'PERLU_REVISI' | 'TAYANG' | 'NONAKTIF_STOK_0' | 'TAKEDOWN' | 'ARSIP';
export interface VarianProduk {
  id: string; produk_id: string; nama_varian: string; sku: string;
  harga_idr: number; stok: number; satuan: 'g' | 'ml' | 'pcs' | 'paket';
  berat_kirim_gram: number; tanggal_produksi: string | null; kedaluwarsa: string | null;
}
export interface ProdukTayang {
  id: string; judul: string; kategori_kode: string; tag: string[];
  deskripsi: string; lapak: { id: string; nama_lapak: string; slug: string };
  varian: VarianProduk[]; media: { url: string; jenis: 'FOTO' | 'VIDEO'; utama: boolean }[];
  rating_rata: number; total_ulasan: number;
}
```

Kotlin (KMP shared):

```kotlin
@Serializable
data class VarianProduk(
  val id: String, val produkId: String, val namaVarian: String, val sku: String,
  val hargaIdr: Int, val stok: Int, val satuan: String, val beratKirimGram: Int,
  val tanggalProduksi: String?, val kedaluwarsa: String?
)
```

## 4. Aturan Konsistensi

1. Stok adalah satu-satunya penentu keterbelian varian. Stok 0 = tidak dapat dimasukkan keranjang.
2. Harga tidak pernah float. Semua kalkulasi (komisi, ongkir, total) dalam integer IDR dengan pembulatan ke bawah per baris item.
3. Slug lapak dan SKU tidak dapat diubah setelah produk pertama tayang.
4. Soft-delete: produk dihapus → status `ARSIP`, data dipertahankan untuk audit pesanan lama.
5. Sinkronisasi offline (lihat `platform/60-offline-sinkron.md`) memakai `diperbarui_pada` + vektor versi per lapak untuk resolusi konflik stok (server menang).
