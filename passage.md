# API Dokumentasi — Teks Bacaan (Passages)

> **Base URL:** `/api`
> **Auth:** Bearer Token (Laravel Sanctum)
> **Content-Type:** `application/json`

---

## Tentang Modul Ini

`passages` adalah teks bacaan bersama yang bisa digunakan oleh beberapa soal sekaligus. Passage bersifat independen — terikat ke mata pelajaran (`subject`), bukan ke soal tertentu. Soal yang menggunakan passage dihubungkan via `kode_passage`.

**Posisi dalam alur:**
```
subjects      ← passage terikat ke mapel
    ↓
passages      ← dibuat di sini (opsional, hanya jika soal berkelompok)
    ↓
questions     ← soal dihubungkan ke passage via kode_passage
```

**Dua field penting:**

| Field | Fungsi |
|-------|--------|
| `title` | Label singkat untuk **dropdown/select** di form input soal. Contoh: `"Bacaan 1"`, `"Teks Ekosistem"` |
| `description` | Instruksi yang **tampil di atas soal** saat ujian berlangsung. Contoh: `"Untuk soal nomor 1–3, bacalah teks berikut ini dengan seksama"` |

> `title` dipakai di sisi admin (form), `description` dipakai di sisi user (saat ujian).

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/passages` | Daftar semua passage (dengan filter query param) |
| GET | `/api/passages/subject/{kode_subject}` | Daftar passage by subject (path param) |
| POST | `/api/passages` | Buat passage baru |
| GET | `/api/passages/{kode_passage}` | Detail passage beserta soal-soalnya |
| PUT | `/api/passages/{kode_passage}` | Update semua field passage |
| PATCH | `/api/passages/{kode_passage}/update` | Update sebagian field passage |
| DELETE | `/api/passages/{kode_passage}` | Hapus passage |

---

## Schema Database

```sql
question_passages
├── kode_passage   STRING   PRIMARY KEY   -- format: PAS-001-250515143022
├── kode_subject   STRING   NOT NULL      -- FK → subjects(kode_subject) CASCADE DELETE
├── title          STRING   NULLABLE      -- label singkat untuk dropdown di form soal
├── description    STRING   NULLABLE      -- instruksi yang tampil di atas soal saat ujian
├── content        TEXT     NOT NULL      -- isi teks bacaan (HTML)
├── seq            INTEGER  DEFAULT 1     -- urutan jika ada beberapa passage dalam satu mapel
├── created_at     TIMESTAMP
└── updated_at     TIMESTAMP
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

## 1. GET /api/passages

Ambil semua passage. Umumnya dipanggil dengan filter `kode_subject` via query param untuk populate dropdown di form input soal.

**Headers**
```
Authorization: Bearer {token}
```

**Query Parameters**

| Parameter | Keterangan |
|-----------|------------|
| `kode_subject` | Filter berdasarkan mata pelajaran (dianjurkan selalu diisi) |

**Contoh Request**
```
GET /api/passages?kode_subject=SUB-002-xxx
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar teks bacaan berhasil diambil",
    "data": [
        {
            "kode_passage": "PAS-001-250515143022",
            "kode_subject": "SUB-002-xxx",
            "title": "Bacaan 1 — Pertanian",
            "description": "Untuk soal nomor 1-3, bacalah teks berikut ini dengan seksama",
            "content": "<p>Di sebuah daerah pertanian, perubahan pola curah hujan...</p>",
            "seq": 1,
            "created_at": "2025-05-15T14:30:22.000000Z",
            "updated_at": "2025-05-15T14:30:22.000000Z",
            "subject": {
                "kode_subject": "SUB-002-xxx",
                "name": "Bahasa Indonesia"
            }
        }
    ]
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil data teks bacaan",
    "data": null
}
```

---

## 2. GET /api/passages/subject/{kode_subject}

Ambil semua passage berdasarkan mata pelajaran via **path param**. Cocok dipakai di frontend untuk populate dropdown passage saat mapel sudah dipilih — URL lebih eksplisit dibanding query param.

**Perbedaan dengan endpoint #1:**

| | `GET /api/passages?kode_subject=xxx` | `GET /api/passages/subject/{kode_subject}` |
|---|---|---|
| Filter mapel | Query param (opsional) | Path param (wajib) |
| Hasil kosong | List kosong `[]` | Kembalikan list kosong `[]` |
| Use case | List semua passage, filter opsional | Fokus passage satu mapel |

**Headers**
```
Authorization: Bearer {token}
```

**Parameter URL**

| Parameter | Keterangan |
|-----------|------------|
| `kode_subject` | Kode mata pelajaran, contoh: `SUB-002-250515090000` |

**Contoh Request**
```
GET /api/passages/subject/SUB-002-250515090000
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar teks bacaan berhasil diambil",
    "data": [
        {
            "kode_passage": "PAS-001-250515143022",
            "kode_subject": "SUB-002-xxx",
            "title": "Bacaan 1 — Pertanian",
            "description": "Untuk soal nomor 1-3, bacalah teks berikut ini dengan seksama",
            "content": "<p>Di sebuah daerah pertanian, perubahan pola curah hujan...</p>",
            "seq": 1,
            "created_at": "2025-05-15T14:30:22.000000Z",
            "updated_at": "2025-05-15T14:30:22.000000Z",
            "subject": {
                "kode_subject": "SUB-002-xxx",
                "name": "Bahasa Indonesia"
            }
        }
    ]
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil data teks bacaan",
    "data": null
}
```

---

## 3. POST /api/passages

Buat passage baru. Setelah passage dibuat, gunakan `kode_passage` yang dikembalikan saat membuat soal via `POST /api/questions`.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_subject` | string | ✅ | FK ke `subjects` |
| `content` | string | ✅ | Isi teks bacaan (HTML dari rich text editor) |
| `title` | string, max:255 | ❌ | Label singkat untuk dropdown di form soal. Contoh: `"Bacaan 1"`, `"Teks Ekosistem"` |
| `description` | string | ❌ | Instruksi yang tampil di atas soal saat ujian. Contoh: `"Untuk soal nomor 1–3, bacalah teks berikut ini dengan seksama"` |
| `seq` | integer ≥ 1 | ❌ | Urutan passage dalam mapel. Default `1` |

**Contoh Request**
```json
{
    "kode_subject": "SUB-002-250515090000",
    "title": "Bacaan 1 — Pertanian",
    "description": "Untuk soal nomor 1-3, bacalah teks berikut ini dengan seksama",
    "content": "<p>Di sebuah daerah pertanian, perubahan pola curah hujan menyebabkan musim tanam menjadi tidak menentu...</p>",
    "seq": 1
}
```

**Response 201 — Berhasil Dibuat**
```json
{
    "statusCode": 201,
    "message": "Teks bacaan berhasil ditambahkan",
    "data": {
        "kode_passage": "PAS-001-250515143022",
        "kode_subject": "SUB-002-250515090000",
        "title": "Bacaan 1 — Pertanian",
        "description": "Untuk soal nomor 1-3, bacalah teks berikut ini dengan seksama",
        "content": "<p>Di sebuah daerah pertanian...</p>",
        "seq": 1,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z",
        "subject": {
            "kode_subject": "SUB-002-xxx",
            "name": "Bahasa Indonesia"
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
        "kode_subject": ["The selected kode subject is invalid."],
        "content": ["The content field is required."]
    }
}
```

**Response 500 — Gagal Ditambahkan**
```json
{
    "statusCode": 500,
    "message": "Teks bacaan gagal ditambahkan",
    "data": null
}
```

---

## 4. GET /api/passages/{kode_passage}

Detail satu passage beserta semua soal yang menggunakannya.

**Headers**
```
Authorization: Bearer {token}
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail teks bacaan berhasil diambil",
    "data": {
        "kode_passage": "PAS-001-250515143022",
        "kode_subject": "SUB-002-xxx",
        "title": "Bacaan 1 — Pertanian",
        "description": "Untuk soal nomor 1-3, bacalah teks berikut ini dengan seksama",
        "content": "<p>Di sebuah daerah pertanian...</p>",
        "seq": 1,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z",
        "subject": {
            "kode_subject": "SUB-002-xxx",
            "name": "Bahasa Indonesia",
            "icon": "book",
            "color": "#F59E0B"
        },
        "questions": [
            {
                "kode_soal": "SOA-001-xxx",
                "seq": 1,
                "question": "<p>Simpulan yang paling tepat berdasarkan bacaan adalah...</p>",
                "type": 1,
                "difficulty": "sedang",
                "options": [
                    { "option_number": 1, "content": "<p>Penurunan pendapatan disebabkan kebijakan pemerintah</p>" },
                    { "option_number": 2, "content": "<p>Perubahan curah hujan berkontribusi terhadap menurunnya pendapatan</p>" }
                ]
            },
            {
                "kode_soal": "SOA-002-xxx",
                "seq": 2,
                "question": "<p>Mengapa pendapatan petani belum mengalami peningkatan?</p>",
                "type": 1,
                "difficulty": "sedang",
                "options": [ { "..." } ]
            }
        ]
    }
}
```

> `correct_answer` dan `discussion` tidak muncul karena disembunyikan secara default.

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Teks bacaan tidak ditemukan",
    "data": null
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Gagal mengambil detail teks bacaan",
    "data": null
}
```

---

## 5. PUT /api/passages/{kode_passage}

Update data passage. Semua field bersifat `sometimes` — kirim hanya yang ingin diubah.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request**

| Field | Tipe | Keterangan |
|-------|------|------------|
| `kode_subject` | string | FK ke `subjects` |
| `title` | string \| null, max:255 | Label singkat untuk dropdown di form soal |
| `description` | string \| null | Instruksi yang tampil di atas soal saat ujian |
| `content` | string | Isi teks bacaan |
| `seq` | integer ≥ 1 | Urutan |

**Contoh Request**
```json
{
    "title": "Bacaan 1 — Pertanian (revisi)",
    "description": "Untuk soal nomor 1-4, bacalah teks berikut ini dengan seksama",
    "content": "<p>Di sebuah daerah pertanian, perubahan pola curah hujan yang ekstrem...</p>"
}
```

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Teks bacaan berhasil diperbarui",
    "data": {
        "kode_passage": "PAS-001-250515143022",
        "kode_subject": "SUB-002-xxx",
        "title": "Bacaan 1 — Pertanian (revisi)",
        "description": "Untuk soal nomor 1-4, bacalah teks berikut ini dengan seksama",
        "content": "<p>Di sebuah daerah pertanian...</p>",
        "seq": 1,
        "created_at": "2025-05-15T09:01:00.000000Z",
        "updated_at": "2025-05-15T16:00:00.000000Z",
        "subject": { "kode_subject": "SUB-002-xxx", "name": "Bahasa Indonesia" }
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Teks bacaan tidak ditemukan",
    "data": null
}
```

**Response 500 — Gagal Diperbarui**
```json
{
    "statusCode": 500,
    "message": "Teks bacaan gagal diperbarui",
    "data": null
}
```

---

## 6. PATCH /api/passages/{kode_passage}/update

Update sebagian field passage. Minimal 1 field harus dikirim.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request** *(minimal 1 field)*

| Field | Tipe | Keterangan |
|-------|------|------------|
| `kode_subject` | string | |
| `title` | string \| null, max:255 | Label singkat untuk dropdown di form soal |
| `description` | string \| null | Instruksi yang tampil di atas soal saat ujian |
| `content` | string | |
| `seq` | integer ≥ 1 | |

**Contoh — Ubah judul saja**
```json
{ "title": "Bacaan 1 — Ekosistem" }
```

**Contoh — Ubah instruksi**
```json
{ "description": "Untuk soal nomor 1-5, bacalah teks berikut ini" }
```

**Contoh — Ubah urutan**
```json
{ "seq": 2 }
```

**Response 200 — Berhasil Diperbarui Sebagian**
```json
{
    "statusCode": 200,
    "message": "Teks bacaan berhasil diperbarui sebagian",
    "data": { "..." }
}
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

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Teks bacaan tidak ditemukan",
    "data": null
}
```

**Response 500 — Server Error**
```json
{
    "statusCode": 500,
    "message": "Teks bacaan gagal diperbarui",
    "data": null
}
```

---

## 7. DELETE /api/passages/{kode_passage}

Hapus passage. **Gagal** jika masih ada soal yang terikat.

> Lepaskan semua soal dari passage terlebih dahulu (set `kode_passage: null` via
> `PATCH /api/questions/{kode_soal}/update`) sebelum menghapus passage.

**Headers**
```
Authorization: Bearer {token}
```

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Teks bacaan berhasil dihapus",
    "data": null
}
```

**Response 409 — Konflik (Masih Ada Soal)**
```json
{
    "statusCode": 409,
    "message": "Teks bacaan tidak bisa dihapus karena masih digunakan oleh 3 soal",
    "data": null
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Teks bacaan tidak ditemukan",
    "data": null
}
```

**Response 500 — Gagal Dihapus**
```json
{
    "statusCode": 500,
    "message": "Teks bacaan gagal dihapus",
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
| `404` | Passage tidak ditemukan |
| `409` | Konflik — passage masih digunakan soal |
| `422` | Validasi gagal |
| `500` | Server error |
