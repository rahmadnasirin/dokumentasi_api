# Produk API Documentation

## Base URL
```
/api/produk
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/produk` | Get all products with pagination and filters |
| GET | `/produk/tipe` | Get all product types |
| GET | `/produk/kategori` | Get all product categories (can filter by type) |
| GET | `/produk/data` | Get complete product hierarchy (Type → Category → Product) |
| POST | `/produk/create` | Create new product |
| GET | `/produk/{kodeProduk}` | Get specific product by code |
| PUT | `/produk/update/{kodeProduk}` | Update product |
| DELETE | `/produk/delete/{kodeProduk}` | Delete product |
| PATCH | `/produk/status/{kodeProduk}` | Toggle product status |

---

## 1. Get All Products

### Request
```http
GET /api/produk
```

### Query Parameters
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `kode_kategori_produk` | string | No | Filter by category code | `KAT-240907123456` |
| `kode_tipe_produk` | string | No | Filter by type code | `TIP-240907123456` |
| `nama` | string | No | Filter by product name (partial match) | `Vitamin` |
| `is_active` | boolean | No | Filter by active status | `true` or `false` |
| `search` | string | No | Search in name or description | `suplemen` |
| `per_page` | integer | No | Items per page (default: 10) | `15` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Produk data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_produk": "PRD-240907123456",
                "kode_kategori_produk": "KAT-240907123456",
                "nama": "Vitamin C 500mg",
                "deskripsi": "Suplemen vitamin C untuk daya tahan tubuh",
                "satuan": "tablet",
                "berat_produk": 100,
                "harga_beli": "25000.00",
                "harga_jual": "50000.00",
                "fee_penjualan_suster": "2500.00",
                "fee_penjualan_dokter": "5000.00",
                "addons": false,
                "is_active": true,
                "foto_produk": "http://localhost/storage/images/produk/produk123.jpg",
                "created_at": "2024-09-07T12:34:56.000000Z",
                "updated_at": "2024-09-07T12:34:56.000000Z",
                "kategoriproduk": {
                    "kode_kategori_produk": "KAT-240907123456",
                    "kode_tipe_produk": "TIP-240907123456",
                    "nama": "Vitamin & Suplemen",
                    "tipeproduk": {
                        "kode_tipe_produk": "TIP-240907123456",
                        "nama": "Obat & Suplemen"
                    }
                }
            }
        ],
        "first_page_url": "http://localhost/api/produk?page=1",
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
        "message": "Failed to retrieve produk data: [error message]"
    },
    "data": null
}
```

---

## 2. Get Product Types

### Request
```http
GET /api/produk/tipe
```

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Tipe produk data retrieved successfully"
    },
    "data": [
        {
            "kode_tipe_produk": "TIP-240907123456",
            "nama": "Obat & Suplemen",
            "deskripsi": "Tipe untuk produk obat dan suplemen kesehatan",
            "is_active": true,
            "created_at": "2024-09-07T12:34:56.000000Z",
            "updated_at": "2024-09-07T12:34:56.000000Z"
        },
        {
            "kode_tipe_produk": "TIP-240907123457",
            "nama": "Alat Kesehatan",
            "deskripsi": "Tipe untuk alat-alat kesehatan medis",
            "is_active": true,
            "created_at": "2024-09-07T12:34:56.000000Z",
            "updated_at": "2024-09-07T12:34:56.000000Z"
        }
    ]
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

## 3. Get Product Categories

### Request
```http
GET /api/produk/kategori
```

### Query Parameters
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `kode_tipe_produk` | string | No | Filter by type code | `TIP-240907123456` |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Kategori produk data retrieved successfully"
    },
    "data": [
        {
            "kode_kategori_produk": "KAT-240907123456",
            "kode_tipe_produk": "TIP-240907123456",
            "nama": "Vitamin & Suplemen",
            "deskripsi": "Kategori untuk vitamin dan suplemen kesehatan",
            "is_active": true,
            "created_at": "2024-09-07T12:34:56.000000Z",
            "updated_at": "2024-09-07T12:34:56.000000Z",
            "tipeproduk": {
                "kode_tipe_produk": "TIP-240907123456",
                "nama": "Obat & Suplemen"
            }
        }
    ]
}
```

---

## 4. Get Product Hierarchy

### Request
```http
GET /api/produk/data
```

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Product hierarchy data retrieved successfully"
    },
    "data": [
        {
            "kode_tipe_produk": "TIP-240907123456",
            "nama": "Obat & Suplemen",
            "deskripsi": "Tipe untuk produk obat dan suplemen kesehatan",
            "is_active": true,
            "created_at": "2024-09-07T12:34:56.000000Z",
            "updated_at": "2024-09-07T12:34:56.000000Z",
            "kategoriproduk": [
                {
                    "kode_kategori_produk": "KAT-240907123456",
                    "kode_tipe_produk": "TIP-240907123456",
                    "nama": "Vitamin & Suplemen",
                    "deskripsi": "Kategori untuk vitamin dan suplemen kesehatan",
                    "is_active": true,
                    "produk": [
                        {
                            "kode_produk": "PRD-240907123456",
                            "nama": "Vitamin C 500mg",
                            "harga_jual": "50000.00",
                            "is_active": true
                        }
                    ]
                }
            ]
        }
    ]
}
```

---

## 5. Create Product

### Request
```http
POST /api/produk/create
Content-Type: multipart/form-data
```

### Request Body (Form Data)
```
kode_kategori_produk: KAT-240907123456
nama: Vitamin C 500mg
deskripsi: Suplemen vitamin C untuk daya tahan tubuh
satuan: tablet
berat_produk: 100
harga_beli: 25000
harga_jual: 50000
fee_penjualan_dokter: 5000
fee_penjualan_suster: 2500
addons: false
is_active: true
foto_produk: [file]
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `kode_kategori_produk` | string | Yes | Category code (must exist) | Valid category code |
| `nama` | string | Yes | Product name (max: 255) | Any string |
| `deskripsi` | string | Yes | Product description | Any long text |
| `satuan` | string | Yes | Product unit (max: 50) | tablet, kapsul, botol, dll |
| `berat_produk` | integer | Yes | Product weight in grams (min: 1) | Positive integer |
| `harga_beli` | decimal | No | Purchase price (min: 0) | Positive number |
| `harga_jual` | decimal | No | Selling price (min: 0) | Positive number |
| `fee_penjualan_dokter` | decimal | No | Doctor sales fee (min: 0) | Positive number |
| `fee_penjualan_suster` | decimal | No | Nurse sales fee (min: 0) | Positive number |
| `addons` | boolean | No | Is addon product (default: false) | `true`, `false` |
| `is_active` | boolean | No | Product status (default: true) | `true`, `false` |
| `foto_produk` | file | No | Product image (max: 2MB) | jpg, png, gif, jpeg |

### Response Success (201)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Produk created successfully"
    },
    "data": {
        "kode_produk": "PRD-240907123456",
        "kode_kategori_produk": "KAT-240907123456",
        "nama": "Vitamin C 500mg",
        "deskripsi": "Suplemen vitamin C untuk daya tahan tubuh",
        "satuan": "tablet",
        "berat_produk": 100,
        "harga_beli": "25000.00",
        "harga_jual": "50000.00",
        "fee_penjualan_suster": "2500.00",
        "fee_penjualan_dokter": "5000.00",
        "addons": false,
        "is_active": true,
        "foto_produk": "http://localhost/storage/images/produk/produk123.jpg",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T12:34:56.000000Z",
        "kategoriproduk": {
            "kode_kategori_produk": "KAT-240907123456",
            "kode_tipe_produk": "TIP-240907123456",
            "nama": "Vitamin & Suplemen",
            "tipeproduk": {
                "kode_tipe_produk": "TIP-240907123456",
                "nama": "Obat & Suplemen"
            }
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
        "satuan": ["The satuan field is required."],
        "berat_produk": ["The berat produk field is required."]
    }
}
```

---

## 6. Get Specific Product

### Request
```http
GET /api/produk/{kodeProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeProduk` | string | Yes | Product code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Produk data retrieved successfully"
    },
    "data": {
        "kode_produk": "PRD-240907123456",
        "kode_kategori_produk": "KAT-240907123456",
        "nama": "Vitamin C 500mg",
        "deskripsi": "Suplemen vitamin C untuk daya tahan tubuh",
        "satuan": "tablet",
        "berat_produk": 100,
        "harga_beli": "25000.00",
        "harga_jual": "50000.00",
        "fee_penjualan_suster": "2500.00",
        "fee_penjualan_dokter": "5000.00",
        "addons": false,
        "is_active": true,
        "foto_produk": "http://localhost/storage/images/produk/produk123.jpg",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T12:34:56.000000Z",
        "kategoriproduk": {
            "kode_kategori_produk": "KAT-240907123456",
            "kode_tipe_produk": "TIP-240907123456",
            "nama": "Vitamin & Suplemen",
            "deskripsi": "Kategori untuk vitamin dan suplemen kesehatan",
            "tipeproduk": {
                "kode_tipe_produk": "TIP-240907123456",
                "nama": "Obat & Suplemen",
                "deskripsi": "Tipe untuk produk obat dan suplemen kesehatan"
            }
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
        "message": "Produk not found"
    },
    "data": null
}
```

---

## 7. Update Product

### Request
```http
PUT /api/produk/update/{kodeProduk}
Content-Type: multipart/form-data
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeProduk` | string | Yes | Product code |

### Request Body (Form Data)
```
nama: Vitamin C 1000mg
deskripsi: Suplemen vitamin C dosis tinggi untuk daya tahan tubuh
harga_jual: 75000
fee_penjualan_dokter: 7500
foto_produk: [file]
```

### Body Parameters
| Parameter | Type | Required | Description | Valid Values |
|-----------|------|----------|-------------|--------------|
| `kode_kategori_produk` | string | No | Category code (must exist) | Valid category code |
| `nama` | string | No | Product name (max: 255) | Any string |
| `deskripsi` | string | No | Product description | Any long text |
| `satuan` | string | No | Product unit (max: 50) | tablet, kapsul, botol, dll |
| `berat_produk` | integer | No | Product weight in grams (min: 1) | Positive integer |
| `harga_beli` | decimal | No | Purchase price (min: 0) | Positive number |
| `harga_jual` | decimal | No | Selling price (min: 0) | Positive number |
| `fee_penjualan_dokter` | decimal | No | Doctor sales fee (min: 0) | Positive number |
| `fee_penjualan_suster` | decimal | No | Nurse sales fee (min: 0) | Positive number |
| `addons` | boolean | No | Is addon product | `true`, `false` |
| `is_active` | boolean | No | Product status | `true`, `false` |
| `foto_produk` | file | No | Product image (max: 2MB) | jpg, png, gif, jpeg |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Produk updated successfully"
    },
    "data": {
        "kode_produk": "PRD-240907123456",
        "kode_kategori_produk": "KAT-240907123456",
        "nama": "Vitamin C 1000mg",
        "deskripsi": "Suplemen vitamin C dosis tinggi untuk daya tahan tubuh",
        "satuan": "tablet",
        "berat_produk": 100,
        "harga_beli": "25000.00",
        "harga_jual": "75000.00",
        "fee_penjualan_suster": "2500.00",
        "fee_penjualan_dokter": "7500.00",
        "addons": false,
        "is_active": true,
        "foto_produk": "http://localhost/storage/images/produk/updated_produk.jpg",
        "created_at": "2024-09-07T12:34:56.000000Z",
        "updated_at": "2024-09-07T14:30:00.000000Z",
        "kategoriproduk": {
            "kode_kategori_produk": "KAT-240907123456",
            "kode_tipe_produk": "TIP-240907123456",
            "nama": "Vitamin & Suplemen",
            "tipeproduk": {
                "kode_tipe_produk": "TIP-240907123456",
                "nama": "Obat & Suplemen"
            }
        }
    }
}
```

---

## 8. Delete Product

### Request
```http
DELETE /api/produk/delete/{kodeProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeProduk` | string | Yes | Product code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Produk deleted successfully"
    },
    "data": null
}
```

---

## 9. Toggle Product Status

### Request
```http
PATCH /api/produk/status/{kodeProduk}
```

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeProduk` | string | Yes | Product code |

### Response Success (200)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Produk deactivated successfully"
    },
    "data": {
        "kode_produk": "PRD-240907123456",
        "nama": "Vitamin C 500mg",
        "harga_jual": "50000.00",
        "is_active": false
    }
}
```

---

## File Upload Specifications

### Image Requirements
- **Allowed formats**: JPEG, PNG, JPG, GIF
- **Maximum size**: 2MB (2048KB)
- **Storage location**: `storage/app/public/images/produk/`
- **Public access**: `storage/images/produk/filename.jpg`

### File Naming
- Files are automatically renamed with unique timestamps
- Original file extension is preserved
- Path is stored as: `images/produk/filename.ext`

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

### Get Product Types
```bash
curl "http://localhost/api/produk/tipe"
```

### Get Categories for Specific Type
```bash
curl "http://localhost/api/produk/kategori?kode_tipe_produk=TIP-240907123456"
```

### Get Product Hierarchy
```bash
curl "http://localhost/api/produk/hierarchy"
```

### Create Product with Image
```bash
curl -X POST http://localhost/api/produk/create \
  -H "Content-Type: multipart/form-data" \
  -F "kode_kategori_produk=KAT-240907123456" \
  -F "nama=Paracetamol 500mg" \
  -F "deskripsi=Obat penurun panas dan pereda nyeri" \
  -F "satuan=tablet" \
  -F "berat_produk=50" \
  -F "harga_beli=5000" \
  -F "harga_jual=10000" \
  -F "fee_penjualan_dokter=1000" \
  -F "fee_penjualan_suster=500" \
  -F "foto_produk=@/path/to/image.jpg"
```

### Get Products by Category
```bash
curl "http://localhost/api/produk?kode_kategori_produk=KAT-240907123456"
```

### Get Products by Type
```bash
curl "http://localhost/api/produk?kode_tipe_produk=TIP-240907123456"
```

### Search Products
```bash
curl "http://localhost/api/produk?search=vitamin&is_active=true"
```

### Update Product Image
```bash
curl -X PUT http://localhost/api/produk/update/PRD-240907123456 \
  -H "Content-Type: multipart/form-data" \
  -F "foto_produk=@/path/to/new_image.jpg"
```
