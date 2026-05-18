# API Dokumentasi — Subjects & Exam System

> **Base URL:** `/api`
> **Auth:** Bearer Token (Laravel Sanctum)
> **Content-Type:** `application/json`

---

## Daftar Tabel (Schema)

### `subjects_categories`
```sql
Schema::create('subjects_categories', function (Blueprint $table) {
    $table->string('kode_kategori')->primary(); // CAT-001-250515090000
    $table->string('name');                     // "TKD", "TKA Saintek", "TKA Soshum"
    $table->string('slug')->unique();           // "tkd", "tka-saintek", "tka-soshum"
    $table->string('description')->nullable();  // Deskripsi singkat (opsional)
    $table->integer('seq')->default(1);         // Urutan tampil di UI
    $table->timestamps();
});
```

---

## Daftar Endpoint

| Method | Endpoint | Controller | Fungsi |
|--------|----------|------------|--------|
| GET | `/api/category` | `CategoryController@index` | Ambil semua kategori |
| POST | `/api/category` | `CategoryController@store` | Tambah kategori baru |
| GET | `/api/category/{kode_kategori}` | `CategoryController@show` | Detail satu kategori |
| PUT | `/api/category/{kode_kategori}` | `CategoryController@update` | Update semua field |
| PATCH | `/api/category/{kode_kategori}/update` | `CategoryController@editData` | Update sebagian field |
| DELETE | `/api/category/{kode_kategori}` | `CategoryController@destroy` | Hapus kategori |
| GET | `/api/subjects` | `SubjectController@index` | Ambil semua mata pelajaran |
| POST | `/api/subjects` | `SubjectController@store` | Tambah mata pelajaran baru |
| GET | `/api/subjects/{kode_subject}` | `SubjectController@show` | Detail satu mata pelajaran |
| PUT | `/api/subjects/{kode_subject}` | `SubjectController@update` | Update semua field |
| PATCH | `/api/subjects/{kode_subject}/update` | `SubjectController@editData` | Update sebagian field |
| DELETE | `/api/subjects/{kode_subject}` | `SubjectController@destroy` | Hapus mata pelajaran |
| GET | `/api/feature` | `FeatureController@index` | Ambil semua paket ujian |
| POST | `/api/feature` | `FeatureController@store` | Tambah paket ujian baru |
| GET | `/api/feature/{kode_feature}` | `FeatureController@show` | Detail satu paket ujian |
| PUT | `/api/feature{kode_feature}` | `FeatureController@update` | Update semua field |
| PATCH | `/api/feature{kode_feature}/update` | `FeatureController@editData` | Update sebagian field |
| DELETE | `/api/feature/{kode_feature}` | `FeatureController@destroy` | Hapus paket ujian |
| GET | `/api/feature/{kode_feature}/package-subjects` | `PackageSubjectController@index` | Ambil semua package subject |
| POST | `/api/feature/{kode_feature}/package-subjects` | `PackageSubjectController@store` | Tambah package subject |
| GET | `/api/feature/{kode_feature}/package-subjects/{kode_package_subject}` | `PackageSubjectController@show` | Detail satu package subject |
| PUT | `/api/feature/{kode_feature}/package-subjects/{kode_package_subject}` | `PackageSubjectController@update` | Update semua field |
| PATCH | `/api/feature/{kode_feature}/package-subjects/{kode_package_subject}/update` | `PackageSubjectController@editData` | Update sebagian field |
| DELETE | `/api/feature/{kode_feature}/package-subjects/{kode_package_subject}` | `PackageSubjectController@destroy` | Hapus package subject |
| GET | `/api/questions/passages` | `QuestionController@getPassages` | Ambil daftar passage |
| DELETE | `/api/questions/bulk-delete` | `QuestionController@bulkDelete` | Hapus banyak soal sekaligus |
| GET | `/api/questions` | `QuestionController@index` | Ambil semua soal |
| POST | `/api/questions` | `QuestionController@store` | Tambah soal baru |
| GET | `/api/questions/{kode_soal}` | `QuestionController@show` | Detail satu soal |
| GET | `/api/questions/{kode_soal}/answer` | `QuestionController@showAnswer` | Lihat jawaban soal |
| PUT | `/api/questions/{kode_soal}` | `QuestionController@update` | Update semua field |
| PATCH | `/api/questions/{kode_soal}/update` | `QuestionController@editData` | Update sebagian field |
| DELETE | `/api/questions/{kode_soal}` | `QuestionController@destroy` | Hapus soal |

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

---

## A. Subjects Categories

### 1. GET /api/category

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

### 2. POST /api/category

Menambahkan kategori mapel baru. `kode_kategori` di-generate otomatis oleh server dengan format `CAT-{sequential}-{timestamp}`.

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:100 | ✅ Ya | Nama kategori |
| `slug` | string, max:100, unique | ❌ Opsional | Jika kosong, di-generate otomatis dari `name` |
| `description` | string, max:255 | ❌ Opsional | Deskripsi singkat |
| `seq` | integer, min:1 | ❌ Opsional | Urutan tampil; jika kosong otomatis urutan terakhir + 1 |

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
        "name": ["The name field is required."],
        "slug": ["The slug has already been taken."]
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

> **Catatan format kode:** `CAT-003-250515143022`
> - `CAT` = prefix tabel
> - `003` = nomor sequential dari kode terakhir di database (increment +1)
> - `250515143022` = timestamp saat data dibuat (format `ymdhis`)

---

### 3. GET /api/category/{kode_kategori}

Mengambil detail satu kategori beserta daftar mata pelajaran aktif (`is_active = true`) yang terikat, diurutkan berdasarkan `name`.

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

### 4. PUT /api/category/{kode_kategori}

Memperbarui data kategori. Semua field bersifat `sometimes` — hanya kirim field yang ingin diubah. `kode_kategori` tidak bisa diubah setelah dibuat.

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

**Contoh Request**
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
        "slug": ["The slug has already been taken."]
    }
}
```

---

### 5. PATCH /api/category/{kode_kategori}/update

Update sebagian field saja (partial update). Minimal harus ada 1 field yang dikirim.

**Kapan pakai `PATCH` vs `PUT`?**

| Skenario | Gunakan |
|----------|---------|
| Ubah urutan tampil saja | `PATCH` |
| Ubah deskripsi saja | `PATCH` |
| Update semua field sekaligus | `PUT` |

**Headers**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_kategori` | string | Kode kategori yang ingin diupdate sebagian |

**Body Request** *(minimal 1 field)*

| Field | Tipe | Keterangan |
|-------|------|------------|
| `name` | string, max:100 | Jika diisi, slug di-regenerate otomatis |
| `slug` | string, max:100, unique | Jika kosong saat `name` diubah, slug otomatis di-regenerate |
| `description` | string / null | Deskripsi kategori |
| `seq` | integer, min:1 | Urutan tampil |

**Contoh Request**
```json
{
    "seq": 3
}
```

**Response 200 — Berhasil Diperbarui Sebagian**
```json
{
    "statusCode": 200,
    "message": "Kategori mapel berhasil diperbarui sebagian",
    "data": {
        "kode_kategori": "CAT-001-250515090000",
        "name": "Tes Kemampuan Dasar",
        "slug": "tkd",
        "description": "Tes Kemampuan Dasar untuk jalur SAINTEK dan SOSHUM",
        "seq": 3,
        "created_at": "2025-05-15T09:00:00.000000Z",
        "updated_at": "2025-05-15T16:00:00.000000Z"
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
    "message": "Kategori tidak ditemukan",
    "data": null
}
```

---

### 6. DELETE /api/category/{kode_kategori}

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

## B. Subjects

> Dokumentasi detail untuk `SubjectController` — struktur endpoint mengikuti pola yang sama seperti Category. Semua request memerlukan `Authorization: Bearer {token}`.

### 1. GET /api/subjects

Mengambil semua mata pelajaran.

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar mata pelajaran berhasil diambil",
    "data": [
        {
            "kode_subject": "SUB-001-250515090100",
            "kode_kategori": "CAT-001-250515090000",
            "name": "Matematika Dasar",
            "slug": "matematika-dasar",
            "icon": "calculator",
            "color": "#3B82F6",
            "is_active": true,
            "created_at": "2025-05-15T09:01:00.000000Z",
            "updated_at": "2025-05-15T09:01:00.000000Z"
        }
    ]
}
```

---

### 2. POST /api/subjects

Menambahkan mata pelajaran baru. `kode_subject` di-generate otomatis oleh server.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_kategori` | string | ✅ Ya | Kode kategori yang terikat |
| `name` | string, max:100 | ✅ Ya | Nama mata pelajaran |
| `slug` | string, max:100, unique | ❌ Opsional | Jika kosong, di-generate dari `name` |
| `icon` | string | ❌ Opsional | Nama icon (e.g. `calculator`, `book`) |
| `color` | string | ❌ Opsional | Hex color (e.g. `#3B82F6`) |
| `is_active` | boolean | ❌ Opsional | Default `true` |

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Mata pelajaran berhasil ditambahkan",
    "data": {
        "kode_subject": "SUB-003-250515143022",
        "kode_kategori": "CAT-001-250515090000",
        "name": "Bahasa Inggris",
        "slug": "bahasa-inggris",
        "icon": "globe",
        "color": "#10B981",
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
        "name": ["The name field is required."],
        "kode_kategori": ["The selected kode_kategori is invalid."]
    }
}
```

---

### 3. GET /api/subjects/{kode_subject}

Mengambil detail satu mata pelajaran.

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_subject` | string | Kode subject, contoh: `SUB-001-250515090100` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail mata pelajaran berhasil diambil",
    "data": {
        "kode_subject": "SUB-001-250515090100",
        "kode_kategori": "CAT-001-250515090000",
        "name": "Matematika Dasar",
        "slug": "matematika-dasar",
        "icon": "calculator",
        "color": "#3B82F6",
        "is_active": true,
        "created_at": "2025-05-15T09:01:00.000000Z",
        "updated_at": "2025-05-15T09:01:00.000000Z",
        "category": {
            "kode_kategori": "CAT-001-250515090000",
            "name": "Tes Kemampuan Dasar",
            "slug": "tkd"
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

---

### 4. PUT /api/subjects/{kode_subject}

Memperbarui data mata pelajaran. Semua field bersifat `sometimes`.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_kategori` | string | ❌ Opsional | Pindah ke kategori lain |
| `name` | string, max:100 | ❌ Opsional | |
| `slug` | string, max:100, unique | ❌ Opsional | |
| `icon` | string | ❌ Opsional | |
| `color` | string | ❌ Opsional | |
| `is_active` | boolean | ❌ Opsional | |

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Mata pelajaran berhasil diperbarui",
    "data": { }
}
```

---

### 5. PATCH /api/subjects/{kode_subject}/update

Update sebagian field saja. Minimal 1 field harus dikirim.

**Body Request** *(minimal 1 field — sama seperti PUT)*

**Response 200 — Berhasil Diperbarui Sebagian**
```json
{
    "statusCode": 200,
    "message": "Mata pelajaran berhasil diperbarui sebagian",
    "data": { }
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

---

### 6. DELETE /api/subjects/{kode_subject}

Menghapus mata pelajaran.

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Mata pelajaran berhasil dihapus",
    "data": null
}
```

**Response 409 — Konflik**
```json
{
    "statusCode": 409,
    "message": "Mata pelajaran tidak bisa dihapus karena masih digunakan",
    "data": null
}
```

---

## C. Exam Packages (Feature)

> Endpoint untuk mengelola paket ujian / feature. Semua request memerlukan `Authorization: Bearer {token}`.
>
> ⚠️ **Catatan:** Pada route `PUT` dan `PATCH`, terdapat typo pada definisi route — tidak ada `/` antara `feature` dan `{kode_feature}` (`feature{kode_feature}`). Pastikan dikonfirmasi ke backend developer.

### 1. GET /api/feature

Mengambil semua paket ujian.

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar feature berhasil diambil",
    "data": [
        {
            "kode_feature": "FTR-001-250515090000",
            "name": "Simulasi SNBT 2025",
            "slug": "simulasi-snbt-2025",
            "description": "Paket simulasi lengkap SNBT 2025",
            "is_active": true,
            "created_at": "2025-05-15T09:00:00.000000Z",
            "updated_at": "2025-05-15T09:00:00.000000Z"
        }
    ]
}
```

---

### 2. POST /api/feature

Menambahkan paket ujian baru.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:100 | ✅ Ya | Nama paket |
| `slug` | string, max:100, unique | ❌ Opsional | Jika kosong, di-generate dari `name` |
| `description` | string, max:255 | ❌ Opsional | Deskripsi singkat |
| `is_active` | boolean | ❌ Opsional | Default `true` |

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Feature berhasil ditambahkan",
    "data": {
        "kode_feature": "FTR-001-250515143022",
        "name": "Simulasi SNBT 2025",
        "slug": "simulasi-snbt-2025",
        "description": "Paket simulasi lengkap SNBT 2025",
        "is_active": true,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z"
    }
}
```

---

### 3. GET /api/feature/{kode_feature}

Mengambil detail satu paket ujian beserta daftar package subject yang terikat.

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_feature` | string | Kode feature, contoh: `FTR-001-250515090000` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail feature berhasil diambil",
    "data": {
        "kode_feature": "FTR-001-250515090000",
        "name": "Simulasi SNBT 2025",
        "slug": "simulasi-snbt-2025",
        "description": "Paket simulasi lengkap SNBT 2025",
        "is_active": true,
        "created_at": "2025-05-15T09:00:00.000000Z",
        "updated_at": "2025-05-15T09:00:00.000000Z",
        "package_subjects": [ ]
    }
}
```

---

### 4. PUT /api/feature{kode_feature}

Memperbarui data paket ujian.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `name` | string, max:100 | ❌ Opsional | |
| `slug` | string, max:100, unique | ❌ Opsional | |
| `description` | string, max:255 | ❌ Opsional | |
| `is_active` | boolean | ❌ Opsional | |

---

### 5. PATCH /api/feature{kode_feature}/update

Update sebagian field saja. Minimal 1 field harus dikirim.

---

### 6. DELETE /api/feature/{kode_feature}

Menghapus paket ujian. Tidak bisa dihapus jika masih memiliki package subject yang terikat.

**Response 409 — Konflik**
```json
{
    "statusCode": 409,
    "message": "Feature tidak bisa dihapus karena masih memiliki package subject",
    "data": null
}
```

---

## D. Package Subjects

> Endpoint ini bersifat nested di bawah `feature`. Semua request memerlukan `Authorization: Bearer {token}`.

### 1. GET /api/feature/{kode_feature}/package-subjects

Mengambil semua package subject dari satu paket ujian.

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_feature` | string | Kode feature induk |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar package subject berhasil diambil",
    "data": [
        {
            "kode_package_subject": "PKS-001-250515090000",
            "kode_feature": "FTR-001-250515090000",
            "kode_subject": "SUB-001-250515090100",
            "jumlah_soal": 30,
            "durasi_menit": 60,
            "created_at": "2025-05-15T09:00:00.000000Z",
            "updated_at": "2025-05-15T09:00:00.000000Z"
        }
    ]
}
```

---

### 2. POST /api/feature/{kode_feature}/package-subjects

Menambahkan package subject ke dalam paket ujian.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_subject` | string | ✅ Ya | Kode mata pelajaran |
| `jumlah_soal` | integer, min:1 | ✅ Ya | Jumlah soal dalam sesi ini |
| `durasi_menit` | integer, min:1 | ✅ Ya | Durasi pengerjaan dalam menit |

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Package subject berhasil ditambahkan",
    "data": {
        "kode_package_subject": "PKS-001-250515143022",
        "kode_feature": "FTR-001-250515090000",
        "kode_subject": "SUB-001-250515090100",
        "jumlah_soal": 30,
        "durasi_menit": 60,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z"
    }
}
```

---

### 3. GET /api/feature/{kode_feature}/package-subjects/{kode_package_subject}

Mengambil detail satu package subject.

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_feature` | string | Kode feature induk |
| `kode_package_subject` | string | Kode package subject |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail package subject berhasil diambil",
    "data": {
        "kode_package_subject": "PKS-001-250515090000",
        "kode_feature": "FTR-001-250515090000",
        "kode_subject": "SUB-001-250515090100",
        "jumlah_soal": 30,
        "durasi_menit": 60,
        "subject": {
            "kode_subject": "SUB-001-250515090100",
            "name": "Matematika Dasar",
            "slug": "matematika-dasar"
        }
    }
}
```

---

### 4. PUT /api/feature/{kode_feature}/package-subjects/{kode_package_subject}

Memperbarui data package subject.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_subject` | string | ❌ Opsional | |
| `jumlah_soal` | integer, min:1 | ❌ Opsional | |
| `durasi_menit` | integer, min:1 | ❌ Opsional | |

---

### 5. PATCH /api/feature/{kode_feature}/package-subjects/{kode_package_subject}/update

Update sebagian field saja. Minimal 1 field harus dikirim.

---

### 6. DELETE /api/feature/{kode_feature}/package-subjects/{kode_package_subject}

Menghapus package subject dari paket ujian.

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "Package subject berhasil dihapus",
    "data": null
}
```

---

## E. Questions

> Semua request memerlukan `Authorization: Bearer {token}`.

### 1. GET /api/questions/passages

Mengambil daftar passage yang tersedia untuk soal bertipe reading/passage-based.

> ⚠️ **Catatan:** Route ini harus didefinisikan **sebelum** `GET /api/questions/{kode_soal}` agar Laravel tidak salah menginterpretasikan `passages` sebagai `{kode_soal}`.

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar passage berhasil diambil",
    "data": [
        {
            "id": 1,
            "title": "Bacaan 1 - Ekosistem Hutan",
            "content": "Hutan merupakan ekosistem yang kompleks...",
            "created_at": "2025-05-15T09:00:00.000000Z"
        }
    ]
}
```

---

### 2. DELETE /api/questions/bulk-delete

Menghapus banyak soal sekaligus.

> ⚠️ **Catatan:** Route ini harus didefinisikan **sebelum** `DELETE /api/questions/{kode_soal}`.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_soal` | array of string | ✅ Ya | Daftar kode soal yang ingin dihapus |

**Contoh Request**
```json
{
    "kode_soal": [
        "SOA-001-250515090000",
        "SOA-002-250515090100",
        "SOA-003-250515090200"
    ]
}
```

**Response 200 — Berhasil Dihapus**
```json
{
    "statusCode": 200,
    "message": "3 soal berhasil dihapus",
    "data": null
}
```

**Response 422 — Validasi Gagal**
```json
{
    "statusCode": 422,
    "message": "Validasi gagal",
    "errors": {
        "kode_soal": ["The kode_soal field is required."]
    }
}
```

---

### 3. GET /api/questions

Mengambil semua soal. Mendukung filter via query parameter.

**Query Parameters (opsional)**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_subject` | string | Filter berdasarkan mata pelajaran |
| `kode_package_subject` | string | Filter berdasarkan package subject |
| `tipe` | string | Filter berdasarkan tipe soal (`pg`, `essay`, dll.) |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Daftar soal berhasil diambil",
    "data": [
        {
            "kode_soal": "SOA-001-250515090000",
            "kode_subject": "SUB-001-250515090100",
            "tipe": "pg",
            "pertanyaan": "Nilai dari 2 + 2 adalah...",
            "passage_id": null,
            "created_at": "2025-05-15T09:00:00.000000Z",
            "updated_at": "2025-05-15T09:00:00.000000Z"
        }
    ]
}
```

---

### 4. POST /api/questions

Menambahkan soal baru.

**Body Request**

| Field | Tipe | Wajib | Keterangan |
|-------|------|-------|------------|
| `kode_subject` | string | ✅ Ya | Kode mata pelajaran |
| `tipe` | string | ✅ Ya | Tipe soal: `pg`, `essay`, dll. |
| `pertanyaan` | string | ✅ Ya | Teks pertanyaan |
| `passage_id` | integer | ❌ Opsional | ID passage jika soal berbasis teks |
| `pilihan` | array | ❌ Opsional | Pilihan jawaban (untuk tipe `pg`) |
| `kunci_jawaban` | string | ❌ Opsional | Jawaban benar |
| `pembahasan` | string | ❌ Opsional | Penjelasan jawaban |

**Response 201 — Berhasil Ditambahkan**
```json
{
    "statusCode": 201,
    "message": "Soal berhasil ditambahkan",
    "data": {
        "kode_soal": "SOA-001-250515143022",
        "kode_subject": "SUB-001-250515090100",
        "tipe": "pg",
        "pertanyaan": "Nilai dari 2 + 2 adalah...",
        "passage_id": null,
        "created_at": "2025-05-15T14:30:22.000000Z",
        "updated_at": "2025-05-15T14:30:22.000000Z"
    }
}
```

---

### 5. GET /api/questions/{kode_soal}

Mengambil detail satu soal (tanpa kunci jawaban dan pembahasan).

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_soal` | string | Kode soal, contoh: `SOA-001-250515090000` |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Detail soal berhasil diambil",
    "data": {
        "kode_soal": "SOA-001-250515090000",
        "kode_subject": "SUB-001-250515090100",
        "tipe": "pg",
        "pertanyaan": "Nilai dari 2 + 2 adalah...",
        "passage_id": null,
        "pilihan": [
            { "key": "A", "text": "3" },
            { "key": "B", "text": "4" },
            { "key": "C", "text": "5" },
            { "key": "D", "text": "6" }
        ]
    }
}
```

---

### 6. GET /api/questions/{kode_soal}/answer

Mengambil kunci jawaban dan pembahasan untuk soal tertentu. Endpoint ini bisa dibatasi aksesnya berdasarkan role/permission.

**Parameter URL**

| Parameter | Tipe | Keterangan |
|-----------|------|------------|
| `kode_soal` | string | Kode soal |

**Response 200 — Berhasil**
```json
{
    "statusCode": 200,
    "message": "Jawaban soal berhasil diambil",
    "data": {
        "kode_soal": "SOA-001-250515090000",
        "kunci_jawaban": "B",
        "pembahasan": "2 + 2 = 4 karena penjumlahan bilangan bulat."
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

### 7. PUT /api/questions/{kode_soal}

Memperbarui data soal. Semua field bersifat `sometimes`.

**Body Request** *(sama seperti POST, semua opsional)*

**Response 200 — Berhasil Diperbarui**
```json
{
    "statusCode": 200,
    "message": "Soal berhasil diperbarui",
    "data": { }
}
```

---

### 8. PATCH /api/questions/{kode_soal}/update

Update sebagian field saja. Minimal 1 field harus dikirim.

**Response 200 — Berhasil Diperbarui Sebagian**
```json
{
    "statusCode": 200,
    "message": "Soal berhasil diperbarui sebagian",
    "data": { }
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

---

### 9. DELETE /api/questions/{kode_soal}

Menghapus satu soal.

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

**Response 500 — Gagal Dihapus**
```json
{
    "statusCode": 500,
    "message": "Soal gagal dihapus",
    "data": null
}
```
