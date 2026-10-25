# AGENTS.md — Template Blogger Baru untuk terapijarum.com

Dokumen ini adalah panduan utama untuk agen AI (GitHub Copilot coding agent, Claude Code, dsb.) yang mengerjakan repo ini. Baca seluruh file ini sebelum menulis kode. Urutan kerja ada di `docs/TASKS.md`.

## 1. Tujuan

Membuat **template Blogger baru** untuk blog edukasi akupunktur/TCM berbahasa Indonesia (terapijarum.com) sebagai pengganti template Avia v3.4.2 (Theme62).

Syarat utama:
- Fitur setara template lama (lihat bagian 4), kecuali yang dibuang.
- Desain **lebih bersih, modern, dan mudah dibaca**, mobile-first.
- **100% mandiri**: tanpa ketergantungan ke `theme62.pages.dev` atau kunci lisensi Theme62.
- Cepat: font sistem, JS vanilla, tanpa library besar.
- CSS/JS dibangun di repo ini (privat), dipublikasikan ke **repo publik terpisah** yang hanya berisi hasil build, lalu dimuat lewat jsDelivr (lihat bagian 2A).

Pemilik repo: Dahlan (Azdahdev). Bahasa kerja dan antarmuka: **Bahasa Indonesia**. Pembaca blog: praktisi dan mahasiswa akupunktur di Indonesia.

## 2. Keputusan yang sudah final (jangan diubah tanpa izin)

| Topik | Keputusan |
|---|---|
| Ketergantungan Theme62 | Dibuang. Semua JS ditulis ulang sebagai vanilla JS buatan sendiri. |
| Dipertahankan | dark mode, TOC, lightbox, bookmark, safelink, shortlink, shortcode download-box, related posts, lazyload, load-more, autolink Pointera, **disable copy/paste**, **watermark**, **SPA**, live search, form kontak, halaman daftar isi, share button, author profile, komentar Blogger, 404. |
| Dibuang | **AMP**, **PWA** (manifest, service worker), **split post**. Hapus semua cabang kode `amp` dari template lama. |
| Warna utama | Hijau `#278c5f` dipertahankan. |
| Font | Font sistem (tanpa webfont). |
| Iklan | Posisi AdSense sama seperti sekarang, tapi tampilannya dirapikan agar tidak mengganggu bacaan. |
| Distribusi aset | CSS/JS dimuat lewat jsDelivr dari **repo publik terpisah** dengan **versi/tag** (lihat bagian 2A). |
| Repositori | Dua repo: `azdah/templateblogTerapijarum` (privat, pengembangan) dan `azdah/templateblogTerapijarum-dist` (publik, hanya hasil build). |
| Pengujian | Dipasang manual oleh pemilik di `azdahtester.blogspot.com`, baru dipasang di terapijarum.com setelah lolos. |

Catatan: SPA tidak termasuk daftar yang dibuang, jadi dipertahankan. Karena rawan bug di Blogger, SPA harus berupa **peningkatan progresif**: kalau JS gagal atau dimatikan, link biasa tetap berfungsi sebagai navigasi penuh.

## 2A. Dua repositori dan alur publikasi

| | Repo pengembangan | Repo distribusi |
|---|---|---|
| Nama | `azdah/templateblogTerapijarum` | `azdah/templateblogTerapijarum-dist` |
| Visibilitas | **Privat** | **Publik** (wajib, agar jsDelivr bisa membaca) |
| Isi | semua kode sumber, `docs/`, referensi lama | **hanya** `theme.xml`, `theme.min.css`, `theme.min.js`, `README.md` singkat |
| Dikerjakan oleh | agen (lewat PR) | **hanya oleh GitHub Actions**, bukan manual dan bukan agen langsung |

Aturan:
1. Isi repo distribusi harus dihasilkan otomatis oleh workflow di repo pengembangan. Berkas di repo distribusi berada di **root** (tanpa folder `dist/`).
2. **Dilarang** ada di repo distribusi: berkas di `docs/` (terutama `theme-*.xml` dan `script-*.js` lama), kode sumber, `AGENTS.md`, `TASKS.md`, rahasia apa pun. Skrip `validate` harus memastikan hasil build tidak mengandung `theme62`.
3. Setiap PR menghasilkan **build pratinjau**: CSS/JS dipublikasikan ke repo distribusi (cabang `preview`), lalu `theme.xml` dibangun dengan URL jsDelivr yang menunjuk ke **commit SHA** itu (`@<sha>`, tidak berubah dan bebas masalah cache). `theme.xml` pratinjau disediakan sebagai **artifact GitHub Actions** pada PR agar pemilik bisa mengunggahnya ke blog uji.
4. **Rilis** dilakukan dengan tag versi (`v0.1.0`, `v1.0.0`) di repo pengembangan. Workflow lalu mempublikasikan hasil build ke cabang `main` repo distribusi dan memberi **tag yang sama** di sana. `theme.xml` rilis menunjuk ke `@vX.Y.Z`.
5. Workflow membutuhkan kredensial untuk mendorong ke repo distribusi. **Pemilik** yang membuatnya (deploy key atau token terbatas khusus repo distribusi) dan menyimpannya di GitHub Secrets repo pengembangan dengan nama `DIST_REPO_TOKEN`. Agen hanya merujuk nama secret itu, tidak pernah menulis nilainya, dan menjelaskan langkahnya di `docs/INSTALL.md`.
6. Bila rancangan di atas ternyata tidak bisa dijalankan (misalnya batasan izin Actions), jelaskan kendalanya di PR dan usulkan alternatif paling sederhana. Jangan mengubah repo distribusi menjadi tempat kode sumber.

## 3. Referensi template lama

Template lama (Avia v3.4.2, ekspor Blogger) ada di `docs/theme-6290942502869749790.xml`. Berkas ini **hanya-baca**: jangan diubah, dipindah, atau ditimpa oleh proses build. Gunakan sebagai referensi perilaku dan struktur, **bukan untuk disalin mentah**. Jangan menyalin kunci lisensi, domain `theme62.pages.dev`, atau credit Theme62 ke template baru.

Untuk memahami perilaku fitur (sintaks shortcode, alur safelink, format shortlink, dsb.), agen boleh membaca script lama di `https://theme62.pages.dev/template/js/script-3.4.1.js` bila jaringan memungkinkan. Jika tidak bisa diakses, minta pemilik menaruh salinannya di `docs/script-3.4.1.js` (juga hanya-baca). Tulis ulang logikanya dengan kode sendiri.

Folder `docs/` di repo ini berisi dua jenis berkas: dokumen kerja (`TASKS.md`, `FEATURES.md`, `SETTINGS.md`, `INSTALL.md`, `TESTING.md`) dan referensi lama (`theme-*.xml`, `script-3.4.1.js`). `scripts/validate.mjs` hanya memeriksa `dist/` dan `src/`, **tidak** memeriksa referensi lama (yang memang berisi `theme62` dan AMP).

## 4. Inventaris fitur yang harus ada

### 4.1 Tampilan dan navigasi
- Header tetap (sembunyi saat scroll ke bawah, muncul saat scroll ke atas), tombol menu, pencarian, dark mode, bookmark.
- Menu samping (drawer) berisi: Beranda, Daftar Isi, Affiliate Azdah, Toko, Cari Terapis, Penanganan Terapi, Aplikasi (Pointera, simulator, tes, kalkulator), Kelas Online, Download, Safelink. Item menu persis seperti pada widget `HTML100` di template lama, hanya tampilannya diperbarui.
- Tombol "Back to Top".
- Halaman beranda: daftar artikel terbaru (load-more) dan blok "Post by Labels" (label Unduhan).
- Halaman label, arsip, dan hasil pencarian.
- Halaman 404 yang juga menangani shortlink (lihat 4.4).

### 4.2 Halaman artikel
- Hero judul (lebih ringkas dari 415px lama, terutama di HP), breadcrumb, label, penulis, tanggal, jumlah komentar, tombol bookmark.
- Daftar isi (TOC) otomatis dari heading, posisi seperti pengaturan lama (setelah paragraf ke-2), label "Daftar Isi", bisa dibuka/ditutup.
- Lightbox untuk gambar di dalam artikel.
- Share button (Facebook, WhatsApp, X, Telegram, Line, Tumblr, LinkedIn, Pinterest, share sistem).
- Author profile, related posts (berdasarkan label), pagination artikel lama/baru, komentar Blogger.
- Slot iklan `{post:before}`, `{post:middle}`, `{post:after}`, `{adsense:display}`, `{adsense:multiplex}`, serta "Insert to Post" (iklan tengah artikel, dengan blacklist).

### 4.3 Perlindungan dan pengaman konten
- **Disable copy/paste**: aktif secara default lewat pengaturan. Tidak boleh menghalangi input, textarea, form komentar, dan blok `code/pre`. Harus bisa dimatikan lewat satu pengaturan.
- **Watermark** gambar artikel: tipe gambar (logo terapijarum) atau teks, ukuran, opacity, posisi, whitelist/blacklist selector, bisa dimatikan lewat pengaturan. Status awal: nonaktif (sama seperti sekarang).

### 4.4 Link
- **Safelink**: halaman `/safelink`, hitung mundur 10 detik, tombol "Please wait.." → "Click here to continue" → "Get link", tautan acak ke artikel sebagai perantara, mode otomatis untuk domain `drive.google.com`.
- **Shortlink**: `shortpermalink` untuk artikel dan daftar link kustom (`/lisensi-ma`, `/wac`, `/login-affiliate`, `/faq-ma`, `/ebook-tongue-diagnosis`, `/ebook-tan`, `/dekode-yinyang`, dst.). Daftar ini dibaca dari widget pengaturan, bukan di-hardcode.
- **Autolink Pointera**: pertahankan pemuatan `https://app.terapijarum.com/pointera/blogger/pointera-tcm-autolink.js` (defer) beserta gaya `.pointera-tcm-point-link`. Jangan mengubah script eksternal itu.

### 4.5 Komponen konten (kompatibilitas artikel lama)
Ribuan artikel lama memakai kelas HTML dari Avia. **Semua kelas ini harus tetap bekerja**, dengan tampilan baru:
`.box-download` (+ shortcode `{{nama}} {{size}} {{url}}`), `.button` (+ `.outlined`, `.rounded`, `.icon-download`), `.alert` (`-success/-info/-warning/-error`), `.accordion`, `.nav-tabs` (+ `.vertical`), `.columns`, `.slider`, `.list-container`, `.fixed-pre`, `.fixed-nowrap`, `.contactform`, `.inserttopost`, `blockquote`, `kbd`, `mark`, `code`/`pre` (+ `.syntax`, `.line-numbers`), tabel dengan kelas `table-header`, `table-striped`, `table-hover`, `table-border-y`, `table-compact`, `table-noborder-box`, `table-noborder-x`, `table-sticky-t`, `table-sticky-l`, `table-none`, serta `p.indent` dan `p.dropcap`.
Audit daftar ini terhadap `docs/theme-6290942502869749790.xml` di Tahap 6 dan tambahkan yang terlewat.

### 4.6 Halaman khusus
Pertahankan URL dan perilaku: `/p/daftar-isi.html` (sitemap otomatis), `/p/contact.html` (form kontak, penerima `terapijarum@gmail.com`), `/p/cari-terapis.html`, dan halaman lain yang dirujuk menu. Halaman memakai konten Blogger yang sudah ada, template hanya menyediakan JS/CSS-nya.

## 5. Batasan teknis Blogger (wajib diperhatikan)

1. Hasil akhir adalah **satu berkas `theme.xml`** yang valid dan bisa diunggah lewat Tema → Pulihkan.
2. XML harus well-formed. Karakter `&`, `<`, `>` di dalam XML harus di-escape, atau dibungkus `<![CDATA[ ]]>` bila aman. Skrip dan CSS panjang taruh di berkas eksternal, bukan inline.
3. Wajib ada elemen `<b:skin>` (boleh minimal) dan minimal satu `<b:section>` yang memuat widget `Blog`.
4. **Pertahankan ID section dan widget** dari template lama agar konfigurasi widget yang sudah ada tidak hilang: `Header`/`Header1`, `Navigation`/`HTML100`, `Page_Body`/`Blog1`, `Post_by_Labels`/`HTML110`–`HTML117`, `Sidebar`, `Sidebar_sticky`, `Footer_1`, `Footer_2`, `Footer_Credit`/`HTML119`, `Settings`/`HTML200`–`HTML213`, `Shortcode`/`HTML1`. Widget `HTML2`–`HTML6` (iklan dan script Pointera) juga dipertahankan.
5. Inline yang tidak boleh dilewatkan: skrip kecil di `<head>` yang menerapkan dark mode **sebelum render pertama** (mencegah kilatan putih).
6. Pemuatan aset memakai jsDelivr dengan **versi tetap** dari repo distribusi: `https://cdn.jsdelivr.net/gh/azdah/templateblogTerapijarum-dist@<REF>/theme.min.css` dan `.../theme.min.js`, dengan `<REF>` berupa tag rilis (`v1.0.0`) atau commit SHA (pratinjau). Jangan memakai `@main` atau `@latest` di versi produksi karena cache jsDelivr tidak langsung diperbarui. Nilai `azdah` dan nama repo dibaca dari `config.json` (huruf besar-kecil harus persis).
7. Pengaturan fitur (aktif/nonaktif, teks, daftar shortlink, dll.) dibaca dari widget di section `Settings` (JSON dalam tag `<script type="application/json">`), sama seperti template lama, agar pemilik bisa mengubahnya dari Tata Letak tanpa menyentuh kode.
8. Jaringan sandbox agen sering dibatasi. Jangan bergantung pada akses web saat build.

## 6. Prinsip desain

- **Bersih dan lega**: banyak ruang kosong, hierarki jelas, sedikit dekorasi.
- Lebar teks bacaan sekitar **68–72 karakter** (`max-width: 70ch`), ukuran font dasar 17–18px di artikel, `line-height` 1.7.
- Font: `system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`. Tanpa webfont.
- Token desain di `:root` (warna, spasi, radius, bayangan, ukuran font) dan versi gelap di `body.dark-mode` / `[data-theme="dark"]`. Semua warna lewat variabel, tidak ada warna hardcode di komponen.
- Warna utama `#278c5f` (hijau). Pastikan kontras teks memenuhi WCAG AA, terutama teks putih di atas hijau dan tautan di mode gelap.
- Kartu artikel sederhana: thumbnail, judul (maks 2 baris), meta kecil, cuplikan (opsional).
- Tabel, blockquote, kode, dan blok peringatan harus enak dibaca karena konten banyak berisi istilah dan tabel (titik, meridian, sindrom).
- Hero artikel ringkas di HP; jangan memakan lebih dari sekitar sepertiga layar pertama.
- Iklan: beri label kecil "Iklan", jarak jelas dari teks, ukuran tinggi minimum agar tidak menyebabkan layout shift.
- Aksesibilitas: fokus terlihat, elemen interaktif bisa dipakai keyboard, `aria-label` pada tombol ikon, `prefers-reduced-motion` dihormati, `prefers-color-scheme` dipakai sebagai default dark mode.
- Mobile-first: rancang di 360px dulu, lalu naik ke tablet dan desktop. Tidak ada scroll horizontal di halaman.

## 7. Performa dan SEO

- Target Lighthouse mobile: Performance ≥ 90, Accessibility ≥ 95, Best Practices ≥ 95, SEO ≥ 95 (di blog uji, tanpa iklan memengaruhi skor).
- CSS kritis (header, tipografi dasar) boleh di-inline, sisanya dimuat dari berkas eksternal. JS dimuat `defer`.
- Lazyload gambar dengan `loading="lazy"` bila memungkinkan, plus `IntersectionObserver` untuk yang perlu. Beri `width`/`height` atau `aspect-ratio` agar tidak ada CLS.
- AdSense dan GA4 dimuat **setelah interaksi pertama atau saat idle** (lazyload), sama seperti perilaku lama. ID: AdSense `ca-pub-2829059760225284`, GA4 `G-364984992`. Ambil nilai lengkap lainnya (ID verifikasi, `fb:admins`, dsb.) dari `docs/theme-6290942502869749790.xml`.
- Meta: `og:*`, `twitter:*`, `canonical`, `theme-color`, verifikasi mesin pencari (Google, Bing, Yandex) dipertahankan.
- **Ganti tipe schema artikel** dari `NewsArticle` ke `BlogPosting`. Pertahankan `BreadcrumbList`, `WebSite` + `SearchAction`, dan `Organization`.
- Hapus meta `keywords` yang panjang (tidak dipakai Google). Boleh diganti meta keywords singkat atau dihilangkan.
- Pastikan `<h1>` hanya satu per halaman, heading berurutan, dan semua gambar punya `alt`.

## 8. Struktur repo yang diharapkan

```
templateblog/
├── AGENTS.md
├── config.json              # lihat contoh di bawah diagram
├── package.json
├── .github/workflows/       # build+validate tiap PR, pratinjau, rilis ke repo dist
├── docs/
│   ├── TASKS.md             # tahapan kerja
│   ├── theme-6290942502869749790.xml   # template lama (Avia), HANYA-BACA
│   ├── script-3.4.1.js      # (opsional) script Theme62 lama, HANYA-BACA
│   ├── FEATURES.md          # daftar fitur dan cara mengaturnya
│   ├── SETTINGS.md          # dokumentasi widget pengaturan JSON
│   ├── INSTALL.md           # cara memasang di Blogger
│   └── TESTING.md           # checklist pengujian manual
├── src/
│   ├── theme.xml            # kerangka template (placeholder aset)
│   ├── partials/            # potongan XML: head, header, nav, footer, post, dst.
│   ├── css/
│   │   ├── tokens.css
│   │   ├── base.css
│   │   ├── layout.css
│   │   ├── components/      # card, button, alert, table, accordion, tabs, dst.
│   │   └── dark.css
│   └── js/
│       ├── core/            # util, settings, dark-mode
│       ├── features/        # toc, lightbox, bookmark, safelink, shortlink, ...
│       └── main.js
├── scripts/
│   ├── build.mjs            # gabung src → dist/theme.xml + dist/theme.min.css|js
│   └── validate.mjs         # cek XML well-formed + ID wajib + tidak ada "theme62"/"amp"
├── dist/                    # hasil build lokal (tidak di-commit; masuk .gitignore)
└── CHANGELOG.md
```

Contoh `config.json`:

```json
{
  "version": "0.0.0",
  "jsdelivrUser": "azdah",
  "devRepo": "templateblogTerapijarum",
  "distRepo": "templateblogTerapijarum-dist",
  "testBlog": "azdahtester.blogspot.com",
  "productionBlog": "www.terapijarum.com"
}
```

Boleh menyesuaikan detail, tetapi jelaskan alasannya di PR.

## 9. Aturan kerja agen

1. **Kerjakan satu tahap per PR** sesuai `docs/TASKS.md`. Jangan loncat tahap dan jangan menggabungkan beberapa tahap.
2. Di akhir setiap PR, tulis di deskripsi: apa yang dibuat, cara menguji di `azdahtester.blogspot.com`, dan **checklist uji manual** untuk pemilik. Lalu berhenti dan tunggu persetujuan sebelum tahap berikutnya.
3. Cari **akar masalah** sebelum memperbaiki bug. Jangan menambal gejala di permukaan; jelaskan penyebabnya di PR.
4. Setiap PR harus lolos `npm run build` dan `npm run validate`. Jangan merilis `dist/theme.xml` yang tidak valid.
5. Tanpa dependensi runtime. Boleh memakai devDependencies kecil untuk build (misalnya parser XML, minifier). Jelaskan setiap dependensi yang ditambahkan.
6. Tulis JS modern tetapi aman untuk browser dua tahun terakhir (ES2019+). Tidak memakai framework. Setiap fitur berdiri sendiri, gagal dengan tenang (try/catch, tidak merusak fitur lain), dan membaca pengaturan dari widget `Settings`.
7. Semua teks antarmuka berbahasa Indonesia dan mudah diubah (kumpulkan di satu objek `messages`).
8. Jangan mengubah isi artikel, URL, atau struktur label pemilik. Jangan menyentuh script eksternal Pointera.
9. Jangan memasukkan rahasia, kunci lisensi, atau token ke repo mana pun. ID publik (AdSense, GA4) boleh. Jangan pernah menyalin berkas `docs/theme-*.xml` atau `docs/script-*.js` (referensi lama) ke repo distribusi atau ke `dist/`.
10. Bila ada hal yang ambigu atau berisiko merusak blog yang sudah berjalan, **tanyakan di PR atau issue**, jangan menebak.
11. Setiap perubahan perilaku dicatat di `CHANGELOG.md` dan versi dinaikkan di `config.json` sesuai semver. Rilis diberi tag Git (misalnya `v0.1.0`) di repo pengembangan; workflow memberi tag yang sama di repo distribusi agar bisa dimuat lewat jsDelivr.

## 10. Definition of Done (seluruh proyek)

- Template terpasang di `azdahtester.blogspot.com` tanpa error di konsol pada: beranda, artikel, label, arsip, pencarian, halaman statis, 404, `/safelink`, dan satu shortlink.
- Semua fitur di bagian 4 berfungsi dan dapat dimatikan lewat widget `Settings` bila relevan.
- Artikel lama tampil benar (uji minimal 10 artikel yang memakai tabel, download-box, accordion, tabs, dan gambar).
- Mode terang dan gelap sama-sama nyaman dibaca; tidak ada kilatan putih saat memuat mode gelap.
- Lighthouse mobile memenuhi target di bagian 7.
- Tidak ada referensi ke `theme62`, AMP, PWA, atau split post pada `dist/` maupun di repo distribusi.
- Repo distribusi publik hanya berisi `theme.xml`, `theme.min.css`, `theme.min.js`, dan `README.md`, serta tag rilis yang sama dengan repo pengembangan.
- Dokumentasi `docs/` lengkap sehingga pemilik bisa memasang dan merawat tanpa agen.
