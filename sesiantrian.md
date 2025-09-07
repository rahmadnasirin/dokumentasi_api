# Sesi Antrian API Documentation

## Base URL
```
/api/sesi-antrian
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/sesi-antrian` | Get all sesi antrian with pagination and filters |
| POST | `/sesi-antrian/create` | Create new sesi antrian |
| GET | `/sesi-antrian/{kodeSesiAntrian}` | Get specific sesi antrian by code |
| PUT | `/sesi-antrian/update/{kodeSesiAntrian}` | Update sesi antrian |
| DELETE | `/sesi-antrian/delete/{kodeSesiAntrian}` | Delete sesi antrian |
| PATCH | `/sesi-antrian/status/{kodeSesiAntrian}` | Toggle sesi antrian status |

---

## 1. Get All Sesi Antrian

### Request
```http
GET /api/sesi-antrian
```

### Query Parameters
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `tanggal_sesi` | string | No | Filter by session date (exact match) | `2024-09-07` |
| `jam_sesi` | string | No | Filter by session time (partial match) | `08:00` |
| `is_active` | boolean | No | Filter by active status | `true` or `false` |
| `search` | string | No | Search in date or time | `2024-09` |
| `per_page` | integer | No | Items per page (default: 10) | `15` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Sesi antrian data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_sesi_antrian": "SA-240907123456",
                "tanggal_sesi": "2024-09-07",
                "jam_sesi": "08:00-09:00",
                "is_active": true,
                "created_at": "2024-09-07T12:34:56.000000Z",
                "updated_at": "2024-09-07T12:34:56.000000Z"
            }
        ],
        "first_page_url": "http://localhost/api/sesi-antrian?page=1",
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
        "message": "Failed to retrieve sesi antrian data: [error message]"
    },
    "data": null
}
```

---

## 2. Create Sesi Antrian

### Request
```http
POST /api/sesi-antrian/create
Content-Type: application/json
```

### Request Body
```json
{
    "tanggal_sesi": "2024-09-07",
    "jam_sesi": "08:00-09:00",
    "is_active": true
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `tanggal_sesi` | string | Yes | Session date (max: 255) | Any string format |
| `jam_sesi` | string | Yes | Session time (max: 255) | Any string format |
| `is_active` | boolean | No | Session status (default: true) | `true`, `false` |

### Response Success (201)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Sesi antrian created successfully"
    },
    "data": {
        "kode_sesi_antrian": "SA-240907123456",
        "tanggal_sesi": "2024-09-07",
        "jam_sesi": "08:00-09:00",
        "is_active": true,
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
        "tanggal_sesi": ["The tanggal sesi field is required."],
        "jam_sesi": ["The jam sesi field is required."]
    }
}
```

---

## 3. Get Specific Sesi Antrian

### Request
```http
GET /api/sesi-antrian/{kodeSesiAntrian}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeSesiAntrian` | string | Yes | Sesi antrian code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Sesi antrian data retrieved successfully"
    },
    "data": {
        "kode_sesi_antrian": "SA-240907123456",
        "tanggal_sesi": "2024-09-07",
        "jam_sesi": "08:00-09:00",
        "is_active": true,
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
        "message": "Sesi antrian not found"
    },
    "data": null
}
```

---

## 4. Update Sesi Antrian

### Request
```http
PUT /api/sesi-antrian/update/{kodeSesiAntrian}
Content-Type: application/json
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeSesiAntrian` | string | Yes | Sesi antrian code |

### Request Body
```json
{
    "tanggal_sesi": "2024-09-08",
    "jam_sesi": "09:00-10:00",
    "is_active": false
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `tanggal_sesi` | string | No | Session date (max: 255) | Any string format |
| `jam_sesi` | string | No | Session time (max: 255) | Any string format |
| `is_active` | boolean | No | Session status | `true`, `false` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Sesi antrian updated successfully"
    },
    "data": {
        "kode_sesi_antrian": "SA-240907123456",
        "tanggal_sesi": "2024-09-08",
        "jam_sesi": "09:00-10:00",
        "is_active": false,
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
        "message": "Sesi antrian not found"
    },
    "data": null
}
```

---

## 5. Delete Sesi Antrian

### Request
```http
DELETE /api/sesi-antrian/delete/{kodeSesiAntrian}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeSesiAntrian` | string | Yes | Sesi antrian code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Sesi antrian deleted successfully"
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
        "message": "Sesi antrian not found"
    },
    "data": null
}
```

---

## 6. Toggle Sesi Antrian Status

### Request
```http
PATCH /api/sesi-antrian/status/{kodeSesiAntrian}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeSesiAntrian` | string | Yes | Sesi antrian code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Sesi antrian deactivated successfully"
    },
    "data": {
        "kode_sesi_antrian": "SA-240907123456",
        "tanggal_sesi": "2024-09-07",
        "jam_sesi": "08:00-09:00",
        "is_active": false
    }
}
```

### Status Toggle Logic
- `true` → `false` (activated → deactivated)
- `false` → `true` (deactivated → activated)

---

## Status Values

| Status | Description |
|--------|-------------|
| `true` | Session is active and available |
| `false` | Session is inactive/disabled |

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

### Create a Morning Session
```bash
curl -X POST http://localhost/api/sesi-antrian/create \
  -H "Content-Type: application/json" \
  -d '{
    "tanggal_sesi": "2024-09-07",
    "jam_sesi": "08:00-09:00",
    "is_active": true
  }'
```

### Get All Active Sessions
```bash
curl "http://localhost/api/sesi-antrian?is_active=true&per_page=20"
```

### Update Session Status
```bash
curl -X PATCH http://localhost/api/sesi-antrian/status/SA-240907123456
```

### Search Sessions by Date
```bash
curl "http://localhost/api/sesi-antrian?search=2024-09"
```

### Get Sessions for Specific Date
```bash
curl "http://localhost/api/sesi-antrian?tanggal_sesi=2024-09-07"
```

---

## Model Information

### SesiAntrian Model
```php
class SesiAntrian extends Model
{
    use HasFactory;
    
    protected $table = 'sesi_antrian';
    protected $primaryKey = 'kode_sesi_antrian';
    public $incrementing = false;
    protected $keyType = 'string';
    
    protected $guarded = [];
    
    protected $casts = [
        'tanggal_sesi' => 'date',
        'is_active' => 'boolean',
    ];
}
```

### Route Registration
Add these routes to `routes/api.php`:
```php
// Sesi Antrian routes
Route::get('/sesi-antrian', [SesiAntrianController::class, 'index']);
Route::post('/sesi-antrian/create', [SesiAntrianController::class, 'store']);
Route::get('/sesi-antrian/{kodeSesiAntrian}', [SesiAntrianController::class, 'show']);
Route::put('/sesi-antrian/update/{kodeSesiAntrian}', [SesiAntrianController::class, 'update']);
Route::delete('/sesi-antrian/delete/{kodeSesiAntrian}', [SesiAntrianController::class, 'destroy']);
Route::patch('/sesi-antrian/status/{kodeSesiAntrian}', [SesiAntrianController::class, 'updateStatus']);
```

Don't forget to import the controller:
```php
use App\Http\Controllers\Api\SesiAntrianController;
```
