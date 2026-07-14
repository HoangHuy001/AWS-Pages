---
title: "Seed dữ liệu & Tạo người dùng"
date: 2026-07-04
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Seed dữ liệu & Tạo người dùng

Đưa 12 sản phẩm demo vào DynamoDB và tạo hai tài khoản Cognito (customer và admin) để kiểm thử end-to-end.

---

### 5.4.1 Seed dữ liệu sản phẩm

Script seed chèn 12 sản phẩm vào năm danh mục: laptops, phones, audio, accessories và gaming. Script là idempotent — chạy lại sẽ an toàn ghi đè các item đã có.

> Linux/macOS: dùng `python3`. Windows: dùng `python`.

```bash
cd infrastructure
npm run seed:demo
```

Kết quả mong đợi:
```
🌱 Seeding table: EcommerceTable (region: ap-southeast-1)

  prod-laptop-001          | ProBook X15 Laptop
  prod-laptop-002          | UltraSlim Air 13
  prod-laptop-003          | GameForce RTX Laptop
  prod-phone-001           | Nova Pro 12 Smartphone
  prod-phone-002           | Budget Buddy 5G
  prod-phone-003           | FoldPro Z Flip
  prod-audio-001           | SoundMax ANC Headphones
  prod-audio-002           | BassBoost TWS Earbuds
  prod-acc-001             | ProCharge 65W GaN Charger
  prod-acc-002             | MechaStrike RGB Keyboard
  prod-game-001            | StreamBox 4K Console
  prod-game-002            | ProAim Gaming Mouse

Seed complete: 12 succeeded, 0 failed out of 12 products.
```

**Xác minh một sản phẩm đã được ghi:**
```bash
aws dynamodb get-item \
  --table-name EcommerceTable \
  --region ap-southeast-1 \
  --key '{"PK":{"S":"PRODUCT#prod-laptop-001"},"SK":{"S":"METADATA"}}' \
  --query "Item.{name:name.S,price:price.N,slug:slug.S,category:category.S}"
```

Kết quả mong đợi:
```json
{
    "name": "ProBook X15 Laptop",
    "price": "1299.99",
    "slug": "probook-x15-laptop",
    "category": "laptops"
}
```

**Xác minh API trả về sản phẩm:**
```bash
curl -s "https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/products" \
  | python3 -m json.tool | grep '"count"'
# Kết quả mong đợi: "count": 12
```

**Xác minh lọc theo danh mục:**
```bash
curl -s "https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/products?category=laptops" \
  | python3 -m json.tool | grep '"count"'
# Kết quả mong đợi: "count": 3
```

---

### 5.4.2 Tạo người dùng demo

Script tạo tài khoản CUSTOMER và ADMIN trong Cognito với mật khẩu vĩnh viễn. Script là idempotent — nếu người dùng đã tồn tại, nó xác nhận group membership và thoát gọn gàng.

**Điều kiện:** Các biến sau phải được đặt trong `infrastructure/.env`:
- `COGNITO_USER_POOL_ID`
- `DEMO_CUSTOMER_EMAIL`
- `DEMO_CUSTOMER_PASSWORD`
- `DEMO_ADMIN_EMAIL`
- `DEMO_ADMIN_PASSWORD`

```bash
npm run seed:users
```

Kết quả mong đợi:
```
👤 Creating demo users in pool: ap-southeast-1_XXXXXXXXX (region: ap-southeast-1)

  Created CUSTOMER  | customer@demo.com
  Created ADMIN     | admin@demo.com

Demo users ready.

  Customer: customer@demo.com
  Admin:    admin@demo.com
```

**Xác minh người dùng ở trạng thái CONFIRMED:**
```bash
aws cognito-idp list-users \
  --user-pool-id ap-southeast-1_XXXXXXXXX \
  --region ap-southeast-1 \
  --query "Users[*].{Username:Username,Status:UserStatus}"
```

Kết quả mong đợi:
```json
[
    {"Username": "admin@demo.com",    "Status": "CONFIRMED"},
    {"Username": "customer@demo.com", "Status": "CONFIRMED"}
]
```

**Xác minh group membership:**
```bash
aws cognito-idp list-users-in-group \
  --user-pool-id ap-southeast-1_XXXXXXXXX \
  --group-name CUSTOMER \
  --region ap-southeast-1 \
  --query "Users[*].Username" --output text
# Kết quả mong đợi: customer@demo.com

aws cognito-idp list-users-in-group \
  --user-pool-id ap-southeast-1_XXXXXXXXX \
  --group-name ADMIN \
  --region ap-southeast-1 \
  --query "Users[*].Username" --output text
# Kết quả mong đợi: admin@demo.com
```

**Xác minh đăng nhập hoạt động — lấy JWT token:**
```bash
aws cognito-idp initiate-auth \
  --auth-flow USER_PASSWORD_AUTH \
  --client-id <your-user-pool-client-id> \
  --auth-parameters USERNAME=customer@demo.com,PASSWORD=Demo@Pass2024! \
  --region ap-southeast-1 \
  --query "AuthenticationResult.IdToken" \
  --output text | cut -c1-60
# Kết quả mong đợi: eyJraWQiOiJ...  (JWT token)
```

Dùng giá trị `AuthStack.UserPoolClientId` từ outputs ở bước 5.2.1 cho `<your-user-pool-client-id>`.

---

### Xử lý lỗi thường gặp

| Lỗi | Giải pháp |
|-----|----------|
| `Could not resolve table name` | Đảm bảo `CDK_DEFAULT_REGION=ap-southeast-1` trong `.env` và DatabaseStack đã được deploy |
| `AccessDeniedException` khi seed | IAM user cần quyền `dynamodb:PutItem` trên EcommerceTable |
| `Demo user env vars not set` | Đặt tất cả năm biến trong `infrastructure/.env` |
| `InvalidPasswordException` | Mật khẩu phải có chữ hoa, chữ thường, số và ký tự đặc biệt — ví dụ `Demo@Pass2024!` |
| Người dùng tồn tại nhưng status là `FORCE_CHANGE_PASSWORD` | Script không hoàn thành việc đặt mật khẩu vĩnh viễn; xóa người dùng trong Cognito console và chạy lại `seed:users` |
