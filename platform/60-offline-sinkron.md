# 60 — Offline & Sinkronisasi

> Varian 1. Mobile offline-first; Web draf-tahan. Kebenaran akhir selalu server.

## 1. Cakupan

- Mobile KMP: katalog baca, keranjang tulis, checkout antre, konfirmasi terima antre.
- Web: draf produk autosave lokal + retry aksi stok/pesanan saat gagal jaringan.
- Bayar tidak pernah offline: tombol bayar nonaktif saat luring.

## 2. Penyimpanan Lokal (Mobile)

- SQLDelight tabel: `cache_produk (id, payload_json, diperbarui_pada, ttl)`, `keranjang (varian_id, qty, harga_snapshot_idr, diperbarui_pada)`, `antrean_aksi (id, jenis, payload_json, idempotency_key, percobaan, dibuat_pada)`.
- Jenis antrean: `TAMBAH_KERANJANG | CHECKOUT | KONFIRMASI_TERIMA | ULASAN`. Bayar dikecualikan.
- Setiap aksi antre membawa `idempotency_key` UUID yang dipertahankan hingga sukses 24 jam.

## 3. Alur Sinkron

1. Aplikasi online → `SyncWorker` mengirim antrean FIFO, satu per satu, menunggu respons server.
2. Server memvalidasi stok/harga/trip terkini. Jika berubah → 409 + angka terbaru → klien memperbarui cache + menandai item antrean `BUTUH_TINJAUAN` (tidak auto-retry buta).
3. Respons sukses → hapus dari antrean, perbarui cache, tampilkan notifikasi.
4. Gagal jaringan → backoff eksponensial (5 dtk, 30 dtk, 5 mnt) via WorkManager (Android) / BGTask (iOS). Maks 24 jam, lalu tandai kedaluwarsa dan minta pengguna mengulang pratinjau.

## 4. Resolusi Konflik

| Konflik | Pemenang | Perilaku klien |
|---|---|---|
| Stok/harga berubah | Server | tampilkan angka baru, minta konfirmasi ulang |
| Qty keranjang beda perangkat | Gabung `max(lokal, server)` lalu pratinjau ulang | beri tahu pengguna |
| Produk di-takedown saat di keranjang | Server | hapus item + jelaskan alasan |
| Trip penuh/dibatalkan | Server (Lumbung) | minta pilih trip lain, kunci stok diperpanjang 15 mnt |
| Ulasan ganda | Server (toleran 1 per produk per pesanan) | abaikan duplikat dengan kunci idempotensi sama |

## 5. Web (Draf-Tahan)

- Draf produk autosave ke `localStorage` tiap 10 detik + saat `beforeunload`.
- Aksi stok/konfirmasi gagal → simpan ke `IndexedDB outbox` + tombol "Kirim ulang" + spanduk luring.
- Tidak ada logika bayar di web, sehingga tidak ada antrean pembayaran.

## 6. Indikator & Pengujian

- Indikator global: `LURING | MENYINKRONKAN (n antre) | TERKINI | BUTUH_TINJAUAN`.
- Uji wajib: matikan jaringan di tengah checkout → antre → nyalakan → verifikasi tidak ada pesanan ganda (cek `idempotency_key`), stok/harga diperbarui, dan bayar tetap memerlukan online.
