## Daftar Isi

- [Cara Berpikir Sistematis dalam CSS](#cara-berpikir-sistematis-dalam-css)
- [Sintaks Dasar, Cascade, dan Specificity Score](#sintaks-dasar-cascade-dan-specificity-score)
  - [Anatomi Aturan CSS](#anatomi-aturan-css)
  - [Jenis-Jenis Selector Utama](#jenis-jenis-selector-utama)
  - [Algoritma Specificity (Mengapa CSS Saya Tidak Berefek?)](#algoritma-specificity-mengapa-css-saya-tidak-berefek)
- [Hukum Pertama CSS: The Box Model](#hukum-pertama-css-the-box-model)
  - [4 Lapisan Kotak Elemen](#4-lapisan-kotak-elemen)
  - [Jebakan `content-box` vs Solusi Wajib `border-box`](#jebakan-content-box-vs-solusi-wajib-border-box)
- [Satuan Ukuran dan CSS Variables](#satuan-ukuran-dan-css-variables)
  - [Kapan Pakai `px`, `rem`, `%`, `vw`, dan `vh`?](#kapan-pakai-px-rem--vw-dan-vh)
  - [CSS Custom Properties (Variabel di CSS)](#css-custom-properties-variabel-di-css)
- [Display dan Positioning](#display-dan-positioning)
  - [`block` vs `inline` vs `inline-block`](#block-vs-inline-vs-inline-block)
  - [Memahami `position`: `relative`, `absolute`, `fixed`, dan `sticky`](#memahami-position-relative-absolute-fixed-dan-sticky)
- [Algoritma Layout 1 Dimensi: Flexbox](#algoritma-layout-1-dimensi-flexbox)
  - [Konsep *Flex Container* vs *Flex Items*](#konsep-flex-container-vs-flex-items)
  - [*Main Axis* vs *Cross Axis*](#main-axis-vs-cross-axis)
  - [Pola Flexbox yang Paling Sering Dipakai di Dunia Nyata](#pola-flexbox-yang-paling-sering-dipakai-di-dunia-nyata)
- [Algoritma Layout 2 Dimensi: CSS Grid](#algoritma-layout-2-dimensi-css-grid)
  - [Kapan Pakai Flexbox vs Kapan Pakai Grid?](#kapan-pakai-flexbox-vs-kapan-pakai-grid)
  - [Membuat Grid Responsif Otomatis Tanpa Media Query](#membuat-grid-responsif-otomatis-tanpa-media-query)
- [Responsive Web Design & Media Queries](#responsive-web-design--media-queries)
- [Latihan Praktikum Modul 1](#latihan-praktikum-modul-1)

# CSS Fundamental, Box Model, dan Algoritma Layouting

## Cara Berpikir Sistematis dalam CSS

Banyak Backend Developer membenci **CSS (*Cascading Style Sheets*)** karena menganggap CSS tidak logis: *"Kenapa waktu saya tambah `width: 100%` kotaknya malah bablas keluar layar? Kenapa `justify-content: center` kadang ke tengah horizontal, kadang vertikal?"*

Sebenarnya, **CSS 100% deterministik dan berbasis algoritma matematika**. CSS terasa seperti "tebak-tebakan" hanya ketika kita belum memahami **3 hukum dasarnya**:
1. **Algoritma Specificity**: Bagaimana browser memutuskan aturan mana yang menang ketika ada dua aturan CSS yang bentrok.
2. **The Box Model**: Setiap elemen HTML di layar pada hakikatnya adalah sebuah **kotak persegi panjang** dengan rumus dimensi yang pasti.
3. **Layout Formatting Context (Flexbox & Grid)**: Sistem koordinat induk-anak (*Parent-Child*) yang mengatur distribusi ruang kosong.

Mari kita pelajari ketiganya secara sistematis.

---

## Sintaks Dasar, Cascade, dan Specificity Score

### Anatomi Aturan CSS

```css
/* Selector */
.card-izin {
    /* Property: Value; */
    background-color: #ffffff;
    border-radius: 8px;
    padding: 16px;
}
```

### Jenis-Jenis Selector Utama

| Jenis Selector | Sintaks CSS | Target Elemen HTML | Rekomendasi Penggunaan |
| :--- | :--- | :--- | :--- |
| **Element / Tag** | `p`, `h1`, `button` | Semua tag `<p>`, `<h1>`, `<button>` | Hanya untuk *reset* atau *base typography* global. |
| **Class** | `.btn-primary` | Elemen dengan `class="btn-primary"` | **Gunakan ini untuk 90% kebutuhan styling!** Bisa dipakai berulang kali (*reusable*). |
| **ID** | `#header-utama` | Elemen dengan `id="header-utama"` | Hindari untuk styling CSS karena *specificity*-nya terlalu tinggi. Gunakan ID untuk JS/Anchor. |
| **Attribute** | `input[type="text"]` | `<input type="text">` | Sangat berguna untuk menargetkan tipe input form tertentu. |
| **Descendant** | `.card p` | Semua `<p>` di dalam `.card` | Menargetkan elemen anak di dalam kontainer tertentu. |
| **Pseudo-class** | `.btn:hover`, `input:focus` | Elemen saat berada pada *state* interaksi tertentu | Efek saat kursor di atas tombol (`:hover`) atau input sedang diketik (`:focus`). |

### Algoritma Specificity (Mengapa CSS Saya Tidak Berefek?)

Kata **Cascading** pada CSS berarti aturan mengalir dari atas ke bawah. Namun, jika ada dua aturan CSS yang menargetkan elemen yang sama, browser **tidak sekadar memilih yang paling bawah**, melainkan menghitung **Skor Spesifisitas (Specificity Tuple: `(Inline, ID, Class, Element)`)**!

Bayangkan skor ini sebagai versi semantik `(A, B, C, D)` atau bilangan 3 digit `(ID - Class - Tag)`:

| Tingkat | Selector | Skor `(ID, Class, Tag)` | Contoh |
| :---: | :--- | :---: | :--- |
| 1 (Terendah) | **Tag / Element** | `(0, 0, 1)` | `button { color: blue; }` |
| 2 | **Class / Attribute / Pseudo-class** | `(0, 1, 0)` | `.btn-submit { color: green; }` |
| 3 | **Kombinasi Class + Tag** | `(0, 1, 1)` | `button.btn-submit { color: purple; }` |
| 4 | **ID Selector** | `(1, 0, 0)` | `#tombol-kirim { color: orange; }` |
| 5 | **Inline Style (`style="..."`)** | `(1, 0, 0, 0)` | `<button style="color: red;">` |
| 6 (Nuklir) | **`!important`** | Mengalahkan segalanya | `color: black !important;` *(Jangan dipakai kecuali terpaksa!)* |

**Contoh Kasus:**
```html
<button id="btn-kirim" class="btn-hijau">Kirim</button>
```
```css
#btn-kirim {
    background-color: blue; /* Skor: (1, 0, 0) -> MENANG! */
}

.btn-hijau {
    background-color: green; /* Skor: (0, 1, 0) -> KALAH walaupun ditulis di bawah! */
}
```
Jika skornya **seri** (misalnya sama-sama satu class `(0, 1, 0)`), barulah aturan yang ditulis **paling bawah** yang menang!

---

## Hukum Pertama CSS: The Box Model

Di mata mesin perender browser, **setiap elemen HTML adalah sebuah kotak persegi panjang** (bahkan lingkaran sekalipun sebenarnya adalah kotak persegi dengan `border-radius: 50%`).

### 4 Lapisan Kotak Elemen

Dari dalam ke luar, setiap kotak terdiri dari 4 lapisan:

```
+-----------------------------------------------------------+
|                         MARGIN                            |
|   (Ruang transparan di LUAR kotak untuk mendorong tetangga)|
|   +---------------------------------------------------+   |
|   |                     BORDER                        |   |
|   |         (Garis tepi/bingkai kotak elemen)         |   |
|   |   +-------------------------------------------+   |   |
|   |   |                 PADDING                   |   |   |
|   |   |  (Ruang bernapas di DALAM kotak elemen)   |   |   |
|   |   |   +-----------------------------------+   |   |   |
|   |   |   |              CONTENT              |   |   |   |
|   |   |   |    (Teks, gambar, atau anak)      |   |   |   |
|   |   |   +-----------------------------------+   |   |   |
|   |   +-------------------------------------------+   |   |
|   +---------------------------------------------------+   |
+-----------------------------------------------------------+
```

- **`padding`**: Jarak antara isi teks (*content*) dengan garis tepi (*border*). Jika kamu memberi `background-color` pada elemen, area `padding` **ikut berwarna**.
- **`border`**: Garis bingkai yang mengelilingi `padding` dan `content`.
- **`margin`**: Jarak luar antara kotak ini dengan kotak elemen tetangganya. Area `margin` selalu **transparan**.

### Jebakan `content-box` vs Solusi Wajib `border-box`

Secara *default* (warisan spesifikasi CSS tahun 1996), properti `box-sizing` bernilai `content-box`.
Pada mode `content-box`, jika kamu menulis:
```css
.kotak {
    width: 300px;
    padding: 20px;
    border: 2px solid black;
}
```
Berapa lebar total kotak tersebut di layar? **Bukan 300px!** Melainkan:
`300px (content) + 20px (padding kiri) + 20px (padding kanan) + 2px (border kiri) + 2px (border kanan) = 344px`!
Inilah alasan utama mengapa saat pemula memberi `width: 100%` ditambah `padding: 20px`, kotaknya melebar keluar layar hingga muncul *horizontal scrollbar*!

**Solusi Standar Industri (`box-sizing: border-box`):**
Dengan mengubah `box-sizing` menjadi `border-box`, maka nilai `width: 300px` sudah mencakup **`content + padding + border`**.

Oleh karena itu, **selalu awali setiap file CSS kamu dengan blok CSS Reset standar ini**:

```css
/* CSS Reset Standar Modern */
*, *::before, *::after {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}
```

---

## Satuan Ukuran dan CSS Variables

### Kapan Pakai `px`, `rem`, `%`, `vw`, dan `vh`?

| Satuan | Sifat | Arti & Cara Hitung | Kapan Digunakan? |
| :--- | :--- | :--- | :--- |
| **`px`** | Absolut | Piksel layar logis. | Ketebalan `border` (`1px`), `border-radius` (`8px`), atau bayangan (`box-shadow`). |
| **`rem`** | Relatif terhadap Root | `1rem` = ukuran `font-size` pada tag `<html>` (standarnya **`16px`**). Jadi `1.5rem` = `24px`, `0.875rem` = `14px`. | **Ukuran huruf (`font-size`), `padding`, `margin`, dan `gap`**. Menghormati preferensi zoom aksesibilitas user. |
| **`%`** | Relatif terhadap Parent | Persentase terhadap ukuran elemen induknya (*parent*). | Mengatur lebar kontainer (`width: 100%`). |
| **`vh` / `vw`** | Relatif terhadap Viewport | `100vh` = 100% tinggi layar browser yang terlihat. `100vw` = 100% lebar layar. | Membuat halaman penuh setinggi layar (`min-height: 100vh`). |

### CSS Custom Properties (Variabel di CSS)

Sebagai programmer, kamu pasti tidak suka melakukan *hardcode* kode warna hex (`#2563eb`) di 50 tempat berbeda. CSS modern mendukung **variabel native** yang dideklarasikan di dalam `:root` dan dipanggil menggunakan fungsi `var(--nama-variabel)`:

```css
:root {
    --color-primary: #2563eb;
    --color-primary-hover: #1d4ed8;
    --color-bg: #f8fafc;
    --color-surface: #ffffff;
    --color-text: #0f172a;
    --color-text-muted: #64748b;
    --color-border: #e2e8f0;
    --radius-md: 8px;
}

body {
    background-color: var(--color-bg);
    color: var(--color-text);
    font-family: system-ui, -apple-system, sans-serif;
}

.btn-primary {
    background-color: var(--color-primary);
    color: #ffffff;
    border-radius: var(--radius-md);
}

.btn-primary:hover {
    background-color: var(--color-primary-hover);
}
```

---

## Display dan Positioning

### `block` vs `inline` vs `inline-block`

Setiap tag HTML memiliki perilaku `display` bawaan:

1. **`display: block`** (Contoh: `<div>`, `<p>`, `<h1>` - `<h6>`, `<section>`, `<form>`):
   - Selalu memulai baris baru (menumpuk ke bawah).
   - Secara otomatis mengambil lebar penuh (`width: 100%` dari induknya) jika lebarnya tidak ditentukan.
   - Bisa diatur `width`, `height`, `margin`, dan `padding`-nya ke segala arah.
2. **`display: inline`** (Contoh: `<span>`, `<a>`, `<strong>`, `<em>`):
   - Mengalir mengikuti baris teks (tidak membuat baris baru).
   - Lebarnya hanya sebesar teks di dalamnya.
   - **PENTING:** Elemen `inline` **mengabaikan** properti `width`, `height`, `margin-top`, dan `margin-bottom`!
3. **`display: inline-block`** (Contoh: `<button>`, `<input>`):
   - Mengalir berjajar seperti `inline`, tetapi **tetap mematuhi** pengaturan `width`, `height`, `padding`, dan `margin` seperti `block`.
4. **`display: none`**:
   - Menghilangkan elemen dari layar sekaligus dari perhitungan ruang tata letak (seolah-olah elemennya tidak ada).

### Memahami `position`: `relative`, `absolute`, `fixed`, dan `sticky`

Secara default, semua elemen memiliki `position: static` (mengikuti alur dokumen normal dari atas ke bawah). Namun, untuk kebutuhan UI khusus seperti *badge notifikasi*, *modal popup*, atau *navbar yang menempel di atas saat di-scroll*, kita menggunakan properti `position`:

- **`position: relative`**: Elemen tetap berada di tempat normalnya, tetapi sekarang ia menjadi **titik acuan koordinat (jangkar)** bagi elemen anaknya yang memiliki `position: absolute`.
- **`position: absolute`**: Elemen dicabut dari alur normal dan diposisikan menggunakan koordinat `top`, `right`, `bottom`, `left` terhadap **induk terdekatnya yang memiliki `position: relative`**. (Contoh: menaruh lencana angka notifikasi merah di pojok kanan atas ikon lonceng).
- **`position: fixed`**: Elemen dicabut dari alur normal dan dipakukan terhadap **layar browser (viewport)**—tidak ikut bergerak walaupun halaman di-scroll. (Contoh: tombol *Floating WhatsApp* di pojok kanan bawah atau *Modal Overlay* gelap).
- **`position: sticky`**: Gabungan `relative` dan `fixed`. Elemen ikut di-scroll secara normal sampai menyentuh batas tertentu (misal `top: 0`), lalu berhenti menempel di atas layar. (Sangat populer untuk *Navbar* dan *Header Tabel*).

---

## Algoritma Layout 1 Dimensi: Flexbox

Lupakan teknik kuno seperti `float: left` atau mengatur `margin` secara manual untuk menengahkan elemen. Di CSS modern, untuk menyusun elemen dalam **satu dimensi (sebaris horizontal ATAU setumpuk vertikal)**, kita menggunakan **Flexbox (`display: flex`)**.

### Konsep *Flex Container* vs *Flex Items*

Begitu kamu memberikan `display: flex` pada sebuah elemen induk (**Flex Container**), maka seluruh **anak langsungnya (*direct children*)** otomatis berubah menjadi **Flex Items** dan berjajar ke samping secara default!

```html
<!-- Flex Container -->
<header class="navbar">
    <!-- Flex Item 1 -->
    <div class="logo">SI-KOMPE</div>
    <!-- Flex Item 2 -->
    <nav class="menu">
        <a href="#">Dashboard</a>
        <a href="#">Pengajuan</a>
    </nav>
    <!-- Flex Item 3 -->
    <button class="btn-logout">Keluar</button>
</header>
```

### *Main Axis* vs *Cross Axis*

Kunci agar tidak pernah bingung menggunakan `justify-content` dan `align-items` adalah memahami **dua sumbu Flexbox**:

1. **`flex-direction: row`** (Default — Arah Horizontal `───►`):
   - **Main Axis (Sumbu Utama)** = Horizontal (Kiri ke Kanan) ──► Diatur dengan **`justify-content`**
   - **Cross Axis (Sumbu Silang)** = Vertikal (Atas ke Bawah) ──► Diatur dengan **`align-items`**
2. **`flex-direction: column`** (Arah Vertikal `▼`):
   - **Main Axis (Sumbu Utama)** = Vertikal (Atas ke Bawah) ──► Diatur dengan **`justify-content`**
   - **Cross Axis (Sumbu Silang)** = Horizontal (Kiri ke Kanan) ──► Diatur dengan **`align-items`**

> **Rumus Hafalan 5 Detik:**
> - **`justify-content`** SELALU mengatur distribusi di sepanjang **Sumbu Utama (`Main Axis`)**.
> - **`align-items`** SELALU mengatur perataan di sepanjang **Sumbu Silang (`Cross Axis`)**.
> - **`gap`** mengatur jarak antar anak tanpa perlu memberi `margin` satu per satu!

Nilai-nilai `justify-content` yang paling sering dipakai:
- `flex-start`: Merapat ke awal (kiri).
- `center`: Berkumpul tepat di tengah.
- `flex-end`: Merapat ke ujung akhir (kanan).
- `space-between`: Anak pertama di ujung paling kiri, anak terakhir di ujung paling kanan, sisanya dibagi rata di tengah (sangat cocok untuk **Navbar**!).

### Pola Flexbox yang Paling Sering Dipakai di Dunia Nyata

```css
/* Pola 1: Navbar (Logo di kiri, Menu di kanan, rata tengah vertikal) */
.navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 2rem;
    gap: 1rem;
}

/* Pola 2: Menengahkan elemen tepat di tengah layar (Login Box / Modal) */
.center-screen {
    display: flex;
    justify-content: center; /* Tengah horizontal */
    align-items: center;     /* Tengah vertikal */
    min-height: 100vh;
}

/* Pola 3: Form Vertikal dengan jarak antar input yang konsisten */
.form-stack {
    display: flex;
    flex-direction: column;
    gap: 1rem; /* Otomatis memberi jarak 16px antar setiap input */
}
```

---

## Algoritma Layout 2 Dimensi: CSS Grid

### Kapan Pakai Flexbox vs Kapan Pakai Grid?

- Gunakan **Flexbox** ketika kamu menyusun komponen **1 dimensi** (baris tombol, navbar, isi di dalam satu kartu, grup form).
- Gunakan **CSS Grid (`display: grid`)** ketika kamu menyusun tata letak **2 dimensi (baris DAN kolom sekaligus)**, seperti daftar kartu dashboard (3 kolom ke samping, otomatis turun ke baris berikutnya) atau struktur layout halaman utama (*Sidebar + Main Content*).

### Membuat Grid Responsif Otomatis Tanpa Media Query

Berikut adalah salah satu *snippet* CSS Grid paling sakti di dunia kerja untuk menampilkan daftar kartu (misalnya daftar pengajuan izin atau daftar produk) yang **otomatis menyesuaikan jumlah kolom** dengan lebar layar:

```css
.card-grid {
    display: grid;
    /* Buat kolom sebanyak mungkin yang muat di layar, 
       dengan lebar minimal tiap kartu 280px dan maksimal membagi rata ruang (1fr) */
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.5rem;
}
```
Dengan 4 baris CSS di atas:
- Di layar monitor lebar (1200px), browser otomatis menampilkan **4 kolom** kartu.
- Di layar laptop/tablet (800px), browser otomatis menampilkan **2 kolom** kartu.
- Di layar HP (360px), browser otomatis menampilkan **1 kolom** kartu ke bawah!

Untuk layout **Sidebar + Main Content**:
```css
.app-layout {
    display: grid;
    grid-template-columns: 260px 1fr; /* Kolom 1 (Sidebar) tetap 260px, Kolom 2 (Main) ambil sisa layar */
    min-height: 100vh;
}
```

---

## Responsive Web Design & Media Queries

Ketika kita perlu mengubah aturan CSS pada ukuran layar tertentu (misalnya menyembunyikan sidebar di layar HP atau mengubah jumlah kolom), kita menggunakan **Media Queries (`@media`)**:

```css
/* Gaya Default (Mobile-First: berlaku untuk semua layar mulai dari layar HP kecil) */
.container {
    width: 100%;
    padding: 1rem;
}

.stats-row {
    display: grid;
    grid-template-columns: 1fr; /* 1 kolom di HP */
    gap: 1rem;
}

/* Tablet ke atas (Lebar layar >= 768px) */
@media (min-width: 768px) {
    .container {
        padding: 2rem;
    }
    .stats-row {
        grid-template-columns: repeat(2, 1fr); /* 2 kolom di Tablet */
    }
}

/* Desktop ke atas (Lebar layar >= 1024px) */
@media (min-width: 1024px) {
    .container {
        max-width: 1200px;
        margin: 0 auto; /* Menengahkan container di layar lebar */
    }
    .stats-row {
        grid-template-columns: repeat(4, 1fr); /* 4 kolom di Desktop */
    }
}
```

---

## Latihan Praktikum Modul 1

Tambahkan file `style.css` dan hubungkan ke halaman HTML **"Portal Pengajuan Izin Kompetisi Mahasiswa"** yang sudah kamu buat di Modul 0, lalu terapkan:

1. **CSS Reset & Variabel**: Gunakan `box-sizing: border-box` dan deklarasikan minimal 5 variabel warna di `:root`.
2. **Navbar dengan Flexbox**: Buat `<header>` memiliki logo/judul di sebelah kiri dan menu `<nav>` di sebelah kanan menggunakan `display: flex; justify-content: space-between; align-items: center;`.
3. **Layout 2 Kolom dengan CSS Grid**: Di layar desktop (`min-width: 1024px`), buat `<main>` menampilkan **Section Form Pengajuan** di sebelah kiri (`380px`) dan **Section Riwayat Pengajuan** di sebelah kanan (`1fr`). Di layar HP (`< 1024px`), keduanya otomatis menumpuk 1 kolom ke bawah.
4. **Styling Form & Tabel**: Berikan `padding`, `border-radius`, `border`, serta efek `:focus` pada setiap `<input>` dan efek `:hover` pada tombol submit serta baris tabel.
