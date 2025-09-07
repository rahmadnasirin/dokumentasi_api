# Ruangan API Documentation

## Base URL
```
/api/ruangan
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/ruangan` | Get all ruangan with pagination and filters |
| POST | `/ruangan/create` | Create new ruangan |
| GET | `/ruangan/{kodeRuangan}` | Get specific ruangan by code |
| PUT | `/ruangan/update/{kodeRuangan}` | Update ruangan |
| DELETE | `/ruangan/delete/{kodeRuangan}` | Delete ruangan |
| PATCH | `/ruangan/status/{kodeRuangan}` | Toggle ruangan status |

---

## 1. Get All Ruangan

### Request
```http
GET /api/ruangan
```

### Query Parameters
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `nama_ruangan` | string | No | Filter by room name (partial match) | `VIP` |
| `tipe_ruangan` | string | No | Filter by room type (partial match) | `Operasi` |
| `status` | string | No | Filter by status | `tersedia` |
| `search` | string | No | Search in name or type | `kamar` |
| `per_page` | integer | No | Items per page (default: 10) | `15` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Ruangan data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_ruangan": "RNG-240907123456",
                "nama_ruangan": "Kamar VIP 1",
                "tipe_ruangan": "VIP",
                "status": "tersedia",
                "created_at": "2024-09-07T12:34:56.000000Z",
                "updated_at": "2024-09-07T12:34:56.000000Z"
            }
        ],
        "first_page_url": "http://localhost/api/ruangan?page=1",
        "from": 1,
        "last_page": 1,
        "per_page": 10,
        "total": 1
    }
}
```

### Response Error (500)
```json
{
    "meta": {
        "code": 500,
        "status": "error",
        "message": "Failed to retrieve ruangan data: [error message]"
    },
    "data": null
}
```

---

## 2. Create Ruangan

### Request
```http
POST /api/ruangan/create
Content-Type: application/json
```

### Request Body
```json
{
    "nama_ruangan": "Kamar VIP 1",
    "tipe_ruangan": "VIP",
    "status": "tersedia"
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `nama_ruangan` | string | Yes | Room name (max: 255) | Any string |
| `tipe_ruangan` | string | Yes | Room type (max: 255) | Any string |
| `status` | string | No | Room status (default: tersedia) | `tersedia`, `tidak_tersedia`, `non_aktif` |

### Response Success (201)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Ruangan created successfully"
    },
    "data": {
        "kode_ruangan": "RNG-240907123456",
        "nama_ruangan": "Kamar VIP 1",
        "tipe_ruangan": "VIP",
        "status": "tersedia",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T12:34:56.000000Z"
    }
}
```

### Response Error (422)
```json
{
    "meta": {
        "code": 422,
        "status": "error",
        "message": "Validation failed"
    },
    "data": {
        "nama_ruangan": ["The nama ruangan field is required."],
        "tipe_ruangan": ["The tipe ruangan field is required."]
    }
}
```

---

## 3. Get Specific Ruangan

### Request
```http
GET /api/ruangan/{kodeRuangan}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeRuangan` | string | Yes | Ruangan code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Ruangan data retrieved successfully"
    },
    "data": {
        "kode_ruangan": "RNG-240907123456",
        "nama_ruangan": "Kamar VIP 1",
        "tipe_ruangan": "VIP",
        "status": "tersedia",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T12:34:56.000000Z"
    }
}
```

### Response Error (404)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "Ruangan not found"
    },
    "data": null
}
```

---

## 4. Update Ruangan

### Request
```http
PUT /api/ruangan/update/{kodeRuangan}
Content-Type: application/json
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeRuangan` | string | Yes | Ruangan code |

### Request Body
```json
{
    "nama_ruangan": "Kamar VIP 1 Updated",
    "tipe_ruangan": "VVIP",
    "status": "tidak_tersedia"
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `nama_ruangan` | string | No | Room name (max: 255) | Any string |
| `tipe_ruangan` | string | No | Room type (max: 255) | Any string |
| `status` | string | No | Room status | `tersedia`, `tidak_tersedia`, `non_aktif` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Ruangan updated successfully"
    },
    "data": {
        "kode_ruangan": "RNG-240907123456",
        "nama_ruangan": "Kamar VIP 1 Updated",
        "tipe_ruangan": "VVIP",
        "status": "tidak_tersedia",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T14:30:00.000000Z"
    }
}
```

### Response Error (404)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "Ruangan not found"
    },
    "data": null
}
```

---

## 5. Delete Ruangan

### Request
```http
DELETE /api/ruangan/delete/{kodeRuangan}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeRuangan` | string | Yes | Ruangan code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Ruangan deleted successfully"
    },
    "data": null
}
```

### Response Error (404)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "Ruangan not found"
    },
    "data": null
}
```

---

## 6. Toggle Ruangan Status

### Request
```http
PATCH /api/ruangan/status/{kodeRuangan}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeRuangan` | string | Yes | Ruangan code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Ruangan status updated to tidak_tersedia successfully"
    },
    "data": {
        "kode_ruangan": "RNG-240907123456",
        "nama_ruangan": "Kamar VIP 1",
        "status": "tidak_tersedia"
    }
}
```

### Status Toggle Logic
- `tersedia` → `tidak_tersedia`
- `tidak_tersedia` → `tersedia`
- `non_aktif` → `tersedia`

---

## Status Values

| Status | Description |
|--------|-------------|
| `tersedia` | Room is available for use |
| `tidak_tersedia` | Room is not available/occupied |
| `non_aktif` | Room is inactive/out of service |

---

## Error Responses

### General Error Structure
```json
{
    "meta": {
        "code": [HTTP_CODE],
        "status": "error",
        "message": "[Error message]"
    },
    "data": null
}
```

### Common HTTP Status Codes
- `200` - Success
- `201` - Created
- `404` - Not Found
- `422` - Validation Error
- `500` - Server Error

---

## Sample Usage

### Create a VIP Room
```bash
curl -X POST http://localhost/api/ruangan/create \
  -H "Content-Type: application/json" \
  -d '{
    "nama_ruangan": "Kamar VIP Premium",
    "tipe_ruangan": "VIP",
    "status": "tersedia"
  }'
```

### Get All Available Rooms
```bash
curl "http://localhost/api/ruangan?status=tersedia&per_page=20"
```

### Update Room Status
```bash
curl -X PATCH http://localhost/api/ruangan/status/RNG-240907123456
```

### Search Rooms by Name
```bash
curl "http://localhost/api/ruangan?search=VIP"
```
