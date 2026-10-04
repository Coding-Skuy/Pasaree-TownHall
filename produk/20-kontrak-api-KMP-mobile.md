# 20 — Kontrak API Mobile KMP

> Varian 1. Konsumen: aplikasi KMP (Android+iOS, Compose Multiplatform + Navigation3). Basis: `https://api.pasaree.chefgenie.id/v1`. Auth: Bearer JWT akun pembeli. Format: JSON, UTF-8. Uang: integer IDR. Waktu: ISO-8601 UTC. Paginasi: cursor `?limit=&cursor=`.

## 1. Header & Kesalahan Baku

- Header wajib: `Authorization: Bearer <jwt>`, `X-Client: kmp-android|kmp-ios`, `X-App-Version: <semver>`.
- Kesalahan baku: `{ "kode": "STOK_HABIS", "pesan": "Stok varian berubah.", "detail": {...} }`.
- Kode kesalahan baku: `VALIDASI_GAGAL | AUTH_KEDALUWARSA | STOK_HABIS | HARGA_BERUBAH | TRIP_PENUH | PESANAN_TIDAK_DITEMUKAN | BATAS_LAJU`.

## 2. Katalog (baca, auth opsional)

### GET /katalog/produk

Query: `q, kategori, tag, lapak_slug, sort=harga_asc|harga_desc|rating|terbaru, limit (default 20, maks 50), cursor`.

Respons 200:

```json
{
  "data": [
    {
      "id": "01J...",
      "judul": "Sambal Bawang Mbok D — 150 g",
      "kategori_kode": "bumbu-dapur",
      "harga_mulai_idr": 18000,
      "rating_rata": 4.8,
      "lapak": { "slug": "mbok-d", "nama_lapak": "Mbok D" },
      "media_utama": "https://cdn.../foto.jpg"
    }
  ],
  "cursor_lanjut": "01J...",
  "harga_berubah": false
}
```

### GET /katalog/produk/{id}

Respons 200: objek `ProdukTayang` penuh (lihat `katalog/10-model-data.md`) + `stok_per_varian` + `trip_tersedia: [{trip_id, jadwal_berangkat, ongkir_idr}]`.

### GET /katalog/lapak/{slug}

Respons 200: profil lapak + daftar produk tayang + skor fulfillment (konfirmasi tepat waktu %, 90 hari).

## 3. Keranjang & Checkout (auth wajib)

### PUT /keranjang

Body: `{ "varian_id": "01J...", "qty": 2 }` (qty 0 = hapus). Respons 200: keranjang terhitung `{ item[], subtotal_idr, peringatan_stok[] }`.

### POST /checkout/pratinjau

Body: `{ "trip_id": "TRP-2026-001", "titik_id": "TITIK-05", "voucher": null }`.

Respons 200:

```json
{
  "subtotal_idr": 56000,
  "ongkir_idr": 8000,
  "biaya_layanan_idr": 2000,
  "total_idr": 66000,
  "kunci_stok_hingga": "2026-10-04T10:30:00Z",
  "peringatan": []
}
```

Jika stok/harga berubah → 409 dengan kode `STOK_HABIS` atau `HARGA_BERUBAH` + angka terbaru. Klien wajib menampilkan ulang pratinjau.

### POST /pesanan

Body sama dengan pratinjau + `catatan_per_lapak: {lapak_id: string}`. Respons 201: `{ "pesanan_id": "PSR-...", "batas_bayar_hingga": "...", "total_idr": 66000 }`.

## 4. Pembayaran & Pesanan (auth wajib)

### POST /pesanan/{id}/bayar

Body: `{ "metode": "VA_BCA | QRIS | EWALLET_DANA", "idempotency_key": "uuid" }`. Respons 200: `{ "status": "DIBAYAR", "escrow_id": "ESC-..." }`. Idempotensi wajib: kirim ulang dengan kunci sama menghasilkan respons identik tanpa tagihan ganda.

### GET /pesanan?status=&limit=&cursor=

Respons: ringkasan pesanan + sub-pesanan per lapak + status trip.

### GET /pesanan/{id}

Respons: detail penuh + timeline status + bukti serah trip + tombol aksi yang tersedia (`konfirmasi_terima`, `ajukan_retur` bila eligible).

### POST /pesanan/{id}/konfirmasi-terima

Respons 200: `{ "status": "SELESAI" }` (atau `DITERIMA_PEMBELI` bila ada sub-pesanan lain yang belum selesai).

### POST /ulasan

Body: `{ "pesanan_id": "...", "produk_id": "...", "bintang": 5, "teks": "...", "foto_url": [] }`. Aturan: 1 ulasan per produk per pesanan, ≤ 7 hari setelah selesai.

## 5. Trip Lumbung (baca, proksi)

### GET /trip?dari=&sampai=&titik_id=

Respons: jadwal trip dari Lumbung yang diproksi Pasaree + `sisa_kapasitas` + `ongkir_idr`. Cache 60 detik; jika Lumbung tidak respons, kembalikan cache terakhir dengan flag `basi: true`.

## 6. DTO Kotlin (shared)

```kotlin
@Serializable
data class PratinjauCheckout(val subtotalIdr: Int, val ongkirIdr: Int, val biayaLayananIdr: Int, val totalIdr: Int, val kunciStokHingga: String)
@Serializable
data class PesananRingkas(val id: String, val totalIdr: Int, val status: String, val dibuatPada: String)
```

## 7. Aturan Klien

1. Selalu panggil pratinjau sebelum buat pesanan; jangan hitung total di klien.
2. Simpan `idempotency_key` per upaya bayar hingga 24 jam untuk retry aman.
3. Poll status pesanan maksimal tiap 15 detik saat menunggu bayar/konfirmasi; gunakan push bila tersedia.
4. Tampilkan komisi sebagai info lapak, bukan beban pembeli.
