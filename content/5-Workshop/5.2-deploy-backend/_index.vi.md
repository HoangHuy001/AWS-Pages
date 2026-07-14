---
title: "Triển khai các Backend Stack"
date: 2026-07-04
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Triển khai các Backend Stack

Triển khai tất cả CDK stack backend theo đúng thứ tự phụ thuộc. Mỗi stack export các giá trị CloudFormation được stack tiếp theo sử dụng.

> **Điều kiện cần cho quy trình thanh toán đầy đủ:** trước khi triển khai ApiStack, hãy chắc chắn các giá trị VNPay đã được điền vào `infrastructure/.env`. Stack sẽ dùng chúng để tạo hoặc cập nhật secret `VNPayConfig` trong AWS Secrets Manager trong quá trình deploy, và Lambda thanh toán sẽ đọc secret này khi chạy. Nếu thiếu các giá trị này thì luồng checkout/payment sẽ không hoạt động. Giá trị `VNP_RETURN_URL` được xây dựng từ `ALLOWED_ORIGIN` và trỏ về `/payment/vnpay-return`.

**Thứ tự triển khai:**
```
Bootstrap → AuthStack → DatabaseStack → EventStack → ApiStack → SecurityStack → MonitoringStack → FrontendStack → InfrastructureStack → Seed Data → Verification → Cleanup
```

---

### 5.2.1 Triển khai AuthStack

**Mục đích:** Tạo Cognito User Pool, User Pool Client và hai nhóm CUSTOMER/ADMIN. Tất cả các route API được bảo vệ đều xác thực token được phát hành bởi pool này.

```bash
cd infrastructure
npx cdk deploy AuthStack --require-approval never
```

Kết quả mong đợi:
```
AuthStack

Outputs:
AuthStack.CognitoRegion = ap-southeast-1
AuthStack.UserPoolClientId = 1g8038olqqhg6psmka7etvc47e
AuthStack.UserPoolId = ap-southeast-1_dOmrwNnTw
```

**Sau khi deploy — cập nhật file môi trường:**

Trong `infrastructure/.env`:
```bash
COGNITO_USER_POOL_ID=ap-southeast-1_XXXXXXXXX
```
> Giá trị này hiển thị trong output `AuthStack.UserPoolId` ở trên.

Trong `frontend/.env`:
```bash
VITE_COGNITO_USER_POOL_ID=ap-southeast-1_XXXXXXXXX
VITE_COGNITO_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxxxx
VITE_COGNITO_REGION=ap-southeast-1
```
> `VITE_COGNITO_USER_POOL_ID` lấy từ `AuthStack.UserPoolId`. `VITE_COGNITO_CLIENT_ID` lấy từ `AuthStack.UserPoolClientId`.

**Xác minh:**
```bash
aws cognito-idp describe-user-pool \
  --user-pool-id ap-southeast-1_XXXXXXXXX \
  --region ap-southeast-1 \
  --query "UserPool.{Name:Name,Status:Status}" \
  --output table
# Kết quả mong đợi: Name=ecommerce-user-pool, Status=Enabled
```

---

### 5.2.2 Triển khai DatabaseStack

**Mục đích:** Tạo `EcommerceTable` — thiết kế DynamoDB single-table với ba GSI cho catalog sản phẩm, lịch sử đơn hàng theo người dùng và danh sách sản phẩm đầy đủ. Tất cả dữ liệu ứng dụng lưu trong một bảng duy nhất.

```bash
npx cdk deploy DatabaseStack --require-approval never
```

Kết quả mong đợi:
```
DatabaseStack

Outputs:
DatabaseStack.TableArn = arn:aws:dynamodb:ap-southeast-1:123456789012:table/EcommerceTable
DatabaseStack.TableName = EcommerceTable
```

**Xác minh:**
```bash
aws dynamodb describe-table \
  --table-name EcommerceTable \
  --region ap-southeast-1 \
  --query "Table.{Status:TableStatus,Billing:BillingModeSummary.BillingMode,GSIs:GlobalSecondaryIndexes[*].IndexName}"
# Kết quả mong đợi: Status=ACTIVE, Billing=PAY_PER_REQUEST, GSIs=[GSI1, GSI2, GSI3]
```

---

### 5.2.3 Triển khai EventStack

**Mục đích:** Tạo pipeline xử lý đơn hàng theo hướng sự kiện: EventBridge custom bus, SQS OrderQueue (visibility 180s, maxReceiveCount=3), SQS Dead Letter Queue và Lambda OrderProcessorFunction.

```bash
npx cdk deploy EventStack --require-approval never
```

Mất khoảng ~2 phút vì CDK bundle Lambda với esbuild.

Kết quả mong đợi:
```
EventStack

Outputs:
EventStack.EventBusName = EcommerceEventBus
EventStack.OrderDLQName = EcommerceOrderDLQ
EventStack.OrderQueueName = EcommerceOrderQueue
EventStack.OrderProcessorFunctionName = OrderProcessorFunction
```

---

### 5.2.4 Triển khai ApiStack

**Mục đích:** Tạo REST API Gateway (100 rps / 200 burst) và ba Lambda function: ProductServiceFunction (public), CartServiceFunction (auth), OrderServiceFunction (auth + EventBridge publisher).

```bash
npx cdk deploy ApiStack --require-approval never
```

Mất ~3 phút (bundle ba Lambda function).

Kết quả mong đợi:
```
ApiStack

Outputs:
ApiStack.ApiUrl = https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/
```

**Sau khi deploy — cập nhật frontend/.env:**
```bash
VITE_API_URL=https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/
```

---

### 5.2.5 Triển khai SecurityStack

**Mục đích:** Tạo WAF v2 WebACL tại **us-east-1** với bốn rule: IP Reputation List, OWASP Common Rule Set, Known Bad Inputs (Log4j, Spring4Shell) và rate limiting 1000 request mỗi 5 phút mỗi IP.

> **Lưu ý:** SecurityStack luôn triển khai tới us-east-1 bất kể `CDK_DEFAULT_REGION`. Đây là yêu cầu cho WAF có phạm vi CloudFront.

```bash
npx cdk deploy SecurityStack --require-approval never
```

Kết quả mong đợi:
```
SecurityStack

Outputs:
SecurityStack.WafWebAclArn = arn:aws:wafv2:us-east-1:123456789012:global/webacl/EcommerceWebACL/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

**Sau khi deploy — cập nhật infrastructure/.env:**
```bash
WAF_WEB_ACL_ARN=arn:aws:wafv2:us-east-1:123456789012:global/webacl/EcommerceWebACL/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
> Giá trị ARN đầy đủ hiển thị trong output `SecurityStack.WafWebAclArn` ở trên.

**Xác minh:**
```bash
aws wafv2 list-web-acls \
  --scope CLOUDFRONT \
  --region us-east-1 \
  --query "WebACLs[?Name=='EcommerceWebACL'].Name" \
  --output text
# Kết quả mong đợi: EcommerceWebACL
```

---

### 5.2.6 Triển khai MonitoringStack

**Mục đích:** Tạo CloudWatch Dashboard (5 hàng bao gồm tất cả tầng dịch vụ), sáu alarm vận hành, SNS topic và subscription email để nhận thông báo alarm.

> **Điều kiện:** `ALERT_EMAIL` phải được đặt trong `infrastructure/.env`.

```bash
npx cdk deploy MonitoringStack --require-approval never
```

Kết quả mong đợi:
```
MonitoringStack

Outputs:
MonitoringStack.AlarmsTopicArn = arn:aws:sns:ap-southeast-1:123456789012:EcommerceAlarmsTopic
MonitoringStack.DashboardName = EcommerceDashboard
```

**Xác nhận SNS subscription:**

AWS gửi email xác nhận ngay lập tức tới `ALERT_EMAIL`. Kiểm tra hộp thư đến:

```
Từ: AWS Notifications <no-reply@sns.amazonaws.com>
Tiêu đề: AWS Notification - Subscription Confirmation
```

**Nhấp vào liên kết "Confirm subscription".** Không có email alarm nào được gửi cho đến khi bạn xác nhận.

**Xác minh alarms:**
```bash
aws cloudwatch describe-alarms \
  --region ap-southeast-1 \
  --query "MetricAlarms[?starts_with(AlarmName,'Ecommerce')].{Alarm:AlarmName,State:StateValue}" \
  --output table
# Kết quả mong đợi: 6 alarm tất cả đều OK hoặc INSUFFICIENT_DATA
```
hoặc 

**Cách mở dashboard:**
![cloudwtach buoc 1](/images/5-Workshop/5.2-deploy-backend/cloudwatcha.jpg)
![cloudwtach buoc 2](/images/5-Workshop/5.2-deploy-backend/cloudwatchb.jpg)

Dashboard này được tạo bởi MonitoringStack.

---

### Xử lý lỗi thường gặp

| Lỗi | Giải pháp |
|-----|----------|
| `EcommerceEventBusArn is not an export` | EventStack chưa được deploy — deploy theo đúng thứ tự |
| `EcommerceApiUrl is not an export` | ApiStack chưa được deploy |
| Lambda bundling thất bại | Chạy `npm install` trong `infrastructure/` để đảm bảo esbuild có mặt |
| SecurityStack deploy vào account sai | Kiểm tra `CDK_DEFAULT_ACCOUNT` trong `.env` |
| Không nhận email xác nhận sau MonitoringStack | Kiểm tra thư mục spam; xác minh `ALERT_EMAIL` không có lỗi chính tả |
