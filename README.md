# Instagram Connection Analyzer (instalyze)

Aplikasi React untuk menganalisis data `followers` dan `following` dari export Instagram. UI ini membantu melihat akun mutual, akun yang tidak follow back, dan akun yang belum Anda follow balik tanpa perlu mengunggah data ke server lain.

Produk ini dikenal juga dengan nama **instalyze**. Coba versi live di https://instalyze.ghaniyyirrahmans.me.

Untuk versi publik/deploy, data JSON dipilih langsung oleh user melalui file picker atau drag & drop, lalu diproses lokal di browser.

## Fitur

- **Upload interaktif** untuk `followers_1.json` dan `following.json` via file picker maupun drag & drop.
- **Ringkasan jumlah akun** untuk followers, following, mutual, dan tidak follow back.
- **Skor Follow back rate**, yaitu persentase akun mutual dari total following.
- **Tab hasil** untuk berpindah kategori analisis: Tidak Follow Back, Belum Anda Follow, dan Mutual.
- **Pencarian username** di setiap kategori.
- **Urutkan A-Z / Z-A**.
- **Copy hasil** ke clipboard.
- **Export CSV** untuk daftar yang sedang tampil (nama file mengikuti tab aktif, misalnya `notFollowingBack.csv`).
- **Link ke profil Instagram** pada setiap username hasil analisa.
- **Tombol Reset** untuk mengosongkan seluruh data yang sudah dimuat.
- **Panduan in-app "Cara dapat JSON"** berupa modal berisi langkah download data, plus link ke halaman panduan `/cara-download-data-instagram/`.
- **Baca dari folder data saat local dev** melalui tombol **Muat data**. Tombol ini tidak muncul di production build.
- **Mode CLI tetap tersedia** melalui `npm run cli` atau `node index.js`.

## Tech Stack

- **React 19** dan **React DOM 19** untuk UI.
- **Vite 7** sebagai bundler dan dev server.
- **lucide-react** untuk ikon.
- **@vitejs/plugin-react** untuk dukungan JSX dan fast refresh saat development.
- Tanpa backend: aplikasi berjalan sepenuhnya di sisi client dan dibuild sebagai static site.

## Prasyarat

- Node.js 20.19+ atau 22.12+ (mengikuti requirement Vite 7).
- File data Instagram:
  - `followers_1.json`
  - `following.json`

## Cara Mendapatkan Data Instagram

1. Buka Instagram.
2. Masuk ke **Settings and privacy**.
3. Pilih **Accounts Center**.
4. Pilih **Your information and permissions**.
5. Pilih **Download your information**.
6. Buat request download untuk akun Instagram Anda.
7. Pilih **Select types of information**.
8. Centang hanya **Followers and following**.
9. Pilih format **JSON**.
10. Submit request dan tunggu file dari Instagram tersedia.

Setelah file ZIP diunduh, ekstrak lalu cari folder `followers_and_following`. Di dalamnya biasanya ada `followers_1.json` dan `following.json`.

## Menyiapkan Folder Data

Simpan file Instagram di folder `data/`:

```text
data
├── followers_1.json
└── following.json
```

File `.json` di folder `data/` sudah di-ignore oleh Git agar data pribadi tidak ikut commit.

## Menjalankan UI React

Clone atau download project ini, lalu jalankan:

```bash
npm install
npm run dev
```

Buka URL yang muncul di terminal, biasanya:

```text
http://localhost:5173
```

Karena dev server berjalan dengan `--host 0.0.0.0`, aplikasi juga bisa dibuka melalui IP address perangkat ini dari device lain dalam satu jaringan lokal.

Di halaman aplikasi saat local dev:

1. Klik **Muat data** pada section "Folder data local dev" untuk membaca `data/followers_1.json` dan `data/following.json`.
2. Jika tidak memakai folder `data/`, pilih file manual pada panel **Followers** dan **Following**.
3. Lihat hasil pada tab **Tidak Follow Back**, **Belum Anda Follow**, dan **Mutual**.
4. Gunakan pencarian, tombol urutkan, copy, atau download CSV sesuai kebutuhan.

Saat aplikasi sudah dideploy, user cukup memilih file manual pada panel **Followers** dan **Following**. File diproses di browser user dan tidak diupload ke server.

## Build dan Deploy

Membuat build static:

```bash
npm run build
```

Output build ada di folder `dist/`. Untuk mengecek hasil build:

```bash
npm run preview
```

Karena berupa static site, project ini bisa dideploy ke layanan static hosting seperti Vercel (Framework Preset: Vite, Output Directory: `dist`). Untuk deployment publik, jangan deploy file Instagram pribadi — biarkan user memilih file lewat browser. Tombol **Muat data** tidak muncul di production, sehingga aplikasi tidak mencoba membaca file JSON dari server.

## SEO dan Sitemap

`public/sitemap.xml` dan `public/robots.txt` di-generate oleh script `scripts/generate-sitemap.cjs`. Script ini bisa dijalankan manual lewat `npm run seo` dan juga otomatis berjalan sebelum setiap `npm run build`.

Default URL production: `https://instalyze.ghaniyyirrahmans.me` (bisa dioverride dengan `SITE_URL=https://domain-baru.example npm run seo`).

Sitemap memuat tiga halaman: `/`, `/cara-download-data-instagram/` (versi Indonesia), dan `/how-to-download-instagram-data/` (versi Inggris). Kedua halaman panduan adalah file HTML statis di `public/` yang saling merujuk lewat tag `hreflang`. Copy halaman utama ditulis bilingual Indonesia-Inggris untuk menjangkau pencarian seperti "cek tidak follow back Instagram" dan "Instagram unfollowers".

## Struktur Project

```text
Analyzer-Connection-Instagram
├── data/                              # followers_1.json + following.json (git-ignored)
├── public/
│   ├── cara-download-data-instagram/
│   │   └── index.html                 # halaman panduan (id)
│   ├── how-to-download-instagram-data/
│   │   └── index.html                 # halaman panduan (en)
│   ├── robots.txt                     # di-generate oleh npm run seo
│   └── sitemap.xml                    # di-generate oleh npm run seo
├── scripts/
│   └── generate-sitemap.cjs           # generator sitemap + robots
├── src/
│   ├── App.css
│   ├── App.jsx
│   └── main.jsx
├── index.html                         # entry HTML + meta SEO
├── index.js                           # mode CLI
├── vite.config.mjs                    # config Vite + middleware /data untuk dev
└── package.json
```

Folder `dist/` dan `node_modules/` tidak ikut ter-commit karena sudah masuk `.gitignore`.

## Menjalankan Mode CLI

Mode CLI lama masih tersedia. Pastikan kedua file JSON sudah ada di folder `data/` (lihat **Menyiapkan Folder Data**), lalu jalankan:

```bash
npm run cli
```

atau:

```bash
node index.js
```

Catatan parsing CLI:

- File JSON dapat berupa root array maupun memiliki key `relationships_followers` atau `relationships_following`.
- Username dinormalkan terlebih dahulu: di-trim, di-lowercase, di-deduplikasi, lalu diurutkan A-Z.

## Contoh Output CLI

```text
[INFO] Successfully loaded 82 Followers from followers_1.json.
[INFO] Successfully loaded 76 Following from following.json.

--- Results ---

Mutual Followers (55):
- username_1
- username_2

Who is not following you back (21):
- username99
- username98

Who you don't follow back (27):
- username73
- username72
```

## Catatan Privasi

UI React memproses file JSON di browser menggunakan `FileReader`. File user tidak diupload ke server hosting, tidak dikirim ke backend, dan tidak disimpan oleh aplikasi.

Aplikasi ini tidak memiliki API upload. Selama tidak ditambahkan backend baru yang menerima file, data user tetap berada di device/browser mereka.

Folder `data/` hanya dibaca lewat middleware dev server Vite saat mode development, dan tombolnya tidak ikut di-build ke production. File Instagram pribadi tidak perlu dan sebaiknya tidak ikut dideploy.

## Credit

Program ini dibuat dan dikembangkan oleh **ghanirahmans** untuk keperluan portofolio.

## License

This project is licensed under the MIT License. Lisensi MIT juga dideklarasikan pada field `license` di `package.json`; repo ini belum menyertakan file `LICENSE` fisik.
