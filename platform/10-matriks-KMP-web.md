# 10 — Matriks KMP vs Web

> Varian 1. Prinsip: satu kebenaran bisnis, dua permukaan. Mobile KMP = beli. Web Bun = jual + kelola.

## 1. Matriks

| Kemampuan | Mobile KMP (pembeli) | Web Bun SvelteKit (lapak/admin) | Keterangan |
|---|---|---|---|
| Jelajah katalog, cari, filter | Ya (utama) | Ya (baca saja, etalase) | API baca sama |
| Detail produk, ulasan | Ya | Ya (baca) | — |
| Keranjang, checkout, bayar | Ya (satu-satunya alur bayar penuh) | Tidak (web hanya pratinjau etalase, tanpa bayar) | cegah duplikasi kasir |
| Lacak pesanan, konfirmasi terima | Ya | Tidak (lapak melihat pesanan masuk saja) | — |
| Tayang produk, kurasi | Tidak | Ya (satu-satunya) | SOP di `lapak/10-*` |
| Stok & harga | Baca | Ya (tulis) | server-menang |
| Konfirmasi & serah-trip lapak | Tidak | Ya | — |
| Antrean kurasi, takedown | Tidak | Ya (kurator/admin) | — |
| Komisi, payout, laporan | Tidak | Ya (ringkasan + tautan Lumbung) | kas penuh di Lumbung |
| Offline-first | Ya (SQLDelight + antrean) | Ya terbatas (draf tersimpan + retry) | lihat `platform/60-*` |
| Notifikasi | Push + in-app | Email + WhatsApp lapak + in-app | — |

## 2. Aturan Anti-Duplikasi

1. Kalkulasi total, ongkir, biaya layanan hanya di server. Klien (KMP maupun Web) tidak menghitung sendiri untuk keputusan bayar/payout.
2. Model data katalog tunggal (`katalog/10-model-data.md`) menghasilkan DTO Kotlin (KMP) dan TS (Web). Perubahan skema wajib mengubah kedua sisi dalam satu rilis minor.
3. Auth terpisah per peran: JWT pembeli (mobile) vs JWT lapak/kurator (web). Token tidak dapat dipertukarkan.
4. Trip Lumbung hanya dibaca (proksi). Baik KMP maupun Web tidak menulis jadwal trip.

## 3. Alasan Pembagian

- Pembeli mayoritas di HP (Android+iOS) dan butuh offline pasar/trip → KMP + Compose Multiplatform.
- Produsen mengelola foto, stok, dan pesanan di perangkat besar + butuh cetak label → Web SvelteKit.
- Tanpa desktop: tidak ada target JVM-desktop (KMP) maupun Electron/Tauri (Web). Web desktop diakses via browser saja, bukan aplikasi desktop.

## 4. Rilis Serentak

- Perubahan kontrak API minor (tambah field opsional) boleh rilis web dulu, mobile menyusul ≤ 14 hari.
- Perubahan mayor (ubah status pesanan, tambah langkah bayar) wajib rilis bersama + flag kompatibilitas `X-App-Version` minimum.
