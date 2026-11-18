## Daftar Isi

- [Arsitektur Komunikasi Client-Server](#arsitektur-komunikasi-client-server)
  - [Mengapa Semua Operasi Jaringan di Browser Wajib Asynchronous?](#mengapa-semua-operasi-jaringan-di-browser-wajib-asynchronous)
  - [Memahami `Promise` dan Sintaks `async / await`](#memahami-promise-dan-sintaks-async--await)
- [Menggunakan `fetch()` API](#menggunakan-fetch-api)
  - [1. Melakukan Request `GET` dan Jebakan Error `fetch()`](#1-melakukan-request-get-dan-jebakan-error-fetch)
  - [2. Melakukan Request `POST` dengan Payload JSON](#2-melakukan-request-post-dengan-payload-json)
  - [3. Melakukan Upload File (`multipart/form-data`) dengan `FormData`](#3-melakukan-upload-file-multipartform-data-dengan-formdata)
- [4 State Wajib pada UI Asynchronous](#4-state-wajib-pada-ui-asynchronous)
  - [Pola Implementasi Loading, Error, Empty, dan Success State](#pola-implementasi-loading-error-empty-dan-success-state)
- [Memahami CORS (Cross-Origin Resource Sharing) secara Tuntas](#memahami-cors-cross-origin-resource-sharing-secara-tuntas)
  - [Mengapa di Postman Lancar, tapi di Browser Error CORS?](#mengapa-di-postman-lancar-tapi-di-browser-error-cors)
  - [Apa itu *Preflight Request* (`OPTIONS`)?](#apa-itu-preflight-request-options)
  - [Cara Memperbaiki Error CORS](#cara-memperbaiki-error-cors)
- [Autentikasi di Frontend: JWT Bearer Token vs HttpOnly Cookie](#autentikasi-di-frontend-jwt-bearer-token-vs-httponly-cookie)
  - [Pendekatan 1: Bearer Token di Header `Authorization`](#pendekatan-1-bearer-token-di-header-authorization)
  - [Pendekatan 2: Session / JWT di dalam `HttpOnly Cookie`](#pendekatan-2-session--jwt-di-dalam-httponly-cookie)
- [Latihan Praktikum Modul 3](#latihan-praktikum-modul-3)

# Asynchronous Frontend & Integrasi REST API

Sebagai seorang **Backend Developer**, ini adalah modul yang menghubungkan API yang biasa kamu bangun (menggunakan Go, Express, Laravel, atau FastAPI) dengan antarmuka pengguna di browser.

---

## Arsitektur Komunikasi Client-Server

### Mengapa Semua Operasi Jaringan di Browser Wajib Asynchronous?

JavaScript di dalam browser berjalan pada **Single Main Thread** yang sama dengan mesin perender UI (*UI Rendering Thread*).
Artinya, ketika JavaScript sedang sibuk mengeksekusi kode secara sinkron (*blocking*), browser **tidak bisa menggambar ulang layar, tidak bisa merespons klik tombol, dan animasi akan membeku (*freeze*)**.

Jika sebuah request HTTP ke server membutuhkan waktu 500 milidetik dan dilakukan secara *blocking*, maka seluruh halaman web akan macet selama setengah detik. Oleh karena itu, browser menyerahkan tugas jaringan ke *Browser Networking Thread* di latar belakang dan menggunakan mekanisme **`Promise`** di JavaScript.

### Memahami `Promise` dan Sintaks `async / await`

Sebuah `Promise` adalah objek yang merepresentasikan hasil dari operasi asinkron yang memiliki 3 kemungkinan status:
1. **`pending`**: Request masih dalam perjalanan menuju/dari server.
2. **`fulfilled` (resolved)**: Operasi selesai dengan sukses.
3. **`rejected`**: Operasi gagal (misalnya koneksi terputus atau *timeout*).

Menggunakan sintaks modern **`async / await`**, kode asinkron dapat ditulis seolah-olah berjalan berurutan dari atas ke bawah seperti kode sinkron biasa, dengan penanganan error menggunakan blok `try { ... } catch (err) { ... } finally { ... }`.

---

## Menggunakan `fetch()` API

Browser modern sudah dilengkapi fungsi bawaan **`fetch(url, options)`** untuk melakukan HTTP Request tanpa perlu menginstal library tambahan.

### 1. Melakukan Request `GET` dan Jebakan Error `fetch()`

> **PENTING — Jebakan Utama `fetch()` yang Wajib Kamu Tahu:**
> Berbeda dengan library seperti Axios, **`fetch()` TIDAK AKAN masuk ke blok `catch` meskipun server mengembalikan status `400 Bad Request`, `401 Unauthorized`, `404 Not Found`, atau `500 Internal Server Error`!**
> `fetch()` hanya melempar error ke `catch` jika terjadi **kegagalan jaringan murni** (misal internet mati, DNS gagal, atau diblokir CORS).
> Oleh karena itu, kamu **WAJIB** selalu memeriksa properti **`response.ok`** (yang bernilai `true` hanya jika status HTTP berada di rentang `200 - 299`)!

Perhatikan pola standar yang benar untuk melakukan `GET` request:

```javascript
const API_BASE_URL = "http://localhost:8080/api/v1";

async function ambilDaftarIzin() {
    try {
        // 1. Tunggu response HTTP Header dari server
        const response = await fetch(`${API_BASE_URL}/izin`, {
            method: "GET",
            headers: {
                "Accept": "application/json"
            }
        });

        // 2. Parse body JSON dari stream response
        const result = await response.json();

        // 3. WAJIB: Cek apakah HTTP status code adalah 2xx (response.ok)
        if (!response.ok) {
            // Lempar pesan error dari JSON backend kamu (misal: { "message": "Token expired" })
            throw new Error(result.message || `HTTP Error ${response.status}`);
        }

        console.log("Data dari backend:", result.data);
        return result.data;
    } catch (error) {
        console.error("Gagal mengambil daftar izin:", error.message);
        throw error;
    }
}
```

### 2. Melakukan Request `POST` dengan Payload JSON

Ketika mengirim data JSON ke endpoint backend, ada **dua syarat mutlak**:
1. Menambahkan header **`"Content-Type": "application/json"`** (agar body parser di backend seperti `express.json()` atau `c.ShouldBindJSON()` di Gin tahu cara mem-parsing body).
2. Mengubah objek JavaScript menjadi string JSON menggunakan **`JSON.stringify(payload)`**.

```javascript
async function kirimPengajuanIzin(payloadObjek) {
    const response = await fetch(`${API_BASE_URL}/izin`, {
        method: "POST",
        headers: {
            "Content-Type": "application/json",
            "Accept": "application/json"
        },
        body: JSON.stringify(payloadObjek)
    });

    const result = await response.json();

    if (!response.ok) {
        throw new Error(result.message || "Gagal menyimpan pengajuan izin");
    }

    return result;
}
```

### 3. Melakukan Upload File (`multipart/form-data`) dengan `FormData`

Bagaimana jika form kamu menyertakan upload file surat tugas (`<input type="file" name="berkas_bukti">`)?
Kita tidak bisa menggunakan JSON biasa, melainkan mengirim objek **`FormData`** langsung sebagai `body`:

```javascript
async function kirimIzinDenganFile(formElement) {
    // FormData otomatis mengemas semua input teks DAN file binary sekaligus!
    const formData = new FormData(formElement);

    const response = await fetch(`${API_BASE_URL}/izin/upload`, {
        method: "POST",
        // ⚠️ PERINGATAN PENTING:
        // Saat mengirim FormData, JANGAN tulis header "Content-Type": "multipart/form-data" secara manual!
        // Biarkan browser yang mengisi header Content-Type secara otomatis agar string 'boundary=---WebKitFormBoundary...' ikut disertakan!
        body: formData
    });

    const result = await response.json();
    if (!response.ok) {
        throw new Error(result.message || "Upload gagal");
    }
    return result;
}
```

---

## 4 State Wajib pada UI Asynchronous

Saat bekerja di backend, sebuah endpoint hanya peduli pada Input dan Output. Namun di Frontend, karena ada jeda waktu jaringan, setiap komponen yang mengambil data dari API **wajib menangani 4 keadaan visual (UI States)**:

```
[Initial / Trigger Fetch]
           │
           ▼
   ┌───────────────┐
   │ 1. LOADING    │  ──► Tampilkan spinner / skeleton & disable tombol submit
   └───────┬───────┘
           │
     ┌─────┴─────┐
     ▼           ▼
(Gagal / 4xx/5xx) (Sukses / 200 OK)
     │           │
     │           ├─────────────────────────┐
     ▼           ▼ (data.length === 0)     ▼ (data.length > 0)
┌─────────┐ ┌─────────┐               ┌─────────┐
│2. ERROR │ │3. EMPTY │               │4.SUCCESS│
└─────────┘ └─────────┘               └─────────┘
Tampilkan   Tampilkan pesan           Render tabel /
alert merah "Belum ada data izin"     daftar kartu data
+ tombol    + ajakan buat baru
"Coba Lagi"
```

### Pola Implementasi Loading, Error, Empty, dan Success State

```javascript
const statusBox = document.querySelector("#status-box");
const tbody = document.querySelector("#tabel-izin tbody");
const btnRefresh = document.querySelector("#btn-refresh");

async function muatDataKeTabel() {
    // 1. Masuk ke LOADING STATE
    btnRefresh.disabled = true;
    btnRefresh.textContent = "Memuat...";
    statusBox.textContent = "Sedang mengambil data dari server...";
    statusBox.className = "status-loading";
    tbody.innerHTML = "";

    try {
        const daftarIzin = await ambilDaftarIzin();

        // 2. Cek EMPTY STATE
        if (!daftarIzin || daftarIzin.length === 0) {
            statusBox.textContent = "Belum ada pengajuan izin yang tercatat.";
            statusBox.className = "status-empty";
            return;
        }

        // 3. Masuk ke SUCCESS STATE
        statusBox.className = "hidden";
        daftarIzin.forEach((item) => {
            // Render setiap baris ke tbody
            renderBarisIzin(item);
        });

    } catch (error) {
        // 4. Masuk ke ERROR STATE
        statusBox.textContent = `Terjadi kesalahan: ${error.message}`;
        statusBox.className = "status-error";
    } finally {
        // Blok finally SELALU dijalankan baik sukses maupun gagal
        btnRefresh.disabled = false;
        btnRefresh.textContent = "Muat Ulang Data";
    }
}
```

---

## Memahami CORS (Cross-Origin Resource Sharing) secara Tuntas

Hampir setiap Backend Developer yang baru membuat Frontend pasti pernah mengalami frustrasi ini:
> *"Endpoint API saya di `http://localhost:8080/api/izin` sudah 100% jalan lancar waktu dites pakai Postman dan `curl`. Tapi waktu dipanggil pakai `fetch()` dari frontend saya di `http://localhost:5500`, malah gagal dan di Console muncul error merah: `Access to fetch at 'http://localhost:8080/api/izin' from origin 'http://localhost:5500' has been blocked by CORS policy`!"*

Mari kita pahami mengapa itu terjadi agar kamu tidak pernah bingung lagi.

### Mengapa di Postman Lancar, tapi di Browser Error CORS?

**CORS bukanlah error pada kode frontend kamu, melainkan mekanisme perlindungan keamanan bawaan Web Browser (*Same-Origin Policy*)**.
- Sebuah **Origin** terdiri dari 3 komponen: **`Protokol + Host/Domain + Port`**.
- Jika frontend berjalan di `http://localhost:5500` dan backend berjalan di `http://localhost:8080`, maka karena **Port-nya berbeda (`5500` vs `8080`)**, browser menganggap keduanya adalah **dua Origin yang berbeda (*Cross-Origin*)**!
- Mengapa browser memblokirnya sedangkan Postman tidak? Karena browser menyimpan *Session Cookies* dan kredensial pengguna. Tanpa aturan CORS, jika kamu sedang login di `bank-kamu.com` lalu membuka web jebakan `web-jahat.com`, script di `web-jahat.com` bisa diam-diam melakukan `fetch("https://bank-kamu.com/api/transfer")` dari browsermu! Postman tidak memiliki konteks sesi browser pengguna sehingga tidak menerapkan *Same-Origin Policy*.

### Apa itu *Preflight Request* (`OPTIONS`)?

Ketika frontend kamu mengirim request `POST`/`PUT`/`DELETE`, atau mengirim header `Content-Type: application/json` maupun `Authorization: Bearer ...` ke *Origin* yang berbeda, browser secara otomatis akan mengirim paket penyelidik terlebih dahulu dengan HTTP Method **`OPTIONS`** (*Preflight Request*):

```
Browser (localhost:5500)                        Backend Server (localhost:8080)
   │                                                          │
   ├─── 1. OPTIONS /api/izin ────────────────────────────────►│
   │       Origin: http://localhost:5500                      │
   │       Access-Control-Request-Method: POST                │
   │       Access-Control-Request-Headers: content-type       │
   │                                                          │
   │◄── 2. HTTP 204 No Content ───────────────────────────────┤
   │       Access-Control-Allow-Origin: http://localhost:5500 │
   │       Access-Control-Allow-Methods: GET, POST, PUT, ...  │
   │       Access-Control-Allow-Headers: Content-Type, Auth.. │
   │                                                          │
   ├─── 3. POST /api/izin (Request Asli dikirim!) ───────────►│
```

Jika server backend kamu belum dikonfigurasi untuk membalas request `OPTIONS` dengan header `Access-Control-Allow-Origin`, maka browser akan membatalkan pengiriman request asli!

### Cara Memperbaiki Error CORS

Ingat aturan emas ini: **CORS dikonfigurasi di kode Server Backend (atau lewat Dev Proxy), BUKAN di dalam kode `fetch()` Frontend!**
- **Di Backend (Go Gin / Fiber, Express.js, Laravel, FastAPI)**: Aktifkan *CORS Middleware* pada server backend kamu dan daftarkan origin frontend (misal `http://localhost:5500` atau `http://localhost:5173`) di dalam daftar `AllowOrigins`, serta izinkan header `Content-Type` dan `Authorization`.

---

## Autentikasi di Frontend: JWT Bearer Token vs HttpOnly Cookie

Bagaimana cara frontend menyimpan dan mengirimkan status login ke backend pada setiap request? Ada dua pola arsitektur utama:

### Pendekatan 1: Bearer Token di Header `Authorization`

Saat user berhasil login (`POST /api/login`), backend mengembalikan string JWT di dalam body JSON: `{ "token": "eyJhbGci..." }`.
1. Frontend menyimpan token tersebut (misal di `localStorage.setItem("access_token", token)`).
2. Pada setiap request ke endpoint yang terproteksi, frontend menyisipkan header `Authorization`:

```javascript
async function fetchWithAuth(endpoint, options = {}) {
    const token = localStorage.getItem("access_token");

    const headers = {
        "Content-Type": "application/json",
        ...options.headers,
    };

    if (token) {
        headers["Authorization"] = `Bearer ${token}`;
    }

    const response = await fetch(`${API_BASE_URL}${endpoint}`, {
        ...options,
        headers,
    });

    // Jika token kadaluarsa atau tidak valid (401 Unauthorized), arahkan kembali ke halaman login
    if (response.status === 401) {
        localStorage.removeItem("access_token");
        window.location.href = "/login.html";
        throw new Error("Sesi Anda telah berakhir, silakan login kembali.");
    }

    return response;
}
```

### Pendekatan 2: Session / JWT di dalam `HttpOnly Cookie`

Pada pendekatan ini, backend mengirim token melalui response header `Set-Cookie: token=...; HttpOnly; Secure; SameSite=Lax`.
- JavaScript di frontend **tidak perlu (dan tidak bisa)** membaca token tersebut lewat `document.cookie` (sehingga kebal terhadap pencurian token via XSS).
- Agar browser otomatis menyertakan cookie tersebut saat melakukan `fetch()` ke subdomain/port backend yang berbeda, cukup tambahkan opsi **`credentials: "include"`** pada `fetch()`:

```javascript
const response = await fetch(`${API_BASE_URL}/profil`, {
    method: "GET",
    credentials: "include" // Menginstruksikan browser mengirim Cookie lintas-origin
});
```
*(Catatan: Jika memakai `credentials: "include"`, header `Access-Control-Allow-Origin` di backend tidak boleh berisi wildcard `*`, melainkan harus menyebutkan origin eksplisit seperti `http://localhost:5173` ditambah `Access-Control-Allow-Credentials: true`).*

---

## Latihan Praktikum Modul 3

1. Hubungkan halaman **"Portal Pengajuan Izin Kompetisi Mahasiswa"** dengan API publik (misalnya `https://jsonplaceholder.typicode.com/users` atau backend lokal buatanmu sendiri di Go / Node.js).
2. Implementasikan fungsi `async/await` dengan `fetch()` untuk memuat daftar data saat halaman pertama kali dibuka (`DOMContentLoaded`).
3. Pastikan terdapat indikator visual ketika data masih berstatus **Loading**, pesan ramah ketika data **Kosong (Empty)**, dan kotak peringatan merah beserta tombol **"Coba Lagi"** ketika terjadi **Error** (uji dengan mematikan server backend atau memutus koneksi internet di tab **Network → Offline** pada DevTools).
4. Buka tab **Network** di Browser DevTools (`F12`), amati setiap baris request `fetch/XHR`, dan periksa bagian **Headers**, **Payload**, serta **Response** saat kamu menekan tombol Submit pada form.
