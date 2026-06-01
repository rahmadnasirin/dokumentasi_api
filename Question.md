# API Dokumentasi — Soal (Questions)

> **Base URL:** `/api`
> **Auth:** Bearer Token (Laravel Sanctum)
> **Content-Type:** `application/json`

---

## Tentang Modul Ini

Modul ini mengelola **bank soal global**. Soal terikat ke mata pelajaran (`subject`), bukan ke paket ujian secara langsung. Dengan pendekatan bank soal, satu soal bisa digunakan oleh banyak paket yang menggunakan mata pelajaran yang sama.

**Posisi dalam alur:**
```
subjects           ← soal terikat ke mapel
    ↓
passages           ← [opsional] buat dulu via POST /api/passages → docs: api-passages.md
    ↓
questions          ← dibuat di sini (bank soal), hubungkan ke passage via kode_passage
    ↓
saat ujian mulai:
    package_subjects (total_questions: 15)
        → random ambil 15 soal dari bank soal mapel yang sama
        → snapshot ke question_sesi (urutan tetap per user)
```

> **Penting:** Soal tidak di-assign manual ke paket.
> Cukup pastikan `kode_subject` soal sama dengan `kode_subject` di `package_subjects`.
> Soal yang baru ditambahkan otomatis masuk pool random paket tersebut.

**Modul ini mencakup 2 tabel:**
- `questions` — data soal utama
- `question_options` — pilihan jawaban A–E

> Untuk mengelola teks bacaan (passage), lihat dokumentasi terpisah: **api-passages.md**

---

## Daftar Endpoint

| Method | Endpoint | Fungsi |
|--------|----------|--------|
| GET | `/api/questions` | Ambil bank soal dengan pagination |
| GET | `/api/questions/subject/{kode_subject}` | Ambil soal berdasarkan mata pelajaran |
| POST | `/api/questions` | Buat soal + pilihan jawaban |
| GET | `/api/questions/{kode_soal}` | Detail soal (tanpa jawaban benar) |
| GET | `/api/questions/{kode_soal}/answer` | Detail soal + jawaban benar (admin only) |
| PUT | `/api/questions/{kode_soal}` | Update soal + replace pilihan jawaban |
| PATCH | `/api/questions/{kode_soal}/update` | Update sebagian field soal |
| DELETE | `/api/questions/{kode_soal}` | Hapus soal |
| DELETE | `/api/questions/bulk-delete` | Hapus banyak soal sekaligus |

**Contoh route di `routes/api.php`:**
```php
/* PASSAGE — controller terpisah, tidak ada konflik wildcard */
Route::get('passages',                         [PassageController::class, 'index']);
Route::post('passages',                        [PassageController::class, 'store']);
Route::get('passages/subject/{kode_subject}',  [PassageController::class, 'getBySubject']);
Route::get('passages/{kode_passage}',          [PassageController::class, 'show']);
Route::put('passages/{kode_passage}',          [PassageController::class, 'update']);
Route::patch('passages/{kode_passage}/update', [PassageController::class, 'editData']);
Route::delete('passages/{kode_passage}',       [PassageController::class, 'destroy']);

/* QUESTION */
Route::delete('questions/bulk-delete',              [QuestionController::class, 'bulkDelete']);
Route::get('questions',                             [QuestionController::class, 'index']);
Route::post('questions',                            [QuestionController::class, 'store']);
Route::get('questions/subject/{kode_subject}',      [QuestionController::class, 'getSubject']);
Route::get('questions/{kode_soal}',                 [QuestionController::class, 'show']);
Route::get('questions/{kode_soal}/answer',          [QuestionController::class, 'showAnswer']);
Route::put('questions/{kode_soal}',                 [QuestionController::class, 'update']);
Route::patch('questions/{kode_soal}/update',        [QuestionController::class, 'editData']);
Route::delete('questions/{kode_soal}',              [QuestionController::class, 'destroy']);
```

---

## Alur Membuat Soal dengan Passage

```
1. POST /api/passages
   → dapat kode_passage: "PAS-001-xxx"

2. POST /api/questions  (soal ke-1)
   body: { kode_passage: "PAS-001-xxx", seq: 1, ... }

3. POST /api/questions  (soal ke-2, share bacaan yang sama)
   body: { kode_passage: "PAS-001-xxx", seq: 2, ... }
```

> Buat passage terlebih dahulu via `POST /api/passages`, lalu gunakan `kode_passage` yang dikembalikan saat membuat soal.

---

## Logika Form Input Soal di Frontend

**Urutan load data sebelum form dibuka:**
```
1. GET /api/subjects                            → populate dropdown "Pilih Mapel"
2. GET /api/passages/subject/{kode_subject}     → populate dropdown "Pakai bacaan yang sudah ada"
   (dipanggil setelah mapel dipilih)
```

**Struktur form berdasarkan type:**
```
[Pilih Mapel]          ← dropdown hasil GET /api/subjects

[Teks Bacaan]          ← pilih salah satu:
  ○ Tidak ada (soal mandiri)
  ○ Pakai yang sudah ada  ← dropdown hasil GET /api/passages/subject/{kode_subject}
                            label dropdown = passage.title
  ○ Buat baru             ← buat dulu via POST /api/passages,
                            lalu kirim kode_passage ke form soal

[Teks Soal]            ← rich text editor (support HTML, gambar, LaTeX)

[Jenis Soal]
  ● Pilihan Ganda (type=1)   ← default
  ○ Esai (type=2)

─── jika type = 1 (Pilihan Ganda) ───────────────────────────
[Pilihan Jawaban]
  A: [rich text editor]   ← option_number: 1
  B: [rich text editor]   ← option_number: 2
  C: [rich text editor]   ← option_number: 3
  D: [rich text editor]   ← option_number: 4
  E: [rich text editor]   ← option_number: 5
  [+ Tambah Pilihan]      ← bisa sampai 9 pilihan (F–I)

[Jawaban Benar]         ← radio button: A / B / C / D / E
                          frontend simpan option_number yang dipilih
                          contoh: pilih C → kirim correct_answer: 3
─────────────────────────────────────────────────────────────

─── jika type = 2 (Esai) ────────────────────────────────────
[Jawaban Benar]         ← textarea biasa
                          dikirim sebagai correct_answer_essay
─────────────────────────────────────────────────────────────

[Tingkat Kesulitan]     ← select: Mudah / Sedang / Sulit
[Bab/Chapter]           ← input text
[Tahun Soal]            ← input number
[Pembahasan]            ← rich text editor (isi jawaban + penjelasan)
[Lampiran File]         ← upload gambar → simpan path ke url_file
[Lampiran Video]        ← input URL → url_video
[Lampiran PDF]          ← input URL → url_pdf
```

**Validasi frontend sebelum submit:**
```javascript
if (type == 1) {
    // Pastikan minimal 2 pilihan terisi
    // Pastikan jawaban benar (correct_answer) sudah dipilih
    // Pastikan correct_answer sesuai salah satu option_number
    // Jangan kirim correct_answer_essay
} else {
    // Kosongkan array options sebelum kirim (atau jangan kirim sama sekali)
    // Kosongkan correct_answer
}
```

---

# SOAL (Questions)

---

## 1. GET /api/questions

Ambil bank soal dengan pagination. Default 20 soal per halaman. Sudah include relasi `subject`, `passage`, dan `options`.

> `correct_answer` dan `discussion` **tidak ikut** di response ini.
> Gunakan endpoint `/answer` khusus untuk admin.

**Query Parameters** *(semua opsional)*

| Parameter | Keterangan |
|-----------|------------|
| `kode_subject` | Filter by kode mata pelajaran |
| `type` | `1` = pilihan ganda, `2` = esai |
| `difficulty` | `mudah` `sedang` `sulit` |
| `tahun` | Filter by tahun soal |
| `chapter` | Filter by bab/topik |
| `has_passage` | `1` = hanya soal dengan bacaan, `0` = tanpa bacaan |
| `per_page` | Jumlah per halaman, default `20` |

**Contoh Request**
```
GET /api/questions?kode_subject=SUB-001-xxx
GET /api/questions?kode_subject=SUB-001-xxx&difficulty=mudah&tahun=2025
GET /api/questions?has_passage=1&per_page=10
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar soal berhasil diambil",
    "data": {
        "list": [
            {
                "kode_soal": "SOA-001-250515143022",
                "kode_subject": "SUB-001-xxx",
                "kode_passage": null,
                "seq": 1,
                "chapter": "Aljabar",
                "question": "<p>Hasil dari 3² + 4² adalah...</p>",
                "url_file": null,
                "score": 1.0,
                "type": 1,
                "difficulty": "mudah",
                "tahun": 2025,
                "subject": { "name": "Matematika SMA", "color": "#3B82F6" },
                "passage": null,
                "options": [
                    { "option_number": 1, "content": "<p>12</p>" },
                    { "option_number": 2, "content": "<p>20</p>" },
                    { "option_number": 3, "content": "<p>25</p>" },
                    { "option_number": 4, "content": "<p>30</p>" },
                    { "option_number": 5, "content": "<p>49</p>" }
                ]
            }
        ],
        "pagination": {
            "total": 500,
            "perPage": 20,
            "currentPage": 1,
            "lastPage": 25,
            "from": 1,
            "to": 20,
            "hasMorePages": true
        }
    }
}
```

---

## 2. GET /api/questions/subject/{kode_subject}

Ambil semua soal berdasarkan mata pelajaran tertentu, dengan pagination. Cocok digunakan untuk halaman **bank soal per mapel**, preview soal sebelum paket dibuat, atau manajemen soal dari sisi admin mapel.

Relasi `passage` dan `options` sudah disertakan. Relasi `subject` **tidak disertakan** karena sudah diketahui dari parameter URL.

> `correct_answer` dan `discussion` **tidak ikut** di response ini.

**URL Parameter**

| Parameter | Keterangan |
|-----------|------------|
| `kode_subject` | *(wajib)* Kode mata pelajaran yang ingin diambil soalnya |

**Query Parameters** *(semua opsional)*

| Parameter | Keterangan |
|-----------|------------|
| `type` | `1` = pilihan ganda, `2` = esai |
| `difficulty` | `mudah` `sedang` `sulit` |
| `tahun` | Filter by tahun soal |
| `chapter` | Filter by bab/topik |
| `per_page` | Jumlah per halaman, default `20` |

**Contoh Request**
```
GET /api/questions/subject/SUB-001-xxx
GET /api/questions/subject/SUB-001-xxx?difficulty=mudah
GET /api/questions/subject/SUB-001-xxx?type=1&tahun=2025&per_page=10
```

**Perbedaan dengan `GET /api/questions`**

| | `GET /api/questions` | `GET /api/questions/subject/{kode_subject}` |
|---|---|---|
| Filter mapel | Query param `?kode_subject=` | URL param (wajib) |
| Relasi `subject` | ✅ Disertakan | ❌ Tidak disertakan (redundant) |
| Hasil kosong | Kembalikan list kosong | Return `404` |
| Use case | List semua soal lintas mapel | Fokus soal satu mapel |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar soal berhasil diambil",
    "data": {
        "list": [
            {
                "kode_soal": "SOA-001-250515143022",
                "kode_subject": "SUB-001-xxx",
                "kode_passage": null,
                "seq": 1,
                "chapter": "Aljabar",
                "question": "<p>Hasil dari 3² + 4² adalah...</p>",
                "url_file": null,
                "score": 1.0,
                "type": 1,
                "difficulty": "mudah",
                "tahun": 2025,
                "passage": null,
                "options": [
                    { "option_number": 1, "content": "<p>12</p>" },
                    { "option_number": 2, "content": "<p>20</p>" },
                    { "option_number": 3, "content": "<p>25</p>" },
                    { "option_number": 4, "content": "<p>30</p>" },
                    { "option_number": 5, "content": "<p>49</p>" }
                ]
            },
            {
                "kode_soal": "SOA-002-250515143045",
                "kode_subject": "SUB-001-xxx",
                "kode_passage": "PAS-001-250515143022",
                "seq": 2,
                "chapter": "Geometri",
                "question": "<p>Luas segitiga dengan alas 6 dan tinggi 4 adalah...</p>",
                "url_file": null,
                "score": 1.0,
                "type": 1,
                "difficulty": "sedang",
                "tahun": 2025,
                "passage": {
                    "kode_passage": "PAS-001-250515143022",
                    "title": "Bacalah teks berikut",
                    "content": "<p>Sebuah taman berbentuk segitiga...</p>",
                    "seq": 1
                },
                "options": [
                    { "option_number": 1, "content": "<p>10</p>" },
                    { "option_number": 2, "content": "<p>12</p>" },
                    { "option_number": 3, "content": "<p>14</p>" },
                    { "option_number": 4, "content": "<p>16</p>" },
                    { "option_number": 5, "content": "<p>24</p>" }
                ]
            }
        ],
        "pagination": {
            "total": 85,
            "perPage": 20,
            "currentPage": 1,
            "lastPage": 5,
            "from": 1,
            "to": 20,
            "hasMorePages": true
        }
    }
}
```

**Response 404 — Tidak Ada Soal**
```json
{
    "statusCode": 404,
    "message": "Soal untuk subject ini tidak ditemukan",
    "data": null
}
```

---

## 3. POST /api/questions

Buat soal baru. Dalam **satu request** bisa sekaligus:
- Buat soal utama
- Buat pilihan jawaban A–E
- Hubungkan ke passage yang sudah ada via `kode_passage` (opsional)

`kode_soal` di-generate otomatis dengan format `SOA-{sequential}-{timestamp}`.

> Untuk soal yang menggunakan passage, buat passage terlebih dahulu via `POST /api/passages`,
> lalu gunakan `kode_passage` yang dikembalikan di request ini.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_subject` | string | ✅ | FK ke `subjects` |
| `question` | string | ✅ | Teks soal (HTML dari rich text editor, support gambar & LaTeX) |
| `type` | integer | ❌ | Default `1` (pilihan ganda). `1`=pilgan `2`=esai |
| `correct_answer` | integer 1–9 | ✅* | Wajib jika `type=1`. Harus sesuai salah satu `option_number` yang dikirim |
| `options` | array | ✅* | Wajib jika `type=1`. Min 2, max 9 pilihan |
| `options.*.option_number` | integer 1–9 | ✅ | Nomor pilihan (1=A, 2=B, 3=C, dst) |
| `options.*.content` | string | ✅ | Isi pilihan jawaban (HTML) |
| `kode_passage` | string | ❌ | Hubungkan ke teks bacaan yang sudah ada |
| `seq` | integer ≥ 1 | ❌ | Urutan soal dalam kelompok passage |
| `difficulty` | string | ❌ | `mudah` `sedang` `sulit`. Tampil sebagai badge di pembahasan |
| `tahun` | integer 2000–2099 | ❌ | Tahun soal — berguna untuk filter hapus soal lama per tahun |
| `chapter` | string, max:100 | ❌ | Bab atau topik soal |
| `discussion` | string | ❌ | Pembahasan soal (HTML). Hanya tampil setelah sesi ujian selesai |
| `score` | float | ❌ | Nilai jika menjawab benar. Default `1.0` |
| `correct_answer_essay` | string | ❌ | Jawaban benar untuk soal esai (`type=2`) |
| `url_file` | string | ❌ | Path lampiran gambar/file tambahan |
| `url_video` | string | ❌ | Path lampiran video |
| `url_pdf` | string | ❌ | Path lampiran PDF |

> **Catatan penting perbedaan type=1 dan type=2:**
>
> | | type=1 (Pilihan Ganda) | type=2 (Esai) |
> |---|---|---|
> | `options` | **Wajib** (min 2, max 9) | Tidak dikirim |
> | `correct_answer` | **Wajib** (1–9, sesuai option_number) | Tidak dikirim |
> | `correct_answer_essay` | Tidak dikirim | Opsional |
> | Form di frontend | Muncul input pilihan A–E + radio jawaban benar | Muncul textarea jawaban benar |

**Skenario 1 — Soal mandiri tanpa bacaan**
```json
{
    "kode_subject": "SUB-001-xxx",
    "question": "<p>Hasil dari 3² + 4² adalah...</p>",
    "type": 1,
    "correct_answer": 3,
    "difficulty": "mudah",
    "tahun": 2025,
    "chapter": "Aljabar",
    "discussion": "<p>3²=9, 4²=16, maka 9+16=<strong>25</strong>.</p>",
    "score": 1.0,
    "options": [
        { "option_number": 1, "content": "<p>12</p>" },
        { "option_number": 2, "content": "<p>20</p>" },
        { "option_number": 3, "content": "<p>25</p>" },
        { "option_number": 4, "content": "<p>30</p>" },
        { "option_number": 5, "content": "<p>49</p>" }
    ]
}
```

**Skenario 2 — Soal dengan teks bacaan**
```json
{
    "kode_subject": "SUB-002-xxx",
    "kode_passage": "PAS-001-250515143022",
    "seq": 1,
    "question": "<p>Simpulan yang paling tepat berdasarkan bacaan adalah...</p>",
    "type": 1,
    "correct_answer": 2,
    "difficulty": "sedang",
    "tahun": 2025,
    "options": [
        { "option_number": 1, "content": "<p>Penurunan pendapatan disebabkan oleh kebijakan pemerintah</p>" },
        { "option_number": 2, "content": "<p>Perubahan curah hujan berkontribusi terhadap menurunnya pendapatan</p>" },
        { "option_number": 3, "content": "<p>Bantuan pupuk terbukti meningkatkan pendapatan</p>" },
        { "option_number": 4, "content": "<p>Petani belum mampu memanfaatkan bantuan secara optimal</p>" },
        { "option_number": 5, "content": "<p>Pemerintah memberikan bantuan setelah pendapatan meningkat</p>" }
    ]
}
```

**Skenario 3 — Soal esai (type=2)**
```json
{
    "kode_subject": "SUB-001-xxx",
    "question": "<p>Jelaskan pengertian dan contoh penerapan teorema Pythagoras dalam kehidupan sehari-hari!</p>",
    "type": 2,
    "correct_answer_essay": "Teorema Pythagoras menyatakan bahwa pada segitiga siku-siku, kuadrat sisi miring sama dengan jumlah kuadrat dua sisi lainnya (a² + b² = c²).",
    "difficulty": "sedang",
    "tahun": 2025,
    "chapter": "Geometri",
    "discussion": "<p>Teorema Pythagoras: <strong>a² + b² = c²</strong>. Contoh: layar TV 32 inci berarti diagonal 32 inci...</p>"
}
```

> Soal esai **tidak mengirim `options`** dan **tidak mengirim `correct_answer`**.

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Soal berhasil ditambahkan",
    "data": {
        "kode_soal": "SOA-001-250515143022",
        "kode_subject": "SUB-001-xxx",
        "kode_passage": null,
        "seq": 1,
        "chapter": "Aljabar",
        "question": "<p>Hasil dari 3² + 4² adalah...</p>",
        "type": 1,
        "difficulty": "mudah",
        "tahun": 2025,
        "score": 1.0,
        "subject": { "name": "Matematika SMA" },
        "passage": null,
        "options": [
            { "kode_opsi": "OPT-001-xxx", "option_number": 1, "content": "<p>12</p>" },
            { "kode_opsi": "OPT-002-xxx", "option_number": 2, "content": "<p>20</p>" },
            { "kode_opsi": "OPT-003-xxx", "option_number": 3, "content": "<p>25</p>" },
            { "kode_opsi": "OPT-004-xxx", "option_number": 4, "content": "<p>30</p>" },
            { "kode_opsi": "OPT-005-xxx", "option_number": 5, "content": "<p>49</p>" }
        ]
    }
}
```

**Response 422 — Jawaban Benar Tidak Sesuai Pilihan**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "correct_answer": ["Jawaban benar harus sesuai dengan salah satu nomor pilihan yang ada"]
    }
}
```

---

## 4. GET /api/questions/{kode_soal}

Detail satu soal. `correct_answer` dan `discussion` **tidak ikut** di response ini — sengaja disembunyikan agar tidak bocor ke user saat ujian berlangsung.

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail soal berhasil diambil",
    "data": {
        "kode_soal": "SOA-001-xxx",
        "question": "<p>Hasil dari 3² + 4² adalah...</p>",
        "type": 1,
        "difficulty": "mudah",
        "tahun": 2025,
        "subject": { "name": "Matematika SMA" },
        "passage": null,
        "options": [ { "..." } ]
    }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Soal tidak ditemukan",
    "data": null
}
```

---

## 5. GET /api/questions/{kode_soal}/answer

Detail soal **beserta** `correct_answer` dan `discussion`. Khusus admin, atau dipakai oleh frontend saat menampilkan halaman pembahasan setelah sesi ujian selesai.

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail soal dengan jawaban berhasil diambil",
    "data": {
        "kode_soal": "SOA-001-xxx",
        "question": "<p>Hasil dari 3² + 4² adalah...</p>",
        "correct_answer": 3,
        "discussion": "<p>3²=9, 4²=16, maka 9+16=<strong>25</strong>.</p>",
        "difficulty": "mudah",
        "options": [
            { "option_number": 1, "content": "<p>12</p>" },
            { "option_number": 2, "content": "<p>20</p>" },
            { "option_number": 3, "content": "<p>25</p>" },
            { "option_number": 4, "content": "<p>30</p>" },
            { "option_number": 5, "content": "<p>49</p>" }
        ]
    }
}
```

---

## 6. PUT /api/questions/{kode_soal}

Update soal. Semua field bersifat `sometimes`. Jika `options` dikirim → **semua pilihan lama dihapus** dan diganti dengan pilihan baru yang dikirim.

> **Perhatian saat update `type`:**
> Jika mengubah dari `type=1` ke `type=2` (atau sebaliknya), pastikan
> menyesuaikan field yang dikirim — hapus `options` + `correct_answer` jika
> ganti ke esai, atau kirim `options` + `correct_answer` baru jika ganti ke pilihan ganda.

**Body Request** — semua field opsional (`sometimes`):

| Field | Tipe | Keterangan |
|-------|------|------------|
| `kode_subject` | string | |
| `kode_passage` | string \| null | Set `null` untuk melepas dari passage |
| `seq` | integer ≥ 1 | |
| `chapter` | string \| null | |
| `tahun` | integer \| null | |
| `question` | string | |
| `discussion` | string \| null | |
| `url_file` | string \| null | |
| `url_video` | string \| null | |
| `url_pdf` | string \| null | |
| `correct_answer` | integer \| null | |
| `correct_answer_essay` | string \| null | |
| `score` | float ≥ 0 | |
| `type` | integer | `1` atau `2` |
| `difficulty` | string \| null | |
| `keterangan` | string \| null | |
| `options` | array | Jika dikirim, **menggantikan semua opsi lama** |

**Contoh — update teks soal + replace semua pilihan**
```json
{
    "question": "<p>Nilai dari 2³ + 3² adalah...</p>",
    "correct_answer": 2,
    "options": [
        { "option_number": 1, "content": "<p>15</p>" },
        { "option_number": 2, "content": "<p>17</p>" },
        { "option_number": 3, "content": "<p>18</p>" },
        { "option_number": 4, "content": "<p>19</p>" },
        { "option_number": 5, "content": "<p>20</p>" }
    ]
}
```

**Contoh — update teks saja tanpa ubah pilihan jawaban**
```json
{
    "question": "<p>Hasil dari 2³ + 3² adalah... (diperbarui)</p>"
}
```

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Soal berhasil diperbarui",
    "data": { "..." }
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Soal tidak ditemukan",
    "data": null
}
```

---

## 7. PATCH /api/questions/{kode_soal}/update

Update **sebagian field** saja. Minimal 1 field harus dikirim. Tidak bisa mengganti `options` lewat endpoint ini — gunakan `PUT` untuk itu.

**Body Request** *(minimal 1 field)*

| Field | Tipe | Keterangan |
|-------|------|------------|
| `kode_passage` | string \| null | Set `null` untuk melepas dari passage |
| `seq` | integer ≥ 1 | |
| `chapter` | string \| null | |
| `tahun` | integer \| null | |
| `question` | string | |
| `discussion` | string \| null | |
| `url_file` | string \| null | |
| `url_video` | string \| null | |
| `url_pdf` | string \| null | |
| `correct_answer` | integer \| null | |
| `score` | float ≥ 0 | |
| `difficulty` | string \| null | |
| `keterangan` | string \| null | |

**Contoh — koreksi jawaban benar**
```json
{ "correct_answer": 3 }
```

**Contoh — update tahun dan chapter**
```json
{ "tahun": 2026, "chapter": "Fungsi Kuadrat" }
```

**Contoh — ubah tingkat kesulitan**
```json
{ "difficulty": "sulit" }
```

**Contoh — lepas dari bacaan (jadikan soal mandiri)**
```json
{ "kode_passage": null }
```

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Soal berhasil diperbarui sebagian",
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

---

## 8. DELETE /api/questions/{kode_soal}

Hapus soal. Pilihan jawaban ikut terhapus otomatis (cascade di database).

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Soal berhasil dihapus",
    "data": null
}
```

**Response 404 — Tidak Ditemukan**
```json
{
    "statusCode": 404,
    "message": "Soal tidak ditemukan",
    "data": null
}
```

---

## 9. DELETE /api/questions/bulk-delete

Hapus banyak soal sekaligus berdasarkan kriteria. Wajib kirim minimal 1 kriteria. Pilihan jawaban ikut terhapus otomatis (cascade).

> Sangat berguna untuk admin yang ingin **membersihkan soal lama per tahun**
> tanpa perlu hapus satu-satu.

**Body Request** *(minimal 1 field)*

| Field | Tipe | Keterangan |
|-------|------|------------|
| `kode_soal` | array of string | Hapus soal spesifik by kode |
| `kode_subject` | string | Hapus semua soal mapel tertentu |
| `tahun` | integer | Hapus semua soal tahun tertentu |

> Kriteria bisa dikombinasikan:
> `{ "kode_subject": "SUB-001-xxx", "tahun": 2023 }` = hapus soal Matematika tahun 2023 saja.

**Contoh — hapus soal tahun lama**
```json
{ "tahun": 2023 }
```

**Contoh — hapus soal mapel + tahun tertentu**
```json
{
    "kode_subject": "SUB-001-250515090000",
    "tahun": 2023
}
```

**Contoh — hapus soal spesifik**
```json
{
    "kode_soal": ["SOA-001-xxx", "SOA-002-xxx", "SOA-005-xxx"]
}
```

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "45 soal berhasil dihapus",
    "data": null
}
```

**Response 422 — Tidak Ada Kriteria**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "field": ["Wajib kirim salah satu: kode_soal, kode_subject, atau tahun"]
    }
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
| `404` | Soal / subject tidak ditemukan |
| `422` | Validasi gagal |
| `500` | Server error |
