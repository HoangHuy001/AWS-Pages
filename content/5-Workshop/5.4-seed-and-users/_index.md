---
title: "Seed Data & Create Users"
date: 2026-07-04
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Seed Data & Create Users

Populate DynamoDB with 12 demo products and create two Cognito user accounts (customer and admin) for end-to-end testing.

---

### 5.4.1 Seed Demo Products

The seed script inserts 12 products across five categories: laptops, phones, audio, accessories, and gaming. It is idempotent — re-running it safely overwrites existing items.

> Linux/macOS: use `python3`. Windows: use `python`.

```bash
cd infrastructure
npm run seed:demo
```

Expected:
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

**Verify a product was written:**
```bash
aws dynamodb get-item \
  --table-name EcommerceTable \
  --region ap-southeast-1 \
  --key '{"PK":{"S":"PRODUCT#prod-laptop-001"},"SK":{"S":"METADATA"}}' \
  --query "Item.{name:name.S,price:price.N,slug:slug.S,category:category.S}"
```

Expected:
```json
{
    "name": "ProBook X15 Laptop",
    "price": "1299.99",
    "slug": "probook-x15-laptop",
    "category": "laptops"
}
```

**Verify the API now returns products:**
```bash
curl -s "https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/products" \
  | python3 -m json.tool | grep '"count"'
# Expected: "count": 12
```

**Verify category filter:**
```bash
curl -s "https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/products?category=laptops" \
  | python3 -m json.tool | grep '"count"'
# Expected: "count": 3
```

---

### 5.4.2 Create Demo Users

The script creates a CUSTOMER and an ADMIN account in Cognito with permanent passwords. It is idempotent — if users already exist, it confirms group membership and exits cleanly.

**Prerequisites:** The following must be set in `infrastructure/.env`:
- `COGNITO_USER_POOL_ID`
- `DEMO_CUSTOMER_EMAIL`
- `DEMO_CUSTOMER_PASSWORD`
- `DEMO_ADMIN_EMAIL`
- `DEMO_ADMIN_PASSWORD`

```bash
npm run seed:users
```

Expected:
```
👤 Creating demo users in pool: ap-southeast-1_XXXXXXXXX (region: ap-southeast-1)

  Created CUSTOMER  | customer@demo.com
  Created ADMIN     | admin@demo.com

Demo users ready.

  Customer: customer@demo.com
  Admin:    admin@demo.com
```

**Verify users are CONFIRMED:**
```bash
aws cognito-idp list-users \
  --user-pool-id ap-southeast-1_XXXXXXXXX \
  --region ap-southeast-1 \
  --query "Users[*].{Username:Username,Status:UserStatus}"
```

Expected:
```json
[
    {"Username": "admin@demo.com",    "Status": "CONFIRMED"},
    {"Username": "customer@demo.com", "Status": "CONFIRMED"}
]
```

**Verify group memberships:**
```bash
aws cognito-idp list-users-in-group \
  --user-pool-id ap-southeast-1_XXXXXXXXX \
  --group-name CUSTOMER \
  --region ap-southeast-1 \
  --query "Users[*].Username" --output text
# Expected: customer@demo.com

aws cognito-idp list-users-in-group \
  --user-pool-id ap-southeast-1_XXXXXXXXX \
  --group-name ADMIN \
  --region ap-southeast-1 \
  --query "Users[*].Username" --output text
# Expected: admin@demo.com
```

**Verify login works — acquire a JWT token:**
```bash
aws cognito-idp initiate-auth \
  --auth-flow USER_PASSWORD_AUTH \
  --client-id <your-user-pool-client-id> \
  --auth-parameters USERNAME=customer@demo.com,PASSWORD=Demo@Pass2024! \
  --region ap-southeast-1 \
  --query "AuthenticationResult.IdToken" \
  --output text | cut -c1-60
# Expected: eyJraWQiOiJ...  (JWT token)
```

Use the `AuthStack.UserPoolClientId` value from the outputs in step 5.2.1 for `<your-user-pool-client-id>`.

---

### Troubleshooting

| Problem | Solution |
|---------|---------|
| `Could not resolve table name` | Ensure `CDK_DEFAULT_REGION=ap-southeast-1` is in `.env` and DatabaseStack is deployed |
| `AccessDeniedException` on seed | Your IAM user needs `dynamodb:PutItem` on EcommerceTable |
| `Demo user env vars not set` | Set all five variables in `infrastructure/.env` |
| `InvalidPasswordException` | Password must have uppercase, lowercase, digit, and symbol — e.g. `Demo@Pass2024!` |
| Users exist but status is `FORCE_CHANGE_PASSWORD` | Script did not finish setting permanent password; delete the users in Cognito console and re-run `seed:users` |
