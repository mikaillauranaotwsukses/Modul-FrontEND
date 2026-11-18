## Daftar Isi

- [Mengapa Backend Developer Menyukai Tailwind CSS?](#mengapa-backend-developer-menyukai-tailwind-css)
  - [Masalah CSS Tradisional vs Pendekatan *Utility-First*](#masalah-css-tradisional-vs-pendekatan-utility-first)
- [Cara Memasang Tailwind CSS dengan Cepat](#cara-memasang-tailwind-css-dengan-cepat)
  - [Opsi 1: Play CDN (Untuk Praktikum Cepat Tanpa Build Step)](#opsi-1-play-cdn-untuk-praktikum-cepat-tanpa-build-step)
  - [Opsi 2: Vite + Tailwind CLI (Standar Proyek Production)](#opsi-2-vite--tailwind-cli-standar-proyek-production)
- [Sistem Skala dan Design Tokens di Tailwind](#sistem-skala-dan-design-tokens-di-tailwind)
  - [1. Rumus Skala Spasi (`p-*`, `m-*`, `gap-*`) — Kelipatan 4px](#1-rumus-skala-spasi-p--m--gap---kelipatan-4px)
  - [2. Sistem Warna Semantik (`bg-*`, `text-*`, `border-*`)](#2-sistem-warna-semantik-bg--text--border-)
  - [3. Tipografi, Ukuran, dan Ketebalan Huruf](#3-tipografi-ukuran-dan-ketebalan-huruf)
  - [4. Flexbox & Grid dalam Hitungan Detik](#4-flexbox--grid-dalam-hitungan-detik)
- [Interactive States & Responsive Breakpoints](#interactive-states--responsive-breakpoints)
  - [Modifier Interaksi (`hover:`, `focus:`, `disabled:`)](#modifier-interaksi-hover-focus-disabled)
  - [Modifier Responsif (`sm:`, `md:`, `lg:`, `xl:`)](#modifier-responsif-sm-md-lg-xl)
- [Prinsip UI/UX Praktis agar Desain Tidak Terlihat Amatir](#prinsip-uiux-praktis-agar-desain-tidak-terlihat-amatir)
- [Katalog Blueprint Komponen Siap Pakai](#katalog-blueprint-komponen-siap-pakai)
  - [1. Komponen Status Badge](#1-komponen-status-badge)
  - [2. Komponen Card & Button Modern](#2-komponen-card--button-modern)
  - [3. Komponen Form Input yang Rapi](#3-komponen-form-input-yang-rapi)
- [Latihan Praktikum Modul 4](#latihan-praktikum-modul-4)

# Modern Styling dengan Tailwind CSS & Design System

## Mengapa Backend Developer Menyukai Tailwind CSS?

Saat menulis CSS secara tradisional (Modul 1), kita sering menghabiskan banyak waktu untuk dua hal yang melelahkan:
1. **Memikirkan nama class**: `.container-wrapper`, `.card-inner-box`, `.header-left-section`—dan harus terus bolak-balik antara file `.html` dan file `.css`.
2. **Takut menghapus atau mengubah CSS**: Saat proyek membesar, kita takut mengubah `.card` di `style.css` karena khawatir merusak tampilan halaman lain yang ikut memakai class `.card`.

### Masalah CSS Tradisional vs Pendekatan *Utility-First*

**Tailwind CSS** menyelesaikan masalah ini dengan pendekatan **Utility-First CSS**: alih-alih membuat class bernama `.kartu-profil` di file terpisah, kita merakit tampilan elemen langsung di atribut `class` HTML menggunakan blok-blok fungsi atomik (*utility classes*) yang sudah terstandarisasi!

Perhatikan perbandingannya:

```css
/* ❌ Cara Tradisional: Harus buat nama class di style.css */
.btn-simpan {
    background-color: #2563eb;
    color: #ffffff;
    padding: 0.5rem 1rem;
    border-radius: 0.5rem;
    font-weight: 600;
}
.btn-simpan:hover {
    background-color: #1d4ed8;
}
```

```html
<!-- ✅ Cara Tailwind CSS: Langsung selesai di HTML tanpa pindah file! -->
<button class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg font-semibold">
    Simpan Data
</button>
```

Keuntungan utama bagi seorang Engineer:
1. **Zero Context-Switching**: Kamu tidak perlu berpindah-pindah tab antara file HTML/JSX dan file CSS.
2. **Built-in Design System**: Kamu tidak perlu menebak kode warna hex atau ukuran piksel sembarangan, karena Tailwind sudah menyediakan skala warna, spasi, dan bayangan yang proporsional.
3. **Aman dari Efek Samping**: Mengubah class pada satu tombol hanya mengubah tombol itu saja—tidak akan pernah merusak halaman lain secara tidak sengaja.

---

## Cara Memasang Tailwind CSS dengan Cepat

### Opsi 1: Play CDN (Untuk Praktikum Cepat Tanpa Build Step)

Jika kamu hanya memiliki satu file `index.html` dan ingin langsung berlatih Tailwind CSS sekarang juga tanpa instalasi `npm`, cukup tambahkan satu baris `<script>` berikut di dalam `<head>` dokumen HTML-mu:

```html
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Praktikum Tailwind CSS</title>
    <!-- Tailwind CSS Play CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-50 text-slate-900 antialiased">
    <!-- Isi halamanmu di sini -->
</body>
</html>
```

### Opsi 2: Vite + Tailwind CLI (Standar Proyek Production)

Saat membangun aplikasi nyata (seperti di Modul 5 dengan React/Vite), Tailwind diinstal melalui `npm` agar saat di-*build* hanya menyertakan class CSS yang benar-benar terpakai (ukuran file CSS akhir biasanya hanya `< 10 KB`).

---

## Sistem Skala dan Design Tokens di Tailwind

Tailwind sebenarnya adalah **pemetaan 1-ke-1 ke properti CSS yang sudah kamu pelajari di Modul 1**. Begitu kamu tahu rumus penamaannya, kamu hampir tidak perlu menghafal dokumentasi!

### 1. Rumus Skala Spasi (`p-*`, `m-*`, `gap-*`) — Kelipatan 4px

Di Tailwind, **1 unit angka spasi = `0.25rem` = `4px`**!
Jadi kalau kamu butuh ukuran `16px`, bagi saja dengan 4 (`16 / 4 = 4`), maka gunakan angka **`4`**.

| Angka Tailwind | Ukuran `rem` | Ukuran `px` | Contoh Penggunaan |
| :---: | :---: | :---: | :--- |
| `1` | `0.25rem` | `4px` | `p-1`, `gap-1` |
| `2` | `0.5rem` | `8px` | `p-2`, `mt-2`, `gap-2` |
| `3` | `0.75rem` | `12px` | `px-3`, `py-3` |
| `4` | `1rem` | `16px` | `p-4`, `mb-4`, `gap-4` |
| `6` | `1.5rem` | `24px` | `p-6`, `gap-6` |
| `8` | `2rem` | `32px` | `p-8`, `my-8` |

Arah sumbu untuk **Padding (`p`)** dan **Margin (`m`)**:
- `p-4`: Padding di keempat sisi (`16px`).
- `px-4`: Padding horizontal (**kiri dan kanan** = `16px`).
- `py-2`: Padding vertikal (**atas dan bawah** = `8px`).
- `pt-4`, `pr-4`, `pb-4`, `pl-4`: Padding *Top*, *Right*, *Bottom*, *Left*.
- `mx-auto`: `margin-left: auto; margin-right: auto;` (menengahkan blok secara horizontal).

### 2. Sistem Warna Semantik (`bg-*`, `text-*`, `border-*`)

Setiap palet warna di Tailwind (seperti `slate`, `gray`, `blue`, `indigo`, `emerald`, `amber`, `rose`) memiliki skala kecerahan dari **`50` (paling terang/pucat)** hingga **`950` (paling gelap/pekat)**:

```
50 ──► 100 ──► 200 ──► 300 ──► 400 ──► 500 ──► 600 ──► 700 ──► 800 ──► 900 ──► 950
(Sangat Terang: Cocok untuk     (Pertengahan)   (Pekat: Cocok untuk Tombol Utama,
 Background lembut & Badge)                      Teks, & Dark Mode Surface)
```

| Kebutuhan UI | Palet & Angka yang Direkomendasikan |
| :--- | :--- |
| **Background Halaman Utama** | `bg-slate-50` atau `bg-gray-50` (Jangan putih murni agar kartu putih terlihat menonjol!) |
| **Background Kartu / Tabel / Form** | `bg-white` dengan bingkai tipis `border border-slate-200` |
| **Teks Utama (Judul & Isi)** | `text-slate-900` atau `text-slate-800` (Lebih nyaman di mata daripada hitam pekat `#000`) |
| **Teks Sekunder (Subtitle / Tanggal)** | `text-slate-500` atau `text-slate-600` |
| **Tombol Aksi Utama (Primary)** | `bg-blue-600 hover:bg-blue-700 text-white` (atau `indigo-600`) |
| **Status Sukses / Disetujui** | `bg-emerald-100 text-emerald-800` (untuk badge) atau `bg-emerald-600` (untuk tombol) |
| **Status Peringatan / Menunggu** | `bg-amber-100 text-amber-800` |
| **Status Bahaya / Ditolak / Hapus** | `bg-rose-100 text-rose-800` (untuk badge) atau `bg-rose-600` (untuk tombol hapus) |

### 3. Tipografi, Ukuran, dan Ketebalan Huruf

- **Ukuran Huruf**:
  - `text-xs` (`12px`), `text-sm` (`14px`), `text-base` (`16px` - default), `text-lg` (`18px`), `text-xl` (`20px`), `text-2xl` (`24px`), `text-3xl` (`30px`).
- **Ketebalan Huruf**:
  - `font-normal` (400), `font-medium` (500), `font-semibold` (600), `font-bold` (700).
- **Lengkungan Sudut (`border-radius`)**:
  - `rounded` (`4px`), `rounded-md` (`6px`), `rounded-lg` (`8px`), `rounded-xl` (`12px`), `rounded-2xl` (`16px`), `rounded-full` (bentuk kapsul/lingkaran).
- **Bayangan (`box-shadow`)**:
  - `shadow-sm` (bayangan halus modern), `shadow`, `shadow-md`, `shadow-lg`.

### 4. Flexbox & Grid dalam Hitungan Detik

Semua konsep Flexbox dan CSS Grid dari Modul 1 bisa langsung ditulis dengan sangat ringkas:

| CSS Tradisional (Modul 1) | Class Tailwind CSS |
| :--- | :--- |
| `display: flex; align-items: center; justify-content: space-between;` | `flex items-center justify-between` |
| `display: flex; flex-direction: column; gap: 1rem;` | `flex flex-col gap-4` |
| `display: grid; grid-template-columns: repeat(3, 1fr); gap: 1.5rem;` | `grid grid-cols-3 gap-6` |
| `width: 100%; max-width: 80rem; margin: 0 auto;` | `w-full max-w-7xl mx-auto` |

---

## Interactive States & Responsive Breakpoints

### Modifier Interaksi (`hover:`, `focus:`, `disabled:`)

Di Tailwind, kamu cukup menambahkan awalan (*prefix*) `hover:`, `focus:`, atau `disabled:` di depan class apa pun:

```html
<input 
    type="text" 
    placeholder="Masukkan NRP..."
    class="w-full px-3.5 py-2 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition"
>
```

### Modifier Responsif (`sm:`, `md:`, `lg:`, `xl:`)

Tailwind menggunakan pendekatan **Mobile-First**. Artinya:
- Class **tanpa prefix** (misal `grid-cols-1`) berlaku untuk **semua ukuran layar mulai dari HP kecil**.
- Class **dengan prefix** (misal `md:grid-cols-2`, `lg:grid-cols-4`) berlaku ketika lebar layar mencapai *breakpoint* tersebut ke atas:
  - `sm:` (`>= 640px`)
  - `md:` (`>= 768px` - Tablet)
  - `lg:` (`>= 1024px` - Laptop)
  - `xl:` (`>= 1280px` - Monitor Desktop)

Contoh membuat layout yang di HP tampil 1 kolom, di Tablet 2 kolom, dan di Laptop 3 kolom:
```html
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
    <!-- Kartu 1 -->
    <!-- Kartu 2 -->
    <!-- Kartu 3 -->
</div>
```

---

## Prinsip UI/UX Praktis agar Desain Tidak Terlihat Amatir

Jika kamu bukan seorang desainer grafis namun ingin aplikasi buatanmu terlihat bersih dan profesional seperti produk SaaS modern, ikuti **4 aturan praktis** berikut:

1. **Jangan Gunakan Border Tebal & Bayangan Gelap Sekaligus**: Untuk kartu (`Card`), cukup gunakan `bg-white border border-slate-200 rounded-xl shadow-sm`. Kombinasi border abu-abu tipis (`slate-200`) dengan bayangan tipis (`shadow-sm`) selalu terlihat jauh lebih elegan daripada `border: 2px solid black`.
2. **Berikan Ruang Bernapas (*Padding* & *Gap*) yang Cukup**: Kesalahan paling umum programmer backend saat mendesain UI adalah teks yang terlalu mepet ke garis tepi kotak. Gunakan minimal `p-5` atau `p-6` (`20px-24px`) untuk padding di dalam Card, dan `px-4 py-2` untuk tombol.
3. **Bangun Hierarki Visual dengan Ketebalan & Warna, Bukan Hanya Ukuran Huruf**:
   - Judul Kartu: `text-base font-semibold text-slate-900`
   - Deskripsi/Metadata: `text-sm text-slate-500`
4. **Batasi Lebar Maksimal Konten (`max-w-*`)**: Jangan biarkan tabel atau form membentang dari ujung kiri sampai ujung kanan pada monitor 24 inci! Bungkus konten utama halaman dengan `<main class="max-w-6xl mx-auto px-4 py-8">`.

---

## Katalog Blueprint Komponen Siap Pakai

### 1. Komponen Status Badge

```html
<!-- Status: Disetujui -->
<span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-emerald-100 text-emerald-800">
    Disetujui
</span>

<!-- Status: Menunggu Verifikasi -->
<span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-amber-100 text-amber-800">
    Menunggu
</span>

<!-- Status: Ditolak -->
<span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-rose-100 text-rose-800">
    Ditolak
</span>
```

### 2. Komponen Card & Button Modern

```html
<article class="bg-white border border-slate-200 rounded-xl p-6 shadow-sm hover:shadow-md transition">
    <div class="flex items-start justify-between gap-4">
        <div>
            <span class="text-xs font-semibold uppercase tracking-wider text-blue-600">
                Kompetisi Nasional
            </span>
            <h3 class="mt-1 text-lg font-bold text-slate-900">
                Final Gemastik Divisi Keamanan Siber
            </h3>
            <p class="mt-1 text-sm text-slate-500">
                Diajukan oleh: <strong class="text-slate-700">Muhammad Mikail (5027261053)</strong>
            </p>
        </div>
        <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-amber-100 text-amber-800">
            Menunggu
        </span>
    </div>

    <div class="mt-5 pt-4 border-t border-slate-100 flex items-center justify-end gap-3">
        <button class="px-3.5 py-1.5 text-sm font-medium text-slate-700 bg-slate-100 hover:bg-slate-200 rounded-lg transition">
            Lihat Surat
        </button>
        <button class="px-3.5 py-1.5 text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 rounded-lg transition">
            Setujui Izin
        </button>
    </div>
</article>
```

### 3. Komponen Form Input yang Rapi

```html
<div class="flex flex-col gap-1.5">
    <label for="nrp" class="text-sm font-medium text-slate-700">
        NRP Mahasiswa <span class="text-rose-500">*</span>
    </label>
    <input 
        id="nrp" 
        name="nrp" 
        type="text" 
        placeholder="Contoh: 5027261053"
        class="w-full px-3.5 py-2 text-sm bg-white border border-slate-300 rounded-lg text-slate-900 placeholder-slate-400 focus:outline-none focus:ring-2 focus:ring-blue-500/20 focus:border-blue-600 transition"
    >
    <p class="text-xs text-slate-500">Masukkan 10 digit NRP aktif sesuai SIAKAD.</p>
</div>
```

---

## Latihan Praktikum Modul 4

1. Tambahkan script **Tailwind Play CDN** pada halaman **"Portal Pengajuan Izin Kompetisi Mahasiswa"** kamu.
2. Ubah tampilan halaman tersebut menggunakan *utility classes* Tailwind CSS:
   - Gunakan `bg-slate-50` pada `<body>` dan batasi kontainer utama dengan `max-w-6xl mx-auto px-4 py-8`.
   - Buat **Summary Stat Cards** di bagian atas halaman (`grid grid-cols-1 sm:grid-cols-3 gap-4`) yang menampilkan: *Total Pengajuan*, *Disetujui*, dan *Menunggu Verifikasi*.
   - Gunakan blueprint **Card**, **Badge**, dan **Form Input** di atas sehingga portal terlihat seperti aplikasi dashboard produksi yang modern dan responsif di layar HP maupun Desktop.
