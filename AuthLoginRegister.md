# API Dokumentasi — Autentikasi (Auth)

> **Base URL:** `/api`
> **Auth:** Bearer Token (JWT)
> **Content-Type:** `application/json`

---

## Tentang Modul Ini

Modul ini menangani autentikasi user menggunakan **JWT (JSON Web Token)** via `tymon/jwt-auth`. Mendukung dua metode login:

- **Local** — register dan login dengan email + password
- **Google OAuth** — login via akun Google (frontend handle OAuth, kirim `access_token` ke API)

**Package yang digunakan:**
- `tymon/jwt-auth ^2.3` — JWT token
- `laravel/socialite ^5.27` — Google OAuth
- `spatie/laravel-permission ^6.0` — Role & Permission

---

## Daftar Endpoint

### Public (tidak butuh token)

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| POST | `/api/auth/register` | Register akun baru |
| POST | `/api/auth/login` | Login dengan email & password |
| POST | `/api/auth/google/callback` | Login / register via Google OAuth |
| POST | `/api/auth/forgot-password` | Kirim email link reset password |
| POST | `/api/auth/reset-password` | Submit password baru |

### Protected (butuh token)

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/auth/me` | Data user yang sedang login |
| POST | `/api/auth/refresh` | Perpanjang token |
| POST | `/api/auth/logout` | Logout & invalidate token |

---

## Cara Pakai Token

Semua endpoint protected wajib menyertakan token di header:

```
Authorization: Bearer {token}
```

**Di Postman:**
- Tab **Authorization** → Type: `Bearer Token` → isi token

**Di Vue (axios):**
```javascript
axios.defaults.headers.common['Authorization'] = `Bearer ${token}`
```

---

## Token & Expiry

| | Nilai |
|--|--|
| Token TTL | 60 menit (default) |
| `expires_in` di response | 3600 detik |
| Refresh TTL | 2 minggu |

> Gunakan `POST /api/auth/refresh` sebelum token expired agar sesi tetap aktif.
> Sangat dianjurkan untuk fitur tryout agar sesi ujian tidak terputus.

---

## Role yang Tersedia

| Role | Keterangan |
|------|------------|
| `admin` | Akses penuh semua fitur |
| `staff` | Akses terbatas, hanya `view_dashboard` |
| `user` | Peserta tryout, hanya `view_dashboard` |

> User yang register via endpoint `/register` atau Google OAuth otomatis mendapat role `user`.

---

## Format Respon

```json
{
    "statusCode": 200,
    "message": "Pesan respon",
    "data": {}
}
```

---

## 1. POST /api/auth/register

Register akun baru. `kode_user` di-generate otomatis oleh server dengan format `USR-{sequential}-{timestamp}`. Role `user` otomatis diberikan.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:255 | ✅ | Nama lengkap |
| `email` | string, email | ✅ | Harus unik |
| `password` | string, min:8 | ✅ | |
| `password_confirmation` | string | ✅ | Harus sama dengan `password` |
| `date_of_birth` | string | ✅ | Tanggal lahir |
| `phone` | string, max:20 | ✅ | Nomor HP |
| `gender` | string | ✅ | `Laki-laki` atau `Perempuan` |
| `name_school` | string, max:255 | ✅ | Nama sekolah/instansi |
| `address` | string, max:255 | ❌ | Alamat (opsional) |

**Contoh Request**
```json
{
    "name": "Budi Santoso",
    "email": "budi@email.com",
    "password": "password123",
    "password_confirmation": "password123",
    "date_of_birth": "2000-05-15",
    "phone": "081234567890",
    "gender": "Laki-laki",
    "name_school": "SMA Negeri 1 Jakarta",
    "address": "Jl. Merdeka No. 1"
}
```

**Response 201 — Berhasil**
```json
{
    "statusCode": 201,
    "message": "Registrasi berhasil",
    "data": {
        "user": {
            "kode_user": "USR-001-260530143022",
            "name": "Budi Santoso",
            "email": "budi@email.com",
            "date_of_birth": "2000-05-15",
            "phone": "081234567890",
            "gender": "Laki-laki",
            "name_school": "SMA Negeri 1 Jakarta",
            "address": "Jl. Merdeka No. 1",
            "is_active": true,
            "foto_profile": null,
            "google_id": null,
            "auth_provider": "local",
            "created_at": "2026-05-30T14:30:22.000000Z",
            "updated_at": "2026-05-30T14:30:22.000000Z",
            "roles": [
                {
                    "id": 3,
                    "name": "user",
                    "guard_name": "api"
                }
            ]
        },
        "token": "eyJ0eXAiOiJKV1Q...",
        "token_type": "bearer",
        "expires_in": 3600
    }
}
```

**Response 422 — Validasi Gagal**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "email": ["The email has already been taken."],
        "password": ["The password confirmation does not match."]
    }
}
```

---

## 2. POST /api/auth/login

Login dengan email dan password. Hanya untuk akun dengan `auth_provider: local`.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `email` | string, email | ✅ | |
| `password` | string | ✅ | |

**Contoh Request**
```json
{
    "email": "admin@admin.com",
    "password": "admin"
}
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Login berhasil",
    "data": {
        "user": {
            "kode_user": "admin-001",
            "name": "Admin Utama",
            "email": "admin@admin.com",
            "date_of_birth": "1990-01-01",
            "phone": "081234567890",
            "gender": "Laki-laki",
            "name_school": "-",
            "address": null,
            "is_active": true,
            "foto_profile": null,
            "google_id": null,
            "auth_provider": "local",
            "created_at": "2026-05-29T19:06:28.000000Z",
            "updated_at": "2026-05-29T19:06:28.000000Z",
            "roles": [
                {
                    "id": 1,
                    "name": "admin",
                    "guard_name": "api"
                }
            ]
        },
        "token": "eyJ0eXAiOiJKV1Q...",
        "token_type": "bearer",
        "expires_in": 3600
    }
}
```

**Response 404 — Email Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Email tidak ditemukan",
    "data": null
}
```

**Response 422 — Password Salah**
```json
{
    "statusCode": 422,
    "message": "Login gagal",
    "errors": {
        "password": ["Password salah"]
    }
}
```

**Response 422 — Akun Google**
```json
{
    "statusCode": 422,
    "message": "Login gagal",
    "errors": {
        "email": ["Akun ini terdaftar via google. Gunakan metode login tersebut."]
    }
}
```

**Response 403 — Akun Nonaktif**
```json
{
    "statusCode": 403,
    "message": "Akun Anda dinonaktifkan. Hubungi admin.",
    "data": null
}
```

---

## 3. POST /api/auth/google/callback

Login atau register otomatis via Google OAuth. Frontend harus mendapatkan `access_token` dari Google terlebih dahulu.

**Flow di Frontend (Vue):**
```
1. User klik "Login dengan Google"
2. Vue buka Google OAuth popup/redirect
3. Google return access_token ke Vue
4. Vue kirim access_token ke endpoint ini
5. Laravel verifikasi ke Google → return JWT
```

**Test manual via browser (tanpa Vue):**
```
Buka URL ini di browser:
https://accounts.google.com/o/oauth2/auth
  ?client_id={GOOGLE_CLIENT_ID}
  &redirect_uri={REDIRECT_URI}
  &response_type=token
  &scope=email profile

Setelah redirect, copy access_token dari URL
→ kirim ke endpoint ini via Postman
```

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `access_token` | string | ✅ | Access token dari Google OAuth |

**Contoh Request**
```json
{
    "access_token": "ya29.a0ARrdaM..."
}
```

**Response 200 — Login Berhasil (user sudah ada)**
```json
{
    "statusCode": 200,
    "message": "Login Google berhasil",
    "data": {
        "user": {
            "kode_user": "USR-001-260530143022",
            "name": "Budi Santoso",
            "email": "budi@gmail.com",
            "auth_provider": "google",
            "foto_profile": "https://lh3.googleusercontent.com/...",
            "roles": [{ "name": "user" }]
        },
        "token": "eyJ0eXAiOiJKV1Q...",
        "token_type": "bearer",
        "expires_in": 3600
    }
}
```

> **Catatan user baru via Google** — field `date_of_birth`, `phone`, `gender`, `name_school`
> akan kosong karena Google tidak menyediakan data tersebut.
> Arahkan user ke halaman **lengkapi profil** setelah login pertama kali.

**Response 422 — Token Google Tidak Valid**
```json
{
    "statusCode": 422,
    "message": "Login Google gagal",
    "errors": {
        "access_token": ["Token Google tidak valid atau sudah expired"]
    }
}
```

**Response 422 — Email Sudah Terdaftar via Local**
```json
{
    "statusCode": 422,
    "message": "Login Google gagal",
    "errors": {
        "email": ["Email ini sudah terdaftar dengan password. Silakan login dengan password lalu hubungkan akun Google di pengaturan profil."]
    }
}
```

---

## 4. GET /api/auth/me

Ambil data user yang sedang login beserta role dan permission-nya.

**Headers**
```
Authorization: Bearer {token}
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Data user berhasil diambil",
    "data": {
        "kode_user": "admin-001",
        "name": "Admin Utama",
        "email": "admin@admin.com",
        "date_of_birth": "1990-01-01",
        "phone": "081234567890",
        "gender": "Laki-laki",
        "name_school": "-",
        "address": null,
        "is_active": true,
        "foto_profile": null,
        "google_id": null,
        "auth_provider": "local",
        "created_at": "2026-05-29T19:06:28.000000Z",
        "updated_at": "2026-05-29T19:06:28.000000Z",
        "roles": [
            {
                "id": 1,
                "name": "admin",
                "guard_name": "api",
                "permissions": [
                    {
                        "id": 1,
                        "name": "view_dashboard",
                        "guard_name": "api"
                    }
                ]
            }
        ]
    }
}
```

**Response 401 — Token Tidak Valid**
```json
{
    "statusCode": 401,
    "message": "Unauthenticated.",
    "data": null
}
```

---

## 5. POST /api/auth/refresh

Perpanjang token yang akan expired tanpa harus login ulang. Dianjurkan dipanggil secara otomatis oleh frontend sebelum token expired (atau saat menerima response `401`).

**Headers**
```
Authorization: Bearer {token}
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Token berhasil diperbarui",
    "data": {
        "user": { "..." },
        "token": "eyJ0eXAiOiJKV1Q...",
        "token_type": "bearer",
        "expires_in": 3600
    }
}
```

**Response 401 — Token Tidak Bisa Direfresh**
```json
{
    "statusCode": 401,
    "message": "Token tidak valid atau sudah expired",
    "data": null
}
```

> Jika refresh gagal, arahkan user ke halaman login.

---

## 6. POST /api/auth/logout

Logout dan invalidate token. Token yang sudah di-logout tidak bisa dipakai lagi.

**Headers**
```
Authorization: Bearer {token}
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Logout berhasil",
    "data": null
}
```

---

## 7. POST /api/auth/forgot-password

Kirim email berisi link reset password. Link akan mengarah ke halaman frontend Vue.

> Hanya untuk akun dengan `auth_provider: local`.
> Akun Google tidak perlu reset password.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `email` | string, email | ✅ | Harus terdaftar di sistem |

**Contoh Request**
```json
{
    "email": "budi@email.com"
}
```

**Link di email akan mengarah ke:**
```
{FRONTEND_URL}/reset-password?token={token}&email={email}
contoh:
https://nama-app.vercel.app/reset-password?token=abc123&email=budi%40email.com
```

**Response 200 — Email Terkirim**
```json
{
    "statusCode": 200,
    "message": "Link reset password telah dikirim ke email Anda",
    "data": null
}
```

**Response 422 — Email Tidak Ditemukan**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "email": ["The selected email is invalid."]
    }
}
```

**Response 422 — Akun Google**
```json
{
    "statusCode": 422,
    "message": "Gagal",
    "errors": {
        "email": ["Akun ini terdaftar via google. Tidak perlu reset password."]
    }
}
```

---

## 8. POST /api/auth/reset-password

Submit password baru menggunakan token dari email. Token hanya bisa dipakai sekali dan expired dalam 60 menit.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `token` | string | ✅ | Token dari URL di email |
| `email` | string, email | ✅ | Email yang meminta reset |
| `password` | string, min:8 | ✅ | Password baru |
| `password_confirmation` | string | ✅ | Harus sama dengan `password` |

**Contoh Request**
```json
{
    "token": "abc123xyz...",
    "email": "budi@email.com",
    "password": "newpassword123",
    "password_confirmation": "newpassword123"
}
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Password berhasil direset. Silakan login.",
    "data": null
}
```

**Response 422 — Token Tidak Valid**
```json
{
    "statusCode": 422,
    "message": "Reset password gagal",
    "errors": {
        "token": ["Token tidak valid atau sudah expired"]
    }
}
```

---

## Implementasi Auto Refresh di Vue

Tambahkan axios interceptor agar token diperbarui otomatis saat expired:

```javascript
// src/plugins/axios.js
import axios from 'axios'
import router from '@/router'

const api = axios.create({
    baseURL: import.meta.env.VITE_API_URL,
})

// Request interceptor — selalu sisipkan token
api.interceptors.request.use(config => {
    const token = localStorage.getItem('token')
    if (token) {
        config.headers.Authorization = `Bearer ${token}`
    }
    return config
})

// Response interceptor — auto refresh saat 401
api.interceptors.response.use(
    response => response,
    async error => {
        const originalRequest = error.config

        if (error.response?.status === 401 && !originalRequest._retry) {
            originalRequest._retry = true

            try {
                const token = localStorage.getItem('token')
                const res = await axios.post(
                    `${import.meta.env.VITE_API_URL}/auth/refresh`,
                    {},
                    { headers: { Authorization: `Bearer ${token}` } }
                )

                const newToken = res.data.data.token
                localStorage.setItem('token', newToken)
                originalRequest.headers.Authorization = `Bearer ${newToken}`

                return api(originalRequest)
            } catch {
                localStorage.removeItem('token')
                router.push('/login')
            }
        }

        return Promise.reject(error)
    }
)

export default api
```

---

## Ringkasan Status Code

| Code | Keterangan |
|------|------------|
| `200` | Berhasil |
| `201` | Register berhasil |
| `401` | Token tidak valid / expired |
| `403` | Akun dinonaktifkan |
| `404` | Email tidak ditemukan |
| `422` | Validasi gagal |
| `500` | Server error |
