# Hướng dẫn Test Chức Năng

## 1. CHUẨN BỊ DỮ LIỆU

### 1.1 Tạo Roles (trước tiên)
```
POST http://localhost:3000/roles
Headers: Content-Type: application/json

Body:
{
  "name": "ADMIN",
  "description": "Admin role - full quyền"
}

{
  "name": "MODERATOR",
  "description": "Moderator role - có thể đọc tất cả"
}

{
  "name": "USER",
  "description": "User role - người dùng thường"
}
```

Copy lại ID của 3 roles này để dùng sau (ví dụ: role_id_admin, role_id_mod, role_id_user)

### 1.2 Tạo Users
```
POST http://localhost:3000/auth/register
Headers: Content-Type: application/json

Body tạo ADMIN user:
{
  "username": "admin_test",
  "password": "admin123",
  "email": "admin@test.com",
  "role": "role_id_admin"  // Thay bằng ID Admin role
}

Body tạo MODERATOR user:
{
  "username": "mod_test",
  "password": "mod123",
  "email": "mod@test.com",
  "role": "role_id_mod"  // Thay bằng ID Mod role
}

Body tạo USER thường:
{
  "username": "user_test",
  "password": "user123",
  "email": "user@test.com",
  "role": "role_id_user"  // Thay bằng ID User role
}
```

---

## 2. TEST LOGIN & LẤY TOKEN

### 2.1 Login ADMIN
```
POST http://localhost:3000/auth/login
Headers: Content-Type: application/json

Body:
{
  "username": "admin_test",
  "password": "admin123"
}

Response: Token (copy token này)
```

### 2.2 Login MODERATOR
```
POST http://localhost:3000/auth/login
Headers: Content-Type: application/json

Body:
{
  "username": "mod_test",
  "password": "mod123"
}

Response: Token (copy token này)
```

### 2.3 Login USER
```
POST http://localhost:3000/auth/login
Headers: Content-Type: application/json

Body:
{
  "username": "user_test",
  "password": "user123"
}

Response: Token (copy token này)
```

---

## 3. TEST USERS ENDPOINTS - QUYỀN

### 3.1 GET /users - Xem tất cả users
```
✅ ADMIN: Có quyền
GET http://localhost:3000/users
Headers: 
  Authorization: Bearer admin_token

Response: 200 OK - Danh sách tất cả users

❌ MODERATOR: Có quyền
GET http://localhost:3000/users
Headers: 
  Authorization: Bearer mod_token

Response: 200 OK - Danh sách tất cả users

❌ USER: Không có quyền
GET http://localhost:3000/users
Headers: 
  Authorization: Bearer user_token

Response: 403 Forbidden - "ban khong co quyen"

❌ Không login:
GET http://localhost:3000/users
Headers: (không có Authorization)

Response: 403 Forbidden - "ban chua dang nhap"
```

### 3.2 GET /users/:id - Xem chi tiết user
```
✅ ADMIN: Có quyền
GET http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer admin_token

Response: 200 OK - Chi tiết user

✅ MODERATOR: Có quyền
GET http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer mod_token

Response: 200 OK - Chi tiết user

❌ USER: Không có quyền
GET http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer user_token

Response: 403 Forbidden - "ban khong co quyen"
```

### 3.3 POST /users - Tạo user mới
```
✅ ADMIN: Có quyền
POST http://localhost:3000/users
Headers: 
  Authorization: Bearer admin_token
  Content-Type: application/json

Body:
{
  "username": "newuser",
  "password": "pass123",
  "email": "newuser@test.com",
  "role": "role_id"
}

Response: 200 OK - User mới được tạo

✅ MODERATOR: Có quyền
POST http://localhost:3000/users
Headers: 
  Authorization: Bearer mod_token
  Content-Type: application/json

Body: (tương tự)

Response: 200 OK

❌ USER: Không có quyền
POST http://localhost:3000/users
Headers: 
  Authorization: Bearer user_token

Response: 403 Forbidden - "ban khong co quyen"

❌ Không login:
POST http://localhost:3000/users

Response: 403 Forbidden - "ban chua dang nhap"
```

### 3.4 PUT /users/:id - Cập nhật user
```
✅ ADMIN: Có quyền
PUT http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer admin_token
  Content-Type: application/json

Body:
{
  "fullName": "Admin Updated"
}

Response: 200 OK - User được cập nhật

✅ MODERATOR: Có quyền
PUT http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer mod_token
  Content-Type: application/json

Body: (tương tự)

Response: 200 OK

❌ USER: Không có quyền
PUT http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer user_token

Response: 403 Forbidden - "ban khong co quyen"
```

### 3.5 DELETE /users/:id - Xóa user
```
✅ ADMIN ONLY: Có quyền
DELETE http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer admin_token

Response: 200 OK - User bị delete (isDeleted = true)

❌ MODERATOR: Không có quyền
DELETE http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer mod_token

Response: 403 Forbidden - "ban khong co quyen"

❌ USER: Không có quyền
DELETE http://localhost:3000/users/{user_id}
Headers: 
  Authorization: Bearer user_token

Response: 403 Forbidden - "ban khong co quyen"
```

---

## 4. TEST PRODUCTS ENDPOINTS - QUYỀN

### 4.1 GET /products - Xem tất cả products (KHÔNG CẦN LOGIN)
```
✅ Admin: Có quyền
GET http://localhost:3000/products
Headers: 
  Authorization: Bearer admin_token

Response: 200 OK - Danh sách products

✅ Moderator: Có quyền
GET http://localhost:3000/products
Headers: 
  Authorization: Bearer mod_token

Response: 200 OK

✅ User: Có quyền
GET http://localhost:3000/products
Headers: 
  Authorization: Bearer user_token

Response: 200 OK

✅ KHÔNG LOGIN: Có quyền
GET http://localhost:3000/products
Headers: (không cần Authorization)

Response: 200 OK - Danh sách products
```

### 4.2 GET /products/:id - Xem chi tiết product (KHÔNG CẦN LOGIN)
```
✅ Tất cả người dùng (login hoặc không) đều có quyền
GET http://localhost:3000/products/{product_id}
Headers: (không cần Authorization)

Response: 200 OK - Chi tiết product
```

### 4.3 POST /products - Tạo product mới
```
✅ ADMIN: Có quyền
POST http://localhost:3000/products
Headers: 
  Authorization: Bearer admin_token
  Content-Type: application/json

Body:
{
  "title": "Product Test",
  "price": 100000,
  "description": "Test product",
  "category": "Electronics"
}

Response: 200 OK - Product mới được tạo

✅ MODERATOR: Có quyền
POST http://localhost:3000/products
Headers: 
  Authorization: Bearer mod_token
  Content-Type: application/json

Body: (tương tự)

Response: 200 OK

❌ USER: Không có quyền
POST http://localhost:3000/products
Headers: 
  Authorization: Bearer user_token

Response: 403 Forbidden - "ban khong co quyen"

❌ Không login:
POST http://localhost:3000/products
Headers: (không có Authorization)

Response: 403 Forbidden - "ban chua dang nhap"
```

### 4.4 PUT /products/:id - Cập nhật product
```
✅ ADMIN: Có quyền
PUT http://localhost:3000/products/{product_id}
Headers: 
  Authorization: Bearer admin_token
  Content-Type: application/json

Body:
{
  "price": 150000,
  "description": "Updated description"
}

Response: 200 OK - Product được cập nhật

✅ MODERATOR: Có quyền
PUT http://localhost:3000/products/{product_id}
Headers: 
  Authorization: Bearer mod_token
  Content-Type: application/json

Body: (tương tự)

Response: 200 OK

❌ USER: Không có quyền
PUT http://localhost:3000/products/{product_id}
Headers: 
  Authorization: Bearer user_token

Response: 403 Forbidden - "ban khong co quyen"

❌ Không login:
PUT http://localhost:3000/products/{product_id}

Response: 403 Forbidden - "ban chua dang nhap"
```

### 4.5 DELETE /products/:id - Xóa product (ADMIN ONLY)
```
✅ ADMIN ONLY: Có quyền
DELETE http://localhost:3000/products/{product_id}
Headers: 
  Authorization: Bearer admin_token

Response: 200 OK - Product bị delete (isDeleted = true)

❌ MODERATOR: Không có quyền
DELETE http://localhost:3000/products/{product_id}
Headers: 
  Authorization: Bearer mod_token

Response: 403 Forbidden - "ban khong co quyen"

❌ USER: Không có quyền
DELETE http://localhost:3000/products/{product_id}
Headers: 
  Authorization: Bearer user_token

Response: 403 Forbidden - "ban khong co quyen"

❌ Không login:
DELETE http://localhost:3000/products/{product_id}

Response: 403 Forbidden - "ban chua dang nhap"
```

---

## 5. TEST CHANGE PASSWORD

### 5.1 Đổi mật khẩu (ADMIN)
```
✅ Đổi mật khẩu thành công
POST http://localhost:3000/auth/change-password
Headers: 
  Authorization: Bearer admin_token
  Content-Type: application/json

Body:
{
  "oldpassword": "admin123",
  "newpassword": "admin123_new"
}

Response: 200 OK
{
  "message": "doi mat khau thanh cong",
  "user": { ... }
}

❌ Mật khẩu cũ sai
POST http://localhost:3000/auth/change-password
Headers: 
  Authorization: Bearer admin_token
  Content-Type: application/json

Body:
{
  "oldpassword": "wrongpassword",
  "newpassword": "admin123_new"
}

Response: 403 Forbidden
{
  "message": "mat khau cu khong chinh xac"
}

❌ Thiếu oldpassword hoặc newpassword
POST http://localhost:3000/auth/change-password
Headers: 
  Authorization: Bearer admin_token
  Content-Type: application/json

Body:
{
  "oldpassword": "admin123"
}

Response: 400 Bad Request
{
  "message": "oldpassword va newpassword la bat buoc"
}

❌ Không login
POST http://localhost:3000/auth/change-password
Headers: (không có Authorization)

Body:
{
  "oldpassword": "admin123",
  "newpassword": "admin123_new"
}

Response: 403 Forbidden
{
  "message": "ban chua dang nhap"
}
```

### 5.2 Đổi mật khẩu (MODERATOR)
```
✅ Moderator cũng có thể đổi mật khẩu
POST http://localhost:3000/auth/change-password
Headers: 
  Authorization: Bearer mod_token
  Content-Type: application/json

Body:
{
  "oldpassword": "mod123",
  "newpassword": "mod123_new"
}

Response: 200 OK
```

### 5.3 Đổi mật khẩu (USER)
```
✅ User cũng có thể đổi mật khẩu
POST http://localhost:3000/auth/change-password
Headers: 
  Authorization: Bearer user_token
  Content-Type: application/json

Body:
{
  "oldpassword": "user123",
  "newpassword": "user123_new"
}

Response: 200 OK
```

### 5.4 Login lại với mật khẩu mới
```
✅ Sau khi đổi mật khẩu, phải login lại bằng mật khẩu mới
POST http://localhost:3000/auth/login
Headers: Content-Type: application/json

Body:
{
  "username": "admin_test",
  "password": "admin123_new"
}

Response: 200 OK - Token (login thành công)

❌ Mật khẩu cũ không được dùng nữa
POST http://localhost:3000/auth/login
Headers: Content-Type: application/json

Body:
{
  "username": "admin_test",
  "password": "admin123"
}

Response: 404 Not Found
{
  "message": "username khong ton tai hoac thong tin dang nhap sai"
}
```

---

## 6. CÔNG CỤ TEST

### 6.1 Dùng Postman (Khuyến nghị)
1. Tải Postman: https://www.postman.com/downloads/
2. Import collection từ file hoặc tạo thủ công
3. Lưu tokens vào biến để tái sử dụng
4. Chạy từng request

### 6.2 Dùng cURL (Command Line)
```bash
# Login ADMIN
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin_test","password":"admin123"}'

# GET tất cả users (ADMIN)
curl -X GET http://localhost:3000/users \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"

# GET products (không cần login)
curl -X GET http://localhost:3000/products

# POST product mới (ADMIN/MOD)
curl -X POST http://localhost:3000/products \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -H "Content-Type: application/json" \
  -d '{"title":"Test","price":100000,"description":"Test","category":"Test"}'

# Đổi mật khẩu
curl -X POST http://localhost:3000/auth/change-password \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -H "Content-Type: application/json" \
  -d '{"oldpassword":"admin123","newpassword":"admin123_new"}'
```

### 6.3 Dùng Thunder Client (Extension VS Code)
1. Cài Extension "Thunder Client"
2. Tạo requests tương tự Postman
3. Dùng biến để lưu tokens

---

## 7. TÓMLẠC ĐẶC BIỆT

| Endpoint | GET | POST | PUT | DELETE |
|----------|-----|------|-----|--------|
| /users | ADMIN, MOD | ADMIN, MOD | ADMIN, MOD | ADMIN |
| /products | Tất cả (không cần login) | ADMIN, MOD | ADMIN, MOD | ADMIN |
| /auth/change-password | - | ADMIN, MOD, USER | - | - |

---

## 8. LƯU Ý QUAN TRỌNG

1. **Token hết hạn**: Mỗi token có hiệu lực 1 giờ (3600 * 1000 ms)
2. **Cookie vs Header**: Server hỗ trợ cả 2 cách:
   - Cookie: Tự động sau login
   - Header: `Authorization: Bearer token_here`
3. **Delete**: Sử dụng `isDeleted = true` (soft delete, không xóa dữ liệu)
4. **Password**: Được hash bằng bcrypt trước khi lưu database
5. **Role**: Phải tạo Role trong database trước khi tạo User

---

## 9. KIỂM TRA KẾT NỐI DATABASE

Nếu server không kết nối MongoDB:
```bash
# Kiểm tra MongoDB Atlas connection string
# Chắc chắn IP whitelist đã được thêm vào: 0.0.0.0/0
# Hoặc cấu hình DNS Google trong app.js
```
