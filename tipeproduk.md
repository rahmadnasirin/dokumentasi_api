# Tipe Produk API Documentation

## Base URL
```
/api/tipe-produk
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/tipe-produk` | Get all tipe produk with pagination and filters |
| POST | `/tipe-produk/create` | Create new tipe produk |
| GET | `/tipe-produk/{kodeTipeProduk}` | Get specific tipe produk by code |
| PUT | `/tipe-produk/update/{kodeTipeProduk}` | Update tipe produk |
| DELETE | `/tipe-produk/delete/{kodeTipeProduk}` | Delete tipe produk |
| PATCH | `/tipe-produk/status/{kodeTipeProduk}` | Toggle tipe produk status |

---

## 1. Get All Tipe Produk

### Request
```http
GET /api/tipe-produk
```

### Query Parameters
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `nama` | string | No | Filter by type name (partial match) | `Medis` |
| `is_active` | boolean | No | Filter by active status | `true` or `false` |
| `search` | string | No | Search in name or description | `medis` |
| `per_page` | integer | No | Items per page (default: 10) | `15` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Tipe produk data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_tipe_produk": "TP-240907123456",
                "nama": "Medis",
                "deskripsi": "Produk-produk medis dan kesehatan",
                "is_active": true,
                "created_at": "2024-09-07T12:34:56.000000Z",
                "updated_at": "2024-09-07T12:34:56.000000Z"
            },
            {
                "kode_tipe_produk": "TP-240907123457",
                "nama": "Non Medis",
                "deskripsi": "Produk-produk non medis dan umum",
                "is_active": true,
                "created_at": "2024-09-07T12:35:30.000000Z",
                "updated_at": "2024-09-07T12:35:30.000000Z"
            }
        ],
        "first_page_url": "http://localhost/api/tipe-produk?page=1",
        "from": 1,
        "last_page": 1,
        "per_page": 10,
        "total": 2
    }
}
```

### Response Error (500)
```json
{
    "meta": {
        "code": 500,
        "status": "error",
        "message": "Failed to retrieve tipe produk data: [error message]"
    },
    "data": null
}
```

---

## 2. Create Tipe Produk

### Request
```http
POST /api/tipe-produk/create
Content-Type: application/json
```

### Request Body
```json
{
    "nama": "Medis",
    "deskripsi": "Produk-produk medis dan kesehatan seperti obat-obatan, alat kesehatan, dan suplemen",
    "is_active": true
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `nama` | string | Yes | Type name (max: 255) | Any string |
| `deskripsi` | string | Yes | Type description | Any long text |
| `is_active` | boolean | No | Type status (default: true) | `true`, `false` |

### Response Success (201)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Tipe produk created successfully"
    },
    "data": {
        "kode_tipe_produk": "TP-240907123456",
        "nama": "Medis",
        "deskripsi": "Produk-produk medis dan kesehatan seperti obat-obatan, alat kesehatan, dan suplemen",
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

## 3. Get Specific Tipe Produk

### Request
```http
GET /api/tipe-produk/{kodeTipeProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeTipeProduk` | string | Yes | Tipe produk code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Tipe produk data retrieved successfully"
    },
    "data": {
        "kode_tipe_produk": "TP-240907123456",
        "nama": "Medis",
        "deskripsi": "Produk-produk medis dan kesehatan seperti obat-obatan, alat kesehatan, dan suplemen",
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
        "message": "Tipe produk not found"
    },
    "data": null
}
```

---

## 4. Update Tipe Produk

### Request
```http
PUT /api/tipe-produk/update/{kodeTipeProduk}
Content-Type: application/json
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeTipeProduk` | string | Yes | Tipe produk code |

### Request Body
```json
{
    "nama": "Medical Products",
    "deskripsi": "Produk-produk medis, farmasi, dan kesehatan untuk keperluan rumah sakit dan klinik",
    "is_active": false
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `nama` | string | No | Type name (max: 255) | Any string |
| `deskripsi` | string | No | Type description | Any long text |
| `is_active` | boolean | No | Type status | `true`, `false` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Tipe produk updated successfully"
    },
    "data": {
        "kode_tipe_produk": "TP-240907123456",
        "nama": "Medical Products",
        "deskripsi": "Produk-produk medis, farmasi, dan kesehatan untuk keperluan rumah sakit dan klinik",
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
        "message": "Tipe produk not found"
    },
    "data": null
}
```

---

## 5. Delete Tipe Produk

### Request
```http
DELETE /api/tipe-produk/delete/{kodeTipeProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeTipeProduk` | string | Yes | Tipe produk code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Tipe produk deleted successfully"
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
        "message": "Tipe produk not found"
    },
    "data": null
}
```

---

## 6. Toggle Tipe Produk Status

### Request
```http
PATCH /api/tipe-produk/status/{kodeTipeProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeTipeProduk` | string | Yes | Tipe produk code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Tipe produk deactivated successfully"
    },
    "data": {
        "kode_tipe_produk": "TP-240907123456",
        "nama": "Medis",
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
| `true` | Type is active and available |
| `false` | Type is inactive/disabled |

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

### Create Medical Type
```bash
curl -X POST http://localhost/api/tipe-produk/create \
  -H "Content-Type: application/json" \
  -d '{
    "nama": "Medis",
    "deskripsi": "Produk-produk medis dan kesehatan seperti obat, alat medis, dan suplemen kesehatan",
    "is_active": true
  }'
```

### Create Non-Medical Type
```bash
curl -X POST http://localhost/api/tipe-produk/create \
  -H "Content-Type: application/json" \
  -d '{
    "nama": "Non Medis",
    "deskripsi": "Produk-produk non medis seperti makanan, minuman, dan perawatan umum",
    "is_active": true
  }'
```

### Get All Active Types
```bash
curl "http://localhost/api/tipe-produk?is_active=true"
```

### Update Type Status
```bash
curl -X PATCH http://localhost/api/tipe-produk/status/TP-240907123456
```

### Search Types by Name
```bash
curl "http://localhost/api/tipe-produk?search=medis"
```

### Get Specific Type
```bash
curl "http://localhost/api/tipe-produk/TP-240907123456"
```

---

## Model Information

### TipeProduk Model
```php
class TipeProduk extends Model
{
    use HasFactory;
    
    protected $table = 'tipe_produk';
    protected $primaryKey = 'kode_tipe_produk';
    public $incrementing = false;
    protected $keyType = 'string';
    
    protected $guarded = [];
    
    protected $casts = [
        'is_active' => 'boolean',
    ];
    
    // Relationship with kategori_produk
    public function kategoriProduk()
    {
        return $this->hasMany(KategoriProduk::class, 'kode_tipe_produk');
    }
}
```

### Route Registration
Add these routes to `routes/api.php`:
```php
// Tipe Produk routes
Route::get('/tipe-produk', [TipeProdukController::class, 'index']);
Route::post('/tipe-produk/create', [TipeProdukController::class, 'store']);
Route::get('/tipe-produk/{kodeTipeProduk}', [TipeProdukController::class, 'show']);
Route::put('/tipe-produk/update/{kodeTipeProduk}', [TipeProdukController::class, 'update']);
Route::delete('/tipe-produk/delete/{kodeTipeProduk}', [TipeProdukController::class, 'destroy']);
Route::patch('/tipe-produk/status/{kodeTipeProduk}', [TipeProdukController::class, 'updateStatus']);
```

Don't forget to import the controller:
```php
use App\Http\Controllers\Api\TipeProdukController;
```

---

## Business Logic Examples

### Get Active Types Only
```bash
curl "http://localhost/api/tipe-produk?is_active=true"
```

### Search in Name and Description
```bash
curl "http://localhost/api/tipe-produk?search=kesehatan"
```

### Get Types with Pagination
```bash
curl "http://localhost/api/tipe-produk?per_page=5&page=1"
```

### Filter by Specific Name
```bash
curl "http://localhost/api/tipe-produk?nama=Medis"
```

---

## Code Generation Pattern

The `kode_tipe_produk` follows this pattern:
- **Format**: `TP-YYMMDDHHMMSS`
- **Example**: `TP-240907123456`
- **Logic**: Auto-generated using current timestamp
- **Uniqueness**: Checked to ensure no duplicates exist

### Code Generation Logic
```php
$kodeTipeProduk = 'TP-' . date('ymdhis');
while (TipeProduk::where('kode_tipe_produk', $kodeTipeProduk)->exists()) {
    $kodeTipeProduk = 'TP-' . date('ymdhis');
}
```

---

## Database Schema

```sql
CREATE TABLE tipe_produk (
    kode_tipe_produk VARCHAR(255) PRIMARY KEY,
    nama VARCHAR(255) NOT NULL,
    deskripsi LONGTEXT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

---

## Related APIs

- **Kategori Produk API**: `/api/kategori-produk`
  - Depends on Tipe Produk via `kode_tipe_produk` foreign key
  - Endpoint: `/api/kategori-produk/tipe` - Get categories by type

---

## Common Use Cases

### 1. Setup Basic Product Types
```bash
# Create Medical Type
curl -X POST http://localhost/api/tipe-produk/create \
  -H "Content-Type: application/json" \
  -d '{"nama": "Medis", "deskripsi": "Produk medis dan kesehatan"}'

# Create Non-Medical Type
curl -X POST http://localhost/api/tipe-produk/create \
  -H "Content-Type: application/json" \
  -d '{"nama": "Non Medis", "deskripsi": "Produk non medis dan umum"}'
```

### 2. Manage Product Type Hierarchy
```bash
# Get all types for dropdown
curl "http://localhost/api/tipe-produk?is_active=true"

# Disable a type (affects all related categories)
curl -X PATCH http://localhost/api/tipe-produk/status/TP-240907123456
```

### 3. Search and Filter
```bash
# Search types
curl "http://localhost/api/tipe-produk?search=medis"

# Get paginated active types
curl "http://localhost/api/tipe-produk?is_active=true&per_page=20"
```
