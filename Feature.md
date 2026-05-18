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
| GET | `/api/feature/{kode_feature}` | Detail paket + daftar mapel |
| PUT | `/api/feature/{kode_feature}` | Update semua field |
| PATCH | `/api/feature/{kode_feature}/update` | Update sebagian field |
| DELETE | `/api/feature/{kode_feature}` | Hapus paket |

---

## 1. GET /api/feature

Ambil semua paket ujian. Bisa difilter via query parameter.

**Query Parameters** *(semua opsional)*

| Parameter | Nilai yang tersedia | Keterangan |
|-----------|---------------------|------------|
| `type` | `universal` `mandiri` `psikotes` `utbk` `cpns` | Filter by jenis paket |
| `track` | `none` `saintek` `soshum` `psikotes` `tpa` `skolastik` `literasi-id` `literasi-en` `penalaran-mat` `cpns` | Filter by jalur |
| `is_active` | `1` / `0` | Filter by status aktif |

**Contoh Request**
```
GET /api/feature
GET /api/feature?type=universal&is_active=1
GET /api/feature?type=mandiri&track=saintek
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar paket ujian berhasil diambil",
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
            "is_per_subject": false,
            "layout_ujian": 1,
            "hide_timer": false,
            "is_active": true,
            "created_at": "2025-05-15T14:30:22.000000Z",
            "updated_at": "2025-05-15T14:30:22.000000Z"
        }
    ]
}
```

---

## 2. POST /api/feature

Buat paket ujian baru. `kode_feature` di-generate otomatis oleh server dengan format `PKG-{sequential}-{timestamp}`.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:255 | ✅ | Nama paket ujian |
| `type` | string | ✅ | `universal` `mandiri` `psikotes` `utbk` `cpns` |
| `track` | string | ✅ | `none` `saintek` `soshum` `psikotes` `tpa` `skolastik` `literasi-id` `literasi-en` `penalaran-mat` `cpns` |
| `description` | string | ❌ | Deskripsi paket |
| `thumbnail` | string | ❌ | Path gambar banner (tampil di halaman hasil) |
| `is_per_subject` | boolean | ❌ | Default `true`. `true` = dikerjakan per mapel (ada sub-sesi + jeda). `false` = semua soal sekaligus |
| `layout_ujian` | integer 1-5 | ❌ | Default `1`. Layout tampilan soal di frontend |
| `hide_timer` | boolean | ❌ | Default `false`. `true` = timer disembunyikan dari user |
| `price` | integer | ❌ | Default `0` (gratis). Satuan rupiah |
| `price_sale` | integer | ❌ | Harga setelah diskon. `null` = tidak ada diskon langsung |
| `sale_start` | datetime | ❌ | Mulai periode diskon |
| `sale_end` | datetime | ❌ | Akhir periode diskon. Harus setelah atau sama dengan `sale_start` |
| `is_active` | boolean | ❌ | Default `true` |

**Contoh Request — Paket gratis**
```json
{
    "name": "Latihan Matematika SMA",
    "type": "universal",
    "track": "none",
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
    "track": "saintek",
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
    "message": "Paket ujian berhasil ditambahkan",
    "data": {
        "kode_feature": "PKG-001-250515143022",
        "name": "TO Mandiri Saintek Part 1",
        "type": "mandiri",
        "track": "saintek",
        "price": 50000,
        "price_sale": 35000,
        "sale_start": "2025-05-01T00:00:00.000000Z",
        "sale_end": "2025-05-31T23:59:59.000000Z",
        "is_per_subject": true,
        "is_active": true
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

---

## 3. GET /api/feature/{kode_feature}

Detail paket beserta semua slot mapel (`package_subjects`) di dalamnya, diurutkan by `seq`. Termasuk slot istirahat (`is_break: true`).

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket, contoh: `PKG-001-250515143022` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail paket ujian berhasil diambil",
    "data": {
        "kode_feature": "PKG-001-250515143022",
        "name": "TO Mandiri Saintek Part 1",
        "type": "mandiri",
        "track": "saintek",
        "price": 50000,
        "price_sale": 35000,
        "is_per_subject": true,
        "is_active": true,
        "package_subjects": [
            {
                "kode_feature_subject": "PSUB-001-xxx",
                "kode_subject": "SUB-005-xxx",
                "is_break": false,
                "total_questions": 15,
                "duration_minutes": 20,
                "break_time_seconds": 30,
                "passing_grade": 0,
                "seq": 1,
                "type": 1,
                "subject": {
                    "kode_subject": "SUB-005-xxx",
                    "name": "Matematika Dasar",
                    "color": "#3B82F6"
                }
            },
            {
                "kode_feature_subject": "PSUB-002-xxx",
                "kode_subject": null,
                "is_break": true,
                "duration_minutes": 1,
                "break_time_seconds": 0,
                "seq": 2,
                "subject": null
            },
            {
                "kode_feature_subject": "PSUB-003-xxx",
                "kode_subject": "SUB-009-xxx",
                "is_break": false,
                "total_questions": 15,
                "duration_minutes": 23,
                "break_time_seconds": 30,
                "seq": 3,
                "subject": {
                    "kode_subject": "SUB-009-xxx",
                    "name": "Fisika",
                    "color": "#F59E0B"
                }
            }
        ]
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Paket ujian tidak ditemukan",
    "data": null
}
```

---

## 4. PUT /api/feature/{kode_feature}

Update semua field paket ujian. Semua field bersifat `sometimes` — kirim field yang ingin diubah.

**Body Request** — sama dengan `store`, semua field opsional (`sometimes`).

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Paket ujian berhasil diperbarui",
    "data": { "..." }
}
```

---

## 5. PATCH /api/feature/{kode_feature}/update

Update **sebagian field** saja. Minimal 1 field harus dikirim. Cocok untuk perubahan kecil tanpa perlu kirim semua data.

**Contoh — nonaktifkan paket**
```json
{ "is_active": false }
```

**Contoh — update harga dan diskon**
```json
{
    "price": 75000,
    "price_sale": 50000,
    "sale_start": "2025-06-01 00:00:00",
    "sale_end": "2025-06-30 23:59:59"
}
```

**Contoh — sembunyikan timer**
```json
{ "hide_timer": true }
```

**Response 422 — Tidak Ada Data**
```json
{
    "statusCode": 422,
    "message": "Tidak ada data yang dikirim",
    "errors": {
        "field": ["Minimal satu field harus diisi untuk patch update"]
    }
}
```

---

## 6. DELETE /api/feature/{kode_feature}

Hapus paket ujian beserta semua `package_subjects` di dalamnya (cascade). **Gagal** jika sudah ada sesi ujian yang terikat — artinya sudah ada user yang pernah mengerjakan paket ini.

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Paket ujian berhasil dihapus",
    "data": null
}
```

**Response 409 — Konflik**
```json
{
    "statusCode": 409,
    "message": "Paket ujian tidak bisa dihapus karena sudah memiliki 150 sesi ujian",
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
