# Treatment API Documentation

## Base URL
```
/api/treatment
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/treatment` | Get all treatments with pagination and filters |
| POST | `/treatment/create` | Create new treatment |
| GET | `/treatment/{kodeTreatment}` | Get specific treatment by code |
| PUT | `/treatment/update/{kodeTreatment}` | Update treatment |
| DELETE | `/treatment/delete/{kodeTreatment}` | Delete treatment |
| PATCH | `/treatment/status/{kodeTreatment}` | Toggle treatment status |

---

## 1. Get All Treatments

### Request
```http
GET /api/treatment
```

### Query Parameters
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `kode_kategori_perawatan` | string | No | Filter by category code | `KP-240907123456` |
| `nama` | string | No | Filter by treatment name (partial match) | `Konsultasi` |
| `harga_min` | decimal | No | Minimum price filter | `100000` |
| `harga_max` | decimal | No | Maximum price filter | `500000` |
| `is_active` | boolean | No | Filter by active status | `true` or `false` |
| `search` | string | No | Search in name or description | `bedah` |
| `per_page` | integer | No | Items per page (default: 10) | `15` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Treatment data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_treatment": "TR-240907123456",
                "kode_kategori_perawatan": "KP-240907123456",
                "nama": "Konsultasi Umum",
                "deskripsi": "Konsultasi kesehatan umum dengan dokter",
                "harga_treatment": "150000.00",
                "fee_dokter": "100000.00",
                "fee_suster": "25000.00",
                "is_active": true,
                "foto_treatment": "images/treatments/treatment123.jpg",
                "created_at": "2024-09-07T12:34:56.000000Z",
                "updated_at": "2024-09-07T12:34:56.000000Z",
                "kategori_perawatan": {
                    "kode_kategori_perawatan": "KP-240907123456",
                    "nama": "Konsultasi",
                    "tipe": "medis"
                }
            }
        ],
        "first_page_url": "http://localhost/api/treatment?page=1",
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
        "message": "Failed to retrieve treatment data: [error message]"
    },
    "data": null
}
```

---

## 2. Create Treatment

### Request
```http
POST /api/treatment/create
Content-Type: multipart/form-data
```

### Request Body (Form Data)
```
kode_kategori_perawatan: KP-240907123456
nama: Konsultasi Umum
deskripsi: Konsultasi kesehatan umum dengan dokter
harga_treatment: 150000
fee_dokter: 100000
fee_suster: 25000
is_active: true
foto_treatment: [file]
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `kode_kategori_perawatan` | string | Yes | Category code (must exist) | Valid category code |
| `nama` | string | Yes | Treatment name (max: 255) | Any string |
| `deskripsi` | string | Yes | Treatment description | Any long text |
| `harga_treatment` | decimal | Yes | Treatment price (min: 0) | Positive number |
| `fee_dokter` | decimal | No | Doctor fee (min: 0) | Positive number |
| `fee_suster` | decimal | No | Nurse fee (min: 0) | Positive number |
| `is_active` | boolean | No | Treatment status (default: true) | `true`, `false` |
| `foto_treatment` | file | No | Treatment image (max: 2MB) | jpg, png, gif, jpeg |

### Response Success (201)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Treatment created successfully"
    },
    "data": {
        "kode_treatment": "TR-240907123456",
        "kode_kategori_perawatan": "KP-240907123456",
        "nama": "Konsultasi Umum",
        "deskripsi": "Konsultasi kesehatan umum dengan dokter",
        "harga_treatment": "150000.00",
        "fee_dokter": "100000.00",
        "fee_suster": "25000.00",
        "is_active": true,
        "foto_treatment": "images/treatments/treatment123.jpg",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T12:34:56.000000Z",
        "kategori_perawatan": {
            "kode_kategori_perawatan": "KP-240907123456",
            "nama": "Konsultasi",
            "tipe": "medis"
        }
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
        "harga_treatment": ["The harga treatment field is required."]
    }
}
```

---

## 3. Get Specific Treatment

### Request
```http
GET /api/treatment/{kodeTreatment}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeTreatment` | string | Yes | Treatment code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Treatment data retrieved successfully"
    },
    "data": {
        "kode_treatment": "TR-240907123456",
        "kode_kategori_perawatan": "KP-240907123456",
        "nama": "Konsultasi Umum",
        "deskripsi": "Konsultasi kesehatan umum dengan dokter",
        "harga_treatment": "150000.00",
        "fee_dokter": "100000.00",
        "fee_suster": "25000.00",
        "is_active": true,
        "foto_treatment": "images/treatments/treatment123.jpg",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T12:34:56.000000Z",
        "kategori_perawatan": {
            "kode_kategori_perawatan": "KP-240907123456",
            "nama": "Konsultasi",
            "tipe": "medis",
            "deskripsi": "Kategori untuk konsultasi medis"
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
        "message": "Treatment not found"
    },
    "data": null
}
```

---

## 4. Update Treatment

### Request
```http
PUT /api/treatment/update/{kodeTreatment}
Content-Type: multipart/form-data
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeTreatment` | string | Yes | Treatment code |

### Request Body (Form Data)
```
nama: Konsultasi Spesialis
deskripsi: Konsultasi dengan dokter spesialis
harga_treatment: 200000
fee_dokter: 150000
foto_treatment: [file]
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `kode_kategori_perawatan` | string | No | Category code (must exist) | Valid category code |
| `nama` | string | No | Treatment name (max: 255) | Any string |
| `deskripsi` | string | No | Treatment description | Any long text |
| `harga_treatment` | decimal | No | Treatment price (min: 0) | Positive number |
| `fee_dokter` | decimal | No | Doctor fee (min: 0) | Positive number |
| `fee_suster` | decimal | No | Nurse fee (min: 0) | Positive number |
| `is_active` | boolean | No | Treatment status | `true`, `false` |
| `foto_treatment` | file | No | Treatment image (max: 2MB) | jpg, png, gif, jpeg |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Treatment updated successfully"
    },
    "data": {
        "kode_treatment": "TR-240907123456",
        "kode_kategori_perawatan": "KP-240907123456",
        "nama": "Konsultasi Spesialis",
        "deskripsi": "Konsultasi dengan dokter spesialis",
        "harga_treatment": "200000.00",
        "fee_dokter": "150000.00",
        "fee_suster": "25000.00",
        "is_active": true,
        "foto_treatment": "images/treatments/updated_treatment.jpg",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T14:30:00.000000Z",
        "kategori_perawatan": {
            "kode_kategori_perawatan": "KP-240907123456",
            "nama": "Konsultasi",
            "tipe": "medis"
        }
    }
}
```

---

## 5. Delete Treatment

### Request
```http
DELETE /api/treatment/delete/{kodeTreatment}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeTreatment` | string | Yes | Treatment code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Treatment deleted successfully"
    },
    "data": null
}
```

---

## 6. Toggle Treatment Status

### Request
```http
PATCH /api/treatment/status/{kodeTreatment}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeTreatment` | string | Yes | Treatment code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Treatment deactivated successfully"
    },
    "data": {
        "kode_treatment": "TR-240907123456",
        "nama": "Konsultasi Umum",
        "harga_treatment": "150000.00",
        "is_active": false
    }
}
```

---

## File Upload Specifications

### Image Requirements
- **Allowed formats**: JPEG, PNG, JPG, GIF
- **Maximum size**: 2MB (2048KB)
- **Storage location**: `storage/app/public/images/treatments/`
- **Public access**: `storage/images/treatments/filename.jpg`

### File Naming
- Files are automatically renamed with unique timestamps
- Original file extension is preserved
- Path is stored as: `images/treatments/filename.ext`

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

### Create Treatment with Image
```bash
curl -X POST http://localhost/api/treatment/create \
  -H "Content-Type: multipart/form-data" \
  -F "kode_kategori_perawatan=KP-240907123456" \
  -F "nama=Konsultasi Jantung" \
  -F "deskripsi=Konsultasi kesehatan jantung" \
  -F "harga_treatment=300000" \
  -F "fee_dokter=200000" \
  -F "fee_suster=50000" \
  -F "foto_treatment=@/path/to/image.jpg"
```

### Get Treatments by Category
```bash
curl "http://localhost/api/treatment?kode_kategori_perawatan=KP-240907123456"
```

### Filter by Price Range
```bash
curl "http://localhost/api/treatment?harga_min=100000&harga_max=500000"
```

### Search Treatments
```bash
curl "http://localhost/api/treatment?search=konsultasi&is_active=true"
```

### Update Treatment Image
```bash
curl -X PUT http://localhost/api/treatment/update/TR-240907123456 \
  -H "Content-Type: multipart/form-data" \
  -F "foto_treatment=@/path/to/new_image.jpg"
```

---

## Model Information

### Treatment Model
```php
class Treatment extends Model
{
    use HasFactory;
    
    protected $table = 'treatments';
    protected $primaryKey = 'kode_treatment';
    public $incrementing = false;
    protected $keyType = 'string';
    
    protected $guarded = [];
    
    protected $casts = [
        'harga_treatment' => 'decimal:2',
        'fee_dokter' => 'decimal:2',
        'fee_suster' => 'decimal:2',
        'is_active' => 'boolean',
    ];
    
    // Relationships
    public function kategoriPerawatan()
    {
        return $this->belongsTo(KategoriPerawatan::class, 'kode_kategori_perawatan', 'kode_kategori_perawatan');
    }
}
```

### Route Registration
Add these routes to `routes/api.php`:
```php
// Treatment routes
Route::get('/treatment', [TreatmentController::class, 'index']);
Route::post('/treatment/create', [TreatmentController::class, 'store']);
Route::get('/treatment/{kodeTreatment}', [TreatmentController::class, 'show']);
Route::put('/treatment/update/{kodeTreatment}', [TreatmentController::class, 'update']);
Route::delete('/treatment/delete/{kodeTreatment}', [TreatmentController::class, 'destroy']);
Route::patch('/treatment/status/{kodeTreatment}', [TreatmentController::class, 'updateStatus']);
```

Don't forget to import the controller:
```php
use App\Http\Controllers\Api\TreatmentController;
```

---

## Storage Configuration

### Symbolic Link
Make sure to create symbolic link for public access:
```bash
php artisan storage:link
```

### Access Image URL
```
http://localhost/storage/images/treatments/filename.jpg
```

### Directory Permissions
Ensure `storage/app/public/images/treatments/` directory is writable.
