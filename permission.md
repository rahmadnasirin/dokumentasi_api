# API Dokumentasi - CRUD Permissions

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

## 1. Get All Permissions (Grouped by Category)

### Endpoint
```http
GET /permissions
```

### Description
Mendapatkan semua permissions yang dikelompokkan berdasarkan kategori.

### Query Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `category` | string | No | Filter berdasarkan kategori tertentu |

### Example Request
```http
GET /permissions?category=dokter
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Permissions retrieved successfully"
    },
    "data": [
        {
            "category": "dokter",
            "permissions": [
                {
                    "id": 1,
                    "name": "dokter_create",
                    "guard_name": "api",
                    "created_at": "2024-01-15T10:30:00.000000Z",
                    "updated_at": "2024-01-15T10:30:00.000000Z"
                },
                {
                    "id": 2,
                    "name": "dokter_read",
                    "guard_name": "api",
                    "created_at": "2024-01-15T10:30:00.000000Z",
                    "updated_at": "2024-01-15T10:30:00.000000Z"
                },
                {
                    "id": 3,
                    "name": "dokter_update",
                    "guard_name": "api",
                    "created_at": "2024-01-15T10:30:00.000000Z",
                    "updated_at": "2024-01-15T10:30:00.000000Z"
                },
                {
                    "id": 4,
                    "name": "dokter_delete",
                    "guard_name": "api",
                    "created_at": "2024-01-15T10:30:00.000000Z",
                    "updated_at": "2024-01-15T10:30:00.000000Z"
                }
            ],
            "count": 4
        },
        {
            "category": "jadwal",
            "permissions": [
                {
                    "id": 5,
                    "name": "jadwal_create",
                    "guard_name": "api",
                    "created_at": "2024-01-15T10:30:00.000000Z",
                    "updated_at": "2024-01-15T10:30:00.000000Z"
                }
            ],
            "count": 1
        }
    ]
}
```

---

## 2. Get All Permissions (Simple List)

### Endpoint
```http
GET /permissions/list
```

### Description
Mendapatkan daftar sederhana semua permissions tanpa pengelompokan.

### Example Request
```http
GET /permissions/list
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Permissions list retrieved successfully"
    },
    "data": [
        {
            "id": 1,
            "name": "dokter_create",
            "guard_name": "api",
            "created_at": "2024-01-15T10:30:00.000000Z",
            "updated_at": "2024-01-15T10:30:00.000000Z"
        },
        {
            "id": 2,
            "name": "dokter_read",
            "guard_name": "api",
            "created_at": "2024-01-15T10:30:00.000000Z",
            "updated_at": "2024-01-15T10:30:00.000000Z"
        }
    ]
}
```

---

## 3. Create New Permission

### Endpoint
```http
POST /permissions
```

### Description
Membuat permission baru.

### Request Body
```json
{
    "name": "create",
    "category": "dokter"
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `name` | required, string, max:255, unique:permissions |
| `category` | optional, string, max:50 |

### Success Response (201 Created)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Permission created successfully"
    },
    "data": {
        "id": 10,
        "name": "dokter_create",
        "category": "dokter",
        "guard_name": "api",
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
        "name": ["The name has already been taken."]
    }
}
```

---

## 4. Create Multiple Permissions

### Endpoint
```http
POST /permissions/multi
```

### Description
Membuat beberapa permissions sekaligus dalam satu request.

### Request Body
```json
{
    "permissions": [
        {
            "name": "create",
            "category": "jadwal"
        },
        {
            "name": "read",
            "category": "jadwal"
        },
        {
            "name": "update",
            "category": "jadwal"
        },
        {
            "name": "delete",
            "category": "jadwal"
        }
    ]
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `permissions` | required, array |
| `permissions.*.name` | required, string, max:255, unique:permissions |
| `permissions.*.category` | optional, string, max:50 |

### Success Response (201 Created)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "4 permissions created successfully"
    },
    "data": [
        {
            "id": 11,
            "name": "jadwal_create",
            "category": "jadwal",
            "guard_name": "api",
            "created_at": "2024-01-15T10:30:00.000000Z"
        },
        {
            "id": 12,
            "name": "jadwal_read",
            "category": "jadwal",
            "guard_name": "api",
            "created_at": "2024-01-15T10:30:00.000000Z"
        },
        {
            "id": 13,
            "name": "jadwal_update",
            "category": "jadwal",
            "guard_name": "api",
            "created_at": "2024-01-15T10:30:00.000000Z"
        },
        {
            "id": 14,
            "name": "jadwal_delete",
            "category": "jadwal",
            "guard_name": "api",
            "created_at": "2024-01-15T10:30:00.000000Z"
        }
    ]
}
```

---

## 5. Get Single Permission

### Endpoint
```http
GET /permissions/{id}
```

### Description
Mendapatkan detail permission berdasarkan ID beserta jumlah roles dan users yang menggunakannya.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | ID unik permission |

### Example Request
```http
GET /permissions/1
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Permission retrieved successfully"
    },
    "data": {
        "id": 1,
        "name": "dokter_create",
        "category": "dokter",
        "guard_name": "api",
        "roles_count": 2,
        "users_count": 5,
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
        "message": "Permission not found"
    },
    "data": null
}
```

---

## 6. Update Permission

### Endpoint
```http
PUT /permissions/{id}
```

### Description
Mengupdate nama permission yang sudah ada.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | ID unik permission |

### Request Body
```json
{
    "name": "dokter_create_updated"
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `name` | optional, string, max:255, unique:permissions (except current) |

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Permission updated successfully"
    },
    "data": {
        "id": 1,
        "name": "dokter_create_updated",
        "category": "dokter",
        "guard_name": "api",
        "created_at": "2024-01-15T10:30:00.000000Z",
        "updated_at": "2024-01-15T15:45:00.000000Z"
    }
}
```

---

## 7. Delete Permission

### Endpoint
```http
DELETE /permissions/{id}
```

### Description
Menghapus permission secara permanen. Permission tidak dapat dihapus jika masih digunakan oleh roles atau users.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | ID unik permission |

### Example Request
```http
DELETE /permissions/1
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Permission deleted successfully"
    },
    "data": null
}
```

### Error Response (400 Bad Request - Permission in Use)
```json
{
    "meta": {
        "code": 400,
        "status": "error",
        "message": "Cannot delete permission that is assigned to roles or users"
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
        "message": "Permission not found"
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
        "message": "Failed to retrieve permissions: Database connection error"
    },
    "data": null
}
```

---

## Notes

1. **Authentication**: Semua endpoint memerlukan authentication dengan Bearer Token
2. **Permissions**: Biasanya hanya super admin yang dapat mengakses endpoint ini
3. **Guard Name**: Semua permissions menggunakan guard_name 'api' untuk API authentication
4. **Category System**: Permission names menggunakan format `category_action` (contoh: `dokter_create`)
5. **Auto Categorization**: Jika category disediakan, sistem akan otomatis menambahkan prefix category ke nama permission
6. **Spatie Laravel Permission**: Menggunakan package Spatie untuk role & permission management
7. **Delete Protection**: Permission tidak dapat dihapus jika masih diassign ke roles atau users
8. **Usage Tracking**: Endpoint show menampilkan jumlah roles dan users yang menggunakan permission tersebut

---

## Common Permission Categories

Berdasarkan sistem dokter dan jadwal, berikut adalah kategori permissions yang umum digunakan:

### Dokter Permissions
- `dokter_create` - Membuat dokter baru
- `dokter_read` - Melihat data dokter
- `dokter_update` - Mengupdate data dokter
- `dokter_delete` - Menghapus dokter
- `dokter_toggle_status` - Mengaktifkan/nonaktifkan dokter

### Jadwal Permissions
- `jadwal_create` - Membuat jadwal dokter
- `jadwal_read` - Melihat jadwal dokter
- `jadwal_update` - Mengupdate jadwal dokter
- `jadwal_delete` - Menghapus jadwal dokter

### User Permissions
- `user_create` - Membuat user baru
- `user_read` - Melihat data user
- `user_update` - Mengupdate data user
- `user_delete` - Menghapus user

### Permission Management
- `permission_create` - Membuat permission baru
- `permission_read` - Melihat permissions
- `permission_update` - Mengupdate permission
- `permission_delete` - Menghapus permission

---

## Sample cURL Commands

### Create Single Permission
```bash
curl -X POST "https://your-api-domain.com/api/permissions" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "create",
    "category": "dokter"
  }'
```

### Create Multiple Permissions
```bash
curl -X POST "https://your-api-domain.com/api/permissions/multiple" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "permissions": [
      {"name": "create", "category": "jadwal"},
      {"name": "read", "category": "jadwal"},
      {"name": "update", "category": "jadwal"},
      {"name": "delete", "category": "jadwal"}
    ]
  }'
```

### Get Permissions by Category
```bash
curl -X GET "https://your-api-domain.com/api/permissions?category=dokter" \
  -H "Authorization: Bearer your-token"
```

### Update Permission
```bash
curl -X PUT "https://your-api-domain.com/api/permissions/1" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "dokter_create_advanced"
  }'
```
