# Kategori Produk API Documentation

## Base URL
```
/api/kategori-produk
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/kategori-produk` | Get all kategori produk with pagination and filters |
| GET | `/kategori-produk/tipe` | Get all tipe produk for dropdown |
| POST | `/kategori-produk/create` | Create new kategori produk |
| GET | `/kategori-produk/{kodeKategoriProduk}` | Get specific kategori produk by code |
| PUT | `/kategori-produk/update/{kodeKategoriProduk}` | Update kategori produk |
| DELETE | `/kategori-produk/delete/{kodeKategoriProduk}` | Delete kategori produk |
| PATCH | `/kategori-produk/status/{kodeKategoriProduk}` | Toggle kategori produk status |

---

## 1. Get All Kategori Produk

### Request
```http
GET /api/kategori-produk
```

### Query Parameters
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `nama` | string | No | Filter by category name (partial match) | `Suplemen` |
| `is_active` | boolean | No | Filter by active status | `true` or `false` |
| `search` | string | No | Search in name or description | `vitamin` |
| `per_page` | integer | No | Items per page (default: 10) | `15` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori produk data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_kategori_produk": "KPR-240907123456",
                "kode_tipe_produk": "TP-240907123455",
                "nama": "Suplemen",
                "deskripsi": "Produk suplemen kesehatan dan vitamin",
                "is_active": true,
                "created_at": "2024-09-07T12:34:56.000000Z",
                "updated_at": "2024-09-07T12:34:56.000000Z"
            },
            {
                "kode_kategori_produk": "KPR-240907123457",
                "kode_tipe_produk": "TP-240907123455",
                "nama": "Alat Kesehatan",
                "deskripsi": "Peralatan medis dan kesehatan",
                "is_active": true,
                "created_at": "2024-09-07T12:35:30.000000Z",
                "updated_at": "2024-09-07T12:35:30.000000Z"
            }
        ],
        "first_page_url": "http://localhost/api/kategori-produk?page=1",
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
        "message": "Failed to retrieve kategori produk data: [error message]"
    },
    "data": null
}
```

---

## 2. Get Tipe Produk for Dropdown

### Request
```http
GET /api/kategori-produk/tipe
```

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Tipe data retrieved successfully"
    },
    "data": [
        {
            "kode_tipe_produk": "TP-240907123455",
            "nama": "Medis",
            "deskripsi": "Produk-produk medis dan kesehatan",
            "is_active": true,
            "created_at": "2024-09-07T12:30:00.000000Z",
            "updated_at": "2024-09-07T12:30:00.000000Z"
        },
        {
            "kode_tipe_produk": "TP-240907123456",
            "nama": "Non Medis",
            "deskripsi": "Produk-produk non medis dan umum",
            "is_active": true,
            "created_at": "2024-09-07T12:31:00.000000Z",
            "updated_at": "2024-09-07T12:31:00.000000Z"
        }
    ]
}
```

---

## 3. Create Kategori Produk

### Request
```http
POST /api/kategori-produk/create
Content-Type: application/json
```

### Request Body
```json
{
    "kode_tipe_produk": "TP-240907123455",
    "nama": "Suplemen",
    "deskripsi": "Kategori untuk produk suplemen kesehatan, vitamin, dan nutrisi tambahan",
    "is_active": true
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `kode_tipe_produk` | string | Yes | Foreign key to tipe_produk | Valid tipe produk code |
| `nama` | string | Yes | Category name (max: 255) | Any string |
| `deskripsi` | string | Yes | Category description | Any long text |
| `is_active` | boolean | No | Category status (default: true) | `true`, `false` |

### Response Success (201)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Kategori produk created successfully"
    },
    "data": {
        "kode_kategori_produk": "KPR-240907123456",
        "kode_tipe_produk": "TP-240907123455",
        "nama": "Suplemen",
        "deskripsi": "Kategori untuk produk suplemen kesehatan, vitamin, dan nutrisi tambahan",
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
        "kode_tipe_produk": ["The kode tipe produk field is required."],
        "nama": ["The nama field is required."],
        "deskripsi": ["The deskripsi field is required."]
    }
}
```

### Response Error - Invalid Tipe Produk (422)
```json
{
    "meta": {
        "code": 422,
        "status": "error",
        "message": "Validation failed"
    },
    "data": {
        "kode_tipe_produk": ["The selected kode tipe produk is invalid."]
    }
}
```

---

## 4. Get Specific Kategori Produk

### Request
```http
GET /api/kategori-produk/{kodeKategoriProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeKategoriProduk` | string | Yes | Kategori produk code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori produk data retrieved successfully"
    },
    "data": {
        "kode_kategori_produk": "KPR-240907123456",
        "kode_tipe_produk": "TP-240907123455",
        "nama": "Suplemen",
        "deskripsi": "Kategori untuk produk suplemen kesehatan, vitamin, dan nutrisi tambahan",
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
        "message": "Kategori produk not found"
    },
    "data": null
}
```

---

## 5. Update Kategori Produk

### Request
```http
PUT /api/kategori-produk/update/{kodeKategoriProduk}
Content-Type: application/json
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeKategoriProduk` | string | Yes | Kategori produk code |

### Request Body
```json
{
    "kode_tipe_produk": "TP-240907123456",
    "nama": "Suplemen & Vitamin",
    "deskripsi": "Kategori untuk produk suplemen kesehatan, vitamin, mineral, dan nutrisi tambahan untuk kesehatan optimal",
    "is_active": false
}
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `kode_tipe_produk` | string | No | Foreign key to tipe_produk | Valid tipe produk code |
| `nama` | string | No | Category name (max: 255) | Any string |
| `deskripsi` | string | No | Category description | Any long text |
| `is_active` | boolean | No | Category status | `true`, `false` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori produk updated successfully"
    },
    "data": {
        "kode_kategori_produk": "KPR-240907123456",
        "kode_tipe_produk": "TP-240907123456",
        "nama": "Suplemen & Vitamin",
        "deskripsi": "Kategori untuk produk suplemen kesehatan, vitamin, mineral, dan nutrisi tambahan untuk kesehatan optimal",
        "is_active": false,
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T14:30:00.000000Z",
        "tipeproduk": {
            "kode_tipe_produk": "TP-240907123456",
            "nama": "Non Medis"
        }
    }
}
```

### Response Error (404)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "Kategori produk not found"
    },
    "data": null
}
```

---

## 6. Delete Kategori Produk

### Request
```http
DELETE /api/kategori-produk/delete/{kodeKategoriProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeKategoriProduk` | string | Yes | Kategori produk code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori produk deleted successfully"
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
        "message": "Kategori produk not found"
    },
    "data": null
}
```

---

## 7. Toggle Kategori Produk Status

### Request
```http
PATCH /api/kategori-produk/status/{kodeKategoriProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeKategoriProduk` | string | Yes | Kategori produk code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori produk deactivated successfully"
    },
    "data": {
        "kode_kategori_produk": "KPR-240907123456",
        "nama": "Suplemen",
        "tipe": null,
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

### Create Medical Category
```bash
curl -X POST http://localhost/api/kategori-produk/create \
  -H "Content-Type: application/json" \
  -d '{
    "kode_tipe_produk": "TP-240907123455",
    "nama": "Suplemen",
    "deskripsi": "Kategori untuk produk suplemen kesehatan dan vitamin",
    "is_active": true
  }'
```

### Create Non-Medical Category
```bash
curl -X POST http://localhost/api/kategori-produk/create \
  -H "Content-Type: application/json" \
  -d '{
    "kode_tipe_produk": "TP-240907123456",
    "nama": "Makanan & Minuman",
    "deskripsi": "Kategori untuk produk makanan dan minuman sehat",
    "is_active": true
  }'
```

### Get All Categories
```bash
curl "http://localhost/api/kategori-produk"
```

### Get Active Categories Only
```bash
curl "http://localhost/api/kategori-produk?is_active=true"
```

### Get Tipe Produk for Form Dropdown
```bash
curl "http://localhost/api/kategori-produk/tipe"
```

### Update Category Status
```bash
curl -X PATCH http://localhost/api/kategori-produk/status/KPR-240907123456
```

### Search Categories by Name
```bash
curl "http://localhost/api/kategori-produk?search=suplemen"
```

### Get Specific Category
```bash
curl "http://localhost/api/kategori-produk/KPR-240907123456"
```

### Update Category with New Type
```bash
curl -X PUT http://localhost/api/kategori-produk/update/KPR-240907123456 \
  -H "Content-Type: application/json" \
  -d '{
    "kode_tipe_produk": "TP-240907123456",
    "nama": "Suplemen Premium",
    "deskripsi": "Kategori khusus untuk suplemen premium dan berkualitas tinggi"
  }'
```

---

## Model Information

### KategoriProduk Model
```php
class KategoriProduk extends Model
{
    use HasFactory;
    
    protected $table = 'kategori_produk';
    protected $primaryKey = 'kode_kategori_produk';
    public $incrementing = false;
    protected $keyType = 'string';
    
    protected $guarded = [];
    
    protected $casts = [
        'is_active' => 'boolean',
    ];
    
    // Relationship with tipe_produk
    public function tipeProduk()
    {
        return $this->belongsTo(TipeProduk::class, 'kode_tipe_produk');
    }
    
    // Relationship with produk
    public function produk()
    {
        return $this->hasMany(Produk::class, 'kode_kategori_produk');
    }
}
```

### Route Registration
Add these routes to `routes/api.php`:
```php
// Kategori Produk routes
Route::get('/kategori-produk', [KategoriProdukController::class, 'index']);
Route::get('/kategori-produk/tipe', [KategoriProdukController::class, 'getTipe']);
Route::post('/kategori-produk/create', [KategoriProdukController::class, 'store']);
Route::get('/kategori-produk/{kodeKategoriProduk}', [KategoriProdukController::class, 'show']);
Route::put('/kategori-produk/update/{kodeKategoriProduk}', [KategoriProdukController::class, 'update']);
Route::delete('/kategori-produk/delete/{kodeKategoriProduk}', [KategoriProdukController::class, 'destroy']);
Route::patch('/kategori-produk/status/{kodeKategoriProduk}', [KategoriProdukController::class, 'updateStatus']);
```

Don't forget to import the controller:
```php
use App\Http\Controllers\Api\KategoriProdukController;
```

---

## Business Logic Examples

### Get Categories by Type
```bash
# First get the tipe produk
curl "http://localhost/api/kategori-produk/tipe"

# Then filter categories (manual filtering needed in frontend)
curl "http://localhost/api/kategori-produk"
```

### Search in Name and Description
```bash
curl "http://localhost/api/kategori-produk?search=vitamin"
```

### Get Categories with Pagination
```bash
curl "http://localhost/api/kategori-produk?per_page=5&page=2"
```

### Filter by Specific Name
```bash
curl "http://localhost/api/kategori-produk?nama=Suplemen"
```

---

## Code Generation Pattern

The `kode_kategori_produk` follows this pattern:
- **Format**: `KPR-YYMMDDHHMMSS`
- **Example**: `KPR-240907123456`
- **Logic**: Auto-generated using current timestamp
- **Uniqueness**: Checked to ensure no duplicates exist

### Code Generation Logic
```php
$kodeKategoriProduk = 'KPR-' . date('ymdhis');
while (KategoriProduk::where('kode_kategori_produk', $kodeKategoriProduk)->exists()) {
    $kodeKategoriProduk = 'KP-' . date('ymdhis'); // Fallback pattern
}
```

---

## Database Schema

```sql
CREATE TABLE kategori_produk (
    kode_kategori_produk VARCHAR(255) PRIMARY KEY,
    kode_tipe_produk VARCHAR(255) NOT NULL,
    nama VARCHAR(255) NOT NULL,
    deskripsi LONGTEXT NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    FOREIGN KEY (kode_tipe_produk) REFERENCES tipe_produk(kode_tipe_produk) ON DELETE CASCADE
);
```

---

## Related APIs

- **Tipe Produk API**: `/api/tipe-produk`
  - Parent of Kategori Produk via `kode_tipe_produk` foreign key
- **Produk API**: `/api/produk` (future)
  - Child of Kategori Produk via `kode_kategori_produk` foreign key

---

## Hierarchical Structure

The product hierarchy works as follows:
```
Tipe Produk (Level 1)
├── Medis
│   ├── Suplemen (Level 2)
│   ├── Alat Kesehatan (Level 2)
│   └── Obat-obatan (Level 2)
└── Non Medis
    ├── Makanan & Minuman (Level 2)
    └── Perawatan Tubuh (Level 2)
```

---

## Common Use Cases

### 1. Setup Product Categories for Medical Products
```bash
# Get medical type first
curl "http://localhost/api/kategori-produk/tipe"

# Create medical categories
curl -X POST http://localhost/api/kategori-produk/create \
  -H "Content-Type: application/json" \
  -d '{"kode_tipe_produk": "TP-240907123455", "nama": "Suplemen", "deskripsi": "Produk suplemen kesehatan"}'

curl -X POST http://localhost/api/kategori-produk/create \
  -H "Content-Type: application/json" \
  -d '{"kode_tipe_produk": "TP-240907123455", "nama": "Alat Kesehatan", "deskripsi": "Peralatan medis"}'
```

### 2. Manage Category Hierarchy
```bash
# Get all categories with their types
curl "http://localhost/api/kategori-produk"

# Change category type
curl -X PUT http://localhost/api/kategori-produk/update/KPR-240907123456 \
  -H "Content-Type: application/json" \
  -d '{"kode_tipe_produk": "TP-240907123456"}'
```

### 3. Search and Filter Categories
```bash
# Search categories
curl "http://localhost/api/kategori-produk?search=suplemen"

# Get active categories only
curl "http://localhost/api/kategori-produk?is_active=true"

# Get paginated results
curl "http://localhost/api/kategori-produk?per_page=20&page=1"
```

---

## Foreign Key Validation

The API validates that `kode_tipe_produk` exists in the `tipe_produk` table:
- **Create**: Required and must exist
- **Update**: Optional but must exist if provided
- **Database**: Foreign key constraint ensures referential integrity

### Example Validation Error
```json
{
    "meta": {
        "code": 422,
        "status": "error",
        "message": "Validation failed"
    },
    "data": {
        "kode_tipe_produk": ["The selected kode tipe produk is invalid."]
    }
}
```
