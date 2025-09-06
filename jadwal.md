# API Dokumentasi - CRUD Jadwal Dokter

## Base URL
```
https://your-api-domain.com/api
```

## Authentication
Semua endpoint memerlukan authentication dengan Bearer Token.
```
Authorization: Bearer {your-token}
```

---

## 1. Get All Jadwal Dokter

### Endpoint
```http
GET /jadwal
```

### Description
Mendapatkan daftar semua jadwal dokter dengan pagination dan filtering.

### Query Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `per_page` | integer | No | Jumlah data per halaman (default: 10) |
| `page` | integer | No | Nomor halaman (default: 1) |
| `kode_dokter` | string | No | Filter berdasarkan kode dokter |
| `hari` | string | No | Filter berdasarkan hari (senin/selasa/rabu/kamis/jumat/sabtu/minggu) |
| `is_active` | boolean | No | Filter berdasarkan status aktif (true/false) |

### Example Request
```http
GET /jadwal?kode_dokter=DOK001&hari=senin&is_active=true&per_page=15
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Jadwal dokter retrieved successfully"
    },
    "data": {
        "jadwal_dokter": [
            {
                "kode_jadwal": "JDWABCD1234",
                "kode_dokter": "DOK001",
                "hari": "senin",
                "jam_mulai": "08:00",
                "jam_selesai": "12:00",
                "durasi_konsultasi": 30,
                "maksimal_pasien": 20,
                "is_active": true,
                "keterangan": "Jadwal pagi",
                "created_at": "2024-01-01T08:00:00.000000Z",
                "updated_at": "2024-01-01T08:00:00.000000Z"
            }
        ],
        "pagination": {
            "current_page": 1,
            "last_page": 3,
            "per_page": 15,
            "total": 45,
            "from": 1,
            "to": 15
        }
    }
}
```

---

## 2. Create New Jadwal Dokter

### Endpoint
```http
POST /jadwal/create
```

### Description
Membuat jadwal dokter baru.

### Request Body
```json
{
    "kode_dokter": "DOK001",
    "hari": "senin",
    "jam_mulai": "08:00",
    "jam_selesai": "12:00",
    "durasi_konsultasi": 30,
    "maksimal_pasien": 20,
    "is_active": true,
    "keterangan": "Jadwal pagi"
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `kode_dokter` | required, string, max:255 |
| `hari` | required, enum (senin/selasa/rabu/kamis/jumat/sabtu/minggu) |
| `jam_mulai` | required, time format (H:i) |
| `jam_selesai` | required, time format (H:i), after:jam_mulai |
| `durasi_konsultasi` | nullable, integer, min:1 |
| `maksimal_pasien` | nullable, integer, min:1 |
| `is_active` | boolean (default: true) |
| `keterangan` | nullable, string |

### Success Response (201 Created)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Jadwal dokter created successfully"
    },
    "data": {
        "kode_jadwal": "JDWABCD1234",
        "kode_dokter": "DOK001",
        "hari": "senin",
        "jam_mulai": "08:00",
        "jam_selesai": "12:00",
        "durasi_konsultasi": 30,
        "maksimal_pasien": 20,
        "is_active": true,
        "keterangan": "Jadwal pagi",
        "created_at": "2024-01-01T08:00:00.000000Z",
        "updated_at": "2024-01-01T08:00:00.000000Z"
    }
}
```

### Error Response (422 Validation Error)
```json
{
    "meta": {
        "code": 422,
        "status": "error",
        "message": "Validation failed"
    },
    "data": {
        "kode_dokter": ["The kode dokter field is required."],
        "hari": ["The selected hari is invalid."],
        "jam_selesai": ["The jam selesai must be after jam mulai."]
    }
}
```

### Error Response (409 Schedule Conflict)
```json
{
    "meta": {
        "code": 409,
        "status": "error",
        "message": "Jadwal dokter pada hari dan jam tersebut sudah ada"
    },
    "data": null
}
```

---

## 3. Get Single Jadwal Dokter

### Endpoint
```http
GET /jadwal/{kode_jadwal}
```

### Description
Mendapatkan detail jadwal dokter berdasarkan kode jadwal.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kode_jadwal` | string | Yes | Kode unik jadwal dokter |

### Example Request
```http
GET /jadwal/JDWABCD1234
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Jadwal dokter retrieved successfully"
    },
    "data": {
        "kode_jadwal": "JDWABCD1234",
        "kode_dokter": "DOK001",
        "hari": "senin",
        "jam_mulai": "08:00",
        "jam_selesai": "12:00",
        "durasi_konsultasi": 30,
        "maksimal_pasien": 20,
        "is_active": true,
        "keterangan": "Jadwal pagi",
        "created_at": "2024-01-01T08:00:00.000000Z",
        "updated_at": "2024-01-01T08:00:00.000000Z"
    }
}
```

### Error Response (404 Not Found)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "Jadwal dokter not found"
    },
    "data": null
}
```

---

## 4. Update Jadwal Dokter

### Endpoint
```http
PUT /jadwal/update/{kode_jadwal}
```

### Description
Mengupdate jadwal dokter yang sudah ada. Semua field bersifat optional.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kode_jadwal` | string | Yes | Kode unik jadwal dokter |

### Request Body (All fields optional)
```json
{
    "kode_dokter": "DOK001",
    "hari": "selasa",
    "jam_mulai": "09:00",
    "jam_selesai": "13:00",
    "durasi_konsultasi": 45,
    "maksimal_pasien": 15,
    "is_active": true,
    "keterangan": "Jadwal pagi diperpanjang"
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `kode_dokter` | sometimes, required, string, max:255 |
| `hari` | sometimes, required, enum (senin/selasa/rabu/kamis/jumat/sabtu/minggu) |
| `jam_mulai` | sometimes, required, time format (H:i) |
| `jam_selesai` | sometimes, required, time format (H:i), after:jam_mulai |
| `durasi_konsultasi` | nullable, integer, min:1 |
| `maksimal_pasien` | nullable, integer, min:1 |
| `is_active` | sometimes, boolean |
| `keterangan` | nullable, string |

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Jadwal dokter updated successfully"
    },
    "data": {
        "kode_jadwal": "JDWABCD1234",
        "kode_dokter": "DOK001",
        "hari": "selasa",
        "jam_mulai": "09:00",
        "jam_selesai": "13:00",
        "durasi_konsultasi": 45,
        "maksimal_pasien": 15,
        "is_active": true,
        "keterangan": "Jadwal pagi diperpanjang",
        "created_at": "2024-01-01T08:00:00.000000Z",
        "updated_at": "2024-01-01T09:00:00.000000Z"
    }
}
```

---

## 5. Delete Jadwal Dokter

### Endpoint
```http
DELETE /jadwal/delete/{kode_jadwal}
```

### Description
Menghapus jadwal dokter secara permanen.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kode_jadwal` | string | Yes | Kode unik jadwal dokter |

### Example Request
```http
DELETE /jadwal/delete/JDWABCD1234
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Jadwal dokter deleted successfully"
    },
    "data": null
}
```

### Error Response (404 Not Found)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "Jadwal dokter not found"
    },
    "data": null
}
```

---

## 6. Get Jadwal by Dokter

### Endpoint
```http
GET /jadwal/dokter/{kode_dokter}
```

### Description
Mendapatkan semua jadwal aktif berdasarkan kode dokter.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kode_dokter` | string | Yes | Kode unik dokter |

### Example Request
```http
GET /jadwal/dokter/DOK001
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Jadwal dokter retrieved successfully"
    },
    "data": [
        {
            "kode_jadwal": "JDWABCD1234",
            "kode_dokter": "DOK001",
            "hari": "senin",
            "jam_mulai": "08:00",
            "jam_selesai": "12:00",
            "durasi_konsultasi": 30,
            "maksimal_pasien": 20,
            "is_active": true,
            "keterangan": "Jadwal pagi",
            "created_at": "2024-01-01T08:00:00.000000Z",
            "updated_at": "2024-01-01T08:00:00.000000Z"
        }
    ]
}
```

### Error Response (404 Not Found)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "No active schedule found for this doctor"
    },
    "data": null
}
```

---

## 7. Get Jadwal by Hari

### Endpoint
```http
GET /jadwal/hari/{hari}
```

### Description
Mendapatkan semua jadwal aktif berdasarkan hari.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `hari` | string | Yes | Hari (senin/selasa/rabu/kamis/jumat/sabtu/minggu) |

### Example Request
```http
GET /jadwal/hari/senin
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Jadwal dokter retrieved successfully"
    },
    "data": [
        {
            "kode_jadwal": "JDWABCD1234",
            "kode_dokter": "DOK001",
            "hari": "senin",
            "jam_mulai": "08:00",
            "jam_selesai": "12:00",
            "durasi_konsultasi": 30,
            "maksimal_pasien": 20,
            "is_active": true,
            "keterangan": "Jadwal pagi",
            "created_at": "2024-01-01T08:00:00.000000Z",
            "updated_at": "2024-01-01T08:00:00.000000Z"
        }
    ]
}
```

### Error Response (400 Bad Request)
```json
{
    "meta": {
        "code": 400,
        "status": "error",
        "message": "Invalid day. Must be one of: senin, selasa, rabu, kamis, jumat, sabtu, minggu"
    },
    "data": null
}
```

### Error Response (404 Not Found)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "No active schedule found for senin"
    },
    "data": null
}
```

---

## Error Responses

### 401 Unauthorized
```json
{
    "meta": {
        "code": 401,
        "status": "error",
        "message": "Unauthorized"
    },
    "data": null
}
```

### 403 Forbidden
```json
{
    "meta": {
        "code": 403,
        "status": "error",
        "message": "Insufficient permissions"
    },
    "data": null
}
```

### 500 Internal Server Error
```json
{
    "meta": {
        "code": 500,
        "status": "error",
        "message": "Failed to retrieve jadwal dokter: Database connection error"
    },
    "data": null
}
```

---

## Notes

1. **Authentication**: Semua endpoint memerlukan authentication dengan Bearer Token
2. **Permissions**: Biasanya admin dan dokter yang bersangkutan dapat mengakses endpoint ini
3. **Validation**: Semua input akan divalidasi sesuai rules yang telah ditentukan
4. **Schedule Conflict**: Sistem akan mengecek overlap jadwal untuk dokter yang sama pada hari yang sama
5. **Auto-generated Code**: `kode_jadwal` akan digenerate otomatis dengan format "JDW" + 8 karakter random
6. **Time Format**: Format waktu menggunakan H:i (24 jam), contoh: "08:00", "13:30"
7. **Active Status**: Hanya jadwal dengan `is_active = true` yang akan ditampilkan pada endpoint by dokter dan by hari

---

## Sample cURL Commands

### Create Jadwal Dokter
```bash
curl -X POST "https://your-api-domain.com/api/jadwal/create" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "kode_dokter": "DOK001",
    "hari": "senin",
    "jam_mulai": "08:00",
    "jam_selesai": "12:00",
    "durasi_konsultasi": 30,
    "maksimal_pasien": 20,
    "is_active": true,
    "keterangan": "Jadwal pagi"
  }'
```

### Get All Jadwal with Filters
```bash
curl -X GET "https://your-api-domain.com/api/jadwal?kode_dokter=DOK001&hari=senin&is_active=true&per_page=10" \
  -H "Authorization: Bearer your-token"
```

### Update Jadwal Dokter
```bash
curl -X PUT "https://your-api-domain.com/api/jadwal/update/JDWABCD1234" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "jam_mulai": "09:00",
    "jam_selesai": "13:00",
    "durasi_konsultasi": 45
  }'
```
