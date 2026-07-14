---
title: "Triển khai Frontend Stack"
date: 2026-07-04
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Triển khai Frontend Stack

Build React SPA và triển khai lên S3 + CloudFront, sau đó cấu hình runtime config để trình duyệt đọc đúng API URL và cài đặt Cognito mà không cần nhúng cứng chúng vào JavaScript bundle.

---

### 5.3.1 Build Frontend

```bash
cd ../frontend
npm run build
```

Kết quả mong đợi:
```
vite v7.x.x building client environment for production...
✓ 2504 modules transformed.
dist/index.html                         1.14 kB │ gzip:   0.59 kB
dist/assets/index-XXXXXXXX.js         750.06 kB │ gzip: 217.53 kB
✓ built in 35s
```

> **Lưu ý:** Build có thể in cảnh báo chunk size (`Some chunks are larger than 500 kB`). Đây là gợi ý về hiệu năng, không phải lỗi — build thành công khi bạn thấy `✓ built in Xs`.

Output đã biên dịch nằm trong `frontend/dist/`.

---

### 5.3.2 Triển khai FrontendStack

**Điều kiện:**
- `WAF_WEB_ACL_ARN` đã được đặt trong `infrastructure/.env` (từ SecurityStack — bước 5.2.5)
- `frontend/dist/` đã tồn tại (build ở bước 5.3.1)

```bash
cd ../infrastructure
npx cdk deploy FrontendStack --require-approval never
```

Bước này mất **5–10 phút** — CDK tải tất cả asset lên S3 và tạo CloudFront distribution.

Kết quả mong đợi:
```
FrontendStack

Outputs:
FrontendStack.CloudFrontDistributionId = EXXXXXXXXXXXXXXXXX
FrontendStack.CloudFrontDomainName = dXXXXXXXXXXXXXX.cloudfront.net
FrontendStack.FrontendBucketName = ecommerce-frontend-123456789012-ap-southeast-1
FrontendStack.FrontendUrl = https://dXXXXXXXXXXXXXX.cloudfront.net
```
---

### 5.3.3 Cấu hình Runtime Config

Frontend đọc API URL và cấu hình Cognito từ `/config.json` được phục vụ bởi CloudFront. File này được CDK tạo ra tại thời điểm deploy — không có giá trị nào được hardcode trong JavaScript bundle.

**Xác minh config.json đúng:**

> Trên **Linux/macOS** dùng `python3`. Trên **Windows** dùng `python`.

```bash
curl -s "https://dXXXXXXXXXXXXXX.cloudfront.net/config.json" | python3 -m json.tool
```

Kết quả mong đợi:
```json
{
    "apiUrl": "https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/",
    "cognito": {
        "userPoolId": "ap-southeast-1_XXXXXXXXX",
        "clientId": "xxxxxxxxxxxxxxxxxxxxxxxxxx",
        "region": "ap-southeast-1"
    }
}
```

---

### 5.3.4 Cập nhật CORS và Redeploy

Bây giờ bạn đã có CloudFront URL, cập nhật CORS origin cho API và redeploy.

**Bước 1 — Cập nhật `infrastructure/.env`:**
```bash
ALLOWED_ORIGIN=https://dXXXXXXXXXXXXXX.cloudfront.net
```
> Dùng giá trị thực của `FrontendStack.FrontendUrl` từ output ở trên làm URL CloudFront.

**Bước 2 — Redeploy ApiStack** để áp dụng CORS origin mới cho cả ba Lambda function:
```bash
npx cdk deploy ApiStack --require-approval never
```

Kết quả mong đợi — Lambda function được cập nhật:
```
ApiStack |  UPDATE_COMPLETE | AWS::Lambda::Function | ProductServiceFunction
ApiStack |  UPDATE_COMPLETE | AWS::Lambda::Function | CartServiceFunction
ApiStack |  UPDATE_COMPLETE | AWS::Lambda::Function | OrderServiceFunction
ApiStack
```

**Bước 3 — Rebuild frontend và redeploy FrontendStack** để tái tạo `config.json` với các giá trị đúng:
```bash
cd ../frontend && npm run build && cd ../infrastructure
npx cdk deploy FrontendStack --require-approval never
```

**Bước 4 — Xác minh CORS header từ API:**
```bash
curl -I \
  -H "Origin: https://dXXXXXXXXXXXXXX.cloudfront.net" \
  "https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/products"
```

Kết quả mong đợi:
```
access-control-allow-origin: https://dXXXXXXXXXXXXXX.cloudfront.net
access-control-allow-credentials: true
```

---

### 5.3.5 Triển khai InfrastructureStack

```bash
npx cdk deploy InfrastructureStack --require-approval never
```

Kết quả mong đợi:
```
InfrastructureStack (no changes)
```

Stack này không có tài nguyên — đây là placeholder điều phối dependency.

---

### Xử lý lỗi thường gặp

| Lỗi | Giải pháp |
|-----|----------|
| Không tìm thấy folder `dist/` | Chạy `npm run build` trong `frontend/` trước |
| CloudFront trả về 403 | Đợi 2–3 phút để distribution propagate |
| `config.json` hiển thị giá trị sai | Invalidate CloudFront cache: `aws cloudfront create-invalidation --distribution-id EXXXXXXXXX --paths "/*"` |
| Lỗi CORS trong browser console | `ALLOWED_ORIGIN` chưa được đặt đúng; làm lại bước 5.3.4 |
| Cảnh báo `WAF_WEB_ACL_ARN` trống | Sao chép ARN từ SecurityStack outputs vào `infrastructure/.env` |
