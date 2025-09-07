# Kategori Perawatan API Documentation

## Base URL
```
/api/kategori-perawatan
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/kategori-perawatan` | Get all kategori perawatan with pagination and filters |
| POST | `/kategori-perawatan/create` | Create new kategori perawatan |
| GET | `/kategori-perawatan/{kodeKategoriPerawatan}` | Get specific kategori perawatan by code |
| PUT | `/kategori-perawatan/update/{kodeKategoriPerawatan}` | Update kategori perawatan |
| DELETE | `/kategori-perawatan/delete/{kodeKategoriPerawatan}` | Delete kategori perawatan |
| PATCH | `/kategori-perawatan/status/{kodeKategoriPerawatan}` | Toggle kategori perawatan status |

---

## 1. Get All Kategori Perawatan

### Request
```http
GET /api/kategori-perawatan
```

### Query Parameters
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `nama` | string | No | Filter by category name (partial match) | `Bedah` |
| `tipe` | string | No | Filter by type | `medis` or `non_medis` |
| `is_active` | boolean | No | Filter by active status | `true` or `false` |
| `search` | string | No | Search in name or description | `operasi` |
| `per_page` | integer | No | Items per page (default: 10) | `15` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori perawatan data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_kategori_perawatan": "KP-240907123456",
                "nama": "Bedah Umum",
                "tipe": "medis",
                "deskripsi": "Kategori untuk tindakan bedah umum dan operasi",
                "is_active": true,
                "created_at": "2024-09-07T12:34:56.000000Z",
                "updated_at": "2024-09-07T12:34:56.000000Z"
            }
        ],
        "first_page_url": "http://localhost/api/kategori-perawatan?page=1",
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
        "message": "Failed to retrieve kategori perawatan data: [error message]"
    },
    "data": null
}
```

---

## 2. Create Kategori Perawatan

### Request
```http
POST /api/kategori-perawatan/create
Content-Type: application/json
```

### Request Body
```json
{
    "nama": "Bedah Umum",
    "tipe": "medis",
    "deskripsi": "Kategori untuk tindakan bedah umum dan operasi",
    "is_active": true
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `nama` | string | Yes | Category name (max: 255) | Any string |
| `tipe` | string | No | Category type (default: medis) | `medis`, `non_medis` |
| `deskripsi` | string | Yes | Category description | Any long text |
| `is_active` | boolean | No | Category status (default: true) | `true`, `false` |

### Response Success (201)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Kategori perawatan created successfully"
    },
    "data": {
        "kode_kategori_perawatan": "KP-240907123456",
        "nama": "Bedah Umum",
        "tipe": "medis",
        "deskripsi": "Kategori untuk tindakan bedah umum dan operasi",
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
        "nama": ["The nama field is required."],
        "deskripsi": ["The deskripsi field is required."]
    }
}
```

---

## 3. Get Specific Kategori Perawatan

### Request
```http
GET /api/kategori-perawatan/{kodeKategoriPerawatan}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeKategoriPerawatan` | string | Yes | Kategori perawatan code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori perawatan data retrieved successfully"
    },
    "data": {
        "kode_kategori_perawatan": "KP-240907123456",
        "nama": "Bedah Umum",
        "tipe": "medis",
        "deskripsi": "Kategori untuk tindakan bedah umum dan operasi",
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
        "message": "Kategori perawatan not found"
    },
    "data": null
}
```

---

## 4. Update Kategori Perawatan

### Request
```http
PUT /api/kategori-perawatan/update/{kodeKategoriPerawatan}
Content-Type: application/json
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeKategoriPerawatan` | string | Yes | Kategori perawatan code |

### Request Body
```json
{
    "nama": "Bedah Khusus",
    "tipe": "medis",
    "deskripsi": "Kategori untuk tindakan bedah khusus dan spesialis",
    "is_active": false
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `nama` | string | No | Category name (max: 255) | Any string |
| `tipe` | string | No | Category type | `medis`, `non_medis` |
| `deskripsi` | string | No | Category description | Any long text |
| `is_active` | boolean | No | Category status | `true`, `false` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori perawatan updated successfully"
    },
    "data": {
        "kode_kategori_perawatan": "KP-240907123456",
        "nama": "Bedah Khusus",
        "tipe": "medis",
        "deskripsi": "Kategori untuk tindakan bedah khusus dan spesialis",
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
        "message": "Kategori perawatan not found"
    },
    "data": null
}
```

---

## 5. Delete Kategori Perawatan

### Request
```http
DELETE /api/kategori-perawatan/delete/{kodeKategoriPerawatan}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeKategoriPerawatan` | string | Yes | Kategori perawatan code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori perawatan deleted successfully"
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
        "message": "Kategori perawatan not found"
    },
    "data": null
}
```

---

## 6. Toggle Kategori Perawatan Status

### Request
```http
PATCH /api/kategori-perawatan/status/{kodeKategoriPerawatan}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeKategoriPerawatan` | string | Yes | Kategori perawatan code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori perawatan deactivated successfully"
    },
    "data": {
        "kode_kategori_perawatan": "KP-240907123456",
        "nama": "Bedah Umum",
        "tipe": "medis",
        "is_active": false
    }
}
```

### Status Toggle Logic
- `true` → `false` (activated → deactivated)
- `false` → `true` (deactivated → activated)

---

## Tipe Values

| Tipe | Description |
|------|-------------|
| `medis` | Medical treatment category |
| `non_medis` | Non-medical treatment category |

## Status Values

| Status | Description |
|--------|-------------|
| `true` | Category is active and available |
| `false` | Category is inactive/disabled |

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

### Create a Medical Category
```bash
curl -X POST http://localhost/api/kategori-perawatan/create \
  -H "Content-Type: application/json" \
  -d '{
    "nama": "Bedah Jantung",
    "tipe": "medis",
    "deskripsi": "Kategori untuk operasi dan tindakan bedah jantung",
    "is_active": true
  }'
```

### Get All Medical Categories
```bash
curl "http://localhost/api/kategori-perawatan?tipe=medis&is_active=true"
```

### Update Category Status
```bash
curl -X PATCH http://localhost/api/kategori-perawatan/status/KP-240907123456
```

### Search Categories by Name
```bash
curl "http://localhost/api/kategori-perawatan?search=bedah"
```

### Get Non-Medical Categories
```bash
curl "http://localhost/api/kategori-perawatan?tipe=non_medis&per_page=20"
```

---

## Model Information

### KategoriPerawatan Model
```php
class KategoriPerawatan extends Model
{
    use HasFactory;
    
    protected $table = 'kategori_perawatan';
    protected $primaryKey = 'kode_kategori_perawatan';
    public $incrementing = false;
    protected $keyType = 'string';
    
    protected $guarded = [];
    
    protected $casts = [
        'is_active' => 'boolean',
    ];
}
```

### Route Registration
Add these routes to `routes/api.php`:
```php
// Kategori Perawatan routes
Route::get('/kategori-perawatan', [KategoriPerawatanController::class, 'index']);
Route::post('/kategori-perawatan/create', [KategoriPerawatanController::class, 'store']);
Route::get('/kategori-perawatan/{kodeKategoriPerawatan}', [KategoriPerawatanController::class, 'show']);
Route::put('/kategori-perawatan/update/{kodeKategoriPerawatan}', [KategoriPerawatanController::class, 'update']);
Route::delete('/kategori-perawatan/delete/{kodeKategoriPerawatan}', [KategoriPerawatanController::class, 'destroy']);
Route::patch('/kategori-perawatan/status/{kodeKategoriPerawatan}', [KategoriPerawatanController::class, 'updateStatus']);
```

Don't forget to import the controller:
```php
use App\Http\Controllers\Api\KategoriPerawatanController;
```

---

## Business Logic Examples

### Filter Medical Categories Only
```bash
curl "http://localhost/api/kategori-perawatan?tipe=medis"
```

### Search in Name and Description
```bash
curl "http://localhost/api/kategori-perawatan?search=operasi"
```

### Get Active Categories with Pagination
```bash
curl "http://localhost/api/kategori-perawatan?is_active=true&per_page=25&page=2"
```
