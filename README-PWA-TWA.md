# TashihQ Pro — Paket PWA untuk Android APK (PWABuilder / TWA)

Paket ini berisi semua berkas pendukung agar `index.html` (aplikasi TashihQ Pro)
bisa dikenali sebagai PWA yang valid oleh **PWABuilder**, lalu dibungkus jadi
APK/AAB Android via **Trusted Web Activity (TWA)**.

## Isi paket

```
tashihq-pro-pwa/
├── index.html                     ← aplikasi utama (sudah ditautkan ke manifest & service worker)
├── manifest.webmanifest           ← Web App Manifest (nama, ikon, warna, dll)
├── sw.js                          ← Service Worker (agar bisa dibuka offline / status "installable")
├── icons/                         ← 13 ikon (72–512px, termasuk versi maskable, favicon, apple-touch-icon)
└── .well-known/
    └── assetlinks.json            ← TEMPLATE Digital Asset Links (WAJIB diganti, lihat Langkah 4)
```

## Langkah 1 — Hosting (wajib HTTPS)

PWABuilder & TWA **mengharuskan** aplikasi diakses lewat HTTPS (bukan `file://`
atau `http://` biasa). Upload seluruh isi folder ini (jaga strukturnya, jangan
diacak) ke hosting statis, misalnya:

- **Netlify** / **Vercel** (drag-and-drop folder, gratis, otomatis HTTPS)
- **GitHub Pages**
- **Firebase Hosting**
- Hosting sekolah yang sudah ada (asal support HTTPS)

Pastikan setelah upload, `index.html` bisa diakses langsung di root domain,
misalnya `https://tashihq-sembalun.netlify.app/index.html`, dan
`https://tashihq-sembalun.netlify.app/manifest.webmanifest` juga bisa dibuka.

> Jika nanti hostingnya di sub-folder (misal `https://domain.com/tashihq/`),
> buka `manifest.webmanifest` dan sesuaikan `"start_url"` dan `"scope"` agar
> cocok dengan path tersebut.

## Langkah 2 — Cek kelayakan PWA di PWABuilder

1. Buka **https://www.pwabuilder.com/**
2. Masukkan URL situs yang sudah di-hosting, klik **Start**.
3. PWABuilder akan menilai 3 hal: **Manifest**, **Service Worker**, **Security (HTTPS)**.
   Ketiganya seharusnya sudah ✅ berkat berkas di paket ini.
4. Jika ada catatan kuning/merah soal ikon, biasanya hanya saran tambahan
   (misalnya screenshot untuk Play Store listing) — bisa dilengkapi belakangan.

## Langkah 3 — Build paket Android (TWA)

1. Di halaman hasil scan PWABuilder, klik **"Package for Store"** → pilih **Android**.
2. Isi form yang muncul:
   - **Package ID**: identitas unik aplikasi, format kebalikan domain.
     Contoh: `com.tashihqsembalun.app` (harus unik, tidak bisa diganti setelah rilis di Play Store).
   - **App name**: `TashihQ Pro`
   - **Signing key**: pilih **"Create new signing key"** kalau ini rilis pertama
     (PWABuilder akan generate keystore untuk Anda — **unduh dan simpan file
     keystore ini baik-baik**, dibutuhkan lagi setiap kali update aplikasi ke Play Store).
   - Opsi lain (status bar color, splash screen, dsb) boleh dibiarkan default —
     nilainya sudah diambil otomatis dari `manifest.webmanifest`.
3. Klik **Generate** / **Download**. Anda akan menerima file `.zip` berisi:
   - `app-release-signed.aab` (untuk upload ke Play Console) atau `.apk` (untuk install langsung/testing)
   - `signing.keystore` + `signing-key-info.txt` (info password keystore — **JAGA FILE INI, JANGAN HILANG**)
   - **`assetlinks.json`** yang sudah otomatis terisi `package_name` dan `sha256_cert_fingerprints` yang BENAR sesuai keystore Anda

## Langkah 4 — Pasang Digital Asset Links (WAJIB, agar tidak muncul address bar)

Tanpa langkah ini, APK tetap jalan tapi akan menampilkan address bar Chrome di
atas (mode browser biasa), bukan tampilan aplikasi native penuh.

1. **Ganti** file `.well-known/assetlinks.json` di paket ini dengan
   `assetlinks.json` yang baru saja diberikan oleh PWABuilder di Langkah 3
   (bukan yang template placeholder ini).
2. Upload ulang file tersebut ke hosting, tepat di path:
   `https://domain-anda.com/.well-known/assetlinks.json`
3. Verifikasi filenya sudah benar dan bisa diakses via:
   `https://developers.google.com/digital-asset-links/tools/generator`
   (masukkan domain + package name, klik **Generate Statement** → **Test Statement**)

## Langkah 5 — Coba APK-nya

- Untuk sekadar dicoba di HP: install file `.apk` langsung (perlu izin
  "install dari sumber tidak dikenal" di Android).
- Untuk dirilis publik: upload file `.aab` ke **Google Play Console**
  (butuh akun developer, sekali bayar USD 25).

## Catatan penting

- **Data santri (localStorage/IndexedDB) bersifat per-origin** — artinya data
  tersimpan berdasarkan domain hosting. Selama domain tidak berubah, data
  aman walau APK diupdate. Kalau ganti domain, data lama tidak ikut pindah
  (tetap disarankan pakai fitur **Ekspor Data JSON** di tab "Santri & Data"
  sebagai cadangan rutin).
- **Update konten**: setiap kali `index.html` di-upload ulang dengan
  perubahan, naikkan angka `CACHE_VERSION` di `sw.js` (baris paling atas),
  supaya pengguna lama otomatis menerima versi terbaru, bukan versi cache lama.
- **Koneksi internet saat pertama kali dibuka tetap diperlukan** karena
  Tailwind CSS, FontAwesome, Chart.js, dan Google Fonts (Amiri) dimuat dari
  CDN. Setelah dibuka sekali, `sw.js` akan menyimpan cache-nya sehingga
  pemakaian berikutnya lebih tahan koneksi lambat/putus-putus. Kalau target
  penggunaan benar-benar 100% offline sejak instalasi awal (tanpa internet
  sama sekali), CDN tersebut perlu diunduh & disimpan lokal (self-host) —
  ini bisa dikerjakan menyusul kalau dibutuhkan.
- **Play Store mewajibkan Privacy Policy URL** saat submit aplikasi (walau
  aplikasi ini menyimpan data hanya secara lokal di perangkat, bukan ke
  server). Siapkan halaman singkat yang menjelaskan hal ini sebelum submit.
- **Alternatif ke PWABuilder**: kalau ingin kontrol lebih detail, bisa juga
  pakai **Bubblewrap CLI** (`npm i -g @bubblewrap/cli`, lalu
  `bubblewrap init --manifest=https://domain-anda.com/manifest.webmanifest`).
  Hasil akhirnya sama (APK/AAB + assetlinks.json), hanya prosesnya lewat
  command line.
