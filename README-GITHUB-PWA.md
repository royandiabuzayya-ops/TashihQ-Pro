# TashihQ Pro — GitHub Pages & PWA Package

Paket ini sudah disiapkan dari aplikasi `index.html` yang diberikan, dengan file pendukung agar dapat:
- di-upload ke GitHub;
- dijalankan melalui GitHub Pages;
- dipasang di HP sebagai aplikasi PWA (Add to Home Screen / Install App);
- menggunakan ikon TashihQ Pro berdasarkan logo referensi;
- menggunakan Service Worker untuk membantu penggunaan offline setelah aplikasi pertama kali dibuka online.

## Struktur

```text
TashihQ-Pro-GitHub-PWA/
├── index.html
├── manifest.webmanifest
├── sw.js
├── logo-referensi.jpg
└── icons/
    ├── icon-1024.png
    ├── icon-512.png
    ├── icon-384.png
    ├── icon-192.png
    ├── icon-512-maskable.png
    ├── icon-192-maskable.png
    ├── apple-touch-icon.png
    ├── favicon-32.png
    └── favicon-16.png
```

## Cara upload ke GitHub

1. Buat repository baru di GitHub, misalnya `TashihQ-Pro`.
2. Upload **isi folder ini**, bukan file ZIP-nya.
3. Pastikan `index.html` berada di root repository.
4. Buka **Settings → Pages**.
5. Pada Source pilih **Deploy from a branch**.
6. Pilih branch `main` dan folder `/ (root)`.
7. Simpan dan tunggu sampai GitHub Pages selesai dipublikasikan.

## Cara instal di HP Android

1. Buka alamat GitHub Pages TashihQ Pro menggunakan Chrome.
2. Tunggu aplikasi selesai dimuat sekali ketika online.
3. Buka menu Chrome.
4. Pilih **Install app** atau **Tambahkan ke layar utama** (nama menu dapat berbeda).
5. Setelah terpasang, TashihQ Pro akan tampil seperti aplikasi di HP.

## Catatan penting

Paket ini membuat **PWA yang dapat di-install**, bukan file APK native.

Jika nanti ingin menjadi APK/AAB untuk distribusi Android, paket ini sudah memiliki komponen dasar PWA (manifest + service worker + ikon) yang dapat dilanjutkan ke proses packaging seperti PWABuilder/TWA.

## Catatan offline

Aplikasi asli sudah memiliki penyimpanan data lokal dan mekanisme cache Mushaf yang digunakan oleh aplikasinya. Service Worker di paket ini menambahkan lapisan cache aplikasi agar shell aplikasi dan resource yang pernah berhasil dimuat dapat digunakan kembali ketika koneksi terputus.

Untuk memastikan semua library CDN tersedia saat offline, buka aplikasi setidaknya sekali dalam kondisi online sebelum mencoba mode offline.
