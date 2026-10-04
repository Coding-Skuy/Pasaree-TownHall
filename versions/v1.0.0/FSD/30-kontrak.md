> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 30 — Kontrak API, Event, dan Galat

## Basis dan Autentikasi

Sumber isi lama: `produk/20-kontrak-api-KMP-mobile.md` dan `produk/21-kontrak-api-web-bun.md` yang dipindah dengan `git mv` ke file ini dan digabung.

- Basis: `https://api.pasaree.chefgenie.id/v1`. Format JSON UTF-8. Uang integer IDR. Waktu ISO-8601 UTC. Paginasi cursor `?limit=&cursor=`.
- Autentikasi dikunci: Bearer JWT dengan klaim audien tepat `pasaree`; contoh klaim `{"sub": "pengguna-01", "aud": "pasaree", "iss": "pasaree-auth"}`. Token lintas divisi tanpa audien pasaree ditolak 401. Proksi ke Lumbung memakai token layanan internal, bukan token pengguna. Basis data terpisah `pasaree`.
- Sehat: `GET /v1/kesehatan` kembali status ok, waktu server, dan status db pasaree.

## Kontrak Mobile KMP

Konsumen aplikasi KMP Android dan iOS Compose Multiplatform Navigation3. Header wajib: `Authorization: Bearer <jwt>`, `X-Client: kmp-android|kmp-ios`, `X-App-Version: <semver>`. Kesalahan baku berisi kode, pesan, detail dengan kode VALIDASI_GAGAL, AUTH_KEDALUWARSA, STOK_HABIS, HARGA_BERUBAH, TRIP_PENUH, PESANAN_TIDAK_DITEMUKAN, BATAS_LAJU.

- Katalog baca auth opsional: `GET /katalog/produk` query q, kategori, tag, lapak_slug, sort harga_asc, harga_desc, rating, terbaru, limit default 20 maksimal 50, cursor; respons data berisi id, judul, kategori_kode, harga_mulai_idr, rating_rata, lapak slug nama, media_utama, cursor_lanjut. `GET /katalog/produk/{id}` respons ProdukTayang penuh ditambah stok_per_varian dan trip_tersedia berisi trip_id, jadwal_berangkat, ongkir_idr. `GET /katalog/lapak/{slug}` respons profil lapak, produk tayang, skor fulfillment 90 hari.
- Keranjang dan checkout auth wajib: `PUT /keranjang` body varian_id dan qty dengan 0 berarti hapus; `POST /checkout/pratinjau` body trip_id, titik_id, voucher; respons subtotal_idr, ongkir_idr, biaya_layanan_idr, total_idr, kunci_stok_hingga. Stok atau harga berubah mengembalikan 409. `POST /pesanan` body pratinjau ditambah catatan_per_lapak; respons pesanan_id, batas_bayar_hingga, total_idr.
- Pembayaran dan pesanan: `POST /pesanan/{id}/bayar` body metode VA_BCA, QRIS, EWALLET_DANA dan idempotency_key; respons status DIBAYAR dan escrow_id. `GET /pesanan` ringkasan dan sub-pesanan per lapak. `POST /pesanan/{id}/konfirmasi-terima` menjadi SELESAI atau DITERIMA_PEMBELI. `POST /ulasan` 1 per produk per pesanan maksimal 7 hari.
- Trip Lumbung baca proksi: `GET /trip` query dari, sampai, titik_id; respons jadwal trip Lumbung diproksi ditambah sisa_kapasitas dan ongkir_idr; cache 60 detik; Lumbung tidak merespons mengembalikan cache terakhir flag basi true.
- Aturan klien: selalu pratinjau sebelum buat pesanan; simpan idempotency_key 24 jam; poll maksimal 15 detik; tampilkan komisi sebagai info lapak bukan beban pembeli.

## Kontrak Web Bun Lapak dan Admin

Konsumen web lapak dan admin Bun 1.4.x Svelte 5 SvelteKit 2 TS 5.9.x. SvelteKit memanggil API via server load dan actions agar token tidak terekspos; CSRF untuk cookie session; semua POST mutasi menerima header Idempotency-Key uuid; unggah media via presigned; paginasi cursor sama.

- Lapak: `GET /web/lapak-saya` daftar lapak, status, skor_fulfillment, saldo_tertahan_idr. `PATCH /web/lapak/{id}` nama, deskripsi, titik_serah_lumbung_id; slug tidak dapat diubah.
- Produk: `POST /web/produk/draf`; `PUT /web/produk/{id}` plus cek_otomatis lolos dan kesalahan per kolom; `POST /web/produk/{id}/ajukan-kurasi` atau 422; `GET /web/produk` filter status; `POST /web/media/presigned` foto maksimal 5 MB video maksimal 50 MB.
- Stok dan harga: `PATCH /web/varian/{id}/stok` otomatis TAYANG bila sebelumnya NONAKTIF_STOK_0; `PATCH /web/varian/{id}/harga` naik di atas 20 persen menandai kurasi_ulang_ringan.
- Pesanan masuk: `GET /web/pesanan-masuk` sub-pesanan, batas_konfirmasi_hingga, trip pembeli; `POST /web/pesanan-masuk/{sub_id}/konfirmasi` terima boolean; `POST /web/pesanan-masuk/{sub_id}/serah-trip` trip_id dan kode_serah cocok pindaian kurir Lumbung.
- Kurator dan admin: `GET /web/kurasi/antrean` umur_antrean_jam prioritas pangan segar; `POST /web/kurasi/{produk_id}/keputusan` SETUJU, REVISI, TOLAK dengan alasan_baku; `PATCH /web/admin/komisi` hanya admin dengan pengumuman 14 hari.
- Galat web: 401 ke masuk; 409 STOK_HABIS tampilkan terkini; 422 sorot kolom Indonesia; tulis memakai optimistic UI rollback dan toast Indonesia.

## Event dan Galat

- Event: produk.dibuat, harga.berubah, stok.berubah, tayang.berubah. Setiap event membawa id ULID, jenis, waktu ISO-8601 UTC, lapak_id, produk_id.
- Idempotensi: semua POST membawa UUID klien; kirim ulang sama kembali 200 duplikat true tanpa rekaman ganda. Bayar wajib idempotency-key.
- Galat baku: 401 audien salah; 403 peran ditolak; 409 stok, harga, trip berubah; 422 validasi; 429 maksimal 60 request per menit per perangkat.

## Batasan

Batasan dokumen ini: hanya kontrak, event, dan galat. Implementasi server ada di repo pasaree-backend-service. Konsumen dilarang menghitung total, komisi, atau payout di klien; semua wajib memakai angka server. Klien dilarang menyimpan token layanan Lumbung.
