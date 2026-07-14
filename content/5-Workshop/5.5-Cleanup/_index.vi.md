---
title: "Dọn dẹp tài nguyên"
date: 2026-07-04
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Dọn dẹp tài nguyên

Xóa tất cả tài nguyên AWS đã tạo trong workshop này để tránh phát sinh chi phí.

> **Cảnh báo:** Thao tác này **không thể hoàn tác**. Tất cả dữ liệu bao gồm DynamoDB items, Cognito users, CloudWatch logs và S3 assets sẽ bị xóa vĩnh viễn. Đừng chạy cleanup nếu bạn muốn giữ lại ứng dụng đã triển khai.

---

### 5.6.1 Xóa tất cả Stack

Chạy từ thư mục `infrastructure/`:

```bash
cd infrastructure
npx cdk destroy --all --force
```

CDK sẽ xóa từng stack theo thứ tự ngược chiều dependency tự động.

Kết quả mong đợi cho mỗi stack:
```
FrontendStack: destroying...
FrontendStack: destroyed

InfrastructureStack: destroying...
InfrastructureStack: destroyed

MonitoringStack: destroying...
MonitoringStack: destroyed

SecurityStack: destroying...
SecurityStack: destroyed

ApiStack: destroying...
ApiStack: destroyed

EventStack: destroying...
EventStack: destroyed

DatabaseStack: destroying...
DatabaseStack: destroyed

AuthStack: destroying...
AuthStack: destroyed
```

> **Lưu ý:** CloudFront distribution mất 5–10 phút để disable hoàn toàn trước khi xóa. Nếu FrontendStack bị kẹt, đợi và thử lại:
> ```bash
> npx cdk destroy FrontendStack --force
> ```

---

### 5.6.2 Xác minh tất cả Stack đã bị xóa

```bash
# Các stack ở ap-southeast-1
aws cloudformation list-stacks \
  --region ap-southeast-1 \
  --stack-status-filter DELETE_COMPLETE \
  --query "StackSummaries[?contains(['AuthStack','DatabaseStack','EventStack','ApiStack','MonitoringStack','FrontendStack','InfrastructureStack'],StackName)].{Stack:StackName,Status:StackStatus}" \
  --output table
```

```bash
# SecurityStack ở us-east-1
aws cloudformation list-stacks \
  --region us-east-1 \
  --stack-status-filter DELETE_COMPLETE \
  --query "StackSummaries[?StackName=='SecurityStack'].{Stack:StackName,Status:StackStatus}" \
  --output table
```

Cả hai query đều phải hiển thị tất cả stack với trạng thái `DELETE_COMPLETE`.

---

### 5.6.3 Xác minh DynamoDB Table đã bị xóa

```bash
aws dynamodb describe-table \
  --table-name EcommerceTable \
  --region ap-southeast-1 2>&1 | grep -i "error\|not found\|ResourceNotFoundException"
# Kết quả mong đợi: ResourceNotFoundException
```

---

### 5.6.4 Xác minh S3 Bucket đã bị xóa

```bash
aws s3 ls | grep ecommerce-frontend
# Kết quả mong đợi: không có output (bucket đã bị xóa)
```

Nếu bucket vẫn còn tồn tại (CDK không thể làm trống):

```bash
# Làm trống bucket trước
aws s3 rm s3://ecommerce-frontend-123456789012-ap-southeast-1 --recursive

# Thay 123456789012 bằng ID tài khoản AWS từ lệnh `aws sts get-caller-identity` ở bước trước.

# Sau đó thử lại xóa FrontendStack
npx cdk destroy FrontendStack --force
```

---

### 5.6.5 Dọn dẹp build artifacts cục bộ

```bash
cd infrastructure && rm -rf dist/ cdk.out/
cd ../frontend && rm -rf dist/
```

---

### Những gì đã bị xóa

| Tài nguyên | Stack |
|-----------|-------|
| Cognito User Pool + toàn bộ người dùng | AuthStack |
| DynamoDB EcommerceTable + toàn bộ dữ liệu | DatabaseStack |
| EventBridge bus + rule | EventStack |
| SQS OrderQueue + DLQ | EventStack |
| Lambda × 4 (Products, Cart, Orders, Processor) | ApiStack + EventStack |
| API Gateway REST API | ApiStack |
| CloudWatch log groups × 5 | ApiStack + EventStack |
| WAF WebACL (us-east-1) | SecurityStack |
| CloudWatch Dashboard + 6 alarm | MonitoringStack |
| SNS topic + email subscription | MonitoringStack |
| S3 frontend bucket | FrontendStack |
| CloudFront distribution | FrontendStack |
| CDK Bootstrap resources | (giữ lại — an toàn cho lần sử dụng sau) |

---

### Xử lý lỗi thường gặp

| Lỗi | Giải pháp |
|-----|----------|
| CloudFront distribution kẹt khi xóa | Đợi 5–10 phút; chạy lại `npx cdk destroy FrontendStack --force` |
| Lỗi S3 bucket không trống | Chạy `aws s3 rm s3://ecommerce-frontend-ACCOUNT-ap-southeast-1 --recursive` rồi thử lại |
| Stack kẹt ở `DELETE_IN_PROGRESS` | Kiểm tra tab Events trên CloudFormation console để tìm resource đang block; đợi và thử lại |
| SecurityStack chưa bị xóa | Chạy `npx cdk destroy SecurityStack --force` (nội bộ nhắm tới us-east-1) |
| DynamoDB table vẫn còn | Bảng có `RemovalPolicy.DESTROY` — nếu vẫn tồn tại, xóa thủ công trong DynamoDB console |

---

### Chúc mừng bạn đã hoàn thành workshop!

Bạn đã triển khai thành công một nền tảng thương mại điện tử AWS Serverless cấp sản xuất hoàn chỉnh bao gồm:

- Xác thực Cognito với JWT
- Thiết kế DynamoDB single-table với 3 GSI
- Xử lý đơn hàng theo hướng sự kiện (EventBridge → SQS → Lambda)
- REST API với Cognito authorizer
- Bảo vệ WAF (OWASP CRS + IP reputation + rate limiting)
- CloudFront CDN với OAC và security headers
- Quan sát CloudWatch (Dashboard + 6 alarm + SNS email)
- React 19 SPA với runtime config injection
