# API Dokumentasi — Paket Ujian (Exam Packages)

> **Base URL:** `/api`
> **Auth:** Bearer Token (Laravel Sanctum)
> **Content-Type:** `application/json`

---

## Tentang Modul Ini

Modul ini mengelola **paket ujian** — master data yang menjadi wadah dari semua konfigurasi ujian. Satu paket bisa bertipe universal (latihan bebas), mandiri (simulasi PTN), utbk, psikotes, atau cpns.

**Posisi dalam alur:**
```
subjects          ← harus ada dulu
    ↓
exam_packages     ← dibuat di sini
    ↓
package_subjects  → docs: api-package-subjects.md
    ↓
questions         → docs: api-questions.md
```

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/feature` | Ambil semua paket |
| POST | `/api/feature` | Buat paket baru |
| GET | `/api/feature/{kode_feature}` | Detail paket + daftar package subjects |
| PUT | `/api/feature/{kode_feature}` | Update semua field |
| DELETE | `/api/feature/{kode_feature}` | Hapus paket |
| PATCH | `/api/feature/{kode_feature}/update` | Update sebagian field |

---

## Schema Database

```sql
feature
├── kode_feature      STRING   PRIMARY KEY          -- format: PKG-001-250515143022
├── name              STRING   NOT NULL
├── description       TEXT     NULLABLE
├── thumbnail         STRING   NULLABLE             -- path banner (tampil di halaman hasil)
├── type              STRING   DEFAULT 'universal'  -- universal | mandiri | psikotes | utbk | cpns
├── track             STRING   DEFAULT 'none'       -- lihat nilai yang tersedia di bawah
├── is_per_subject    BOOLEAN  DEFAULT true         -- true = per mapel (sub-sesi + jeda), false = semua sekaligus
├── layout_ujian      TINYINT  DEFAULT 1            -- layout tampilan soal di frontend (1–5)
├── hide_timer        BOOLEAN  DEFAULT false        -- true = timer disembunyikan dari user
├── price             BIGINT   DEFAULT 0            -- 0 = gratis, >0 = wajib transaksi paid
├── price_sale        BIGINT   NULLABLE             -- harga coret/diskon, null = tidak ada diskon
├── sale_start        DATETIME NULLABLE
├── sale_end          DATETIME NULLABLE
├── is_active         BOOLEAN  DEFAULT true
├── created_at        TIMESTAMP
└── updated_at        TIMESTAMP
```

**Nilai yang tersedia untuk `type`:**

| Nilai | Keterangan |
|-------|------------|
| `universal` | Latihan per mapel bebas akses |
| `mandiri` | Simulasi ujian mandiri PTN |
| `psikotes` | Psikotes & TPA |
| `utbk` | UTBK-SNBT |
| `cpns` | SKD/CPNS *(coming soon)* |

**Nilai yang tersedia untuk `track`:**

| Nilai | Keterangan |
|-------|------------|
| `none` | Universal, tidak ada jalur khusus |
| `saintek` | Mandiri jalur Saintek |
| `soshum` | Mandiri jalur Soshum |
| `psikotes` | Target 3A |
| `tpa` | Target 3B |
| `skolastik` | UTBK Tes Skolastik |
| `literasi-id` | UTBK Literasi Bahasa Indonesia |
| `literasi-en` | UTBK Literasi Bahasa Inggris |
| `penalaran-mat` | UTBK Penalaran Matematika |
| `cpns` | SKD/CPNS |

> **Penting:** Field `track` **tidak dapat diubah** setelah paket dibuat. Nilai selalu di-set ke `none` oleh server saat `store` — field ini tidak diterima dari request. Jika ke depan butuh support track lain, perlu perubahan di controller.

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

## 1. GET /api/feature

Ambil semua paket ujian, diurutkan berdasarkan `created_at` terbaru. Bisa difilter via query parameter.

**Headers**
```
Authorization: Bearer {token}
```

**Query Parameters** *(semua opsional)*

| Parameter | Nilai yang tersedia | Keterangan |
|-----------|---------------------|------------|
| `type` | `universal` `mandiri` `psikotes` `utbk` `cpns` | Filter berdasarkan jenis paket |
| `is_active` | `1` / `0` | Filter berdasarkan status aktif |

> **Catatan:** Filter `track` **tidak tersedia** di endpoint ini — controller tidak mengimplementasikannya.

**Contoh Request**
```
GET /api/feature
GET /api/feature?type=universal&is_active=1
GET /api/feature?type=mandiri
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar feature berhasil diambil",
    "data": [
        {
            "kode_feature": "PKG-001-250515143022",
            "name": "Latihan Matematika SMA",
            "description": null,
            "thumbnail": null,
            "type": "universal",
            "track": "none",
            "price": 0,
            "price_sale": null,
            "sale_start": null,
            "sale_end": null,
            "is_per_subject": true,
            "layout_ujian": 1,
            "hide_timer": false,
            "is_active": true,
            "created_at": "2025-05-15T14:30:22.000000Z",
            "updated_at": "2025-05-15T14:30:22.000000Z"
        }
    ]
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil data feature",
    "data": null
}
```

---

## 2. POST /api/feature

Buat paket ujian baru. `kode_feature` di-generate otomatis oleh server dengan format `PKG-{sequential}-{timestamp}`.

> **Catatan:** Field `track` selalu di-set ke `"none"` oleh server — tidak perlu dan tidak bisa dikirim dari frontend.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request**

| Field | Tipe | Wajib | Default | Keterangan |
|-------|------|-------|---------|------------|
| `name` | string, max:255 | ✅ | — | Nama paket ujian |
| `type` | string | ✅ | — | `universal` `mandiri` `psikotes` `utbk` `cpns` |
| `description` | string | ❌ | `null` | Deskripsi paket |
| `thumbnail` | string, max:255 | ❌ | `null` | Path gambar banner |
| `is_per_subject` | boolean | ❌ | `true` | `true` = dikerjakan per mapel (sub-sesi + jeda). `false` = semua soal sekaligus |
| `layout_ujian` | integer 1–5 | ❌ | `1` | Layout tampilan soal di frontend |
| `hide_timer` | boolean | ❌ | `false` | `true` = timer disembunyikan dari user |
| `price` | integer ≥ 0 | ❌ | `0` | Harga dalam rupiah. `0` = gratis |
| `price_sale` | integer ≥ 0 | ❌ | `null` | Harga setelah diskon. `null` = tidak ada diskon |
| `sale_start` | datetime | ❌ | `null` | Mulai periode diskon |
| `sale_end` | datetime | ❌ | `null` | Akhir periode diskon. Harus ≥ `sale_start` |
| `is_active` | boolean | ❌ | `true` | Status aktif paket |

**Contoh Request — Paket gratis**
```json
{
    "name": "Latihan Matematika SMA",
    "type": "universal",
    "is_per_subject": false,
    "price": 0
}
```

**Contoh Request — Paket berbayar dengan diskon periode**
```json
{
    "name": "TO Mandiri Saintek Part 1",
    "description": "Simulasi ujian mandiri PTN jalur saintek",
    "type": "mandiri",
    "is_per_subject": true,
    "price": 50000,
    "price_sale": 35000,
    "sale_start": "2025-05-01 00:00:00",
    "sale_end": "2025-05-31 23:59:59"
}
```

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Feature berhasil ditambahkan",
    "data": {
        "kode_feature": "PKG-001-250515143022",
        "name": "TO Mandiri Saintek Part 1",
        "description": "Simulasi ujian mandiri PTN jalur saintek",
        "thumbnail": null,
        "type": "mandiri",
        "track": "none",
        "is_per_subject": true,
        "layout_ujian": 1,
        "hide_timer": false,
        "price": 50000,
        "price_sale": 35000,
        "sale_start": "2025-05-01T00:00:00.000000Z",
        "sale_end": "2025-05-31T23:59:59.000000Z",
        "is_active": true,
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
        "type": ["The selected type is invalid."],
        "sale_end": ["The sale end field must be a date after or equal to sale start."]
    }
}
```

**Response 500 — Gagal Ditambahkan**
```json
{
    "statusCode": 500,
    "message": "Feature gagal ditambahkan",
    "data": null
}
```

---

## 3. GET /api/feature/{kode_feature}

Detail paket beserta semua slot `package_subjects` di dalamnya, diurutkan berdasarkan `seq`. Response `package_subjects` menyertakan data kategori dan daftar mapel aktif dalam kategori tersebut (diurutkan by `name`).

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket, contoh: `PKG-001-250515143022` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail feature berhasil diambil",
    "data": {
        "kode_feature": "PKG-001-250515143022",
        "name": "TO Mandiri Saintek Part 1",
        "description": null,
        "thumbnail": null,
        "type": "mandiri",
        "track": "none",
        "is_per_subject": true,
        "layout_ujian": 1,
        "hide_timer": false,
        "price": 50000,
        "price_sale": 35000,
        "sale_start": "2025-05-01T00:00:00.000000Z",
        "sale_end": "2025-05-31T23:59:59.000000Z",
        "is_active": true,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z",
        "package_subjects": [
            {
                "kode_package_subject": "PSUB-001-xxx",
                "name_package_subject": "Matematika Dasar",
                "kode_kategori": "CAT-001-xxx",
                "is_break": false,
                "sub_section": null,
                "total_questions": 15,
                "duration_minutes": 20,
                "break_time_seconds": 30,
                "passing_grade": 0,
                "seq": 1,
                "type": 1,
                "category": {
                    "kode_kategori": "CAT-001-xxx",
                    "name": "Tes Kemampuan Dasar",
                    "slug": "tkd",
                    "subjects": [
                        {
                            "kode_subject": "SUB-005-xxx",
                            "name": "Matematika Dasar",
                            "icon": "calculator",
                            "color": "#3B82F6"
                        }
                    ]
                }
            },
            {
                "kode_package_subject": "PSUB-002-xxx",
                "name_package_subject": null,
                "kode_kategori": null,
                "is_break": true,
                "sub_section": null,
                "total_questions": null,
                "duration_minutes": 1,
                "break_time_seconds": 0,
                "passing_grade": null,
                "seq": 2,
                "type": null,
                "category": null
            },
            {
                "kode_package_subject": "PSUB-003-xxx",
                "name_package_subject": "Fisika",
                "kode_kategori": "CAT-002-xxx",
                "is_break": false,
                "sub_section": null,
                "total_questions": 15,
                "duration_minutes": 23,
                "break_time_seconds": 30,
                "passing_grade": 0,
                "seq": 3,
                "type": 1,
                "category": {
                    "kode_kategori": "CAT-002-xxx",
                    "name": "TKA Saintek",
                    "slug": "tka-saintek",
                    "subjects": [
                        {
                            "kode_subject": "SUB-009-xxx",
                            "name": "Fisika",
                            "icon": "atom",
                            "color": "#F59E0B"
                        }
                    ]
                }
            }
        ]
    }
}
```

> **Catatan `is_break: true`** — slot istirahat antar sesi. Field `category`, `kode_kategori`, `total_questions`, `passing_grade`, dan `type` bernilai `null`. Hanya `duration_minutes` dan `break_time_seconds` yang relevan.
>
> **Catatan `subjects` dalam `category`** — hanya memuat mapel yang `is_active = true`, diurutkan berdasarkan `name`.

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Feature tidak ditemukan",
    "data": null
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil detail feature",
    "data": null
}
```

---

## 4. PUT /api/feature/{kode_feature}

Update data paket ujian. Semua field bersifat `sometimes` — kirim hanya field yang ingin diubah. `kode_feature` dan `track` **tidak bisa diubah**.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket yang ingin diupdate |

**Body Request** — sama dengan POST, semua field opsional (`sometimes`):

| Field | Tipe | Keterangan |
|-------|------|------------|
| `name` | string, max:255 | |
| `description` | string \| null | |
| `thumbnail` | string \| null, max:255 | |
| `type` | string | `universal` `mandiri` `psikotes` `utbk` `cpns` |
| `is_per_subject` | boolean | |
| `layout_ujian` | integer 1–5 | |
| `hide_timer` | boolean | |
| `price` | integer ≥ 0 | |
| `price_sale` | integer \| null, ≥ 0 | |
| `sale_start` | datetime \| null | |
| `sale_end` | datetime \| null | Harus ≥ `sale_start` jika diisi |
| `is_active` | boolean | |

**Contoh Request — Nonaktifkan paket**
```json
{
    "is_active": false
}
```

**Contoh Request — Update harga dan diskon**
```json
{
    "price": 75000,
    "price_sale": 50000,
    "sale_start": "2025-06-01 00:00:00",
    "sale_end": "2025-06-30 23:59:59"
}
```

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Feature berhasil diperbarui",
    "data": {
        "kode_feature": "PKG-001-250515143022",
        "name": "TO Mandiri Saintek Part 1",
        "description": null,
        "thumbnail": null,
        "type": "mandiri",
        "track": "none",
        "is_per_subject": true,
        "layout_ujian": 1,
        "hide_timer": false,
        "price": 75000,
        "price_sale": 50000,
        "sale_start": "2025-06-01T00:00:00.000000Z",
        "sale_end": "2025-06-30T23:59:59.000000Z",
        "is_active": false,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T16:00:00.000000Z"
    }
}
```

> **Catatan:** Response PUT **tidak menyertakan** `package_subjects` — berbeda dengan GET detail.

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Feature tidak ditemukan",
    "data": null
}
```

**Response 422 — Validasi Gagal**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "sale_end": ["The sale end field must be a date after or equal to sale start."]
    }
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Feature gagal diperbarui",
    "data": null
}
```

---

## 5. DELETE /api/feature/{kode_feature}

Hapus paket ujian. Cascade delete ke semua `package_subjects` di dalamnya. **Gagal** jika sudah ada sesi ujian yang terikat (artinya sudah ada user yang pernah mengerjakan paket ini).

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket yang ingin dihapus |

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Feature berhasil dihapus",
    "data": null
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Feature tidak ditemukan",
    "data": null
}
```

**Response 409 — Konflik (Sudah Ada Sesi Ujian)**
```json
{
    "statusCode": 409,
    "message": "Feature tidak bisa dihapus karena sudah memiliki 150 sesi ujian",
    "data": null
}
```

**Response 500 — Gagal Dihapus**
```json
{
    "statusCode": 500,
    "message": "Feature gagal dihapus",
    "data": null
}
```

---

## 6. PATCH /api/feature/{kode_feature}/update

Update **sebagian field saja** (partial update). Minimal 1 field harus dikirim. Cocok untuk perubahan kecil tanpa perlu kirim semua data.

**Perbedaan `PATCH` vs `PUT` di endpoint ini:**

| Skenario | Gunakan |
|----------|---------|
| Toggle aktif / nonaktif | `PATCH` |
| Ganti harga atau diskon saja | `PATCH` |
| Sembunyikan / tampilkan timer | `PATCH` |
| Update banyak field sekaligus | `PUT` |

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket yang ingin diupdate sebagian |

**Body Request** *(minimal 1 field)* — field sama dengan PUT, semua opsional:

| Field | Tipe | Keterangan |
|-------|------|------------|
| `name` | string, max:255 | |
| `description` | string \| null | |
| `thumbnail` | string \| null, max:255 | |
| `type` | string | `universal` `mandiri` `psikotes` `utbk` `cpns` |
| `is_per_subject` | boolean | |
| `layout_ujian` | integer 1–5 | |
| `hide_timer` | boolean | |
| `price` | integer ≥ 0 | |
| `price_sale` | integer \| null, ≥ 0 | |
| `sale_start` | datetime \| null | |
| `sale_end` | datetime \| null | Harus ≥ `sale_start` jika diisi |
| `is_active` | boolean | |

**Contoh Request — Nonaktifkan paket**
```json
{
    "is_active": false
}
```

**Contoh Request — Update harga dan diskon**
```json
{
    "price": 75000,
    "price_sale": 50000,
    "sale_start": "2025-06-01 00:00:00",
    "sale_end": "2025-06-30 23:59:59"
}
```

**Contoh Request — Sembunyikan timer**
```json
{
    "hide_timer": true
}
```

**Response 200 — Berhasil Diperbarui Sebagian**
```json
{
    "statusCode": 200,
    "message": "Feature berhasil diperbarui sebagian",
    "data": {
        "kode_feature": "PKG-001-250515143022",
        "name": "TO Mandiri Saintek Part 1",
        "description": null,
        "thumbnail": null,
        "type": "mandiri",
        "track": "none",
        "is_per_subject": true,
        "layout_ujian": 1,
        "hide_timer": true,
        "price": 50000,
        "price_sale": 35000,
        "sale_start": "2025-05-01T00:00:00.000000Z",
        "sale_end": "2025-05-31T23:59:59.000000Z",
        "is_active": true,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T16:00:00.000000Z"
    }
}
```

> **Catatan:** Response PATCH **tidak menyertakan** `package_subjects` — berbeda dengan GET detail.

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
    "message": "Feature tidak ditemukan",
    "data": null
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Feature gagal diperbarui",
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
| `404` | Paket tidak ditemukan |
| `409` | Konflik — sudah ada sesi ujian terikat |
| `422` | Validasi gagal |
| `500` | Server error |
