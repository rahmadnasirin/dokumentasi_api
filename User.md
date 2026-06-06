# API Dokumentasi — User Management

> **Base URL:** `/api`
> **Auth:** Bearer Token (JWT)
> **Content-Type:** `application/json`

---

## Tentang Modul Ini

Modul ini mengelola **semua akun user** — admin, staff, dan siswa (user). Admin bisa menambah, melihat, mengubah, dan menghapus akun dari semua role melalui satu endpoint dengan filter `?role=`.

User/siswa yang daftar sendiri via `POST /api/auth/register` otomatis mendapat role `user`.

**Posisi dalam alur:**
```
admin login
    ↓
POST /api/user + field role   ← tambah admin/staff/user manual
GET  /api/user?role=admin     ← lihat semua admin
GET  /api/user?role=staff     ← lihat semua staff
GET  /api/user?role=user      ← lihat semua siswa
    ↓
atau user daftar sendiri via POST /api/auth/register → otomatis role: user
```

**Tabel yang digunakan:**
- `users` — data akun
- `model_has_roles` — relasi user ke role

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/user` | Daftar semua user (bisa filter by role) |
| POST | `/api/user` | Tambah user baru (admin/staff/user) |
| GET | `/api/user/{kode_user}` | Detail user |
| PUT | `/api/user/{kode_user}` | Update data user |
| DELETE | `/api/user/{kode_user}` | Hapus user |

**Route di `routes/api.php`:**
```php
/* USER MANAGEMENT — admin only */
Route::middleware(['auth:api', 'role:admin'])->group(function () {
    Route::get('user',                [UserController::class, 'index']);
    Route::post('user',               [UserController::class, 'store']);
    Route::get('user/{kode_user}',    [UserController::class, 'show']);
    Route::put('user/{kode_user}',    [UserController::class, 'update']);
    Route::delete('user/{kode_user}', [UserController::class, 'destroy']);
});
```

---

## Schema Database

```sql
users
├── kode_user          STRING   PRIMARY KEY
│                               format: USR-ADMIN-001 / USR-STAFF-001 / USR-USR-001
├── name               STRING   NOT NULL
├── email              STRING   NOT NULL UNIQUE
├── password           STRING   NOT NULL      -- bcrypt hashed
├── date_of_birth      STRING   NOT NULL      -- default '1990-01-01' untuk admin/staff
├── phone              STRING   NULLABLE      -- default '-' jika tidak diisi
├── gender             STRING   NOT NULL      -- default 'Laki-laki' untuk admin/staff
├── name_school        STRING   NOT NULL      -- default '-' untuk admin/staff
├── address            STRING   NULLABLE
├── auth_provider      STRING   DEFAULT 'local'
├── google_id          STRING   NULLABLE
├── foto_profile       STRING   NULLABLE
├── is_active          BOOLEAN  DEFAULT true
├── email_verified_at  TIMESTAMP
├── created_at         TIMESTAMP
└── updated_at         TIMESTAMP

model_has_roles
├── role_id    → roles.id (admin=1 / user=2 / staff=3)
├── model_type → App\Models\User
└── model_id   → users.kode_user
```

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

## 1. GET /api/user

Ambil semua user. Bisa difilter by role via query param.

**Headers**
```
Authorization: Bearer {token}
```

**Query Parameters** *(opsional)*

| Parameter | Keterangan |
|-----------|------------|
| `role` | Filter by role: `admin`, `staff`, atau `user` |

**Contoh Request**
```
GET /api/user              → semua user
GET /api/user?role=admin   → hanya admin
GET /api/user?role=staff   → hanya staff
GET /api/user?role=user    → hanya siswa
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar user berhasil diambil",
    "data": [
        {
            "kode_user": "USR-ADMIN-001",
            "name": "Admin Utama",
            "email": "admin@tryout.com",
            "phone": "081234567890",
            "is_active": true,
            "created_at": "2026-06-01T10:00:00.000000Z",
            "roles": [{ "id": 1, "name": "admin", "guard_name": "api" }]
        },
        {
            "kode_user": "USR-STAFF-001",
            "name": "Budi Santoso",
            "email": "budi@tryout.com",
            "phone": "081234567890",
            "is_active": true,
            "created_at": "2026-06-01T10:00:00.000000Z",
            "roles": [{ "id": 3, "name": "staff", "guard_name": "api" }]
        }
    ]
}
```

---

## 2. POST /api/user

Tambah user baru. `kode_user` di-generate otomatis sesuai role:
- `role: admin` → `USR-ADMIN-001`
- `role: staff` → `USR-STAFF-001`
- `role: user`  → `USR-USR-001`

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:255 | ✅ | Nama lengkap |
| `email` | string, email | ✅ | Email unik, untuk login |
| `password` | string, min:8 | ✅ | Di-hash otomatis |
| `role` | string | ✅ | `admin`, `staff`, atau `user` |
| `phone` | string, max:20 | ❌ | No HP. Default `-` |
| `date_of_birth` | string | ❌ | Wajib untuk role `user` |
| `gender` | string | ❌ | `Laki-laki` atau `Perempuan` |
| `name_school` | string, max:255 | ❌ | Wajib untuk role `user` |
| `address` | string, max:255 | ❌ | Alamat |

**Contoh — tambah staff**
```json
{
    "name": "Budi Santoso",
    "email": "budi@tryout.com",
    "password": "password123",
    "role": "staff",
    "phone": "081234567890"
}
```

**Contoh — tambah siswa**
```json
{
    "name": "Andi Pratama",
    "email": "andi@email.com",
    "password": "password123",
    "role": "user",
    "phone": "082345678901",
    "date_of_birth": "2005-03-15",
    "gender": "Laki-laki",
    "name_school": "SMA Negeri 1 Malang"
}
```

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Staff berhasil ditambahkan",
    "data": {
        "kode_user": "USR-STAFF-002",
        "name": "Budi Santoso",
        "email": "budi@tryout.com",
        "phone": "081234567890",
        "is_active": true,
        "created_at": "2026-06-01T10:00:00.000000Z",
        "role": "staff"
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
        "role": ["The selected role is invalid."]
    }
}
```

---

## 3. GET /api/user/{kode_user}

Detail satu user berdasarkan `kode_user`. Bisa digunakan untuk menampilkan profil user setelah login.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_user` | Kode user, contoh: `USR-ADMIN-001`, `USR-STAFF-001`, `USR-USR-001` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail user berhasil diambil",
    "data": {
        "kode_user": "USR-USR-001",
        "name": "Andi Pratama",
        "email": "andi@email.com",
        "phone": "082345678901",
        "gender": "Laki-laki",
        "name_school": "SMA Negeri 1 Malang",
        "date_of_birth": "2005-03-15",
        "address": null,
        "foto_profile": null,
        "is_active": true,
        "created_at": "2026-06-01T10:00:00.000000Z",
        "roles": [{ "id": 2, "name": "user", "guard_name": "api" }]
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "User tidak ditemukan",
    "data": null
}
```

---

## 4. PUT /api/user/{kode_user}

Update data user. Semua field bersifat `sometimes`.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request** — semua field opsional:

| Field | Tipe | Keterangan |
|-------|------|------------|
| `name` | string, max:255 | |
| `email` | string, email | |
| `password` | string, min:8 | Di-hash otomatis |
| `phone` | string \| null | |
| `is_active` | boolean | Status aktif |
| `role` | string | `admin`, `staff`, atau `user` |
| `date_of_birth` | string \| null | |
| `gender` | string \| null | |
| `name_school` | string \| null | |
| `address` | string \| null | |

**Contoh — nonaktifkan user**
```json
{ "is_active": false }
```

**Contoh — ganti role**
```json
{ "role": "admin" }
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "User berhasil diperbarui",
    "data": { "..." }
}
```

---

## 5. DELETE /api/user/{kode_user}

Hapus user. Role di `model_has_roles` ikut terhapus otomatis.

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "User berhasil dihapus",
    "data": null
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "User tidak ditemukan",
    "data": null
}
```

---

## Ringkasan Status Code

| Code | Keterangan |
|------|------------|
| `200` | Berhasil |
| `201` | Data berhasil dibuat |
| `401` | Tidak terautentikasi |
| `403` | Tidak punya akses (bukan admin) |
| `404` | User tidak ditemukan |
| `422` | Validasi gagal |
| `500` | Server error |
