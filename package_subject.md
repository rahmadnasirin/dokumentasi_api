# API Dokumentasi — Mapel dalam Paket (Package Subjects)

> **Base URL:** `/api`
> **Auth:** Bearer Token (Laravel Sanctum)
> **Content-Type:** `application/json`

---

## Tentang Modul Ini

`package_subjects` adalah **slot konfigurasi** yang menghubungkan paket ujian dengan mata pelajaran. Setiap slot menyimpan konfigurasi ujian per mapel: jumlah soal, durasi, jeda istirahat, dan urutan pengerjaan.

Satu slot bisa berupa:
- **Slot mapel** — sub-ujian normal yang berisi soal (`is_break: false`)
- **Slot istirahat** — jeda antar mapel, tidak berisi soal (`is_break: true`)

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
> URL selalu diawali `/api/exam-packages/{kode_paket}/package-subjects`.

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/exam-packages/{kode_paket}/package-subjects` | Daftar semua slot mapel dalam paket |
| POST | `/api/exam-packages/{kode_paket}/package-subjects` | Tambah slot mapel atau slot istirahat |
| GET | `/api/exam-packages/{kode_paket}/package-subjects/{id}` | Detail slot mapel |
| PUT | `/api/exam-packages/{kode_paket}/package-subjects/{id}` | Update semua field |
| PATCH | `/api/exam-packages/{kode_paket}/package-subjects/{id}/update` | Update sebagian field |
| DELETE | `/api/exam-packages/{kode_paket}/package-subjects/{id}` | Hapus slot mapel |

---

## 1. GET /api/exam-packages/{kode_paket}/package-subjects

Ambil semua slot mapel dalam paket tertentu, diurutkan by `seq`. Termasuk slot istirahat.

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_paket` | Kode paket induk |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar mapel paket berhasil diambil",
    "data": [
        {
            "kode_paket_subject": "PSUB-001-250515143022",
            "kode_paket": "PKG-001-xxx",
            "kode_subject": "SUB-005-xxx",
            "is_break": false,
            "sub_section": null,
            "total_questions": 15,
            "duration_minutes": 20,
            "break_time_seconds": 30,
            "passing_grade": 0,
            "seq": 1,
            "type": 1,
            "subject": {
                "kode_subject": "SUB-005-xxx",
                "name": "Matematika Dasar",
                "icon": "calculator",
                "color": "#3B82F6"
            }
        },
        {
            "kode_paket_subject": "PSUB-002-250515143022",
            "kode_paket": "PKG-001-xxx",
            "kode_subject": null,
            "is_break": true,
            "total_questions": 0,
            "duration_minutes": 1,
            "break_time_seconds": 0,
            "seq": 2,
            "subject": null
        },
        {
            "kode_paket_subject": "PSUB-003-250515143022",
            "kode_paket": "PKG-001-xxx",
            "kode_subject": "SUB-006-xxx",
            "is_break": false,
            "total_questions": 15,
            "duration_minutes": 20,
            "break_time_seconds": 30,
            "seq": 3,
            "subject": {
                "kode_subject": "SUB-006-xxx",
                "name": "Bahasa Indonesia (TKD)",
                "color": "#EF4444"
            }
        }
    ]
}
```

**Response 404 — Paket Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Paket ujian tidak ditemukan",
    "data": null
}
```

---

## 2. POST /api/exam-packages/{kode_paket}/package-subjects

Tambah slot mapel atau slot istirahat ke paket. `kode_paket_subject` di-generate otomatis.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_subject` | string | ✅* | Wajib jika `is_break: false`. FK ke `subjects` |
| `is_break` | boolean | ❌ | Default `false`. `true` = slot istirahat (tidak ada soal) |
| `sub_section` | string | ❌ | Khusus UTBK Skolastik Bagian 1: `induktif` `deduktif` `kuantitatif`. Fitur lain biarkan `null` |
| `total_questions` | integer, min:1 | ❌ | Jumlah soal yang diambil dari bank soal. Default `15` |
| `duration_minutes` | integer, min:1 | ❌ | Durasi sub-sesi mapel ini dalam menit. Default `20` |
| `break_time_seconds` | integer, min:0 | ❌ | Jeda otomatis (detik) setelah sub-sesi ini selesai sebelum lanjut ke berikutnya. Default `0` |
| `passing_grade` | float 0-100 | ❌ | Nilai minimum kelulusan mapel ini. Default `0` (tidak ada KKM) |
| `seq` | integer, min:1 | ❌ | Urutan pengerjaan. Default otomatis urutan terakhir + 1 |
| `type` | integer | ❌ | Default `1`. `1`=pilgan `2`=esai `3`=campuran |

**Contoh — tambah slot mapel**
```json
{
    "kode_subject": "SUB-005-250515090100",
    "total_questions": 15,
    "duration_minutes": 20,
    "break_time_seconds": 30,
    "seq": 1
}
```

**Contoh — tambah slot mapel dengan KKM**
```json
{
    "kode_subject": "SUB-009-xxx",
    "total_questions": 15,
    "duration_minutes": 23,
    "break_time_seconds": 30,
    "passing_grade": 50,
    "seq": 3
}
```

**Contoh — tambah slot istirahat**
```json
{
    "is_break": true,
    "duration_minutes": 1,
    "seq": 2
}
```

**Contoh — UTBK Skolastik sub-bagian**
```json
{
    "kode_subject": "SUB-016-xxx",
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
    "message": "Mapel paket berhasil ditambahkan",
    "data": {
        "kode_paket_subject": "PSUB-004-250515143022",
        "kode_paket": "PKG-001-xxx",
        "kode_subject": "SUB-005-xxx",
        "is_break": false,
        "total_questions": 15,
        "duration_minutes": 20,
        "break_time_seconds": 30,
        "passing_grade": 0,
        "seq": 1,
        "type": 1,
        "subject": {
            "name": "Matematika Dasar",
            "color": "#3B82F6"
        }
    }
}
```

**Response 422 — Subject Tidak Diisi**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "kode_subject": ["Mata pelajaran wajib diisi jika bukan slot istirahat"]
    }
}
```

---

## 3. GET /api/exam-packages/{kode_paket}/package-subjects/{kode_paket_subject}

Detail satu slot mapel. Response sudah include `questions_count` — jumlah soal yang tersedia di bank soal untuk mapel ini.

> `questions_count` penting untuk dicek sebelum user mulai ujian.
> Jika `questions_count < total_questions`, sesi tidak bisa dimulai karena soal tidak cukup untuk di-random.

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail mapel paket berhasil diambil",
    "data": {
        "kode_paket_subject": "PSUB-001-xxx",
        "kode_paket": "PKG-001-xxx",
        "kode_subject": "SUB-005-xxx",
        "is_break": false,
        "total_questions": 15,
        "duration_minutes": 20,
        "break_time_seconds": 30,
        "passing_grade": 0,
        "seq": 1,
        "type": 1,
        "questions_count": 120,
        "subject": {
            "kode_subject": "SUB-005-xxx",
            "name": "Matematika Dasar",
            "icon": "calculator",
            "color": "#3B82F6"
        },
        "exam_package": {
            "kode_paket": "PKG-001-xxx",
            "name": "TO Mandiri Saintek Part 1"
        }
    }
}
```

---

## 4. PUT /api/exam-packages/{kode_paket}/package-subjects/{kode_paket_subject}

Update semua field slot mapel. Semua field bersifat `sometimes`.

> Jika `is_break` diubah ke `true`, `kode_subject` otomatis di-set `null`.

---

## 5. PATCH /api/exam-packages/{kode_paket}/package-subjects/{kode_paket_subject}/update

Update **sebagian field** saja. Minimal 1 field harus dikirim. Cocok untuk perubahan kecil.

**Contoh — ubah durasi saja**
```json
{ "duration_minutes": 25 }
```

**Contoh — ubah jumlah soal**
```json
{ "total_questions": 20 }
```

**Contoh — pindah urutan**
```json
{ "seq": 3 }
```

**Contoh — tambah jeda istirahat setelah slot ini**
```json
{ "break_time_seconds": 60 }
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

## 6. DELETE /api/exam-packages/{kode_paket}/package-subjects/{kode_paket_subject}

Hapus slot mapel dari paket. **Gagal** jika mapel ini masih punya soal di bank soal yang terikat.

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Mapel paket berhasil dihapus",
    "data": null
}
```

**Response 409 — Konflik**
```json
{
    "statusCode": 409,
    "message": "Mapel paket tidak bisa dihapus karena masih memiliki 120 soal",
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
| `404` | Paket atau slot mapel tidak ditemukan |
| `409` | Konflik — masih ada soal terikat |
| `422` | Validasi gagal |
| `500` | Server error |
