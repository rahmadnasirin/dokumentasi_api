# API Dokumentasi — Staff

> **Base URL:** `/api`
> **Auth:** Bearer Token (JWT)
> **Content-Type:** `application/json`

---

## Tentang Modul Ini

Modul ini mengelola **data staff** secara manual oleh superadmin. Staff adalah pengguna dengan role `staff` yang memiliki akses terbatas dibanding superadmin.

**Posisi dalam alur:**
```
superadmin
    ↓
POST /api/staff   ← tambah staff baru (nama, email, password, no hp)
    ↓
staff login via POST /api/auth/login
    ↓
staff bisa akses endpoint sesuai hak akses role-nya
```

**Tabel yang digunakan:**
- `users` — data akun staff
- `model_has_roles` — relasi user ke role `staff`

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/staff` | Daftar semua staff |
| POST | `/api/staff` | Tambah staff baru |
| GET | `/api/staff/{kode_user}` | Detail staff |
| PUT | `/api/staff/{kode_user}` | Update data staff |
| DELETE | `/api/staff/{kode_user}` | Hapus staff |

**Route di `routes/api.php`:**
```php
/* STAFF */
Route::get('staff',                [StaffController::class, 'index']);
Route::post('staff',               [StaffController::class, 'store']);
Route::get('staff/{kode_user}',    [StaffController::class, 'show']);
Route::put('staff/{kode_user}',    [StaffController::class, 'update']);
Route::delete('staff/{kode_user}', [StaffController::class, 'destroy']);
```

---

## Schema Database

```sql
users
├── kode_user          STRING   PRIMARY KEY   -- format: USR-STAFF-001
├── name               STRING   NOT NULL
├── email              STRING   NOT NULL UNIQUE
├── password           STRING   NOT NULL      -- bcrypt hashed
├── phone              STRING   NULLABLE      -- no hp, default '-' jika tidak diisi
├── date_of_birth      STRING   NOT NULL      -- default '1990-01-01'
├── gender             STRING   NOT NULL      -- default 'Laki-laki'
├── name_school        STRING   NOT NULL      -- default '-'
├── is_active          BOOLEAN  DEFAULT true
├── email_verified_at  TIMESTAMP
├── created_at         TIMESTAMP
└── updated_at         TIMESTAMP

model_has_roles
├── role_id    → roles.id (staff)
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

## 1. GET /api/staff

Ambil semua user dengan role `staff`.

**Headers**
```
Authorization: Bearer {token}
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar staff berhasil diambil",
    "data": [
        {
            "kode_user": "USR-STAFF-001",
            "name": "Budi Santoso",
            "email": "budi@tryout.com",
            "phone": "081234567890",
            "is_active": true,
            "created_at": "2026-06-01T10:00:00.000000Z"
        }
    ]
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil data staff",
    "data": null
}
```

---

## 2. POST /api/staff

Tambah staff baru. `kode_user` di-generate otomatis dengan format `USR-STAFF-{sequential}`. Role `staff` otomatis di-assign ke user baru.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:255 | ✅ | Nama lengkap staff |
| `email` | string, email | ✅ | Email unik, digunakan untuk login |
| `password` | string, min:8 | ✅ | Password akan di-hash otomatis |
| `phone` | string, max:20 | ❌ | Nomor HP. Default `-` jika tidak diisi |

**Contoh Request**
```json
{
    "name": "Budi Santoso",
    "email": "budi@tryout.com",
    "password": "password123",
    "phone": "081234567890"
}
```

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Staff berhasil ditambahkan",
    "data": {
        "kode_user": "USR-STAFF-001",
        "name": "Budi Santoso",
        "email": "budi@tryout.com",
        "phone": "081234567890",
        "is_active": true,
        "created_at": "2026-06-01T10:00:00.000000Z"
    }
}
```

**Response 422 — Validasi Gagal**
```json
{
    "statusCode": 422,
    "message": "The email has already been taken.",
    "errors": {
        "email": ["The email has already been taken."]
    }
}
```

**Response 500 — Gagal Ditambahkan**
```json
{
    "statusCode": 500,
    "message": "Staff gagal ditambahkan",
    "data": null
}
```

---

## 3. GET /api/staff/{kode_user}

Detail satu staff berdasarkan `kode_user`.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_user` | Kode user staff, contoh: `USR-STAFF-001` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail staff berhasil diambil",
    "data": {
        "kode_user": "USR-STAFF-001",
        "name": "Budi Santoso",
        "email": "budi@tryout.com",
        "phone": "081234567890",
        "is_active": true,
        "created_at": "2026-06-01T10:00:00.000000Z"
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Staff tidak ditemukan",
    "data": null
}
```

---

## 4. PUT /api/staff/{kode_user}

Update data staff. Semua field bersifat `sometimes` — kirim hanya yang ingin diubah.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request** — semua field opsional (`sometimes`):

| Field | Tipe | Keterangan |
|-------|------|------------|
| `name` | string, max:255 | Nama lengkap |
| `email` | string, email | Email unik |
| `password` | string, min:8 | Password baru, akan di-hash otomatis |
| `phone` | string \| null, max:20 | Nomor HP |
| `is_active` | boolean | Status aktif staff |

**Contoh — update nama dan no HP**
```json
{
    "name": "Budi Santoso (Update)",
    "phone": "089876543210"
}
```

**Contoh — nonaktifkan staff**
```json
{
    "is_active": false
}
```

**Contoh — reset password**
```json
{
    "password": "newpassword123"
}
```

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Staff berhasil diperbarui",
    "data": {
        "kode_user": "USR-STAFF-001",
        "name": "Budi Santoso (Update)",
        "email": "budi@tryout.com",
        "phone": "089876543210",
        "is_active": true,
        "created_at": "2026-06-01T10:00:00.000000Z"
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Staff tidak ditemukan",
    "data": null
}
```

**Response 500 — Gagal Diperbarui**
```json
{
    "statusCode": 500,
    "message": "Staff gagal diperbarui",
    "data": null
}
```

---

## 5. DELETE /api/staff/{kode_user}

Hapus staff. Role di tabel `model_has_roles` ikut terhapus otomatis sebelum user dihapus.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_user` | Kode user staff, contoh: `USR-STAFF-001` |

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Staff berhasil dihapus",
    "data": null
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Staff tidak ditemukan",
    "data": null
}
```

**Response 500 — Gagal Dihapus**
```json
{
    "statusCode": 500,
    "message": "Staff gagal dihapus",
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
| `403` | Tidak punya akses |
| `404` | Staff tidak ditemukan |
| `422` | Validasi gagal |
| `500` | Server error |
