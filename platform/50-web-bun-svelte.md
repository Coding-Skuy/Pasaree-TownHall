# 50 — Web Bun + Svelte (Lapak/Admin)

> Varian 1. Stack dikunci: Bun 1.4.x + Svelte 5 + SvelteKit 2 + TypeScript 5.9.x. Peran: lapak/produsen + kurator/admin. Tanpa desktop (tanpa Electron/Tauri); akses via browser.

## 1. Struktur Proyek

```
pasaree-web/
  bun.lockb + package.json (packageManager: bun@1.4.x)
  svelte.config.js + vite.config.ts
  src/
    routes/
      (publik)/ p/[id]/ + lapak/[slug]/        # etalase baca
      (lapak)/ dasbor/ produk/ tambah/ [id]/ stok/ pesanan-masuk/
      (kurator)/ kurasi/ takedown/
      (admin)/ kategori/ komisi/ audit/
      masuk/ + keluar/
    lib/
      api/pasaree.ts       # fetch terpusat + tipe dari produk/21-*
      stores/ (sesi, keranjang-baca, antrean-draf)
      komponen/ (KartuProduk, FormProduk, TabelStok, TimelinePesanan)
  static/ + cdn-upload/
```

## 2. Versi Dikunci

- `bun >=1.4.0 <1.5.0`, `svelte ^5.0.0`, `@sveltejs/kit ^2.0.0`, `typescript ~5.9.0`, `vite ^6.0.0`.
- Validasi formulir: `zod` + `sveltekit-superforms`. Tanggal: `date-fns` + zona Asia/Jakarta untuk tampil, UTC untuk kirim.
- Tabel: paginasi cursor server, tanpa fetch seluruh data.

## 3. Pola SvelteKit Wajib

1. Semua panggilan API lewat `+page.server.ts` / `+server.ts` (token di `httpOnly` cookie / `Authorization` server-side). Dilarang menyimpan JWT lapak di `localStorage`.
2. Form draf produk memakai `superforms` + autosave 10 detik ke `localStorage` per `produk_id` + tombol Ajukan Kurasi memanggil action server.
3. Unggah media: minta presigned → `PUT` ke CDN dari server SvelteKit (bukan langsung browser bila > 5 MB) → simpan `media_url`.
4. Optimistic UI untuk stok/harga dengan rollback + toast Indonesia.
5. Akses peran dijaga di `hooks.server.ts`: produsen hanya lapaknya, kurator semua lapak baca-tulis kurasi, admin penuh.

## 4. Halaman & Aksi

| Rute | Aksi server |
|---|---|
| `dasbor/` | load: ringkasan penjualan 30 hari, pesanan menunggu, saldo tertahan |
| `produk/` | load: daftar + filter status; action: arsipkan |
| `produk/tambah/` | action: buat draf, unggah media, ajukan kurasi |
| `produk/[id]/` | action: simpan draf, ajukan ulang, nonaktifkan |
| `stok/` | action: ubah stok/harga massal (maks 50 baris) |
| `pesanan-masuk/` | action: konfirmasi, serah-trip |
| `kurasi/` | action: setuju/revisi/tolak (kurator) |
| `komisi/` | tampilkan tarif + riwayat (admin ubah) |

## 5. Kinerja & Operasional

- SSR untuk etalase publik (SEO lapak/produk), CSR+SPA untuk dasbor lapak.
- Target LCP etalase ≤ 2,5 dtk, dasbor interaktif ≤ 1,5 dtk (cache CDN 60 dtk untuk GET publik).
- Perintah baku: `bun install`, `bun run dev`, `bun run build`, `bun run preview`, `bunx svelte-check`, `bunx tsc --noEmit`.
- CI: `bun install --frozen-lockfile` + `svelte-check` + `tsc --noEmit` + build. Gagal bila ada galat tipe.

## 6. Kriteria Selesai Varian 1

- [ ] Produsen dapat draf → ajukan → tayang tanpa menyentuh mobile.
- [ ] Kurator dapat mengosongkan antrean 50 item dalam 1 sesi tanpa reload manual.
- [ ] Tidak ada `any` tak beralasan; semua respons API bertipe dari `lib/api/pasaree.ts`.
