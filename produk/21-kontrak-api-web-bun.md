# 21 — Kontrak API Web Bun (Lapak/Admin)

> Varian 1. Konsumen: Web lapak/admin (Bun 1.4.x + Svelte 5 + SvelteKit 2 + TS 5.9.x). Basis: `https://api.pasaree.chefgenie.id/v1`. Auth: Bearer JWT peran produsen/operator/kurator/admin + CSRF untuk cookie session di SvelteKit. Semua uang integer IDR.

## 1. Konvensi

- SvelteKit memanggil API via server load/actions (`+server.ts` proksi) agar token tidak terekspos ke browser.
- Idempotensi: semua POST mutasi menerima header `Idempotency-Key: <uuid>`.
- Unggah media via URL presigned: minta → unggah ke CDN → konfirmasi.
- Paginasi cursor sama dengan kontrak mobile.

## 2. Lapak

### GET /web/lapak-saya

Respons 200: daftar lapak milik akun + `status`, `skor_fulfillment`, `saldo_tertahan_idr`.

### PATCH /web/lapak/{id}

Body: `{ "nama_lapak"?, "deskripsi"?, "titik_serah_lumbung_id"? }`. Slug tidak dapat diubah. Respons 200: lapak terbaru.

## 3. Produk (tayang)

### POST /web/produk/draf

Body: `{ lapak_id, judul, kategori_kode, tag[], deskripsi, komposisi?, alergen[]? }`. Respons 201: `{ produk_id, status: "DRAF" }`.

### PUT /web/produk/{id}

Body: field draf yang dapat diubah + `varian[]` (lihat model data) + `media[]`. Respons 200: produk + hasil `cek_otomatis: { lolos: boolean, kesalahan: [{kolom, pesan}] }`.

### POST /web/produk/{id}/ajukan-kurasi

Respons 200: `{ status: "MENUNGGU_KURASI", estimasi_sla: "2026-10-06T17:00:00Z" }` atau 422 bila cek otomatis gagal.

### GET /web/produk?status=&lapak_id=&limit=&cursor=

Respons: daftar + `perlu_revisi_catatan` bila ada.

### POST /web/media/presigned

Body: `{ "nama_berkas": "sambal-1.jpg", "tipe_mime": "image/jpeg", "ukuran_byte": 1200000 }`. Respons: `{ "unggah_url": "https://cdn.../put", "media_url": "https://cdn.../foto.jpg", "kedaluwarsa": "..." }`. Batas: foto ≤ 5 MB, video ≤ 50 MB.

## 4. Stok & Harga

### PATCH /web/varian/{id}/stok

Body: `{ "stok": 25, "alasan": "restok" }`. Respons 200: stok baru + `status_produk` (otomatis `TAYANG` bila sebelumnya `NONAKTIF_STOK_0` dan stok > 0).

### PATCH /web/varian/{id}/harga

Body: `{ "harga_idr": 20000 }`. Aturan: naik > 20% menandai `kurasi_ulang_ringan: true`. Riwayat tercatat otomatis.

## 5. Pesanan Masuk (lapak)

### GET /web/pesanan-masuk?status=DIKONFIRMASI_MENUNGGU&lapak_id=

Respons: sub-pesanan per lapak + `batas_konfirmasi_hingga` + trip yang dipilih pembeli.

### POST /web/pesanan-masuk/{sub_id}/konfirmasi

Body: `{ "terima": true }` atau `{ "terima": false, "alasan": "stok_rusak" }`. Respons 200: status baru. Tolak setelah bayar memicu refund otomatis via kasir.

### POST /web/pesanan-masuk/{sub_id}/serah-trip

Body: `{ "trip_id": "TRP-...", "kode_serah": "XXXX" }`. Respons 200: `{ status: "DISERAHKAN_KE_TRIP" }`. Kode serah wajib cocok dengan pindaian kurir Lumbung.

## 6. Kurator/Admin

### GET /web/kurasi/antrean?kategori=&limit=&cursor=

Respons: draf menunggu + `umur_antrean_jam` + flag prioritas pangan segar.

### POST /web/kurasi/{produk_id}/keputusan

Body: `{ "keputusan": "SETUJU" | "REVISI" | "TOLAK", "alasan_baku": "FOTO_TIDAK_ASLI | HARGA_TIDAK_WAJAR | LABEL_KURANG | ...", "catatan": "..." }`. Respons 200: status produk terbaru. Semua keputusan beraudit.

### PATCH /web/admin/komisi

Hanya admin. Body: `{ "tarif_default_persen": 8, "berlaku_mulai": "2026-11-01" }`. Perubahan tercatat dan diumumkan ke lapak 14 hari sebelumnya (lihat `keuangan/10-komisi-transparan.md`).

## 7. Tipe TypeScript (SvelteKit)

```ts
// src/lib/api/pasaree.ts
export interface DrafProdukPayload {
  lapak_id: string; judul: string; kategori_kode: string; tag: string[];
  deskripsi: string; komposisi?: string; alergen?: string[];
}
export interface CekOtomatis { lolos: boolean; kesalahan: { kolom: string; pesan: string }[]; }
export interface SubPesananMasuk {
  sub_id: string; pesanan_id: string; varian: { sku: string; qty: number }[];
  total_idr: number; batas_konfirmasi_hingga: string; trip_id: string;
}
```

## 8. Penanganan Galat Web

- 401 → arahkan ke `/masuk`, simpan redirect.
- 409 `STOK_HABIS` pada konfirmasi → tampilkan stok terkini + tombol tolak-beralasan.
- 422 validasi → sorot kolom + pesan Indonesia, jangan reset formulir.
- Semua aksi tulis memakai optimistic UI dengan rollback bila gagal + toast berbahasa Indonesia.
