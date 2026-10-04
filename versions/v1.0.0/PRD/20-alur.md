> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 20 — Alur Produk

## Alur Beli Multi-Lapak

Sumber isi lama: `produk/10-alur-beli.md` yang dipindah dengan `git mv` ke file ini. Berlaku untuk Mobile KMP pembeli dan Web lapak atau admin dengan etalase baca. Pembayaran via kasir Pasaree, payout via Lumbung, pengiriman fisik via trip Lumbung.

1. Jelajah dan keranjang via mobile utama. Pembeli menambah varian dengan stok di atas 0. Keranjang tersimpan lokal dan tersinkron ke akun. Validasi stok ulang saat checkout.
2. Checkout. Pembeli memilih trip Lumbung berisi jadwal dan titik ambil atau antar, catatan per lapak, voucher bila ada. Sistem menghitung subtotal ditambah ongkir per trip ditambah biaya layanan transparan. Komisi lapak tidak dibebankan ke pembeli.
3. Bayar. Kasir Pasaree memproses pembayaran. Batas bayar 30 menit untuk DIPESAN; lewat itu otomatis DIBATALKAN dan stok dikembalikan. Bayar tidak pernah offline.
4. Konfirmasi lapak. Lapak wajib konfirmasi maksimal 2 jam jam kerja 07.00 sampai 19.00. Tanpa konfirmasi otomatis batal, dana kembali penuh, penalti skor lapak.
5. Siapkan dan serah ke trip. Lapak mengemas sesuai instruksi label pesanan dan tanggal produksi, menyerahkan pada trip yang dipilih. Kurir trip memindai serah-terima menjadi status DISERAHKAN_KE_TRIP.
6. Terima. Pembeli mengonfirmasi terima di mobile atau otomatis 2x24 jam setelah trip tiba. Dana escrow dilepas ke payout.
7. Selesai dan ulasan. Pembeli dapat memberi ulasan 1 sampai 5 dan foto maksimal 7 hari. Ulasan memengaruhi rating dan bobot pencarian.

## Status Pesanan

DRAF_KERANJANG menjadi DIPESAN menjadi DIBAYAR menjadi DIKONFIRMASI_LAPAK menjadi DISERAHKAN_KE_TRIP menjadi DITERIMA_PEMBELI menjadi SELESAI. Cabang: DIBATALKAN dari DIPESAN, DIBAYAR, DIKONFIRMASI_LAPAK dengan aturan; DIKEMBALIKAN dari DITERIMA_PEMBELI maksimal 1x24 jam khusus non-pangan segar.

## Aturan Keranjang Multi-Lapak

- Satu checkout dapat memuat banyak lapak tetapi dikelompokkan per lapak menjadi sub-pesanan dengan status masing-masing.
- Ongkir dihitung per trip bukan per lapak. Sub-pesanan batal tidak mengembalikan ongkir kecuali seluruh checkout batal sebelum serah ke trip.
- Stok dikunci reservasi selama 30 menit pada status DIPESAN. Kegagalan bayar melepas kunci.

## Pembatalan dan Pengembalian

- Batal sebelum bayar: tidak ada gerakan dana; kunci dilepas.
- Batal setelah bayar sebelum konfirmasi lapak: kembali 100 persen maksimal 1x24 jam; stok kembali.
- Batal oleh lapak setelah konfirmasi: kembali 100 persen ditambah voucher ongkir trip berikutnya; stok kembali.
- Batal oleh pembeli setelah konfirmasi: kembali subtotal dikurangi 10 persen penalti ke lapak; ongkir hangus bila sudah diserahkan ke trip; stok tidak kembali bila sudah diserahkan.
- Retur non-pangan segar maksimal 1x24 jam: kembali 100 persen setelah barang diterima lapak; stok bertambah bila layak jual. Pangan segar tidak dapat diretur kecuali salah kirim atau basi saat terima dengan bukti foto maksimal 3 jam.

## Integrasi Trip Lumbung

- Pasaree membaca jadwal trip dari API Lumbung distribusi: trip_id, jadwal_berangkat, rute, titik_serah, kapasitas, ongkir. Pasaree tidak membuat trip sendiri.
- Bukti serah-terima trip menjadi dasar pelepasan escrow. Sengketa mengacu pada log pindaian Lumbung.

## Batasan

Batasan alur ini: hanya jelajah, keranjang, checkout, bayar, konfirmasi, serah trip, terima, ulasan. Di luar batas: pengolahan dapur, penjualan di luar katalog, dan pengantar ke rumah konsumen. Kegagalan kirim notifikasi push dan WhatsApp tidak membatalkan status tetapi dicatat di audit.
