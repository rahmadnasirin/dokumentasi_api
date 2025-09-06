# API Dokumentasi - CRUD Users

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

## 1. Get All Users

### Endpoint
```http
GET /users
```

### Description
Mendapatkan daftar semua users beserta role yang dimiliki dengan fitur pagination, search, dan filter.

### Query Parameters (All Optional)
| Parameter | Type | Description |
|-----------|------|-------------|
| `per_page` | integer | Jumlah data per halaman (default: 10) |
| `search` | string | Mencari berdasarkan nama, email, atau phone |
| `role` | string | Filter berdasarkan role tertentu |
| `is_active` | boolean | Filter berdasarkan status aktif (1/0) |
| `gender` | string | Filter berdasarkan jenis kelamin (male/female) |
| `phone` | string | Mencari berdasarkan nomor telepon |

### Example Request
```http
GET /users?per_page=15&search=john&role=dokter&is_active=1
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "User data retrieved successfully"
    },
    "data": {
        "current_page": 1,
        "data": [
            {
                "kode_user": "USR-2501151030001",
                "name": "Dr. John Doe",
                "email": "john.doe@email.com",
                "phone": "081234567890",
                "date_of_birth": "1985-05-15",
                "gender": "male",
                "address": "Jl. Merdeka No. 123, Jakarta",
                "emergency_contact": "081234567891",
                "foto_profile": "http://localhost:8000/storage/users/profiles/foto1.jpg",
                "is_active": true,
                "role": "dokter",
                "created_at": "2025-01-15T10:30:00.000000Z",
                "updated_at": "2025-01-15T10:30:00.000000Z"
            },
            {
                "kode_user": "USR-2501151030002",
                "name": "Jane Smith",
                "email": "jane.smith@email.com",
                "phone": "081234567892",
                "date_of_birth": "1990-08-20",
                "gender": "female",
                "address": "Jl. Sudirman No. 456, Jakarta",
                "emergency_contact": "081234567893",
                "foto_profile": null,
                "is_active": true,
                "role": "admin",
                "created_at": "2025-01-15T10:30:00.000000Z",
                "updated_at": "2025-01-15T10:30:00.000000Z"
            }
        ],
        "first_page_url": "http://localhost:8000/api/users?page=1",
        "from": 1,
        "last_page": 1,
        "last_page_url": "http://localhost:8000/api/users?page=1",
        "links": [...],
        "next_page_url": null,
        "path": "http://localhost:8000/api/users",
        "per_page": 15,
        "prev_page_url": null,
        "to": 2,
        "total": 2
    }
}
```

---

## 2. Create New User

### Endpoint
```http
POST /users/create
```

### Description
Membuat user baru beserta role yang akan diberikan.

### Request Body (Form Data/JSON)
```json
{
    "name": "Dr. Michael Johnson",
    "email": "michael.johnson@email.com",
    "password": "password123",
    "phone": "081234567894",
    "date_of_birth": "1980-03-10",
    "gender": "male",
    "address": "Jl. Gatot Subroto No. 789, Jakarta",
    "emergency_contact": "081234567895",
    "role": "dokter"
}
```

### File Upload (Multipart Form Data)
```javascript
// Jika menggunakan file upload
const formData = new FormData();
formData.append('name', 'Dr. Michael Johnson');
formData.append('email', 'michael.johnson@email.com');
formData.append('password', 'password123');
formData.append('phone', '081234567894');
formData.append('role', 'dokter');
formData.append('foto_profile', file); // File object
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `name` | required, string, max:255 |
| `email` | required, string, email, max:255, unique:users |
| `password` | required, string, min:8 |
| `phone` | required, string, max:20 |
| `date_of_birth` | nullable, string |
| `gender` | nullable, string, in:male,female |
| `address` | nullable, string, max:500 |
| `emergency_contact` | nullable, string, max:20 |
| `foto_profile` | nullable, image, mimes:jpeg,png,jpg,gif, max:2048 |
| `role` | required, string, exists:roles,name |

### Success Response (201 Created)
```json
{
    "meta": {
        "code": 201,
        "status": "success",
        "message": "User created successfully"
    },
    "data": {
        "kode_user": "USR-2501151030003",
        "name": "Dr. Michael Johnson",
        "email": "michael.johnson@email.com",
        "phone": "081234567894",
        "date_of_birth": "1980-03-10",
        "gender": "male",
        "address": "Jl. Gatot Subroto No. 789, Jakarta",
        "emergency_contact": "081234567895",
        "foto_profile": "http://localhost:8000/storage/users/profiles/foto3.jpg",
        "is_active": true,
        "role": "dokter",
        "created_at": "2025-01-15T10:30:00.000000Z",
        "updated_at": "2025-01-15T10:30:00.000000Z"
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
        "password": ["The password must be at least 8 characters."],
        "role": ["The selected role is invalid."],
        "foto_profile": ["The foto profile must be an image."]
    }
}
```

---

## 3. Get Single User

### Endpoint
```http
GET /users/{kodeUser}
```

### Description
Mendapatkan detail user berdasarkan kode_user beserta role dan permissions.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeUser` | string | Yes | Kode unik user (contoh: USR-2501151030001) |

### Example Request
```http
GET /users/USR-2501151030001
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "User data retrieved successfully"
    },
    "data": {
        "kode_user": "USR-2501151030001",
        "name": "Dr. John Doe",
        "email": "john.doe@email.com",
        "phone": "081234567890",
        "date_of_birth": "1985-05-15",
        "gender": "male",
        "address": "Jl. Merdeka No. 123, Jakarta",
        "emergency_contact": "081234567891",
        "foto_profile": "http://localhost:8000/storage/users/profiles/foto1.jpg",
        "is_active": true,
        "role": "dokter",
        "permissions": [
            "jadwal_read",
            "jadwal_create",
            "jadwal_update",
            "dokter_read"
        ],
        "created_at": "2025-01-15T10:30:00.000000Z",
        "updated_at": "2025-01-15T10:30:00.000000Z"
    }
}
```

### Error Response (404 Not Found)
```json
{
    "meta": {
        "code": 404,
        "status": "error",
        "message": "User not found"
    },
    "data": null
}
```

---

## 4. Update User

### Endpoint
```http
PUT /users/update/{kodeUser}
```

### Description
Mengupdate data user. Semua field bersifat optional kecuali yang divalidasi required dengan sometimes.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeUser` | string | Yes | Kode unik user (contoh: USR-2501151030001) |

### Request Body (All fields optional except when using sometimes)
```json
{
    "name": "Dr. John Doe Updated",
    "email": "john.doe.updated@email.com",
    "password": "newpassword123",
    "phone": "081234567899",
    "date_of_birth": "1985-05-16",
    "gender": "male",
    "address": "Jl. Merdeka No. 124, Jakarta",
    "emergency_contact": "081234567900",
    "role": "admin",
    "is_active": true
}
```

### File Upload Example
```javascript
// Update with file upload
const formData = new FormData();
formData.append('name', 'Dr. John Doe Updated');
formData.append('foto_profile', newFile); // New profile photo
formData.append('_method', 'PUT'); // Method spoofing for file upload
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `name` | sometimes, required, string, max:255 |
| `email` | sometimes, required, string, email, max:255, unique:users (except current) |
| `password` | sometimes, nullable, string, min:8 |
| `phone` | sometimes, required, string, max:20 |
| `date_of_birth` | nullable, string |
| `gender` | nullable, string, in:male,female |
| `address` | nullable, string, max:500 |
| `emergency_contact` | nullable, string, max:20 |
| `foto_profile` | sometimes, nullable, image, mimes:jpeg,png,jpg,gif, max:2048 |
| `role` | sometimes, required, string, exists:roles,name |
| `is_active` | sometimes, boolean |

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "User updated successfully"
    },
    "data": {
        "kode_user": "USR-2501151030001",
        "name": "Dr. John Doe Updated",
        "email": "john.doe.updated@email.com",
        "phone": "081234567899",
        "date_of_birth": "1985-05-16",
        "gender": "male",
        "address": "Jl. Merdeka No. 124, Jakarta",
        "emergency_contact": "081234567900",
        "foto_profile": "http://localhost:8000/storage/users/profiles/foto1_new.jpg",
        "is_active": true,
        "role": "admin",
        "created_at": "2025-01-15T10:30:00.000000Z",
        "updated_at": "2025-01-15T15:45:00.000000Z"
    }
}
```

---

## 5. Delete User

### Endpoint
```http
DELETE /users/delete/{kodeUser}
```

### Description
Menghapus user secara permanen beserta foto profile yang dimiliki.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeUser` | string | Yes | Kode unik user (contoh: USR-2501151030001) |

### Example Request
```http
DELETE /users/delete/USR-2501151030001
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "User deleted successfully"
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
        "message": "User not found"
    },
    "data": null
}
```

---

## 6. Toggle User Status

### Endpoint
```http
PUT /users/{kodeUser}/status
```

### Description
Mengubah status aktif/nonaktif user (toggle is_active).

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeUser` | string | Yes | Kode unik user (contoh: USR-2501151030001) |

### Example Request
```http
PUT /users/USR-2501151030001/status
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "User activated successfully"
    },
    "data": {
        "kode_user": "USR-2501151030001",
        "name": "Dr. John Doe",
        "is_active": true
    }
}
```

---

## 7. Get Users by Role

### Endpoint
```http
GET /users/roles/{role}
```

### Description
Mendapatkan semua user yang memiliki role tertentu.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `role` | string | Yes | Nama role (contoh: dokter, admin, customer) |
| `id` | int | Yes | Id role (contoh: 1, 2, 3) |

### Example Request name role
```http 
GET /users/roles/dokter
```
### Example Request id role
```http
GET /users/roles/1
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Users with role dokter retrieved successfully"
    },
    "data": [
        {
            "kode_user": "USR-2501151030001",
            "name": "Dr. John Doe",
            "email": "john.doe@email.com",
            "phone": "081234567890",
            "gender": "male",
            "is_active": true,
            "foto_profile": "http://localhost:8000/storage/users/profiles/foto1.jpg",
            "role": "dokter",
            "created_at": "2025-01-15T10:30:00.000000Z"
        },
        {
            "kode_user": "USR-2501151030003",
            "name": "Dr. Michael Johnson",
            "email": "michael.johnson@email.com",
            "phone": "081234567894",
            "gender": "male",
            "is_active": true,
            "foto_profile": "http://localhost:8000/storage/users/profiles/foto3.jpg",
            "role": "dokter",
            "created_at": "2025-01-15T10:30:00.000000Z"
        }
    ]
}
```

---

## 8. Change User Password

### Endpoint
```http
PUT /users/{kodeUser}/change-password
```

### Description
Mengubah password user dengan validasi password lama.

### Path Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `kodeUser` | string | Yes | Kode unik user (contoh: USR-2501151030001) |

### Request Body
```json
{
    "current_password": "oldpassword123",
    "new_password": "newpassword123",
    "new_password_confirmation": "newpassword123"
}
```

### Validation Rules
| Field | Rules |
|-------|-------|
| `current_password` | required, string |
| `new_password` | required, string, min:8, confirmed |

### Example Request
```http
PUT /users/USR-2501151030001/change-password
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Password changed successfully"
    },
    "data": null
}
```

### Error Response (400 Bad Request)
```json
{
    "meta": {
        "code": 400,
        "status": "error",
        "message": "Current password is incorrect"
    },
    "data": null
}
```

---

## 9. Get Available Roles

### Endpoint
```http
GET /users/roles
```

### Description
Mendapatkan daftar semua role yang tersedia untuk dropdown/select options.

### Example Request
```http
GET /users/roles
```

### Success Response (200 OK)
```json
{
    "meta": {
        "code": 200,
        "status": "success",
        "message": "Available roles retrieved successfully"
    },
    "data": [
        "superadmin",
        "admin",
        "dokter",
        "suster",
        "kasir",
        "customer"
    ]
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
        "message": "Failed to retrieve users: Database connection error"
    },
    "data": null
}
```

---

## Notes

1. **Authentication**: Semua endpoint memerlukan authentication dengan Bearer Token
2. **Permissions**: Biasanya hanya admin/superadmin yang dapat mengakses endpoint ini
3. **File Upload**: Menggunakan storage link Laravel, file disimpan di `storage/app/public/users/profiles/`
4. **Unique Kode User**: System otomatis generate kode_user unik dengan format USR-YYMMDDHHMMSSXXX
5. **Password Hashing**: Password otomatis di-hash menggunakan bcrypt
6. **Photo Management**: Foto lama otomatis dihapus saat upload foto baru atau delete user
7. **Role Management**: Menggunakan Spatie Laravel Permission package
8. **Soft Delete**: User dihapus permanen (hard delete), bukan soft delete

---

## Common User Roles

Berdasarkan sistem yang telah dibuat, berikut adalah roles yang umum digunakan:

### Super Admin
- Full access ke semua fitur
- Dapat mengelola users, roles, permissions

### Admin
- Dapat mengelola users
- Dapat melihat semua data
- Tidak dapat mengelola roles/permissions

### Dokter
- Dapat mengelola jadwal sendiri
- Dapat melihat data pasien
- Akses terbatas sesuai permissions

### Suster/Staff
- Dapat melihat jadwal
- Dapat input data pasien
- Akses sesuai tugas

### Kasir
- Dapat mengelola pembayaran
- Dapat melihat jadwal
- Akses finansial

### Customer/Pasien
- Dapat melihat jadwal tersedia
- Dapat booking appointment
- Profile management

---

## Sample cURL Commands

### Get All Users with Search
```bash
curl -X GET "https://your-api-domain.com/api/users?search=john&role=dokter&per_page=20" \
  -H "Authorization: Bearer your-token"
```

### Create User with Photo Upload
```bash
curl -X POST "https://your-api-domain.com/api/users/create" \
  -H "Authorization: Bearer your-token" \
  -F "name=Dr. John Doe" \
  -F "email=john@email.com" \
  -F "password=password123" \
  -F "phone=081234567890" \
  -F "role=dokter" \
  -F "foto_profile=@/path/to/photo.jpg"
```

### Update User
```bash
curl -X PUT "https://your-api-domain.com/api/users/update/USR-2501151030001" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Dr. John Doe Updated",
    "phone": "081234567899",
    "is_active": true
  }'
```

### Change Password
```bash
curl -X PUT "https://your-api-domain.com/api/users/USR-2501151030001/change-password" \
  -H "Authorization: Bearer your-token" \
  -H "Content-Type: application/json" \
  -d '{
    "current_password": "oldpassword123",
    "new_password": "newpassword123",
    "new_password_confirmation": "newpassword123"
  }'
```

### Get Users by Role
```bash
curl -X GET "https://your-api-domain.com/api/users/role/dokter" \
  -H "Authorization: Bearer your-token"
```

### Toggle User Status
```bash
curl -X PUT "https://your-api-domain.com/api/users/USR-2501151030001/status" \
  -H "Authorization: Bearer your-token"
```

### Delete User
```bash
curl -X DELETE "https://your-api-domain.com/api/users/delete/USR-2501151030001" \
  -H "Authorization: Bearer your-token"
```
