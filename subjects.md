# API Dokumentasi — Subjects (Mata Pelajaran)

> **Base URL:** `/api`
> **Auth:** Bearer Token (Laravel Sanctum)
> **Content-Type:** `application/json`

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/subjects` | Ambil semua mata pelajaran |
| POST | `/api/subjects` | Tambah mata pelajaran baru |
| GET | `/api/subjects/{kode_subject}` | Detail satu mata pelajaran |
| PUT | `/api/subjects/{kode_subject}` | Update semua field |
| DELETE | `/api/subjects/{kode_subject}` | Hapus mata pelajaran |
| PATCH | `/api/subjects/{kode_subject}/update` | Update sebagian field |

> **Catatan:** Untuk dropdown pilihan kategori di frontend, gunakan endpoint
> `GET /api/subjects-categories` yang sudah tersedia di modul Subjects Categories.

---

## Schema Database

```sql
subjects
├── kode_subject   STRING  PRIMARY KEY           -- format: SUB-001-250515090000
├── kode_kategori  STRING  NULLABLE              -- null = mapel universal (bebas tanpa jalur)
├── name           STRING  NOT NULL
├── sumber_soal    STRING  NOT NULL              -- sumber/penerbit soal
├── tahun          STRING  NOT NULL              -- tahun soal, contoh: "2024"
├── slug           STRING  UNIQUE NOT NULL
├── icon           STRING  NULLABLE              -- nama icon untuk UI, misal: "calculator"
├── color          STRING  NULLABLE              -- warna hex badge, misal: "#3B82F6"
├── is_active      BOOLEAN DEFAULT true          -- false = disembunyikan dari user
├── created_at     TIMESTAMP
└── updated_at     TIMESTAMP

FOREIGN KEY kode_kategori → categories(kode_kategori) ON DELETE SET NULL
```

> **Catatan:** Foreign key `kode_kategori` merujuk ke tabel `categories`.
> Validasi di layer controller menggunakan tabel `subjects_categories`
> — pastikan keduanya merujuk tabel yang sama di environment masing-masing.

---

## Format Respon

Semua endpoint menggunakan format respon yang konsisten:

```json
{
    "statusCode": 200,
    "message": "Pesan respon",
    "data": {}
}
```

---

## 1. GET /api/subjects

Mengambil semua mata pelajaran, diurutkan berdasarkan `name`. Mendukung filter via query parameter.

**Headers**
```
Authorization: Bearer {token}
```

**Query Parameters** *(semua opsional)*

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_kategori` | string | Filter berdasarkan kode kategori |
| `is_active` | boolean (0/1) | Filter berdasarkan status aktif |
| `sumber_soal` | string | Filter berdasarkan sumber soal |
| `tahun` | string | Filter berdasarkan tahun soal |

**Contoh Request**
```
GET /api/subjects
GET /api/subjects?kode_kategori=CAT-001-250515090000
GET /api/subjects?is_active=1
GET /api/subjects?sumber_soal=Kemendikbud
GET /api/subjects?tahun=2024
GET /api/subjects?kode_kategori=CAT-001-250515090000&is_active=1&tahun=2024
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar mata pelajaran berhasil diambil",
    "data": [
        {
            "kode_subject": "SUB-001-250515090000",
            "kode_kategori": null,
            "name": "Matematika SMA",
            "sumber_soal": "Kemendikbud",
            "tahun": "2024",
            "slug": "matematika-sma",
            "icon": "calculator",
            "color": "#3B82F6",
            "is_active": true,
            "created_at": "2025-05-15T09:00:00.000000Z",
            "updated_at": "2025-05-15T09:00:00.000000Z",
            "category": null
        },
        {
            "kode_subject": "SUB-005-250515090100",
            "kode_kategori": "CAT-001-250515090000",
            "name": "Matematika Dasar",
            "sumber_soal": "LTMPT",
            "tahun": "2023",
            "slug": "matematika-dasar",
            "icon": "calculator",
            "color": "#3B82F6",
            "is_active": true,
            "created_at": "2025-05-15T09:01:00.000000Z",
            "updated_at": "2025-05-15T09:01:00.000000Z",
            "category": {
                "kode_kategori": "CAT-001-250515090000",
                "name": "Tes Kemampuan Dasar",
                "slug": "tkd",
                "description": "TKD — dipakai di Mandiri SAINTEK & SOSHUM",
                "seq": 1
            }
        }
    ]
}
```

> **Catatan `kode_kategori: null`** — mata pelajaran universal (tidak masuk kategori TKD/TKA/dll),
> bebas diakses tanpa jalur khusus. Field `category` akan bernilai `null`.

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil data mata pelajaran",
    "data": null
}
```

---

## 2. POST /api/subjects

Menambahkan mata pelajaran baru. `kode_subject` di-generate otomatis oleh server — **frontend tidak perlu mengirim kode**. Slug di-generate otomatis dari `name`, dan jika sudah ada akan diberi suffix `-1`, `-2`, dst.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:100 | ✅ Ya | Nama mata pelajaran |
| `sumber_soal` | string, max:100 | ✅ Ya | Sumber/penerbit soal |
| `tahun` | string, max:4 | ✅ Ya | Tahun soal, contoh: `2024` |
| `kode_kategori` | string \| null | ❌ Opsional | Kode kategori dari `/api/subjects-categories`. Kosongkan jika mapel universal |
| `slug` | string, max:100 | ❌ Opsional | Jika kosong, di-generate otomatis dari `name`. Harus unik jika diisi manual |
| `icon` | string, max:50 | ❌ Opsional | Nama icon untuk UI (contoh: `calculator`, `book`, `flask`) |
| `color` | string, max:7 | ❌ Opsional | Warna hex badge (contoh: `#3B82F6`) |
| `is_active` | boolean | ❌ Opsional | Default `true` jika tidak diisi |

**Contoh Request — Mapel Universal (tanpa kategori)**
```json
{
    "name": "Kimia SMA",
    "sumber_soal": "Kemendikbud",
    "tahun": "2024",
    "icon": "flask",
    "color": "#10B981"
}
```

**Contoh Request — Mapel dengan Kategori**
```json
{
    "kode_kategori": "CAT-002-250515090000",
    "name": "Fisika",
    "sumber_soal": "LTMPT",
    "tahun": "2023",
    "icon": "atom",
    "color": "#F59E0B",
    "is_active": true
}
```

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Mata pelajaran berhasil ditambahkan",
    "data": {
        "kode_subject": "SUB-006-250515143022",
        "kode_kategori": "CAT-002-250515090000",
        "name": "Fisika",
        "sumber_soal": "LTMPT",
        "tahun": "2023",
        "slug": "fisika",
        "icon": "atom",
        "color": "#F59E0B",
        "is_active": true,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z",
        "category": {
            "kode_kategori": "CAT-002-250515090000",
            "name": "TKA Saintek",
            "slug": "tka-saintek",
            "description": "Tes Kemampuan Akademik jalur Saintek",
            "seq": 2
        }
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
        "sumber_soal": [
            "The sumber soal field is required."
        ],
        "tahun": [
            "The tahun field is required."
        ],
        "kode_kategori": [
            "The selected kode kategori is invalid."
        ],
        "color": [
            "The color field must not be greater than 7 characters."
        ]
    }
}
```

**Response 500 — Gagal Ditambahkan**
```json
{
    "statusCode": 500,
    "message": "Mata pelajaran gagal ditambahkan",
    "data": null
}
```

> **Catatan generate `kode_subject`:**
> Format `SUB-006-250515143022` terdiri dari:
> - `SUB` — prefix tabel
> - `006` — nomor sequential dari kode terakhir di database (increment +1, zero-padded 3 digit)
> - `250515143022` — timestamp saat data dibuat (format `ymdhis`)
>
> **Catatan generate `slug`:**
> Jika `name = "Matematika"` dan slug `matematika` sudah ada, sistem otomatis mencoba
> `matematika-1`, `matematika-2`, dst hingga menemukan yang unik.

---

## 3. GET /api/subjects/{kode_subject}

Mengambil detail satu mata pelajaran beserta data kategorinya.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_subject` | string | Kode mata pelajaran, contoh: `SUB-001-250515090000` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail mata pelajaran berhasil diambil",
    "data": {
        "kode_subject": "SUB-005-250515090100",
        "kode_kategori": "CAT-001-250515090000",
        "name": "Matematika Dasar",
        "sumber_soal": "LTMPT",
        "tahun": "2023",
        "slug": "matematika-dasar",
        "icon": "calculator",
        "color": "#3B82F6",
        "is_active": true,
        "created_at": "2025-05-15T09:01:00.000000Z",
        "updated_at": "2025-05-15T09:01:00.000000Z",
        "category": {
            "kode_kategori": "CAT-001-250515090000",
            "name": "Tes Kemampuan Dasar",
            "slug": "tkd",
            "description": "TKD — dipakai di Mandiri SAINTEK & SOSHUM",
            "seq": 1
        }
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Mata pelajaran tidak ditemukan",
    "data": null
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil detail mata pelajaran",
    "data": null
}
```

---

## 4. PUT /api/subjects/{kode_subject}

Memperbarui data mata pelajaran. Semua field bersifat opsional (`sometimes`) — hanya kirim field yang ingin diubah. `kode_subject` **tidak bisa diubah** setelah dibuat.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_subject` | string | Kode mata pelajaran yang ingin diupdate |

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_kategori` | string \| null | ❌ Opsional | Set `null` untuk jadikan mapel universal |
| `name` | string, max:100 | ❌ Opsional | Jika diubah, slug di-regenerate otomatis |
| `sumber_soal` | string, max:100 | ❌ Opsional | Sumber/penerbit soal |
| `tahun` | string, max:4 | ❌ Opsional | Tahun soal, contoh: `2024` |
| `slug` | string, max:100 | ❌ Opsional | Jika diisi manual, harus unik (kecuali milik diri sendiri) |
| `icon` | string \| null, max:50 | ❌ Opsional | |
| `color` | string \| null, max:7 | ❌ Opsional | Format hex: `#RRGGBB` |
| `is_active` | boolean | ❌ Opsional | `true` = aktif, `false` = nonaktif |

**Contoh Request — Nonaktifkan mapel**
```json
{
    "is_active": false
}
```

**Contoh Request — Pindah kategori + ganti nama + update sumber**
```json
{
    "kode_kategori": "CAT-003-250515090000",
    "name": "Sejarah Indonesia",
    "sumber_soal": "Kemendikbud",
    "tahun": "2024"
}
```

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Mata pelajaran berhasil diperbarui",
    "data": {
        "kode_subject": "SUB-012-250515090000",
        "kode_kategori": "CAT-003-250515090000",
        "name": "Sejarah Indonesia",
        "sumber_soal": "Kemendikbud",
        "tahun": "2024",
        "slug": "sejarah-indonesia",
        "icon": "clock",
        "color": "#78716C",
        "is_active": true,
        "created_at": "2025-05-15T09:00:00.000000Z",
        "updated_at": "2025-05-15T15:00:00.000000Z",
        "category": {
            "kode_kategori": "CAT-003-250515090000",
            "name": "TKA Soshum",
            "slug": "tka-soshum",
            "description": "Tes Kemampuan Akademik jalur Soshum",
            "seq": 3
        }
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Mata pelajaran tidak ditemukan",
    "data": null
}
```

**Response 422 — Validasi Gagal**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "color": [
            "The color field must not be greater than 7 characters."
        ]
    }
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Mata pelajaran gagal diperbarui",
    "data": null
}
```

---

## 5. DELETE /api/subjects/{kode_subject}

Menghapus mata pelajaran. **Tidak bisa dihapus** jika masih digunakan di paket ujian manapun.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_subject` | string | Kode mata pelajaran yang ingin dihapus |

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Mata pelajaran berhasil dihapus",
    "data": null
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Mata pelajaran tidak ditemukan",
    "data": null
}
```

**Response 409 — Konflik (Masih Digunakan di Paket)**
```json
{
    "statusCode": 409,
    "message": "Mata pelajaran tidak bisa dihapus karena masih digunakan di 3 paket ujian",
    "data": null
}
```

**Response 500 — Gagal Dihapus**
```json
{
    "statusCode": 500,
    "message": "Mata pelajaran gagal dihapus",
    "data": null
}
```

---

## 6. PATCH /api/subjects/{kode_subject}/update

Update **sebagian field saja** (partial update). Minimal harus ada 1 field yang dikirim.

**Kapan pakai `PATCH` vs `PUT`?**

| Skenario | Gunakan |
|----------|---------|
| Nonaktifkan / aktifkan mapel | `PATCH` |
| Ganti warna atau ikon saja | `PATCH` |
| Pindah kategori saja | `PATCH` |
| Ganti sumber soal atau tahun saja | `PATCH` |
| Update semua field sekaligus | `PUT` |

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_subject` | string | Kode mata pelajaran yang ingin diupdate sebagian |

**Body Request** *(minimal 1 field)*

| Field | Tipe | Keterangan |
|-------|------|------------|
| `kode_kategori` | string \| null | `null` = jadikan mapel universal |
| `name` | string, max:100 | Jika diisi, slug di-regenerate otomatis |
| `sumber_soal` | string, max:100 | Sumber/penerbit soal |
| `tahun` | string, max:4 | Tahun soal, contoh: `2024` |
| `slug` | string, max:100 | Harus unik (kecuali milik diri sendiri) |
| `icon` | string \| null, max:50 | Nama icon |
| `color` | string \| null, max:7 | Format hex: `#RRGGBB` |
| `is_active` | boolean | `true` = aktif, `false` = nonaktif |

**Contoh Request — Nonaktifkan mapel**
```json
{
    "is_active": false
}
```

**Contoh Request — Ganti warna dan ikon saja**
```json
{
    "color": "#EF4444",
    "icon": "flask"
}
```

**Contoh Request — Update sumber soal dan tahun**
```json
{
    "sumber_soal": "SNPMB",
    "tahun": "2025"
}
```

**Contoh Request — Pindah kategori**
```json
{
    "kode_kategori": "CAT-003-250515090000"
}
```

**Response 200 — Berhasil Diperbarui Sebagian**
```json
{
    "statusCode": 200,
    "message": "Mata pelajaran berhasil diperbarui sebagian",
    "data": {
        "kode_subject": "SUB-005-250515090100",
        "kode_kategori": "CAT-001-250515090000",
        "name": "Matematika Dasar",
        "sumber_soal": "SNPMB",
        "tahun": "2025",
        "slug": "matematika-dasar",
        "icon": "calculator",
        "color": "#EF4444",
        "is_active": false,
        "created_at": "2025-05-15T09:01:00.000000Z",
        "updated_at": "2025-05-15T16:00:00.000000Z",
        "category": {
            "kode_kategori": "CAT-001-250515090000",
            "name": "Tes Kemampuan Dasar",
            "slug": "tkd",
            "description": "TKD — dipakai di Mandiri SAINTEK & SOSHUM",
            "seq": 1
        }
    }
}
```

**Response 422 — Tidak Ada Data Dikirim**
```json
{
    "statusCode": 422,
    "message": "Tidak ada data yang dikirim",
    "errors": {
        "field": ["Minimal satu field harus diisi untuk patch update"]
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Mata pelajaran tidak ditemukan",
    "data": null
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Mata pelajaran gagal diperbarui",
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
| `409` | Konflik — mata pelajaran masih digunakan di paket ujian |
| `422` | Validasi gagal |
| `500` | Server error |
