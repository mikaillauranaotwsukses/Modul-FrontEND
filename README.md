<div align="center">
  <h1>Modul Praktikum <br /> Fundamental & Arsitektur Frontend Web</h1>
  <p><em>Disusun khusus dengan pendekatan <b>Mental Model Backend Engineer</b> — sistematis, logis, dan tanpa tebak-tebakan.</em></p>
</div>

---

## Mengapa Modul Ini Berbeda?

Bagi mahasiswa atau developer yang sudah terbiasa di dunia **Backend** (terbiasa dengan *routing*, *database schema*, *struct/class*, *REST API*, dan *concurrency*), belajar **Frontend** sering terasa membingungkan karena:
1. **HTML** sering terlihat hanya seperti tumpukan *tag* tanpa struktur data yang jelas.
2. **CSS** sering terasa seperti *"trial and error"* (mengubah `margin` atau `position` lalu berharap tampilannya pas).
3. **JavaScript di Browser** punya perilaku asinkron, *event propagation*, dan *state synchronization* yang berbeda dengan siklus *Request → Process → Response* di server.

Repositori ini mengadaptasi struktur dan gaya penyampaian [Modul Praktikum ATP (Lab KCKS)](https://github.com/lab-kcks/modul-atp), namun dirancang untuk menjembatani logika **Backend** yang sudah kamu kuasai ke dalam ekosistem **Frontend Modern**.

---

## Daftar Modul dan Materi

### [Pra-Praktikum: Persiapan Environment & Mental Model Frontend](./Modul/Pra-Praktikum-Persiapan-Environment.md)

- Perbedaan Siklus Hidup Backend (*Stateless Request-Response*) vs Frontend (*Stateful Event-Driven Runtime*)
- Persiapan Browser Developer Tools (*Elements, Console, Network, Application*)
- Instalasi Ekstensi Visual Studio Code untuk Frontend
- Instalasi Node.js, Package Manager (`npm`), dan Modern Bundler (`Vite`)

### [Modul 0: Pengenalan HTML5 Semantik, Form, dan Struktur DOM](./Modul/Modul-0-HTML-Semantik-dan-DOM-Tree.md)

- Pengenalan HTML5 sebagai *Serialized Tree Structure* (Mirip JSON / AST)
- Struktur Dasar Dokumen HTML (`<head>`, `<meta>`, `<body>`)
- Elemen Semantik (`<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`) vs Non-Semantik (`<div>`, `<span>`)
- Elemen Teks, Media, Tabel, dan Hyperlink
- Formulir (`<form>`, `<input>`, `<select>`, `<textarea>`) & Mapping ke Payload Backend
- Atribut Penting (`id`, `class`, `name`, `data-*`) dan Native Form Validation

### [Modul 1: CSS Fundamental, Box Model, dan Algoritma Layouting](./Modul/Modul-1-CSS-Box-Model-dan-Layouting.md)

- Cara Kerja CSS Engine: *Selector*, *Cascade*, *Inheritance*, dan *Specificity Score*
- CSS Box Model (`content`, `padding`, `border`, `margin`, dan `box-sizing: border-box`)
- Satuan Ukuran Modern (`px`, `rem`, `em`, `%`, `vw`, `vh`)
- Display & Positioning (`block`, `inline`, `relative`, `absolute`, `fixed`, `sticky`)
- Layout 1 Dimensi: **Flexbox** (*Main Axis* vs *Cross Axis*)
- Layout 2 Dimensi: **CSS Grid** (*Template Columns*, *Gap*, *Responsive Grid*)
- Responsive Design dengan Media Queries (*Mobile-First Approach*)

### [Modul 2: JavaScript DOM, Event Loop, dan Interaktivitas UI](./Modul/Modul-2-JavaScript-DOM-dan-Event-Driven.md)

- Document Object Model (DOM) sebagai *In-Memory Object Tree*
- Seleksi dan Manipulasi Elemen (`querySelector`, `textContent`, `classList`, `dataset`)
- Pembuatan Elemen Dinamis (`createElement`, `appendChild`, *Template Literals*)
- Arsitektur *Event-Driven*: `addEventListener`, *Event Object*, dan `preventDefault()`
- *Event Bubbling*, *Event Capturing*, dan Teknik *Event Delegation*
- State Sederhana & Penyimpanan Browser (`localStorage` vs `sessionStorage` vs `Cookies`)

### [Modul 3: Asynchronous Frontend & Integrasi REST API](./Modul/Modul-3-Async-Fetch-API-dan-Integrasi-Backend.md)

- *Single-Threaded Event Loop* di Browser (Mengapa UI Bisa *Freeze*?)
- `Promise`, `async / await`, dan `fetch()` API
-siklus *Data Fetching* di UI: **Loading State**, **Success State**, **Empty State**, dan **Error State**
- Mengirim Payload ke Backend: Query Params, JSON Body, dan `FormData` (Upload File)
- Memahami **CORS (Cross-Origin Resource Sharing)** dari Sisi Browser & Server
- Autentikasi di Frontend: *Bearer Token (JWT)* vs *HttpOnly Cookie*

### [Modul 4: Modern Styling dengan Tailwind CSS & Design System](./Modul/Modul-4-Tailwind-CSS-dan-Design-System.md)

- Masalah Skalabilitas CSS Tradisional vs Pendekatan *Utility-First*
- Konsep Dasar Tailwind CSS (Mapping Class ke Properti CSS)
- Sistem Desain (*Design Tokens*): Skala Spasi, Tipografi, dan Hierarki Warna
- Membuat Komponen UI Standar: *Card*, *Badge*, *Button*, *Modal*, dan *Table*
- *State Variants* (`hover:`, `focus:`, `disabled:`, `group-hover:`) dan *Responsive Prefixes* (`sm:`, `md:`, `lg:`)
- Prinsip UI/UX Praktis untuk Developer Backend (Kontras, *Whitespace*, dan *Visual Hierarchy*)

### [Modul 5: Arsitektur Frontend Modern (React & State Management)](./Modul/Modul-5-React-Fundamental-dan-State-Management.md)

- Paradigma **Imperative UI** (DOM Manual) vs **Declarative UI** ($UI = f(State)$)
- Konsep *Component-Based Architecture* dan JSX
- **Props** (Parameter Fungsi / *Immutable Input*) vs **State** (Memori Lokal Komponen)
- Siklus Hidup & React Hooks Utama (`useState`, `useEffect`, `useRef`)
- *Conditional Rendering*, *List Rendering* (`map` & `key`), dan *Controlled Forms*
- Arsitektur Folder Proyek Frontend Modern & Alur Kerja Integrasi Fullstack

---

## Peta Analogi Cepat: Backend vs Frontend

| Konsep Backend | Padanan / Analogi di Frontend |
| :--- | :--- |
| **JSON / Struct / Schema** | **HTML & DOM Tree** (Struktur hierarki data dokumen) |
| **Middleware / Config Rules** | **CSS Cascade & Specificity** (Aturan deklaratif yang diterapkan berdasarkan prioritas) |
| **Function Parameters (`args`)** | **HTML Attributes / React Props** (Input yang dikirim ke komponen) |
| **In-Memory Cache / Session** | **Component State / `localStorage`** (Data sementara di sisi klien) |
| **Pub/Sub / Webhook Listener** | **DOM Event Listener (`onclick`, `onsubmit`)** (Menunggu aksi *user* lalu menjalankan *handler*) |
| **HTTP Client (`axios`, `curl`, `http.Client`)** | **Browser `fetch()` API** (Koneksi dari browser ke endpoint API backend) |
