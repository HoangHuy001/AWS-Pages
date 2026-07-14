---
title: "Chuẩn bị môi trường"
date: 2026-07-04
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Chuẩn bị môi trường

Trước khi triển khai bất cứ thứ gì lên AWS, bạn cần cài đặt các công cụ cần thiết, cấu hình thông tin xác thực, clone dự án và cài đặt các gói phụ thuộc.

---

### Chi phí triển khai ước tính

Chi phí hàng tháng ước tính: ~$10.92/tháng.

Nhắc lại: hủy tài nguyên sau khi kiểm thử để tránh phát sinh chi phí tiếp diễn.

---

### Quyền IAM cần thiết

Identity AWS dùng để triển khai cần quyền tối thiểu cho các dịch vụ dùng trong workshop:

- CloudFormation
- CDK bootstrap và deployment
- Lambda
- API Gateway
- DynamoDB
- EventBridge
- SQS
- SNS
- CloudWatch
- IAM
- Cognito
- S3
- CloudFront
- WAF
- Secrets Manager

Đối với tài khoản demo, cách đơn giản nhất là gắn managed policy `AdministratorAccess`. Nếu dùng policy scoped, hãy đảm bảo policy có đủ quyền để tạo và cấu hình các tài nguyên trên.

---

### 5.1.1 Phần mềm cần thiết

Cài đặt các công cụ sau trước khi tiếp tục.

#### Node.js 22 LTS

Tải từ [https://nodejs.org](https://nodejs.org).

```bash
node --version
# Kết quả mong đợi: v22.x.x

npm --version
# Kết quả mong đợi: 10.x.x
```

#### AWS CLI v2

Cài đặt từ [https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

```bash
aws --version
# Kết quả mong đợi: aws-cli/2.x.x
```

#### Git

```bash
git --version
# Kết quả mong đợi: git version 2.x.x
```

---

### 5.1.2 Cấu hình AWS Account

#### Cấu hình thông tin xác thực

```bash
aws configure
```

Nhập các thông tin sau:

```
AWS Access Key ID:     AKIAXXXXXXXXXXXXXXXX
AWS Secret Access Key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Default region name:   ap-southeast-1
Default output format: json
```

#### Xác minh danh tính

```bash
aws sts get-caller-identity
```

Kết quả mong đợi:
```json
{
    "UserId": "AIDAXXXXXXXXXXXXXXXXX",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/your-username"
}
```

Ghi lại giá trị **Account** — bạn sẽ cần nó ở bước tiếp theo.

---

### 5.1.3 Clone dự án và cài đặt dependencies

#### Clone repository

```bash
git clone https://github.com/Hiep-123/ProjectAWS.git
cd ProjectAWS
```

> Đây là URL repository GitHub thực tế, có thể kiểm tra bằng lệnh `git remote -v`.

#### Cài đặt dependencies cho infrastructure

```bash
cd infrastructure
npm install
```

#### Kiểm tra CDK CLI

```bash
npx cdk --version
# Kết quả mong đợi: 2.1126.0 (build xxxxxxx)
```

#### Cài đặt dependencies cho frontend

```bash
cd ../frontend
npm install
cd ../infrastructure
```

---

### 5.1.4 Cấu hình biến môi trường

#### Tạo file môi trường cho infrastructure

```bash
cp .env.example .env
```

Chỉnh sửa `infrastructure/.env` và điền các giá trị:

```bash
# ── AWS ──────────────────────────────────────────────────────────────
CDK_DEFAULT_ACCOUNT=123456789012       # ID tài khoản AWS 12 chữ số
CDK_DEFAULT_REGION=ap-southeast-1

# ── Điền SAU KHI deploy FrontendStack ────────────────────────────────
ALLOWED_ORIGIN=                         # https://dXXXX.cloudfront.net

# ── VNPay (stack ApiStack dùng để tạo secret VNPayConfig) ─────────────
VNP_TMN_CODE=                           # mã merchant VNPay của bạn
VNP_HASH_SECRET=                        # hash secret VNPay của bạn
VNP_URL=https://sandbox.vnpayment.vn/paymentv2/vpcpay.html

# ── Email nhận cảnh báo ───────────────────────────────────────────────
ALERT_EMAIL=your@email.com

# ── Điền SAU KHI deploy SecurityStack ────────────────────────────────
WAF_WEB_ACL_ARN=                        # arn:aws:wafv2:us-east-1:...

# ── Điền SAU KHI deploy AuthStack ─────────────────────────────────────
COGNITO_USER_POOL_ID=                   # ap-southeast-1_XXXXXXXXX

# ── Thông tin đăng nhập demo ──────────────────────────────────────────
DEMO_CUSTOMER_EMAIL=customer@demo.com
DEMO_CUSTOMER_PASSWORD=Demo@Pass2024!  # tối thiểu 8 ký tự, chữ hoa+thường+số+ký tự đặc biệt
```

#### Tạo file môi trường cho frontend

```bash
cd ../frontend
cp .env.example .env
```

Chỉnh sửa `frontend/.env`:

```bash
VITE_API_URL=                           # điền sau khi deploy ApiStack
VITE_COGNITO_USER_POOL_ID=              # điền sau khi deploy AuthStack
VITE_COGNITO_CLIENT_ID=                 # điền sau khi deploy AuthStack
VITE_COGNITO_REGION=ap-southeast-1
VITE_ENVIRONMENT=production
VITE_ENABLE_MOCKS=false
```

```bash
cd ../infrastructure
```

### 5.1.4.1 Chuẩn bị thông tin VNPay (bắt buộc cho quy trình thanh toán đầy đủ)

Trong kiến trúc hiện tại, `ApiStack` đọc các giá trị VNPay từ `infrastructure/.env` và dùng chúng để tự tạo secret `VNPayConfig` trong AWS Secrets Manager khi deploy. Lambda thanh toán sẽ đọc secret này khi chạy.

#### Các giá trị cần điền trong `infrastructure/.env`

- `VNP_TMN_CODE`: mã merchant VNPay được cấp từ tài khoản sandbox.
- `VNP_HASH_SECRET`: khóa hash secret tương ứng với merchant đó.
- `VNP_URL`: URL thanh toán của VNPay. Với sandbox, dùng `https://sandbox.vnpayment.vn/paymentv2/vpcpay.html`.
- `ALLOWED_ORIGIN`: URL CloudFront đã deploy, ví dụ `https://d123abc.cloudfront.net`. Stack sẽ dùng giá trị này để tạo `VNP_RETURN_URL` theo dạng `${ALLOWED_ORIGIN}/payment/vnpay-return`.

Ví dụ:

```bash
VNP_TMN_CODE=YOUR_TMN_CODE
VNP_HASH_SECRET=YOUR_HASH_SECRET
VNP_URL=https://sandbox.vnpayment.vn/paymentv2/vpcpay.html
ALLOWED_ORIGIN=https://YOUR_CLOUDFRONT_DOMAIN
```

Secret được stack tạo sẽ chứa các trường JSON sau:

- `VNP_TMN_CODE`
- `VNP_HASH_SECRET`
- `VNP_URL`
- `VNP_RETURN_URL`

Nếu chỉ kiểm tra các luồng sản phẩm, giỏ hàng và đặt hàng, bạn có thể bỏ qua bước này, nhưng luồng VNPay checkout sẽ không hoạt động đầy đủ.

#### Xác minh secret sau khi deploy

```bash
aws secretsmanager describe-secret --secret-id VNPayConfig --region ap-southeast-1
aws secretsmanager get-secret-value --secret-id VNPayConfig --region ap-southeast-1 --query SecretString --output text
```

Bạn sẽ thấy các trường trên xuất hiện trong payload của secret.

#### Kiểm tra TypeScript biên dịch thành công

```bash
npx tsc --noEmit --skipLibCheck
# Kết quả mong đợi: không có output — exit code 0
```

---

### 5.1.5 Bootstrap CDK

CDK Bootstrap tạo S3 bucket và IAM role được CDK sử dụng để lưu trữ các asset khi triển khai. Chạy một lần cho mỗi tài khoản và region.

#### Bootstrap ap-southeast-1 (region chính)

```bash
npx cdk bootstrap aws://123456789012/ap-southeast-1
```

> Thay `123456789012` bằng ID tài khoản AWS 12 chữ số lấy từ lệnh `aws sts get-caller-identity`.

Kết quả mong đợi:
```
Environment aws://123456789012/ap-southeast-1 bootstrapped.
```

#### Bootstrap us-east-1 (bắt buộc cho WAF WebACL)

```bash
npx cdk bootstrap aws://123456789012/us-east-1
```

> Giá trị này là cùng tài khoản AWS như trên; cần thiết vì WAF WebACL được triển khai ở us-east-1.

Kết quả mong đợi:
```
Environment aws://123456789012/us-east-1 bootstrapped.
```

#### Xác minh

```bash
aws cloudformation describe-stacks \
  --stack-name CDKToolkit \
  --region ap-southeast-1 \
  --query "Stacks[0].StackStatus" \
  --output text
# Kết quả mong đợi: CREATE_COMPLETE
```

---

### Xử lý lỗi thường gặp

| Lỗi | Giải pháp |
|-----|----------|
| `node: command not found` | Khởi động lại terminal sau khi cài đặt |
| `aws: command not found` | Khởi động lại terminal; kiểm tra AWS CLI đã được thêm vào PATH |
| `ExpiredTokenException` | Chạy lại `aws configure` với credentials mới |
| `CDKToolkit in ROLLBACK_COMPLETE` | Xóa stack trong CloudFormation console, sau đó bootstrap lại |
| Lỗi TypeScript khi `tsc --noEmit` | Đảm bảo `npm install` đã chạy thành công trong `infrastructure/` |
