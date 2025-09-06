# Dokumentasi API JadwalDokter

## Overview
API JadwalDokter digunakan untuk mengelola jadwal praktik dokter. API ini menyediakan endpoint untuk melakukan operasi CRUD (Create, Read, Update, Delete) pada jadwal dokter.

## Base URL
```
/api/jadwal
```

## Authentication
Semua endpoint memerlukan autentikasi yang sesuai dengan sistem yang digunakan.

## Response Format
Semua response menggunakan format JSON dengan struktur standar dari `ResponseFormatter`:

### Success Response
```json
{
  "meta": {
    "code": 200,
    "status": "success",
    "message": "Success message"
  },
  "data": {
    // Response data
  }
}
```

### Error Response
```json
{
  "meta": {
    "code": 400,
    "status": "error", 
    "message": "Error message"
  },
  "data": null
}
```

## Data Model

### JadwalDokter Schema
```json
{
  "kode_jadwal": "string (Primary Key)",
  "kode_dokter": "string",
  "hari": "enum (senin|selasa|rabu|kamis|jumat|sabtu|minggu)",
  "jam_mulai": "time (HH:mm)",
  "jam_selesai": "time (HH:mm)",
  "durasi_konsultasi": "integer (nullable)",
  "maksimal_pasien": "integer (nullable)",
  "is_active": "boolean (default: true)",
  "keterangan": "text (nullable)",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

## Endpoints

### 1. Get All Jadwal Dokter
Mengambil daftar semua jadwal dokter dengan pagination dan filter.

**Endpoint:** `GET /api/jadwal`

**Query Parameters:**
- `kode_dokter` (optional) - Filter berdasarkan kode dokter
- `hari` (optional) - Filter berdasarkan hari (senin|selasa|rabu|kamis|jumat|sabtu|minggu)
- `is_active` (optional) - Filter berdasarkan status aktif (true|false)
- `per_page` (optional) - Jumlah data per halaman (default: 10)
- `page` (optional) - Nomor halaman

**Example Request:**
```http
GET /api/jadwal?kode_dokter=DOK001&hari=senin&is_active=true&per_page=15&page=1
```

**Success Response (200):**
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

### 2. Create Jadwal Dokter
Membuat jadwal dokter baru.

**Endpoint:** `POST /api/jadwal/create`

**Request Body:**
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

**Validation Rules:**
- `kode_dokter`: required, string, max:255
- `hari`: required, enum (senin|selasa|rabu|kamis|jumat|sabtu|minggu)
- `jam_mulai`: required, format time (H:i)
- `jam_selesai`: required, format time (H:i), harus setelah jam_mulai
- `durasi_konsultasi`: optional, integer, min:1
- `maksimal_pasien`: optional, integer, min:1
- `is_active`: optional, boolean (default: true)
- `keterangan`: optional, string

**Success Response (201):**
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

**Error Response (422) - Validation Error:**
```json
{
  "meta": {
    "code": 422,
    "status": "error",
    "message": "Validation failed"
  },
  "data": {
    "kode_dokter": ["The kode dokter field is required."],
    "hari": ["The selected hari is invalid."]
  }
}
```

**Error Response (409) - Schedule Conflict:**
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

### 3. Get Jadwal by Dokter
Mengambil semua jadwal aktif berdasarkan kode dokter.

**Endpoint:** `GET /api/jadwal/dokter/{kodeDokter}`

**Path Parameters:**
- `kodeDokter`: Kode dokter yang ingin dicari jadwalnya

**Example Request:**
```http
GET /api/jadwal/dokter/DOK001
```

**Success Response (200):**
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

**Error Response (404):**
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

### 4. Get Jadwal by Hari
Mengambil semua jadwal aktif berdasarkan hari.

**Endpoint:** `GET /api/jadwal/hari/{hari}`

**Path Parameters:**
- `hari`: Hari yang ingin dicari jadwalnya (senin|selasa|rabu|kamis|jumat|sabtu|minggu)

**Example Request:**
```http
GET /api/jadwal/hari/senin
```

**Success Response (200):**
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

**Error Response (400) - Invalid Day:**
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

**Error Response (404):**
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

### 5. Get Single Jadwal Dokter
Mengambil detail jadwal dokter berdasarkan kode jadwal.

**Endpoint:** `GET /api/jadwal/{kodeJadwal}`

**Path Parameters:**
- `kodeJadwal`: Kode jadwal yang ingin diambil detailnya

**Example Request:**
```http
GET /api/jadwal/JDWABCD1234
```

**Success Response (200):**
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

**Error Response (404):**
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

### 6. Update Jadwal Dokter
Mengupdate jadwal dokter yang sudah ada.

**Endpoint:** `PUT /api/jadwal/update/{kodeJadwal}`

**Path Parameters:**
- `kodeJadwal`: Kode jadwal yang ingin diupdate

**Request Body:**
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

**Validation Rules:**
- `kode_dokter`: sometimes required, string, max:255
- `hari`: sometimes required, enum (senin|selasa|rabu|kamis|jumat|sabtu|minggu)
- `jam_mulai`: sometimes required, format time (H:i)
- `jam_selesai`: sometimes required, format time (H:i), harus setelah jam_mulai
- `durasi_konsultasi`: optional, integer, min:1
- `maksimal_pasien`: optional, integer, min:1
- `is_active`: optional, boolean
- `keterangan`: optional, string

**Success Response (200):**
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

**Error Response (404):**
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

**Error Response (409) - Schedule Conflict:**
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

### 7. Delete Jadwal Dokter
Menghapus jadwal dokter.

**Endpoint:** `DELETE /api/jadwal/delete/{kodeJadwal}`

**Path Parameters:**
- `kodeJadwal`: Kode jadwal yang ingin dihapus

**Example Request:**
```http
DELETE /api/jadwal/delete/JDWABCD1234
```

**Success Response (200):**
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

**Error Response (404):**
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

## Error Codes Summary

| Code | Description |
|------|-------------|
| 200  | Success |
| 201  | Created |
| 400  | Bad Request |
| 404  | Not Found |
| 409  | Conflict (Schedule overlap) |
| 422  | Validation Error |
| 500  | Internal Server Error |

## Business Logic Notes

### Schedule Conflict Detection
- Sistem akan mengecek overlap jadwal untuk dokter yang sama pada hari yang sama
- Konflik terjadi jika ada jadwal aktif yang bertabrakan pada jam yang sama
- Validasi conflict dilakukan saat create dan update

### Auto-generated Kode Jadwal
- Kode jadwal otomatis dibuat dengan format: `JDW` + 8 karakter random uppercase
- Sistem akan memastikan kode jadwal unik sebelum disimpan

### Time Validation
- `jam_selesai` harus lebih besar dari `jam_mulai`
- Format waktu menggunakan format `H:i` (24 jam, contoh: "08:00", "13:30")

### Filter dan Pagination
- Endpoint index mendukung filter berdasarkan `kode_dokter`, `hari`, dan `is_active`
- Pagination default 10 item per halaman
- Dapat disesuaikan dengan parameter `per_page`
