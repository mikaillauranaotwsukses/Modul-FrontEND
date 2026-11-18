## Daftar Isi

- [Mengapa Kita Membutuhkan Framework Frontend (React)?](#mengapa-kita-membutuhkan-framework-frontend-react)
  - [Paradigma *Imperative UI* (Vanilla DOM) vs *Declarative UI* (React)](#paradigma-imperative-ui-vanilla-dom-vs-declarative-ui-react)
  - [Rumus Inti React: $UI = f(State)$](#rumus-inti-react-ui--fstate)
- [Memulai Proyek React Modern dengan Vite](#memulai-proyek-react-modern-dengan-vite)
- [Konsep Dasar: Component, JSX, dan Props](#konsep-dasar-component-jsx-dan-props)
  - [1. Component adalah Fungsi yang Mengembalikan UI](#1-component-adalah-fungsi-yang-mengembalikan-ui)
  - [2. Aturan Penulisan JSX (JavaScript XML)](#2-aturan-penulisan-jsx-javascript-xml)
  - [3. Props: Parameter Input Fungsi yang Bersifat *Read-Only*](#3-props-parameter-input-fungsi-yang-bersifat-read-only)
- [Mengelola State dengan `useState`](#mengelola-state-dengan-usestate)
  - [Mengapa Tidak Bisa Menggunakan Variabel Biasa (`let count = 0`)?](#mengapa-tidak-bisa-menggunakan-variabel-biasa-let-count--0)
  - [Aturan Immutability Saat Mengubah State Array dan Object](#aturan-immutability-saat-mengubah-state-array-dan-object)
- [Siklus Rendering Data di React](#siklus-rendering-data-di-react)
  - [Conditional Rendering (Menampilkan Elemen Berdasarkan Kondisi)](#conditional-rendering-menampilkan-elemen-berdasarkan-kondisi)
  - [List Rendering dengan `.map()` dan Pentingnya Prop `key`](#list-rendering-dengan-map-dan-pentingnya-prop-key)
  - [Controlled Form Inputs (Two-Way Binding di React)](#controlled-form-inputs-two-way-binding-di-react)
- [Sinkronisasi & Data Fetching dengan `useEffect`](#sinkronisasi--data-fetching-dengan-useeffect)
  - [Cara Kerja Dependency Array `[]` pada `useEffect`](#cara-kerja-dependency-array--pada-useeffect)
  - [Contoh Lengkap: Fetching Data API di Komponen React](#contoh-lengkap-fetching-data-api-di-komponen-react)
- [Arsitektur Folder Proyek React Standar Industri](#arsitektur-folder-proyek-react-standar-industri)
- [Latihan Praktikum Modul 5](#latihan-praktikum-modul-5)

# Arsitektur Frontend Modern (React & State Management)

## Mengapa Kita Membutuhkan Framework Frontend (React)?

Di **Modul 2**, ketika kita ingin menambahkan satu data izin baru ke halaman menggunakan Vanilla JavaScript, kita harus melakukan langkah-langkah manual terhadap DOM:
1. Cari elemen `<tbody>`,
2. Buat elemen `<tr>` dan `<td>` satu per satu dengan `document.createElement()`,
3. Cari elemen angka penghitung *Total Pengajuan* di atas halaman lalu ubah `textContent`-nya,
4. Cek apakah pesan *"Belum ada data"* masih tampil, jika iya sembunyikan dengan `classList.add("hidden")`.

Bayangkan jika halaman kamu semakin kompleks: ada notifikasi di Navbar, ada filter kategori, ada *pagination*, dan ada modal edit. Jika kamu lupa memperbarui **satu** saja elemen DOM saat data berubah, maka tampilan layar menjadi **tidak sinkron dengan data sebenarnya (*Out-of-Sync UI Bug*)**.

### Paradigma *Imperative UI* (Vanilla DOM) vs *Declarative UI* (React)

- **Imperative UI (Vanilla JS DOM)**: Kamu memberi instruksi langkah demi langkah *bagaimana* cara mengubah elemen di layar (*"Cari elemen X, hapus class Y, ganti teksnya jadi Z"*).
- **Declarative UI (React, Vue, Svelte)**: Kamu cukup mendeklarasikan *seperti apa tampilan yang seharusnya muncul untuk suatu kondisi data (State)*, dan biarkan React yang menghitung serta memperbarui DOM secara otomatis!

### Rumus Inti React: $UI = f(State)$

Sebagai Backend Developer, bayangkan sebuah komponen React sebagai **Pure Function**:
- Kamu memberinya input data (**`Props`** & **`State`**).
- Fungsi tersebut mengembalikan deskripsi tampilan (**`JSX`**).
- Setiap kali **`State`** berubah, React otomatis memanggil ulang fungsi tersebut dan hanya memperbarui bagian DOM yang berubah (*Virtual DOM Diffing / Reconciliation*).

---

## Memulai Proyek React Modern dengan Vite

Untuk membuat proyek React modern di komputermu, jalankan perintah berikut di terminal menggunakan **Vite**:

```bash
npm create vite@latest portal-izin-fe -- --template react
cd portal-izin-fe
npm install
npm run dev
```

Setelah menjalankan `npm run dev`, buka `http://localhost:5173` di browser. Setiap perubahan yang kamu simpan di VS Code akan langsung tampil di layar dalam hitungan milidetik (*Hot Module Replacement / HMR*).

---

## Konsep Dasar: Component, JSX, dan Props

### 1. Component adalah Fungsi yang Mengembalikan UI

Di React, halaman web dipecah menjadi potongan-potongan fungsi modular yang disebut **Components**. Nama fungsi komponen di React **wajib diawali huruf kapital (`PascalCase`)** untuk membedakannya dari tag HTML biasa (`<div>`, `<span>`).

```jsx
// Komponen StatusBadge.jsx
function StatusBadge({ status }) {
    const warna = {
        approved: "bg-emerald-100 text-emerald-800",
        pending: "bg-amber-100 text-amber-800",
        rejected: "bg-rose-100 text-rose-800",
    };

    return (
        <span className={`px-2.5 py-0.5 rounded-full text-xs font-medium ${warna[status]}`}>
            {status.toUpperCase()}
        </span>
    );
}

export default StatusBadge;
```

### 2. Aturan Penulisan JSX (JavaScript XML)

JSX memungkinkan kita menulis sintaks mirip HTML langsung di dalam kode JavaScript. Ada **4 aturan dasar JSX** yang membedakannya dari HTML biasa:

1. **Gunakan `className` sebagai pengganti `class`** (karena `class` adalah keyword reservasi di JavaScript), dan `htmlFor` sebagai pengganti `for` pada `<label>`.
2. **Setiap komponen harus mengembalikan satu elemen induk tunggal** (atau dibungkus dengan *Fragment* kosong `<> ... </>`).
3. **Semua tag wajib ditutup**, termasuk *void elements* seperti `<input />`, `<img />`, dan `<br />`.
4. **Gunakan kurung kurawal `{ ... }`** untuk menyisipkan ekspresi JavaScript (variabel, pemanggilan fungsi, ternary operator, atau `.map()`) di dalam JSX.

### 3. Props: Parameter Input Fungsi yang Bersifat *Read-Only*

**Props (*Properties*)** adalah cara komponen induk (*Parent Component*) mengirim data atau fungsi *callback* ke komponen anak (*Child Component*)—persis seperti mengirim argumen ke dalam sebuah fungsi di backend!

```jsx
// Parent Component mengirimkan props ke Child Component (CardIzin)
function Dashboard() {
    return (
        <div class="grid gap-4">
            <CardIzin 
                nrp="5027261053" 
                nama="Muhammad Mikail" 
                kompetisi="Gemastik Keamanan Siber" 
                status="approved" 
            />
            <CardIzin 
                nrp="5027261099" 
                nama="Budi Santoso" 
                kompetisi="ICPC Asia Jakarta" 
                status="pending" 
            />
        </div>
    );
}
```

> **Aturan Mutlak Props:** Sebuah komponen **tidak boleh mengubah nilai Props-nya sendiri** (*Props are Immutable*). Jika data tersebut perlu berubah seiring interaksi user, maka data tersebut harus disimpan sebagai **State**!

---

## Mengelola State dengan `useState`

### Mengapa Tidak Bisa Menggunakan Variabel Biasa (`let count = 0`)?

Jika kamu mendeklarasikan `let count = 0` di dalam komponen lalu mengubahnya menjadi `count = count + 1` saat tombol diklik:
1. React **tidak tahu** bahwa variabel tersebut berubah, sehingga React **tidak akan memicu *re-render*** (tampilan di layar tidak berubah).
2. Kalaupun komponen di-render ulang, variabel `let count = 0` akan di-reset kembali menjadi `0`!

Oleh karena itu, kita menggunakan React Hook **`useState`**:

```jsx
import { useState } from "react";

function CounterPengajuan() {
    // useState mengembalikan tuple 2 elemen: [nilaiSaatIni, fungsiPengubahNilai]
    const [jumlah, setJumlah] = useState(0);

    function tambah() {
        // Memanggil setJumlah() akan menyimpan nilai baru DAN memicu React me-render ulang UI!
        setJumlah(jumlah + 1);
    }

    return (
        <button onClick={tambah} className="px-4 py-2 bg-blue-600 text-white rounded-lg">
            Total Klik: {jumlah}
        </button>
    );
}
```

### Aturan Immutability Saat Mengubah State Array dan Object

Di React, kita **tidak boleh memodifikasi (*mutate*) state Array atau Object secara langsung** (seperti `daftarIzin.push(itemBaru)`), karena referensi memorinya tetap sama sehingga React menganggap datanya tidak berubah.
Kita harus selalu membuat salinan baru menggunakan **Spread Operator (`...`)**, `.filter()`, atau `.map()`:

```jsx
const [daftarIzin, setDaftarIzin] = useState([]);

// ✅ 1. MENAMBAH item ke State Array (pengganti .push)
function tambahIzin(izinBaru) {
    setDaftarIzin((prev) => [izinBaru, ...prev]);
}

// ✅ 2. MENGHAPUS item dari State Array berdasarkan ID
function hapusIzin(idTarget) {
    setDaftarIzin((prev) => prev.filter((item) => item.id !== idTarget));
}

// ✅ 3. MENGUBAH (Update) satu properti item di dalam State Array
function setujuiIzin(idTarget) {
    setDaftarIzin((prev) =>
        prev.map((item) =>
            item.id === idTarget ? { ...item, status: "approved" } : item
        )
    );
}
```

---

## Siklus Rendering Data di React

### Conditional Rendering (Menampilkan Elemen Berdasarkan Kondisi)

Karena di dalam `{ ... }` pada JSX kita hanya bisa menuliskan **ekspresi** (bukan statement `if-else` biasa), kita menggunakan **Ternary Operator (`kondisi ? A : B`)** atau **Logical AND (`kondisi && A`)**:

```jsx
{isLoading && <p className="text-slate-500">Sedang memuat data...</p>}

{errorMessage && (
    <div className="p-4 bg-rose-50 text-rose-700 rounded-lg">
        Gagal: {errorMessage}
    </div>
)}

{!isLoading && daftarIzin.length === 0 ? (
    <p>Belum ada data pengajuan.</p>
) : (
    <TabelIzin data={daftarIzin} />
)}
```

### List Rendering dengan `.map()` dan Pentingnya Prop `key`

Untuk mengubah sebuah Array data menjadi daftar elemen JSX, gunakan **`Array.map()`**.
Setiap elemen yang dihasilkan di dalam `.map()` **wajib diberi prop `key` yang unik dan stabil** (gunakan `item.id` dari database, hindari menggunakan `index` array jika urutan data bisa berubah/dihapus):

```jsx
<tbody>
    {daftarIzin.map((izin) => (
        <tr key={izin.id} className="border-b border-slate-100">
            <td className="py-3 px-4">{izin.nrp}</td>
            <td className="py-3 px-4 font-medium">{izin.nama}</td>
            <td className="py-3 px-4">{izin.kompetisi}</td>
            <td className="py-3 px-4">
                <StatusBadge status={izin.status} />
            </td>
        </tr>
    ))}
</tbody>
```
*(Analogi Backend: Prop `key` adalah **Primary Key** yang digunakan algoritma Virtual DOM React untuk mengenali baris mana yang ditambah, diubah, atau dihapus tanpa harus menghancurkan ulang seluruh tabel).*

### Controlled Form Inputs (Two-Way Binding di React)

Di React, nilai dari setiap `<input>` biasanya dikendalikan langsung oleh `useState`:

```jsx
function FormIzin({ onTambahIzin }) {
    const [form, setForm] = useState({
        nrp: "",
        nama: "",
        kompetisi: "",
    });

    // Satu handler generik untuk semua input berdasarkan atribut 'name'
    function handleChange(e) {
        const { name, value } = e.target;
        setForm((prev) => ({
            ...prev,
            [name]: value,
        }));
    }

    function handleSubmit(e) {
        e.preventDefault();
        onTambahIzin({
            id: Date.now(),
            ...form,
            status: "pending",
        });
        // Reset form kembali kosong
        setForm({ nrp: "", nama: "", kompetisi: "" });
    }

    return (
        <form onSubmit={handleSubmit} className="flex flex-col gap-4">
            <input
                name="nrp"
                value={form.nrp}
                onChange={handleChange}
                placeholder="NRP Mahasiswa"
                required
                className="border p-2 rounded"
            />
            <input
                name="nama"
                value={form.nama}
                onChange={handleChange}
                placeholder="Nama Lengkap"
                required
                className="border p-2 rounded"
            />
            <input
                name="kompetisi"
                value={form.kompetisi}
                onChange={handleChange}
                placeholder="Nama Kompetisi"
                required
                className="border p-2 rounded"
            />
            <button type="submit" className="bg-blue-600 text-white py-2 rounded">
                Ajukan Izin
            </button>
        </form>
    );
}
```

---

## Sinkronisasi & Data Fetching dengan `useEffect`

Bagaimana jika kita ingin memanggil API backend (`fetch`) secara otomatis tepat ketika komponen pertama kali muncul di layar (*Mount*), atau ketika variabel state pencarian berubah? Kita menggunakan Hook **`useEffect`**.

### Cara Kerja Dependency Array `[]` pada `useEffect`

Parameter kedua dari `useEffect(callback, dependencies)` menentukan **kapan** efek tersebut dijalankan:

| Bentuk `useEffect` | Kapan Dijalankan? | Kegunaan Utama |
| :--- | :--- | :--- |
| `useEffect(() => { ... }, [])` *(Array Kosong)* | **Hanya 1 kali** saat komponen pertama kali tampil di layar (*onMount*). | Mengambil data awal (`GET /api/izin`) dari backend saat halaman dibuka. |
| `useEffect(() => { ... }, [kategori, page])` | Saat pertama tampil **DAN setiap kali nilai `kategori` atau `page` berubah**. | Memuat ulang data dari backend ketika user mengganti filter atau halaman pagination. |

### Contoh Lengkap: Fetching Data API di Komponen React

```jsx
import { useState, useEffect } from "react";

function HalamanDaftarIzin() {
    const [daftarIzin, setDaftarIzin] = useState([]);
    const [isLoading, setIsLoading] = useState(true);
    const [error, setError] = useState(null);
    const [filterStatus, setFilterStatus] = useState("all");

    useEffect(() => {
        // AbortController mencegah race-condition jika user mengganti filter dengan cepat
        const controller = new AbortController();

        async function fetchData() {
            setIsLoading(true);
            setError(null);
            try {
                const query = filterStatus !== "all" ? `?status=${filterStatus}` : "";
                const res = await fetch(`http://localhost:8080/api/v1/izin${query}`, {
                    signal: controller.signal,
                });
                const body = await res.json();

                if (!res.ok) {
                    throw new Error(body.message || "Gagal memuat data dari server");
                }
                setDaftarIzin(body.data);
            } catch (err) {
                if (err.name !== "AbortError") {
                    setError(err.message);
                }
            } finally {
                setIsLoading(false);
            }
        }

        fetchData();

        // Cleanup function: batalkan request lama jika komponen unmount atau filter berubah
        return () => controller.abort();
    }, [filterStatus]);

    return (
        <section className="max-w-5xl mx-auto p-6">
            <div className="flex justify-between items-center mb-6">
                <h1 className="text-2xl font-bold">Daftar Izin Kompetisi</h1>
                <select
                    value={filterStatus}
                    onChange={(e) => setFilterStatus(e.target.value)}
                    className="border rounded-lg px-3 py-2"
                >
                    <option value="all">Semua Status</option>
                    <option value="pending">Menunggu</option>
                    <option value="approved">Disetujui</option>
                </select>
            </div>

            {isLoading && <p>Memuat data dari server...</p>}
            {error && <p className="text-rose-600">Error: {error}</p>}
            {!isLoading && !error && (
                <ul className="space-y-3">
                    {daftarIzin.map((izin) => (
                        <li key={izin.id} className="p-4 bg-white border rounded-xl shadow-sm">
                            <strong>{izin.nama}</strong> — {izin.kompetisi} ({izin.status})
                        </li>
                    ))}
                </ul>
            )}
        </section>
    );
}

export default HalamanDaftarIzin;
```

---

## Arsitektur Folder Proyek React Standar Industri

Sebagai Backend Developer yang terbiasa dengan struktur rapi (`controllers/`, `services/`, `repositories/`, `models/`), berikut adalah padanan struktur folder **`src/`** yang rapi pada proyek Frontend React:

```
src/
├── api/             # Tempat konfigurasi HTTP Client (fetch/axios wrapper & base URL)
│   └── izinApi.js   # Fungsi getIzin(), createIzin(), approveIzin() (mirip Repository Layer!)
├── components/      # Komponen UI yang reusable (bodoh/stateless, hanya terima props)
│   ├── Navbar.jsx
│   ├── StatusBadge.jsx
│   └── Modal.jsx
├── hooks/           # Custom Hooks (logika bisnis/state yang bisa dipakai ulang)
│   └── useAuth.js
├── pages/           # Komponen tingkat halaman penuh (mirip Controller/View untuk tiap route)
│   ├── LoginPage.jsx
│   ├── DashboardPage.jsx
│   └── PengajuanBaruPage.jsx
├── utils/           # Fungsi helper murni (format tanggal, format rupiah, validator)
│   └── formatDate.js
├── App.jsx          # Pengatur Routing halaman utama
└── main.jsx         # Entry point aplikasi React (mirip main.go / index.js)
```

> **Tips Arsitektur untuk Backend Dev:**
> Pisahkan kode pemanggilan `fetch()` ke dalam folder `src/api/` (misalnya `izinApi.js`), jangan menulis URL endpoint `http://localhost:8080/api/...` berceceran langsung di dalam banyak komponen UI. Dengan begitu, jika kontrak API backend berubah, kamu cukup mengubah satu file di `src/api/`!

---

## Latihan Praktikum Modul 5

1. Buat komponen React utuh untuk **"Portal Pengajuan Izin Kompetisi Mahasiswa"** yang dipecah menjadi minimal **4 komponen modular**:
   - `Navbar` (Header & identitas pengguna)
   - `StatCards` (Menghitung otomatis jumlah *Total*, *Pending*, dan *Approved* secara reaktif dari prop `daftarIzin`)
   - `FormPengajuan` (Controlled form untuk menambah pengajuan izin baru ke dalam state utama)
   - `TabelRiwayatIzin` (Menampilkan daftar pengajuan lengkap dengan filter status, pencarian nama/NRP, tombol *Approve/Reject*, dan tombol *Hapus*)
2. Sinkronkan state `daftarIzin` dengan `localStorage` (atau dengan backend REST API milikmu) menggunakan `useEffect`.
