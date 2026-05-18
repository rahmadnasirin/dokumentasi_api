# API Dokumentasi — Slot Kategori dalam Paket (Package Subjects)

> **Base URL:** `/api`
> **Auth:** Bearer Token (Laravel Sanctum)
> **Content-Type:** `application/json`

---

## Tentang Modul Ini

`package_subjects` adalah **slot konfigurasi** yang menghubungkan paket ujian dengan kategori mata pelajaran. Setiap slot menyimpan konfigurasi ujian per kategori: jumlah soal, durasi, jeda istirahat, dan urutan pengerjaan.

Satu slot bisa berupa:
- **Slot kategori** — sub-ujian normal yang berisi soal (`is_break: false`)
- **Slot istirahat** — jeda antar sesi, tidak berisi soal (`is_break: true`)

**Posisi dalam alur:**
```
exam_packages     ← harus ada dulu → docs: api-exam-packages.md
    ↓
package_subjects  ← dibuat di sini (nested dalam exam_packages)
    ↓
questions         ← soal di bank soal otomatis masuk pool
                     jika kode_subject sama → docs: api-questions.md
```

> **Nested resource** — semua endpoint di modul ini selalu dalam konteks paket tertentu.
> URL selalu diawali `/api/feature/{kode_feature}/package-subjects`.

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/feature/{kode_feature}/package-subjects` | Daftar semua slot dalam paket |
| POST | `/api/feature/{kode_feature}/package-subjects` | Tambah slot kategori atau slot istirahat |
| GET | `/api/feature/{kode_feature}/package-subjects/{kode_package_subject}` | Detail slot |
| PUT | `/api/feature/{kode_feature}/package-subjects/{kode_package_subject}` | Update semua field |
| PATCH | `/api/feature/{kode_feature}/package-subjects/{kode_package_subject}/update` | Update sebagian field |
| DELETE | `/api/feature/{kode_feature}/package-subjects/{kode_package_subject}` | Hapus slot |

---

## Schema Database

```sql
package_subjects
├── kode_package_subject  STRING   PRIMARY KEY          -- format: PSUB-001-250515143022
├── name_package_subject  STRING   NOT NULL             -- nama slot/sub-sesi
├── kode_feature          STRING   NOT NULL             -- FK → feature(kode_feature) CASCADE DELETE
├── kode_kategori         STRING   NULLABLE             -- FK → categories(kode_kategori) SET NULL ON DELETE
│                                                       -- null hanya jika is_break = true
├── is_break              BOOLEAN  DEFAULT false        -- false = sub-ujian normal, true = slot istirahat
├── sub_section           STRING   NULLABLE             -- khusus UTBK Skolastik B1: induktif|deduktif|kuantitatif
├── total_questions       INTEGER  DEFAULT 15           -- jumlah soal dari bank soal
├── duration_minutes      INTEGER  DEFAULT 20           -- durasi sub-sesi ini (menit)
├── break_time_seconds    INTEGER  DEFAULT 0            -- jeda otomatis SETELAH sub-sesi ini selesai (detik)
├── passing_grade         FLOAT    DEFAULT 0            -- KKM (0 = tidak ada batas)
├── seq                   INTEGER  DEFAULT 1            -- urutan pengerjaan dalam paket
├── type                  TINYINT  DEFAULT 1            -- 1=pilgan, 2=esai, 3=campuran
├── created_at            TIMESTAMP
└── updated_at            TIMESTAMP
```

**Nilai referensi `total_questions` dan `duration_minutes` per konteks:**

| Konteks | `total_questions` | `duration_minutes` |
|---------|-------------------|---------------------|
| Universal | 40 | 60 |
| TKD (per mapel) | 15 | 20 |
| TKA (per mapel) | 15 | 22.5 |
| UTBK B1 sub-bagian | 10 | 10 |
| UTBK B2 | — | 15 |
| UTBK B3 | — | 25 |
| UTBK B4 | — | 20 |

**Nilai referensi `break_time_seconds` per konteks:**

| Konteks | Detik |
|---------|-------|
| TKD antar mapel | 30 |
| TKD → TKA | 60 |
| TKA antar mapel | 30 |
| Tidak ada jeda | 0 |

> **Catatan:** Nilai di atas adalah referensi default — bukan constraint. Bisa diisi bebas sesuai kebutuhan paket.

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

## 1. GET /api/feature/{kode_feature}/package-subjects

Ambil semua slot dalam paket tertentu, diurutkan berdasarkan `seq`. Termasuk slot istirahat. Response menyertakan data `category` beserta daftar mapel aktif di dalamnya.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket induk, contoh: `PKG-001-250515143022` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar slot kategori berhasil diambil",
    "data": [
        {
            "kode_package_subject": "PSUB-001-250515143022",
            "name_package_subject": "Matematika Dasar",
            "kode_feature": "PKG-001-xxx",
            "kode_kategori": "CAT-001-xxx",
            "is_break": false,
            "sub_section": null,
            "total_questions": 15,
            "duration_minutes": 20,
            "break_time_seconds": 30,
            "passing_grade": 0,
            "seq": 1,
            "type": 1,
            "created_at": "2025-05-15T14:30:22.000000Z",
            "updated_at": "2025-05-15T14:30:22.000000Z",
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
            "kode_package_subject": "PSUB-002-250515143022",
            "name_package_subject": "Istirahat",
            "kode_feature": "PKG-001-xxx",
            "kode_kategori": null,
            "is_break": true,
            "sub_section": null,
            "total_questions": 0,
            "duration_minutes": 1,
            "break_time_seconds": 0,
            "passing_grade": 0,
            "seq": 2,
            "type": 1,
            "created_at": "2025-05-15T14:30:22.000000Z",
            "updated_at": "2025-05-15T14:30:22.000000Z",
            "category": null
        }
    ]
}
```

> **Catatan `is_break: true`** — field `kode_kategori` dan `category` bernilai `null`.
> Field `total_questions`, `passing_grade`, dan `type` tetap ada tapi tidak relevan untuk slot istirahat.

**Response 404 — Paket Tidak Ditemukan**
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
    "message": "Gagal mengambil data slot kategori",
    "data": null
}
```

---

## 2. POST /api/feature/{kode_feature}/package-subjects

Tambah slot kategori atau slot istirahat ke paket. `kode_package_subject` di-generate otomatis oleh server.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket induk |

**Body Request**

| Field | Tipe | Wajib | Default | Keterangan |
|-------|------|-------|---------|------------|
| `name_package_subject` | string, max:100 | ✅ | — | Nama slot / sub-sesi |
| `kode_kategori` | string | ✅* | — | Wajib jika `is_break: false`. FK ke `categories` |
| `is_break` | boolean | ❌ | `false` | `true` = slot istirahat (tidak ada soal, `kode_kategori` diabaikan) |
| `sub_section` | string, max:50 | ❌ | `null` | Khusus UTBK Skolastik B1: `induktif` `deduktif` `kuantitatif`. Biarkan `null` untuk fitur lain |
| `total_questions` | integer ≥ 1 | ❌ | `15` (mapel) / `0` (istirahat) | Jumlah soal dari bank soal |
| `duration_minutes` | integer ≥ 1 | ❌ | `20` (mapel) / `1` (istirahat) | Durasi sub-sesi dalam menit |
| `break_time_seconds` | integer ≥ 0 | ❌ | `0` | Jeda otomatis (detik) setelah sub-sesi ini selesai sebelum lanjut ke berikutnya |
| `passing_grade` | float 0–100 | ❌ | `0` | Nilai minimum kelulusan. `0` = tidak ada KKM |
| `seq` | integer ≥ 1 | ❌ | auto (max seq + 1) | Urutan pengerjaan dalam paket |
| `type` | integer | ❌ | `1` | `1` = pilihan ganda, `2` = esai, `3` = campuran |

**Contoh — Tambah slot kategori**
```json
{
    "name_package_subject": "Matematika Dasar",
    "kode_kategori": "CAT-001-250515090000",
    "total_questions": 15,
    "duration_minutes": 20,
    "break_time_seconds": 30,
    "seq": 1
}
```

**Contoh — Tambah slot kategori dengan KKM**
```json
{
    "name_package_subject": "Fisika",
    "kode_kategori": "CAT-002-xxx",
    "total_questions": 15,
    "duration_minutes": 23,
    "break_time_seconds": 30,
    "passing_grade": 50,
    "seq": 3
}
```

**Contoh — Tambah slot istirahat**
```json
{
    "name_package_subject": "Istirahat",
    "is_break": true,
    "duration_minutes": 1,
    "seq": 2
}
```

**Contoh — UTBK Skolastik sub-bagian**
```json
{
    "name_package_subject": "Skolastik — Penalaran Induktif",
    "kode_kategori": "CAT-005-xxx",
    "sub_section": "induktif",
    "total_questions": 10,
    "duration_minutes": 10,
    "seq": 1
}
```

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Slot kategori berhasil ditambahkan",
    "data": {
        "kode_package_subject": "PSUB-004-250515143022",
        "name_package_subject": "Matematika Dasar",
        "kode_feature": "PKG-001-xxx",
        "kode_kategori": "CAT-001-xxx",
        "is_break": false,
        "sub_section": null,
        "total_questions": 15,
        "duration_minutes": 20,
        "break_time_seconds": 30,
        "passing_grade": 0,
        "seq": 1,
        "type": 1,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z",
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
    }
}
```

**Response 422 — Kategori Tidak Diisi**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "kode_kategori": ["Kategori wajib diisi jika bukan slot istirahat"]
    }
}
```

**Response 422 — Validasi Field**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "name_package_subject": ["The name package subject field is required."],
        "kode_kategori": ["The selected kode kategori is invalid."],
        "type": ["The selected type is invalid."]
    }
}
```

**Response 500 — Gagal Ditambahkan**
```json
{
    "statusCode": 500,
    "message": "Slot kategori gagal ditambahkan",
    "data": null
}
```

---

## 3. GET /api/feature/{kode_feature}/package-subjects/{kode_package_subject}

Detail satu slot. Response menyertakan `total_soal_tersedia` — total soal aktif di bank soal untuk semua mapel dalam kategori slot ini.

> **`total_soal_tersedia` penting** untuk dicek sebelum user mulai ujian.
> Jika `total_soal_tersedia < total_questions`, sesi tidak bisa dimulai karena soal tidak cukup untuk di-random.

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket induk |
| `kode_package_subject` | Kode slot, contoh: `PSUB-001-250515143022` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail slot kategori berhasil diambil",
    "data": {
        "kode_package_subject": "PSUB-001-250515143022",
        "name_package_subject": "Matematika Dasar",
        "kode_feature": "PKG-001-xxx",
        "kode_kategori": "CAT-001-xxx",
        "is_break": false,
        "sub_section": null,
        "total_questions": 15,
        "duration_minutes": 20,
        "break_time_seconds": 30,
        "passing_grade": 0,
        "seq": 1,
        "type": 1,
        "total_soal_tersedia": 120,
        "created_at": "2025-05-15T09:01:00.000000Z",
        "updated_at": "2025-05-15T09:01:00.000000Z",
        "category": {
            "kode_kategori": "CAT-001-xxx",
            "name": "Tes Kemampuan Dasar",
            "slug": "tkd",
            "subjects": [
                {
                    "kode_subject": "SUB-005-xxx",
                    "name": "Matematika Dasar",
                    "icon": "calculator",
                    "color": "#3B82F6",
                    "questions_count": 120
                }
            ]
        },
        "feature": {
            "kode_feature": "PKG-001-xxx",
            "name": "TO Mandiri Saintek Part 1"
        }
    }
}
```

> **Catatan `total_soal_tersedia`** — merupakan penjumlahan `questions_count` dari semua mapel aktif dalam kategori slot ini.
> Untuk slot istirahat (`is_break: true`), field ini akan bernilai `0`.

**Response 404 — Slot Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Slot kategori tidak ditemukan",
    "data": null
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil detail slot kategori",
    "data": null
}
```

---

## 4. PUT /api/feature/{kode_feature}/package-subjects/{kode_package_subject}

Update data slot. Semua field bersifat `sometimes` — kirim hanya field yang ingin diubah.

> Jika `is_break` diubah ke `true`, field `kode_kategori` otomatis di-set `null` oleh server.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket induk |
| `kode_package_subject` | Kode slot yang ingin diupdate |

**Body Request** — semua field opsional (`sometimes`):

| Field | Tipe | Keterangan |
|-------|------|------------|
| `name_package_subject` | string, max:100 | Nama slot / sub-sesi |
| `kode_kategori` | string \| null | FK ke `categories`. Set `null` hanya jika mengubah ke slot istirahat |
| `is_break` | boolean | Jika diubah ke `true`, `kode_kategori` di-null-kan otomatis |
| `sub_section` | string \| null, max:50 | `induktif` `deduktif` `kuantitatif` atau `null` |
| `total_questions` | integer ≥ 1 | |
| `duration_minutes` | integer ≥ 1 | |
| `break_time_seconds` | integer ≥ 0 | |
| `passing_grade` | float 0–100 | |
| `seq` | integer ≥ 1 | |
| `type` | integer | `1` `2` `3` |

**Contoh Request — Ubah durasi dan jumlah soal**
```json
{
    "total_questions": 20,
    "duration_minutes": 25
}
```

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Slot kategori berhasil diperbarui",
    "data": {
        "kode_package_subject": "PSUB-001-250515143022",
        "name_package_subject": "Matematika Dasar",
        "kode_feature": "PKG-001-xxx",
        "kode_kategori": "CAT-001-xxx",
        "is_break": false,
        "sub_section": null,
        "total_questions": 20,
        "duration_minutes": 25,
        "break_time_seconds": 30,
        "passing_grade": 0,
        "seq": 1,
        "type": 1,
        "created_at": "2025-05-15T09:01:00.000000Z",
        "updated_at": "2025-05-15T16:00:00.000000Z",
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
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Slot kategori tidak ditemukan",
    "data": null
}
```

**Response 422 — Validasi Gagal**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "duration_minutes": ["The duration minutes field must be at least 1."]
    }
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Slot kategori gagal diperbarui",
    "data": null
}
```

---

## 5. PATCH /api/feature/{kode_feature}/package-subjects/{kode_package_subject}/update

Update **sebagian field saja** (partial update). Minimal 1 field harus dikirim.

> Jika `is_break` diubah ke `true`, field `kode_kategori` otomatis di-set `null` oleh server.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket induk |
| `kode_package_subject` | Kode slot yang ingin diupdate |

**Body Request** *(minimal 1 field)* — field sama dengan PUT:

| Field | Tipe | Keterangan |
|-------|------|------------|
| `name_package_subject` | string, max:100 | |
| `kode_kategori` | string \| null | |
| `is_break` | boolean | Jika `true`, `kode_kategori` di-null-kan otomatis |
| `sub_section` | string \| null, max:50 | |
| `total_questions` | integer ≥ 1 | |
| `duration_minutes` | integer ≥ 1 | |
| `break_time_seconds` | integer ≥ 0 | |
| `passing_grade` | float 0–100 | |
| `seq` | integer ≥ 1 | |
| `type` | integer | `1` `2` `3` |

**Contoh — Ubah durasi saja**
```json
{ "duration_minutes": 25 }
```

**Contoh — Ubah jumlah soal**
```json
{ "total_questions": 20 }
```

**Contoh — Pindah urutan**
```json
{ "seq": 3 }
```

**Contoh — Tambah jeda setelah slot ini**
```json
{ "break_time_seconds": 60 }
```

**Response 200 — Berhasil Diperbarui Sebagian**
```json
{
    "statusCode": 200,
    "message": "Slot kategori berhasil diperbarui sebagian",
    "data": {
        "kode_package_subject": "PSUB-001-250515143022",
        "name_package_subject": "Matematika Dasar",
        "kode_feature": "PKG-001-xxx",
        "kode_kategori": "CAT-001-xxx",
        "is_break": false,
        "sub_section": null,
        "total_questions": 15,
        "duration_minutes": 25,
        "break_time_seconds": 30,
        "passing_grade": 0,
        "seq": 1,
        "type": 1,
        "created_at": "2025-05-15T09:01:00.000000Z",
        "updated_at": "2025-05-15T16:00:00.000000Z",
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
    "message": "Slot kategori tidak ditemukan",
    "data": null
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Slot kategori gagal diperbarui",
    "data": null
}
```

---

## 6. DELETE /api/feature/{kode_feature}/package-subjects/{kode_package_subject}

Hapus slot dari paket. **Gagal** jika slot ini sudah punya sesi ujian yang terikat (artinya sudah ada user yang pernah mengerjakan sub-sesi ini).

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_feature` | Kode paket induk |
| `kode_package_subject` | Kode slot yang ingin dihapus |

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Slot kategori berhasil dihapus",
    "data": null
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Slot kategori tidak ditemukan",
    "data": null
}
```

**Response 409 — Konflik (Sudah Ada Sesi Terikat)**
```json
{
    "statusCode": 409,
    "message": "Slot tidak bisa dihapus karena sudah ada 5 sesi ujian yang terikat",
    "data": null
}
```

**Response 500 — Gagal Dihapus**
```json
{
    "statusCode": 500,
    "message": "Slot kategori gagal dihapus",
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
| `404` | Paket atau slot tidak ditemukan |
| `409` | Konflik — sudah ada sesi ujian terikat |
| `422` | Validasi gagal |
| `500` | Server error |
