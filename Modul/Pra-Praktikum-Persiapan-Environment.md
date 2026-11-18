## Daftar Isi

- [Persiapan Environment & Mental Model Frontend](#persiapan-environment--mental-model-frontend)
  - [Mental Model: Backend vs Frontend](#mental-model-backend-vs-frontend)
  - [Mengenal Browser sebagai Runtime Environment](#mengenal-browser-sebagai-runtime-environment)
  - [Menguasai Browser Developer Tools (DevTools)](#menguasai-browser-developer-tools-devtools)
    - [1. Tab Elements (DOM & CSS Inspector)](#1-tab-elements-dom--css-inspector)
    - [2. Tab Console (REPL & Log)](#2-tab-console-repl--log)
    - [3. Tab Network (HTTP Traffic Inspector)](#3-tab-network-http-traffic-inspector)
    - [4. Tab Application (Client Storage)](#4-tab-application-client-storage)
  - [Persiapan Visual Studio Code untuk Frontend](#persiapan-visual-studio-code-untuk-frontend)
  - [Instalasi Node.js dan Modern Build Tool (Vite)](#instalasi-nodejs-dan-modern-build-tool-vite)
  - [Cara Menjalankan File Praktikum](#cara-menjalankan-file-praktikum)

# Persiapan Environment & Mental Model Frontend

Sebelum mulai menulis baris kode HTML, CSS, dan JavaScript, kita perlu menyamakan **mental model** terlebih dahulu. Sebagai mahasiswa yang sudah terbiasa dengan **Backend**, kamu sebenarnya sudah memahami lebih dari 50% konsep dasar rekayasa perangkat lunak—kamu hanya perlu memetakan konsep tersebut ke *runtime* yang berbeda, yaitu **Web Browser**.

---

## Mental Model: Backend vs Frontend

Di dunia **Backend**, alur eksekusi program umumnya bersifat **linear dan singkat per request**:

```
[Client Request] ──► [Router] ──► [Controller/Service] ──► [Database] ──► [JSON/HTML Response]
```

Begitu *response* dikirim, siklus untuk *request* tersebut selesai (*stateless*).

Sebaliknya, di dunia **Frontend**, program kamu berjalan terus-menerus di dalam browser milik pengguna (*long-lived, stateful, dan event-driven*):

```
[Initial Load: HTML/CSS/JS]
       │
       ▼
[Browser membangun DOM Tree & merender layar]
       │
       ▼
[Idle / Menunggu di Event Loop] ◄────────────────────────────┐
       │                                                     │
       ├──► User klik tombol / ketik form (DOM Event)        │
       │           │                                         │
       │           ▼                                         │
       │    [Jalankan Event Handler JS]                      │
       │           │                                         │
       │           ├──► Fetch ke API Backend (Async)         │
       │           │                                         │
       │           ▼                                         │
       └──► [Update State / Manipulasi DOM] ──► [Re-render] ─┘
```

Perbedaan kunci yang wajib diingat oleh Backend Developer saat masuk ke Frontend:

1. **Lingkungan Eksekusi Tidak Bisa Dikontrol**: Di backend, kamu tahu persis spesifikasi server, versi runtime (Go/Node/Python/PHP), dan ukuran RAM. Di frontend, kode kamu dijalankan di HP layar kecil, laptop lama, atau monitor 4K dengan kecepatan internet yang bervariasi.
2. **State Berada di Depan Mata User**: Setiap perubahan data harus langsung tercermin di tampilan layar tanpa perlu *refresh* satu halaman penuh.
3. **Semua Kode Frontend Bersifat Publik**: Jangan pernah menaruh *secret key*, kredensial database, atau validasi keamanan murni di frontend. Semua kode HTML, CSS, dan JS dapat dilihat dan dimodifikasi oleh *user* melalui *Inspect Element*.

---

## Mengenal Browser sebagai Runtime Environment

Jika di backend kamu menjalankan kode di atas Node.js, Go Runtime, JVM, atau PHP-FPM, maka di frontend *runtime environment* kamu adalah **Web Browser** (Chrome, Edge, Firefox, Safari).

Di dalam browser terdapat tiga mesin utama yang bekerja sama:

| Komponen Mesin Browser | Tugas Utama | Analogi Backend |
| :--- | :--- | :--- |
| **HTML Parser & DOM Tree** | Membaca teks HTML dan mengubahnya menjadi struktur pohon objek (*Document Object Model*) di memori. | Parser JSON/XML yang mengubah string menjadi *Struct* atau *Object Tree*. |
| **CSS Engine & Layout/Render Tree** | Menghitung ukuran, posisi, warna, dan tata letak setiap elemen di layar (*Box Model*, *Flexbox*, *Grid*). | *Constraint Solver* yang menghitung koordinat geometri. |
| **JavaScript Engine (misal: V8)** | Mengeksekusi logika bisnis, memanipulasi DOM, menangani *event* klik/ketik, dan melakukan HTTP request (`fetch`). | Runtime aplikasi yang memiliki akses ke API sistem (*Web APIs*). |

---

## Menguasai Browser Developer Tools (DevTools)

Di backend, senjata utamamu adalah **Terminal Log**, **Debugger**, dan **Postman/Insomnia**.
Di frontend, senjata utamamu adalah **Browser DevTools**.

Untuk membukanya di Google Chrome / Microsoft Edge / Firefox:
- Tekan **`F12`**, atau
- Tekan **`Ctrl + Shift + I`** (Windows/Linux) / **`Cmd + Option + I`** (Mac), atau
- Klik kanan pada halaman web lalu pilih **Inspect**.

Ada **4 tab utama** di DevTools yang wajib kamu kuasai:

### 1. Tab Elements (DOM & CSS Inspector)

Tab **Elements** menampilkan kondisi **DOM Tree secara *real-time*** (bukan sekadar *source code* HTML awal, melainkan struktur elemen setelah dimodifikasi oleh JavaScript).

- **Panel Kiri (HTML/DOM)**: Kamu bisa mengklik elemen apa pun, mengedit teksnya langsung, atau melihat atributnya.
- **Panel Kanan (Styles & Computed)**:
  - **Styles**: Menampilkan semua aturan CSS yang aktif pada elemen tersebut. Jika ada properti yang dicoret (~~`color: red;`~~), artinya properti tersebut tertimpa (*overridden*) oleh aturan CSS lain yang lebih spesifik.
  - **Computed**: Menampilkan diagram **Box Model** (`content`, `padding`, `border`, `margin`) beserta ukuran piksel aktual elemen di layar. **Tips Backend Dev:** Kalau tata letak elemenmu terasa aneh atau melenceng, selalu cek kotak *Computed Box Model* ini terlebih dahulu!

### 2. Tab Console (REPL & Log)

Tab **Console** berfungsi ganda:
1. Menampilkan output `console.log()`, `console.warn()`, dan pesan error `Uncaught TypeError / ReferenceError`.
2. Berfungsi sebagai **Interactive REPL** (Read-Eval-Print Loop) tempat kamu bisa langsung mengetik perintah JavaScript untuk menguji kode atau memeriksa variabel di halaman yang sedang terbuka.

Contoh perintah yang bisa langsung kamu ketik di Console:

```javascript
document.title = "Belajar Frontend dari Sudut Pandang Backend";
document.querySelector("body").style.backgroundColor = "#f0f9ff";
```

### 3. Tab Network (HTTP Traffic Inspector)

Sebagai orang backend, ini adalah tab yang paling akrab denganmu! Tab **Network** merekam seluruh lalu lintas HTTP/HTTPS dan WebSocket yang keluar-masuk dari browser:

- Melihat **Status Code** (`200 OK`, `400 Bad Request`, `401 Unauthorized`, `404 Not Found`, `500 Internal Server Error`).
- Memeriksa **Request Headers** (apakah header `Authorization: Bearer ...` atau `Content-Type: application/json` sudah terkirim dengan benar?).
- Memeriksa **Payload** (isi body JSON atau FormData yang dikirim frontend ke backend).
- Memeriksa **Preview / Response** (isi JSON yang dikembalikan oleh API backend kamu) serta mendeteksi error **CORS**.

### 4. Tab Application (Client Storage)

Tab **Application** adalah "Database GUI" milik sisi klien (mirip DBeaver/pgAdmin tetapi untuk penyimpanan lokal di browser). Di sini kamu bisa melihat, mengedit, dan menghapus:
- **Local Storage**
- **Session Storage**
- **Cookies** (termasuk flag `HttpOnly`, `Secure`, `SameSite`)

---

## Persiapan Visual Studio Code untuk Frontend

Pastikan kamu sudah menginstal **Microsoft Visual Studio Code (VS Code)**. Untuk mempercepat pengembangan frontend, instal beberapa ekstensi berikut melalui menu **Extensions (`Ctrl + Shift + X`)**:

1. **Live Server** (oleh *Ritwick Dey*): Menjalankan *local development server* untuk file HTML statis lengkap dengan fitur *Auto-Reload* setiap kali kamu menekan `Ctrl + S`.
2. **Prettier - Code formatter**: Merapikan indentasi HTML, CSS, dan JavaScript secara otomatis.
3. **Tailwind CSS IntelliSense** (oleh *Tailwind Labs*): Memberikan *autocomplete* dan *preview* CSS saat nanti kita masuk ke **Modul 4**.
4. **ES7+ React/Redux/React-Native snippets**: Mempercepat penulisan komponen React pada **Modul 5**.

> **Tips Shortcut Emmet di VS Code:**
> VS Code sudah dilengkapi dengan **Emmet** secara bawaan. Jika kamu membuat file `.html` kosong, cukup ketik tanda seru `!` lalu tekan **`Tab`** atau **`Enter`**, maka VS Code akan otomatis membuat kerangka dasar HTML5 secara instan!

---

## Instalasi Node.js dan Modern Build Tool (Vite)

Meskipun HTML, CSS, dan Vanilla JavaScript (Modul 0 s.d. Modul 3) dapat dijalankan langsung di browser tanpa kompilasi, ekosistem frontend modern (Modul 4 & Modul 5) menggunakan **Node.js** sebagai *tooling environment* (untuk menjalankan *package manager* `npm`, *bundler* Vite, dan kompilator Tailwind/React).

Untuk mengecek apakah Node.js dan `npm` sudah terpasang di komputermu, buka **PowerShell** atau **Terminal**, lalu jalankan:

```bash
node --version
npm --version
```

Jika keduanya menampilkan nomor versi (misalnya `v20.x.x` dan `10.x.x`), maka environment kamu sudah siap! Jika belum, unduh versi **LTS (Long Term Support)** dari [nodejs.org](https://nodejs.org/).

---

## Cara Menjalankan File Praktikum

Selama mempelajari **Modul 0 hingga Modul 3**, kamu cukup menggunakan salah satu dari dua cara berikut:

1. **Menggunakan Ekstensi Live Server di VS Code (Direkomendasikan)**:
   - Buka file `index.html` di VS Code.
   - Klik kanan pada editor, lalu pilih **"Open with Live Server"** (atau klik tombol **Go Live** di pojok kanan bawah VS Code).
   - Browser akan otomatis terbuka di alamat `http://127.0.0.1:5500/index.html`.
2. **Menggunakan `npx serve` melalui Terminal**:
   ```bash
   npx serve .
   ```
   Perintah ini akan menyajikan folder kerja saat ini melalui HTTP server lokal. (Hindari membuka file dengan *double-click* `file:///C:/...` saat belajar **Modul 3**, karena request `fetch()` dan ES Modules membutuhkan protokol `http://` atau `https://` agar tidak diblokir oleh kebijakan keamanan CORS browser).
