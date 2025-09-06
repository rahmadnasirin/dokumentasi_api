# API Dokumentasi - CRUD Dokter

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

## 1. Get All Dokter

### Endpoint
```http
GET /dokter
```

### Description
Mendapatkan daftar semua dokter dengan pagination dan filtering.

### Query Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `per_page` | integer | No | Jumlah data per halaman (default: 10) |
| `page` | integer | No | Nomor halaman (default: 1) |
| `sip_dokter` | string | No | Filter berdasarkan SIP dokter |
| `pendidikan` | string | No | Filter berdasarkan pendidikan |
| `tahun_pengalaman` | integer | No | Filter berdasarkan tahun pengalaman |
| `is_active` | boolean | No | Filter berdasarkan status aktif (true/false) |
| `search` | string | No | Pencarian berdasarkan nama dokter |

### Example Request
```http
GET /dokter?per_page=15&search=john&is_active=true&pendidikan=kedokteran
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Dokter data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_dokter": "DOK12345678",
                "kode_user": "USR87654321",
                "sip_dokter": "SIP123456789",
                "pendidikan": "S1 Kedokteran Universitas Indonesia",
                "tahun_pengalaman": 5,
                "created_at": "2024-01-15T10:30:00.000000Z",
                "updated_at": "2024-01-15T10:30:00.000000Z",
                "user": {
                    "kode_user": "USR87654321",
                    "name": "Dr. John Doe",
                    "email": "john.doe@email.com",
                    "phone": "08123456789",
                    "is_active": true,
                    "foto_profile": "profiles/john-doe.jpg",
                    "gender": "laki-laki",
                    "emergency_contact": "08987654321",
                    "address": "Jl. Kesehatan No. 123",
                    "date_of_birth": "1985-05-15"
                }
            }
        ],
        "first_page_url": "http://localhost/api/dokter?page=1",
        "from": 1,
        "last_page": 1,
        "last_page_url": "http://localhost/api/dokter?page=1",
        "links": [...],
        "next_page_url": null,
        "path": "http://localhost/api/dokter",
        "per_page": 15,
        "prev_page_url": null,
        "to": 1,
        "total": 1
    }
}
```

---

## 2. Create New Dokter

### Endpoint
```http
POST /dokter
```

### Description
Membuat dokter baru beserta data user-nya.

### Request Body
```json
{
    "name": "Dr. John Doe",
    "email": "john.doe@email.com",
    "password": "password123",
    "phone": "08123456789",
    "date_of_birth": "1985-05-15",
    "gender": "laki-laki",
    "address": "Jl. Kesehatan No. 123",
    "emergency_contact": "08987654321",
    "foto_profile": "profiles/john-doe.jpg",
    "sip_dokter": "SIP123456789",
    "pendidikan": "S1 Kedokteran Universitas Indonesia",
    "tahun_pengalaman": 5
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `name` | required, string, max:255 |
| `email` | required, string, email, max:255, unique:users |
| `password` | required, string, min:8 |
| `phone` | required, string, max:20, unique:users |
| `date_of_birth` | nullable, date |
| `gender` | nullable, string, in:laki-laki,perempuan |
| `address` | nullable, string, max:500 |
| `emergency_contact` | nullable, string, max:20 |
| `foto_profile` | nullable, string, max:255 |
| `sip_dokter` | nullable, string, max:255, unique:dokter |
| `pendidikan` | required, string, max:255 |
| `tahun_pengalaman` | required, integer, min:0, max:50 |

### Success Response (201 Created)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Dokter created successfully"
    },
    "data": {
        "kode_dokter": "DOK12345678",
        "kode_user": "USR87654321",
        "name": "Dr. John Doe",
        "email": "john.doe@email.com",
        "phone": "08123456789",
        "date_of_birth": "1985-05-15",
        "gender": "laki-laki",
        "address": "Jl. Kesehatan No. 123",
        "emergency_contact": "08987654321",
        "foto_profile": "profiles/john-doe.jpg",
        "sip_dokter": "SIP123456789",
        "pendidikan": "S1 Kedokteran Universitas Indonesia",
        "tahun_pengalaman": 5,
        "is_active": true,
        "role": "dokter",
        "created_at": "2024-01-15T10:30:00.000000Z",
        "updated_at": "2024-01-15T10:30:00.000000Z"
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
        "email": ["The email has already been taken."],
        "phone": ["The phone has already been taken."]
    }
}
```

---

## 3. Get Single Dokter

### Endpoint
```http
GET /dokter/{kode_dokter}
```

### Description
Mendapatkan detail dokter berdasarkan kode dokter.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kode_dokter` | string | Yes | Kode unik dokter |

### Example Request
```http
GET /dokter/DOK12345678
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Dokter data retrieved successfully"
    },
    "data": {
        "kode_dokter": "DOK12345678",
        "kode_user": "USR87654321",
        "sip_dokter": "SIP123456789",
        "pendidikan": "S1 Kedokteran Universitas Indonesia",
        "tahun_pengalaman": 5,
        "user": {
            "kode_user": "USR87654321",
            "name": "Dr. John Doe",
            "email": "john.doe@email.com",
            "phone": "08123456789",
            "date_of_birth": "1985-05-15",
            "gender": "laki-laki",
            "address": "Jl. Kesehatan No. 123",
            "emergency_contact": "08987654321",
            "is_active": true,
            "foto_profile": "profiles/john-doe.jpg",
            "created_at": "2024-01-15T10:30:00.000000Z",
            "updated_at": "2024-01-15T10:30:00.000000Z"
        },
        "created_at": "2024-01-15T10:30:00.000000Z",
        "updated_at": "2024-01-15T10:30:00.000000Z"
    }
}
```

### Error Response (404 Not Found)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "Dokter not found"
    },
    "data": null
}
```

---

## 4. Update Dokter

### Endpoint
```http
PUT /dokter/{kode_dokter}
```

### Description
Mengupdate data dokter dan user terkait. Semua field bersifat optional.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kode_dokter` | string | Yes | Kode unik dokter |

### Request Body (All fields optional)
```json
{
    "name": "Dr. John Doe Updated",
    "email": "john.doe.updated@email.com",
    "password": "newpassword123",
    "phone": "08123456000",
    "date_of_birth": "1985-05-15",
    "gender": "laki-laki",
    "address": "Jl. Kesehatan Baru No. 456",
    "emergency_contact": "08987654000",
    "foto_profile": "profiles/john-doe-new.jpg",
    "is_active": true,
    "sip_dokter": "SIP987654321",
    "pendidikan": "S1 Kedokteran + Spesialis Jantung",
    "tahun_pengalaman": 7
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `name` | sometimes, required, string, max:255 |
| `email` | sometimes, required, string, email, max:255, unique:users (except current) |
| `password` | sometimes, nullable, string, min:8 |
| `phone` | sometimes, required, string, max:20, unique:users (except current) |
| `date_of_birth` | nullable, date |
| `gender` | nullable, string, in:laki-laki,perempuan |
| `address` | nullable, string, max:500 |
| `emergency_contact` | nullable, string, max:20 |
| `foto_profile` | nullable, string, max:255 |
| `is_active` | sometimes, boolean |
| `sip_dokter` | sometimes, nullable, string, max:255, unique:dokter (except current) |
| `pendidikan` | sometimes, required, string, max:255 |
| `tahun_pengalaman` | sometimes, required, integer, min:0, max:50 |

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Dokter updated successfully"
    },
    "data": {
        "kode_dokter": "DOK12345678",
        "kode_user": "USR87654321",
        "name": "Dr. John Doe Updated",
        "email": "john.doe.updated@email.com",
        "phone": "08123456000",
        "date_of_birth": "1985-05-15",
        "gender": "laki-laki",
        "address": "Jl. Kesehatan Baru No. 456",
        "emergency_contact": "08987654000",
        "foto_profile": "profiles/john-doe-new.jpg",
        "sip_dokter": "SIP987654321",
        "pendidikan": "S1 Kedokteran + Spesialis Jantung",
        "tahun_pengalaman": 7,
        "is_active": true,
        "role": "dokter",
        "created_at": "2024-01-15T10:30:00.000000Z",
        "updated_at": "2024-01-15T15:45:00.000000Z"
    }
}
```

---

## 5. Delete Dokter

### Endpoint
```http
DELETE /dokter/{kode_dokter}
```

### Description
Menghapus dokter beserta data user terkait secara permanen.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kode_dokter` | string | Yes | Kode unik dokter |

### Example Request
```http
DELETE /dokter/DOK12345678
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Dokter deleted successfully"
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
        "message": "Dokter not found"
    },
    "data": null
}
```

---

## 6. Toggle Dokter Status

### Endpoint
```http
PATCH /dokter/{kode_dokter}/toggle-status
```

### Description
Mengaktifkan atau menonaktifkan status dokter.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kode_dokter` | string | Yes | Kode unik dokter |

### Example Request
```http
PATCH /dokter/DOK12345678/toggle-status
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Dokter activated successfully"
    },
    "data": {
        "kode_dokter": "DOK12345678",
        "name": "Dr. John Doe",
        "is_active": true
    }
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
        "message": "Failed to create dokter: Database connection error"
    },
    "data": null
}
```

---

## Notes

1. **Authentication**: Semua endpoint memerlukan authentication dengan Bearer Token
2. **Permissions**: Biasanya hanya admin yang dapat mengakses endpoint ini
3. **Validation**: Semua input akan divalidasi sesuai rules yang telah ditentukan
4. **Database Transaction**: Operasi create, update, dan delete menggunakan database transaction untuk data consistency
5. **Role Assignment**: Saat create dokter baru, user akan otomatis mendapat role "dokter"
6. **Unique Codes**: `kode_user` dan `kode_dokter` akan digenerate otomatis dengan format yang unik
7. **Soft Delete**: Jika menggunakan soft delete, ubah endpoint delete untuk hanya mengubah status

---

## Sample cURL Commands

### Create Dokter
```bash
curl -X POST "https://your-api-domain.com/api/dokter" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Dr. John Doe",
    "email": "john.doe@email.com",
    "password": "password123",
    "phone": "08123456789",
    "date_of_birth": "1985-05-15",
    "gender": "laki-laki",
    "address": "Jl. Kesehatan No. 123",
    "sip_dokter": "SIP123456789",
    "pendidikan": "S1 Kedokteran Universitas Indonesia",
    "tahun_pengalaman": 5
  }'
```

### Get All Dokter with Filters
```bash
curl -X GET "https://your-api-domain.com/api/dokter?search=john&is_active=true&per_page=10" \
  -H "Authorization: Bearer your-token"
```
