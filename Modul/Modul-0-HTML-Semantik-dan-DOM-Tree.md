## Daftar Isi

- [Pengenalan HTML5 dan Struktur DOM](#pengenalan-html5-dan-struktur-dom)
  - [Apa itu HTML dari Kacamata Backend?](#apa-itu-html-dari-kacamata-backend)
  - [Anatomi Elemen HTML](#anatomi-elemen-html)
  - [Struktur Dasar Dokumen HTML5](#struktur-dasar-dokumen-html5)
    - [Deklarasi `<!DOCTYPE html>`](#deklarasi-doctype-html)
    - [Elemen `<head>` (Metadata Dokumen)](#elemen-head-metadata-dokumen)
    - [Elemen `<body>` (Visual Viewport)](#elemen-body-visual-viewport)
- [HTML Semantik vs Non-Semantik](#html-semantik-vs-non-semantik)
  - [Mengapa Tidak Pakai `<div>` untuk Semuanya?](#mengapa-tidak-pakai-div-untuk-semuanya)
  - [Peta Elemen Semantik Utama](#peta-elemen-semantik-utama)
  - [Contoh Arsitektur Halaman Semantik](#contoh-arsitektur-halaman-semantik)
- [Elemen Konten Dasar](#elemen-konten-dasar)
  - [Tipografi dan Heading (`<h1>` - `<h6>`, `<p>`)](#tipografi-dan-heading-h1---h6-p)
  - [Hyperlink (`<a>`) dan Navigasi](#hyperlink-a-dan-navigasi)
  - [List (`<ul>`, `<ol>`, `<li>`) — Representasi Array di UI](#list-ul-ol-li--representasi-array-di-ui)
  - [Tabel (`<table>`) — Representasi Data Relasional](#tabel-table--representasi-data-relasional)
- [Formulir (`<form>`) dan Pemetaan ke Request Backend](#formulir-form-dan-pemetaan-ke-request-backend)
  - [Cara Kerja `<form>` Secara Native](#cara-kerja-form-secara-native)
  - [Pentingnya Atribut `name`, `id`, dan `value`](#pentingnya-atribut-name-id-dan-value)
  - [Jenis-Jenis Input dan Validasi Bawaan HTML5](#jenis-jenis-input-dan-validasi-bawaan-html5)
- [Atribut Global & Custom Data Attributes (`data-*`)](#atribut-global--custom-data-attributes-data-)
- [Latihan Praktikum Modul 0](#latihan-praktikum-modul-0)

# Pengenalan HTML5 dan Struktur DOM

## Apa itu HTML dari Kacamata Backend?

**HTML (*HyperText Markup Language*)** bukanlah bahasa pemrograman (karena tidak memiliki *control flow* seperti `if-else` atau `for` loop), melainkan bahasa *markup* untuk mendefinisikan **struktur dan makna data** pada sebuah halaman web.

Bagi seorang **Backend Developer**, cara paling mudah memahami HTML adalah membayangkannya sebagai **format serialisasi data berbentuk pohon (*Tree*)**, mirip dengan **JSON** atau **XML**.

Perhatikan perbandingan berikut. Ketika di backend kamu mengirim *response* JSON seperti ini:

```json
{
  "type": "article",
  "id": "post-101",
  "header": {
    "title": "Mengenal Microservices",
    "author": "Mikail"
  },
  "tags": ["Backend", "Architecture"]
}
```

Maka di Frontend, representasi dokumennya dalam HTML adalah struktur pohon yang ekuivalen:

```html
<article id="post-101">
  <header>
    <h2>Mengenal Microservices</h2>
    <p>Ditulis oleh: <span>Mikail</span></p>
  </header>
  <ul>
    <li>Backend</li>
    <li>Architecture</li>
  </ul>
</article>
```

Ketika browser menerima teks HTML di atas dari server, browser melakukan **parsing** dan mengubahnya menjadi pohon objek di dalam memori (RAM) yang disebut **DOM (*Document Object Model*)**:

```
article#post-101
 ├── header
 │    ├── h2 ("Mengenal Microservices")
 │    └── p ("Ditulis oleh: ")
 │         └── span ("Mikail")
 └── ul
      ├── li ("Backend")
      └── li ("Architecture")
```

> **Poin Kunci:** HTML adalah teks statis yang kamu tulis di file `.html`, sedangkan **DOM** adalah struktur data pohon (*in-memory object tree*) hasil *parsing* HTML tersebut yang nantinya bisa dimodifikasi oleh CSS dan JavaScript.

---

## Anatomi Elemen HTML

Sebuah elemen HTML umumnya terdiri dari **Opening Tag**, **Attributes**, **Content**, dan **Closing Tag**:

```html
<a href="https://github.com" target="_blank" class="btn-link">Buka GitHub</a>
```

- **`<a>`**: *Opening tag* (nama elemen/node).
- **`href="..."`, `target="..."`, `class="..."`**: *Attributes* (pasangan `key="value"` yang berfungsi seperti *parameter* atau *property* pada sebuah *struct/object*).
- **`Buka GitHub`**: *Text Content* (atau bisa berisi *child elements* lain).
- **`</a>`**: *Closing tag*.

Beberapa elemen tidak memiliki konten di dalamnya sehingga tidak membutuhkan *closing tag*. Elemen ini disebut **Void Elements** (atau *Self-Closing Elements*), contohnya:
- `<img src="logo.png" alt="Logo">`
- `<input type="text" name="username">`
- `<br>` (baris baru)
- `<hr>` (garis pemisah horizontal)
- `<meta charset="UTF-8">`

---

## Struktur Dasar Dokumen HTML5

Setiap dokumen HTML5 yang valid memiliki kerangka standar berikut:

```html
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Sistem Perizinan</title>
    <!-- Tempat memuat file CSS eksternal -->
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Halo, Dunia Frontend!</h1>
    <p>Ini adalah paragraf pertama saya.</p>

    <!-- Tempat memuat file JavaScript eksternal -->
    <script src="app.js"></script>
</body>
</html>
```

Mari kita bedah fungsi masing-masing bagiannya:

### Deklarasi `<!DOCTYPE html>`

Baris paling atas `<!DOCTYPE html>` memberi tahu browser bahwa dokumen ini menggunakan standar **HTML5 modern**. Tanpa baris ini, browser bisa masuk ke *Quirks Mode* (mode kompatibilitas era 1990-an) yang membuat perhitungan ukuran CSS menjadi kacau.

### Elemen `<head>` (Metadata Dokumen)

Isi di dalam `<head>` **tidak ditampilkan langsung di kanvas halaman web**, melainkan berisi konfigurasi dan metadata untuk browser serta mesin pencari (mirip seperti *HTTP Headers* pada sebuah paket HTTP):

- `<meta charset="UTF-8">`: Memastikan encoding karakter mendukung seluruh teks Unicode (termasuk simbol dan emoji).
- `<meta name="viewport" content="width=device-width, initial-scale=1.0">`: **Sangat krusial!** Tanpa tag ini, browser di smartphone akan merender halaman seolah-olah di layar desktop lebar (980px) lalu memperkecilnya hingga teks tidak terbaca. Tag ini memaksa lebar halaman mengikuti lebar fisik layar perangkat.
- `<title>`: Judul yang muncul pada tab browser.
- `<link rel="stylesheet" href="...">`: Mengimpor file CSS sebelum halaman dirender agar tidak terjadi *Flash of Unstyled Content (FOUC)*.

### Elemen `<body>` (Visual Viewport)

Seluruh elemen yang ingin dilihat dan diakses oleh pengguna (`header`, tombol, tabel, form, gambar) wajib diletakkan di dalam `<body>`.

---

## HTML Semantik vs Non-Semantik

### Mengapa Tidak Pakai `<div>` untuk Semuanya?

Pemula frontend sering terjebak dalam penyakit yang disebut **"Div Soup"**—yaitu membungkus seluruh bagian halaman hanya dengan tag `<div>`:

```html
<!-- ❌ CONTOH BURUK (Div Soup - Non-Semantik) -->
<div class="header">
  <div class="nav">...</div>
</div>
<div class="main-content">
  <div class="card">...</div>
</div>
<div class="footer">...</div>
```

Meskipun secara visual bisa dibuat terlihat sama menggunakan CSS, penulisan **HTML Semantik** menggunakan tag yang menjelaskan **peran dan makna** dari blok tersebut:

```html
<!-- ✅ CONTOH BAIK (HTML5 Semantik) -->
<header>
  <nav>...</nav>
</header>
<main>
  <article>...</article>
</main>
<footer>...</footer>
```

**Mengapa HTML Semantik penting bagi Engineer?**
1. **Kemudahan Maintenance (Readability)**: Saat membaca kode, kamu langsung tahu mana bagian navigasi (`<nav>`), konten utama (`<main>`), dan bagian samping (`<aside>`) tanpa harus menebak nama class.
2. **Accessibility (A11y)**: Pembaca layar (*screen reader*) menggunakan tag semantik untuk membantu pengguna disabilitas bernavigasi. Selain itu, menggunakan `<button>` asli otomatis memberikan dukungan navigasi keyboard (`Tab`, `Enter`, `Space`), sedangkan `<div class="button">` tidak!
3. **SEO (Search Engine Optimization)**: Crawler mesin pencari (seperti Googlebot) memprioritaskan konten di dalam `<main>`, `<article>`, dan `<h1>` dibandingkan teks di dalam `<div>` biasa.

### Peta Elemen Semantik Utama

| Tag HTML | Kegunaan | Aturan Praktis |
| :--- | :--- | :--- |
| `<header>` | Bagian kepala halaman atau kepala dari sebuah `<article>`/`<section>`. | Biasanya berisi logo, judul halaman, dan `<nav>`. |
| `<nav>` | Blok navigasi utama yang berisi daftar tautan menu. | Gunakan untuk menu utama atau *breadcrumb*. |
| `<main>` | Konten inti yang unik pada halaman tersebut. | Hanya boleh ada **satu** `<main>` yang aktif per halaman. |
| `<section>` | Pengelompokan tematik konten dalam sebuah halaman. | Hampir selalu memiliki *heading* (`<h2>` atau `<h3>`) di dalamnya. |
| `<article>` | Konten independen yang tetap masuk akal jika dipisahkan dari halaman. | Contoh: kartu berita, postingan blog, komentar user, item produk. |
| `<aside>` | Konten pendamping yang tidak menjadi bagian langsung dari alur utama. | Contoh: *sidebar*, filter pencarian, info profil singkat. |
| `<footer>` | Bagian kaki halaman atau kaki sebuah `<article>`. | Berisi *copyright*, tautan tambahan, atau metadata penulis. |
| `<div>` | Kontainer *block-level* generik (tanpa makna semantik). | Gunakan **hanya** jika tidak ada tag semantik yang cocok (misal murni untuk kebutuhan *wrapper* layout CSS). |
| `<span>` | Kontainer *inline* generik di dalam baris teks. | Gunakan untuk memberi gaya pada sepenggal kata di dalam paragraf. |

### Contoh Arsitektur Halaman Semantik

```html
<body>
    <header>
        <h1>Portal Praktikum KCKS</h1>
        <nav>
            <a href="#modul">Daftar Modul</a>
            <a href="#jadwal">Jadwal</a>
            <a href="#profil">Profil</a>
        </nav>
    </header>

    <main>
        <section id="modul">
            <h2>Daftar Modul Aktif</h2>
            <article>
                <h3>Modul 0: HTML Semantik</h3>
                <p>Mempelajari struktur dasar dokumen web dan DOM tree.</p>
            </article>
            <article>
                <h3>Modul 1: CSS Layouting</h3>
                <p>Mempelajari Box Model, Flexbox, dan CSS Grid.</p>
            </article>
        </section>

        <aside>
            <h2>Pengumuman</h2>
            <p>Batas pengumpulan tugas praktikum adalah hari Jumat pukul 23:59 WIB.</p>
        </aside>
    </main>

    <footer>
        <p>&copy; 2026 Laboratorium Pemrograman. Hak Cipta Dilindungi.</p>
    </footer>
</body>
```

---

## Elemen Konten Dasar

### Tipografi dan Heading (`<h1>` - `<h6>`, `<p>`)

HTML menyediakan 6 tingkat judul (*Heading*) dari `<h1>` hingga `<h6>`.
- Anggap `<h1>` s.d. `<h6>` sebagai **hierarki outline dokumen**, bukan sekadar ukuran huruf! (Ukuran huruf selalu bisa diatur lewat CSS).
- Setiap halaman idealnya memiliki tepat **satu `<h1>`** sebagai topik utama halaman, diikuti `<h2>` untuk bab, dan `<h3>` untuk sub-bab.
- Gunakan `<p>` untuk paragraf teks biasa, `<strong>` untuk penekanan penting (teks tebal), `<em>` untuk penekanan intonasi (teks miring), dan `<code>` untuk potongan kode program.

### Hyperlink (`<a>`) dan Navigasi

Tag `<a>` (*Anchor*) digunakan untuk berpindah halaman atau melompat ke bagian tertentu:

```html
<!-- 1. Navigasi ke URL eksternal di tab baru -->
<a href="https://github.com/lab-kcks/modul-atp" target="_blank" rel="noopener noreferrer">
    Lihat Referensi Modul ATP
</a>

<!-- 2. Navigasi ke halaman lain di proyek yang sama -->
<a href="./profil.html">Halaman Profil</a>

<!-- 3. Navigasi internal (Anchor Jump) ke elemen dengan id="jadwal" -->
<a href="#jadwal">Lompat ke Bagian Jadwal</a>
```

> **Catatan Keamanan (`rel="noopener noreferrer"`):** Saat menggunakan `target="_blank"`, selalu tambahkan `rel="noopener noreferrer"` agar halaman eksternal yang dibuka tidak dapat mengakses objek `window.opener` halaman asalmu.

### List (`<ul>`, `<ol>`, `<li>`) — Representasi Array di UI

Ketika API backend kamu mengembalikan sebuah **Array/Slice** (misalnya daftar menu, daftar notifikasi, atau daftar produk), di HTML data tersebut hampir selalu direpresentasikan menggunakan **List**:

- **`<ul>` (*Unordered List*)**: Daftar tanpa urutan angka (poin-poin).
- **`<ol>` (*Ordered List*)**: Daftar berurutan (1, 2, 3...) — misalnya untuk *leaderboard* atau langkah-langkah prosedur.
- **`<li>` (*List Item*)**: Elemen anak langsung di dalam `<ul>` atau `<ol>`.

### Tabel (`<table>`) — Representasi Data Relasional

Jika kamu ingin menampilkan data berbentuk baris dan kolom seperti hasil query SQL (`SELECT id, nama, nrp, status FROM mahasiswa`), gunakan struktur `<table>` yang lengkap dengan `<thead>` (kepala tabel) dan `<tbody>` (isi data):

```html
<table>
    <thead>
        <tr>
            <th>NRP</th>
            <th>Nama Mahasiswa</th>
            <th>Status Izin</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>5027261053</td>
            <td>Muhammad Mikail</td>
            <td>Disetujui</td>
        </tr>
        <tr>
            <td>5027261099</td>
            <td>Budi Santoso</td>
            <td>Menunggu Verifikasi</td>
        </tr>
    </tbody>
</table>
```

---

## Formulir (`<form>`) dan Pemetaan ke Request Backend

Sebagai Backend Developer, kamu pasti sering membuat *endpoint* yang menerima data dari klien, misalnya:
`POST /api/izin-kompe` dengan payload:
```json
{
  "nrp": "5027261053",
  "kategori": "lomba",
  "alasan": "Mengikuti final CTF Nasional",
  "tanggal_mulai": "2026-11-26"
}
```

Di Frontend, komponen standar untuk mengumpulkan input dari pengguna dan memetakannya menjadi payload tersebut adalah **`<form>`**.

### Cara Kerja `<form>` Secara Native

Secara bawaan (tanpa JavaScript sekalipun), elemen `<form>` adalah sebuah **HTTP Request Generator**:
- Atribut **`action`** menentukan URL tujuan (*endpoint*).
- Atribut **`method`** menentukan HTTP Method (`GET` atau `POST`).
- Saat tombol `<button type="submit">` ditekan, browser akan mengumpulkan seluruh input di dalam form yang memiliki atribut **`name`**, lalu mengirimkannya sebagai pasangan `name=value`.

### Pentingnya Atribut `name`, `id`, dan `value`

Banyak pemula bingung membedakan `id`, `name`, dan `value` pada elemen input. Berikut perbedaannya secara tegas:

| Atribut | Fungsi Utama | Analogi Backend |
| :--- | :--- | :--- |
| **`name`** | **Key pada payload HTTP / `FormData`**. Tanpa atribut `name`, nilai input tidak akan ikut terkirim saat form di-submit! | Nama *field* pada JSON / Struct (`json:"nrp"`). |
| **`id`** | Pengenal unik elemen di seluruh dokumen HTML. Digunakan untuk menghubungkan `<label for="id_input">` dan seleksi di JS. | *Primary Key* unik untuk satu node DOM di halaman. |
| **`value`** | Nilai aktual yang sedang tersimpan di dalam input tersebut. | *Value* dari field tersebut. |

Perhatikan contoh form berikut:

```html
<form id="form-izin" action="/api/izin" method="POST">
    <div>
        <label for="input-nrp">NRP Mahasiswa:</label>
        <input 
            type="text" 
            id="input-nrp" 
            name="nrp" 
            placeholder="Contoh: 5027261053" 
            required 
            pattern="[0-9]{10}"
            title="NRP harus terdiri dari 10 digit angka"
        >
    </div>

    <div>
        <label for="select-kategori">Kategori Izin:</label>
        <select id="select-kategori" name="kategori" required>
            <option value="">-- Pilih Kategori --</option>
            <option value="lomba">Dispensasi Kompetisi / Lomba</option>
            <option value="sakit">Sakit (Dengan Surat Dokter)</option>
            <option value="kepanitiaan">Tugas Kepanitiaan Kampus</option>
        </select>
    </div>

    <div>
        <label for="input-tanggal">Tanggal Mulai Izin:</label>
        <input type="date" id="input-tanggal" name="tanggal_mulai" required>
    </div>

    <div>
        <label for="input-alasan">Keterangan / Alasan Lengkap:</label>
        <textarea id="input-alasan" name="alasan" rows="4" minlength="10" required></textarea>
    </div>

    <div>
        <label for="input-berkas">Unggah Bukti Surat (PDF/Gambar):</label>
        <input type="file" id="input-berkas" name="berkas_bukti" accept=".pdf,.jpg,.png">
    </div>

    <button type="submit">Kirim Pengajuan Izin</button>
</form>
```

> **Selalu Pasangkan `<label for="...">` dengan `<input id="...">`!**
> Ketika atribut `for` pada `<label>` sama dengan `id` pada `<input>`, pengguna yang mengklik teks label akan otomatis memfokuskan kursor ke dalam kotak input tersebut. Ini sangat penting untuk pengalaman pengguna (UX) terutama pada perangkat layar sentuh.

### Jenis-Jenis Input dan Validasi Bawaan HTML5

Sebelum kita menulis validasi menggunakan JavaScript, HTML5 sudah menyediakan *Client-Side Constraint Validation* bawaan yang akan otomatis mencegah form terkirim jika syaratnya belum terpenuhi:

- **`required`**: Wajib diisi (tidak boleh kosong).
- **`type="email"`**: Memvalidasi format alamat email (`user@domain`).
- **`type="number"`** (dengan `min="1"` dan `max="100"`): Membatasi input hanya berupa angka dalam rentang tertentu.
- **`type="date"`**: Menampilkan pemilih tanggal (*date picker*) bawaan sistem operasi.
- **`type="password"`**: Menyembunyikan karakter yang diketik.
- **`minlength` & `maxlength`**: Membatasi panjang minimum dan maksimum string.
- **`pattern="REGEX"`**: Memvalidasi input menggunakan *Regular Expression* (misalnya `pattern="[0-9]{10}"` untuk 10 digit NRP).

*(Tentu saja, sebagai Backend Developer kamu sudah tahu: validasi di frontend bertujuan untuk **kenyamanan UX dan umpan balik cepat**, sedangkan validasi di backend tetap **wajib ada** untuk keamanan!)*

---

## Atribut Global & Custom Data Attributes (`data-*`)

Selain atribut standar seperti `id` dan `class`, HTML5 memiliki fitur yang sangat disukai oleh Backend Developer saat bekerja di Frontend, yaitu **Custom Data Attributes (`data-*`)**.

Sering kali ketika kita merender daftar data dari database (misalnya baris tabel pengajuan izin), kita ingin menyimpan metadata seperti `id` database atau `status` langsung pada elemen HTML tersebut supaya mudah diambil oleh JavaScript ketika tombol diklik.

```html
<tr data-izin-id="9821" data-status="pending">
    <td>Muhammad Mikail</td>
    <td>Final CTF Nasional</td>
    <td>
        <button type="button" class="btn-approve" data-action="approve">Setujui</button>
        <button type="button" class="btn-reject" data-action="reject">Tolak</button>
    </td>
</tr>
```

Nantinya di JavaScript (Modul 2), atribut `data-izin-id="9821"` ini bisa langsung dibaca sebagai properti objek lewat `element.dataset.izinId`!

---

## Latihan Praktikum Modul 0

Untuk menguji pemahamanmu tentang HTML5 Semantik dan Form, kerjakan latihan berikut pada file `.html` lokalmu:

1. Buat sebuah halaman HTML5 bertema **"Portal Pengajuan Izin Kompetisi Mahasiswa"**.
2. Gunakan struktur semantik yang terdiri dari:
   - `<header>` berisi judul portal dan `<nav>` dengan 3 tautan menu.
   - `<main>` yang memiliki dua `<section>`:
     - **Section 1 (Form Pengajuan)**: Sebuah `<form>` yang memiliki input Nama, NRP (wajib 10 digit angka), Nama Kompetisi, Pilihan Tingkat Kompetisi (`<select>`: Regional, Nasional, Internasional), Tanggal Mulai, Tanggal Selesai, Unggah Surat Tugas (`type="file"`), dan tombol Submit.
     - **Section 2 (Riwayat Pengajuan)**: Sebuah `<table>` dengan `<thead>` dan `<tbody>` yang menampilkan minimal 3 baris data riwayat pengajuan izin lengkap dengan atribut `data-id` pada setiap baris `<tr>`.
   - `<footer>` berisi informasi laboratorium atau institusi.
3. Buka halaman tersebut di browser, lalu buka **DevTools (`F12`) → Tab Elements** untuk memverifikasi pohon DOM yang terbentuk dan coba klik tombol Submit tanpa mengisi form untuk melihat validasi bawaan HTML5 bekerja.
