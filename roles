# API Dokumentasi - CRUD Roles

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

## 1. Get All Roles

### Endpoint
```http
GET /roles
```

### Description
Mendapatkan daftar semua roles beserta permissions yang dimiliki.

### Example Request
```http
GET /roles
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Roles retrieved successfully"
    },
    "data": [
        {
            "id": 1,
            "name": "admin",
            "guard_name": "api",
            "permissions": [
                "dokter_create",
                "dokter_read",
                "dokter_update",
                "dokter_delete",
                "jadwal_create",
                "jadwal_read",
                "jadwal_update",
                "jadwal_delete"
            ],
            "permissions_count": 8,
            "created_at": "2024-01-15T10:30:00.000000Z",
            "updated_at": "2024-01-15T10:30:00.000000Z"
        },
        {
            "id": 2,
            "name": "dokter",
            "guard_name": "api",
            "permissions": [
                "jadwal_read",
                "jadwal_create",
                "jadwal_update"
            ],
            "permissions_count": 3,
            "created_at": "2024-01-15T10:30:00.000000Z",
            "updated_at": "2024-01-15T10:30:00.000000Z"
        }
    ]
}
```

---

## 2. Create New Role

### Endpoint
```http
POST /roles
```

### Description
Membuat role baru beserta permissions yang akan diberikan.

### Request Body
```json
{
    "name": "supervisor",
    "permissions": [
        "dokter_read",
        "jadwal_read",
        "jadwal_create",
        "jadwal_update"
    ]
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `name` | required, string, max:255, unique:roles |
| `permissions` | optional, array |
| `permissions.*` | exists:permissions,name |

### Success Response (201 Created)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "Role created successfully"
    },
    "data": {
        "id": 3,
        "name": "supervisor",
        "guard_name": "api",
        "permissions": [
            "dokter_read",
            "jadwal_read",
            "jadwal_create",
            "jadwal_update"
        ],
        "permissions_count": 4,
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
        "name": ["The name has already been taken."],
        "permissions.0": ["The selected permissions.0 is invalid."]
    }
}
```

---

## 3. Get Single Role

### Endpoint
```http
GET /roles/{id}
```

### Description
Mendapatkan detail role berdasarkan ID beserta jumlah users yang menggunakan role tersebut.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | ID unik role |

### Example Request
```http
GET /roles/1
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Role retrieved successfully"
    },
    "data": {
        "id": 1,
        "name": "admin",
        "guard_name": "api",
        "permissions": [
            "dokter_create",
            "dokter_read",
            "dokter_update",
            "dokter_delete",
            "jadwal_create",
            "jadwal_read",
            "jadwal_update",
            "jadwal_delete"
        ],
        "permissions_count": 8,
        "users_count": 2,
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
        "message": "Role not found"
    },
    "data": null
}
```

---

## 4. Update Role

### Endpoint
```http
PUT /roles/{id}
```

### Description
Mengupdate role dan permissions yang dimilikinya. Semua field bersifat optional.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | ID unik role |

### Request Body (All fields optional)
```json
{
    "name": "admin_updated",
    "permissions": [
        "dokter_read",
        "dokter_update",
        "jadwal_read",
        "jadwal_create"
    ]
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `name` | optional, string, max:255, unique:roles (except current) |
| `permissions` | optional, array |
| `permissions.*` | exists:permissions,name |

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Role updated successfully"
    },
    "data": {
        "id": 1,
        "name": "admin_updated",
        "guard_name": "api",
        "permissions": [
            "dokter_read",
            "dokter_update",
            "jadwal_read",
            "jadwal_create"
        ],
        "permissions_count": 4,
        "created_at": "2024-01-15T10:30:00.000000Z",
        "updated_at": "2024-01-15T15:45:00.000000Z"
    }
}
```

---

## 5. Delete Role

### Endpoint
```http
DELETE /roles/{id}
```

### Description
Menghapus role secara permanen. Role tidak dapat dihapus jika masih digunakan oleh users.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | ID unik role |

### Example Request
```http
DELETE /roles/3
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Role deleted successfully"
    },
    "data": null
}
```

### Error Response (400 Bad Request - Role in Use)
```json
{
    "meta": {
        "code": 400,
        "status": "error",
        "message": "Cannot delete role that is assigned to users"
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
        "message": "Role not found"
    },
    "data": null
}
```

---

## 6. Assign Permissions to Role

### Endpoint
```http
POST /roles/{id}/permissions
```

### Description
Mengassign permissions ke role tertentu. Permissions lama akan diganti dengan permissions baru.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | ID unik role |

### Request Body
```json
{
    "permissions": [
        "dokter_read",
        "dokter_create",
        "jadwal_read",
        "jadwal_update"
    ]
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `permissions` | required, array |
| `permissions.*` | exists:permissions,name |

### Example Request
```http
POST /roles/2/permissions
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Permissions assigned successfully"
    },
    "data": {
        "id": 2,
        "name": "dokter",
        "permissions": [
            "dokter_read",
            "dokter_create",
            "jadwal_read",
            "jadwal_update"
        ],
        "permissions_count": 4
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
        "permissions": ["The permissions field is required."],
        "permissions.0": ["The selected permissions.0 is invalid."]
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
        "message": "Failed to retrieve roles: Database connection error"
    },
    "data": null
}
```

---

## Notes

1. **Authentication**: Semua endpoint memerlukan authentication dengan Bearer Token
2. **Permissions**: Biasanya hanya super admin yang dapat mengakses endpoint ini
3. **Guard Name**: Semua roles menggunakan guard_name 'api' untuk API authentication
4. **Permission Sync**: Menggunakan `syncPermissions()` yang akan replace semua permissions lama
5. **Spatie Laravel Permission**: Menggunakan package Spatie untuk role & permission management
6. **Delete Protection**: Role tidak dapat dihapus jika masih diassign ke users
7. **Usage Tracking**: Endpoint show menampilkan jumlah users yang menggunakan role tersebut
8. **Eager Loading**: Permissions dimuat bersamaan untuk efisiensi query

---

## Common Roles Structure

Berdasarkan sistem dokter dan jadwal, berikut adalah struktur roles yang umum digunakan:

### Super Admin Role
- Memiliki semua permissions
- Dapat mengelola users, roles, permissions
- Full access ke semua fitur sistem

### Admin Role
```json
{
    "name": "admin",
    "permissions": [
        "dokter_create", "dokter_read", "dokter_update", "dokter_delete",
        "jadwal_create", "jadwal_read", "jadwal_update", "jadwal_delete",
        "user_read", "user_update"
    ]
}
```

### Dokter Role
```json
{
    "name": "dokter",
    "permissions": [
        "jadwal_read", "jadwal_create", "jadwal_update",
        "dokter_read"
    ]
}
```

### Staff/Perawat Role
```json
{
    "name": "staff",
    "permissions": [
        "dokter_read",
        "jadwal_read"
    ]
}
```

### Supervisor Role
```json
{
    "name": "supervisor",
    "permissions": [
        "dokter_read", "dokter_update",
        "jadwal_read", "jadwal_create", "jadwal_update",
        "user_read"
    ]
}
```

---

## Sample cURL Commands

### Create Role with Permissions
```bash
curl -X POST "https://your-api-domain.com/api/roles" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "supervisor",
    "permissions": [
        "dokter_read",
        "jadwal_read",
        "jadwal_create",
        "jadwal_update"
    ]
  }'
```

### Update Role Name Only
```bash
curl -X PUT "https://your-api-domain.com/api/roles/1" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "admin_updated"
  }'
```

### Assign New Permissions to Role
```bash
curl -X POST "https://your-api-domain.com/api/roles/2/permissions" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "permissions": [
        "dokter_read",
        "dokter_create",
        "jadwal_read",
        "jadwal_update"
    ]
  }'
```

### Get All Roles
```bash
curl -X GET "https://your-api-domain.com/api/roles" \
  -H "Authorization: Bearer your-token"
```
