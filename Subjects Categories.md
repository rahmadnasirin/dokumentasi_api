# API Dokumentasi — Subjects Categories

> **Base URL:** `/api`
> **Auth:** Bearer Token (Laravel Sanctum)
> **Content-Type:** `application/json`

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/subjects-categories` | Ambil semua kategori |
| POST | `/api/subjects-categories` | Tambah kategori baru |
| GET | `/api/subjects-categories/{kode_kategori}` | Detail satu kategori |
| PUT | `/api/subjects-categories/{kode_kategori}` | Update kategori |
| DELETE | `/api/subjects-categories/{kode_kategori}` | Hapus kategori |

---

## Format Respon

Semua endpoint menggunakan format respon yang konsisten:

```json
{
    "statusCode": 200,
    "message": "Pesan respon",
    "data": { }
}
```

---

## 1. GET /api/subjects-categories

Mengambil semua kategori mapel, diurutkan berdasarkan `seq`.

**Headers**
```
Authorization: Bearer {token}
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar kategori mapel berhasil diambil",
    "data": [
        {
            "kode_kategori": "CAT-001-250515090000",
            "name": "Tes Kemampuan Dasar",
            "slug": "tkd",
            "description": "TKD — dipakai di Mandiri SAINTEK & SOSHUM",
            "seq": 1,
            "created_at": "2025-05-15T09:00:00.000000Z",
            "updated_at": "2025-05-15T09:00:00.000000Z"
        },
        {
            "kode_kategori": "CAT-002-250515090100",
            "name": "TKA Saintek",
            "slug": "tka-saintek",
            "description": "Tes Kemampuan Akademik jalur Saintek",
            "seq": 2,
            "created_at": "2025-05-15T09:01:00.000000Z",
            "updated_at": "2025-05-15T09:01:00.000000Z"
        }
    ]
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil data kategori",
    "data": null
}
```

---

## 2. POST /api/subjects-categories

Menambahkan kategori mapel baru. `kode_kategori` di-generate otomatis oleh server dengan format `CAT-{sequential}-{timestamp}`, **frontend tidak perlu mengirim kode**.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:100 | ✅ Ya | Nama kategori |
| `slug` | string, max:100 | ❌ Opsional | Jika kosong, di-generate otomatis dari `name` |
| `description` | string, max:255 | ❌ Opsional | Deskripsi singkat |
| `seq` | integer, min:1 | ❌ Opsional | Urutan tampil, jika kosong otomatis urutan terakhir + 1 |

**Contoh Request**
```json
{
    "name": "TKA Soshum",
    "description": "Tes Kemampuan Akademik jalur Soshum"
}
```

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Kategori mapel berhasil ditambahkan",
    "data": {
        "kode_kategori": "CAT-003-250515143022",
        "name": "TKA Soshum",
        "slug": "tka-soshum",
        "description": "Tes Kemampuan Akademik jalur Soshum",
        "seq": 3,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z"
    }
}
```

**Response 422 — Validasi Gagal**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "name": [
            "The name field is required."
        ],
        "slug": [
            "The slug has already been taken."
        ]
    }
}
```

**Response 500 — Gagal Ditambahkan**
```json
{
    "statusCode": 500,
    "message": "Kategori mapel gagal ditambahkan",
    "data": null
}
```

> **Catatan generate kode:**
> Format `CAT-003-250515143022` terdiri dari:
> - `CAT` = prefix tabel
> - `003` = nomor sequential dari kode terakhir di database (increment +1)
> - `250515143022` = timestamp saat data dibuat (format `ymdhis`)

---

## 3. GET /api/subjects-categories/{kode_kategori}

Mengambil detail satu kategori beserta daftar mata pelajaran aktif yang terikat.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_kategori` | string | Kode kategori, contoh: `CAT-001-250515090000` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail kategori berhasil diambil",
    "data": {
        "kode_kategori": "CAT-001-250515090000",
        "name": "Tes Kemampuan Dasar",
        "slug": "tkd",
        "description": "TKD — dipakai di Mandiri SAINTEK & SOSHUM",
        "seq": 1,
        "created_at": "2025-05-15T09:00:00.000000Z",
        "updated_at": "2025-05-15T09:00:00.000000Z",
        "subjects": [
            {
                "kode_subject": "SUB-001-250515090100",
                "kode_kategori": "CAT-001-250515090000",
                "name": "Matematika Dasar",
                "slug": "matematika-dasar",
                "icon": "calculator",
                "color": "#3B82F6",
                "is_active": true
            },
            {
                "kode_subject": "SUB-002-250515090200",
                "kode_kategori": "CAT-001-250515090000",
                "name": "Bahasa Indonesia (TKD)",
                "slug": "bahasa-indonesia-tkd",
                "icon": "book",
                "color": "#EF4444",
                "is_active": true
            }
        ]
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Kategori tidak ditemukan",
    "data": null
}
```

---

## 4. PUT /api/subjects-categories/{kode_kategori}

Memperbarui data kategori. Semua field bersifat opsional (`sometimes`) — hanya kirim field yang ingin diubah. `kode_kategori` **tidak bisa diubah** setelah kategori dibuat.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_kategori` | string | Kode kategori yang ingin diupdate |

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:100 | ❌ Opsional | Jika diisi, slug akan di-regenerate otomatis |
| `slug` | string, max:100, unique | ❌ Opsional | Jika kosong saat `name` diubah, slug otomatis di-regenerate |
| `description` | string, max:255 | ❌ Opsional | |
| `seq` | integer, min:1 | ❌ Opsional | Urutan tampil |

**Contoh Request** — hanya update `name` dan `seq`
```json
{
    "name": "Tes Kemampuan Dasar (TKD)",
    "seq": 1
}
```

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Kategori mapel berhasil diperbarui",
    "data": {
        "kode_kategori": "CAT-001-250515090000",
        "name": "Tes Kemampuan Dasar (TKD)",
        "slug": "tes-kemampuan-dasar-tkd",
        "description": "TKD — dipakai di Mandiri SAINTEK & SOSHUM",
        "seq": 1,
        "created_at": "2025-05-15T09:00:00.000000Z",
        "updated_at": "2025-05-15T15:00:00.000000Z"
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Kategori tidak ditemukan",
    "data": null
}
```

**Response 422 — Validasi Gagal**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "slug": [
            "The slug has already been taken."
        ]
    }
}
```

---

## 5. DELETE /api/subjects-categories/{kode_kategori}

Menghapus kategori. **Tidak bisa dihapus** jika masih ada mata pelajaran yang terikat ke kategori ini.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_kategori` | string | Kode kategori yang ingin dihapus |

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Kategori mapel berhasil dihapus",
    "data": null
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Kategori tidak ditemukan",
    "data": null
}
```

**Response 409 — Konflik (Masih Ada Mapel)**
```json
{
    "statusCode": 409,
    "message": "Kategori tidak bisa dihapus karena masih memiliki 3 mata pelajaran",
    "data": null
}
```

**Response 500 — Gagal Dihapus**
```json
{
    "statusCode": 500,
    "message": "Kategori mapel gagal dihapus",
    "data": null
}
```

---

## Ringkasan Status Code

| Status Code | Keterangan |
|-------------|------------|
| `200` | Request berhasil |
| `201` | Data berhasil dibuat |
| `401` | Tidak terautentikasi (token tidak ada / tidak valid) |
| `403` | Tidak punya akses / permission |
| `404` | Data tidak ditemukan |
| `409` | Konflik — tidak bisa diproses karena ada ketergantungan data |
| `422` | Validasi gagal |
| `500` | Server error |
