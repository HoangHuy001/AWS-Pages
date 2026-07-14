---
title: "Bản đề xuất"
date: 2026-07-04
draft: false
toc: true
weight: 2
chapter: false
pre: "2. "
---

# Nền tảng thương mại điện tử Serverless trên AWS
## Hệ thống mua sắm cloud-native sẵn sàng cho môi trường sản xuất, xây dựng trên kiến trúc AWS Serverless



### 1. Tóm tắt điều hành

Dự án này xây dựng một nền tảng thương mại điện tử hoàn chỉnh, cấp độ sản xuất, dựa hoàn toàn trên các dịch vụ serverless và dịch vụ được quản lý của AWS. Hệ thống hỗ trợ toàn bộ vòng đời mua sắm của khách hàng — duyệt sản phẩm, quản lý giỏ hàng, đặt hàng và theo dõi đơn hàng theo thời gian thực — mà không cần quản lý hay duy trì bất kỳ cơ sở hạ tầng máy chủ nào.

Nền tảng được triển khai tại region **ap-southeast-1 (Singapore)** sử dụng **AWS Cloud Development Kit (CDK) với TypeScript** làm framework infrastructure-as-code. Dự án chứng minh khả năng áp dụng thực tế các nguyên tắc AWS Well-Architected Framework trên cả năm trụ cột: bảo mật, độ tin cậy, hiệu năng, tối ưu chi phí và xuất sắc vận hành.

Dự án được nghiên cứu và triển khai trong suốt kỳ thực tập 1 tháng như một minh chứng thực tiễn về thiết kế kiến trúc cloud, phát triển serverless và triển khai trên AWS.

### 2. Tuyên bố vấn đề

#### Bối cảnh

Các doanh nghiệp thương mại điện tử hiện đại yêu cầu cơ sở hạ tầng có thể xử lý lưu lượng truy cập không thể dự đoán, duy trì tính sẵn sàng cao, thực thi bảo mật ở mọi lớp và tiết kiệm chi phí trong các giai đoạn hoạt động thấp. Các hệ thống triển khai truyền thống dựa trên máy chủ mang lại chi phí vận hành đáng kể: máy chủ phải được cấp phát, vá lỗi, mở rộng quy mô và giám sát liên tục bất kể lưu lượng thực tế.

#### Những thách thức chính

**Sự phức tạp trong vận hành:** Hệ thống dựa trên máy chủ đòi hỏi đội ngũ vận hành chuyên trách, cửa sổ bảo trì theo lịch định kỳ và các quyết định mở rộng quy mô thủ công gây ra rủi ro và chi phí.

**Kém hiệu quả về chi phí ở tải thay đổi:** Máy chủ cố định công suất phát sinh chi phí ngay cả khi nhàn rỗi. Một nền tảng thương mại điện tử tự nhiên có những khoảng thời gian lưu lượng rất thấp (ban đêm, mùa thấp điểm) khiến công suất nhàn rỗi bị lãng phí.

**Diện tích bề mặt bảo mật:** Máy chủ tiếp xúc công khai, dữ liệu không được mã hóa khi lưu trữ và truyền tải, cũng như cơ chế xác thực yếu vẫn là nguyên nhân hàng đầu dẫn đến vi phạm dữ liệu trong thương mại điện tử.

**Tốc độ phát triển:** Backend nguyên khối kết hợp chặt chẽ làm chậm quá trình lặp và khiến việc triển khai các cải tiến cho từng khu vực dịch vụ riêng lẻ trở nên khó khăn mà không gây rủi ro cho toàn bộ hệ thống.

#### Cơ hội

AWS cung cấp một hệ sinh thái serverless được quản lý hoàn toàn giải quyết tất cả những thách thức này. Bằng cách kết hợp API Gateway, Lambda, DynamoDB, EventBridge và CloudFront, hoàn toàn có thể xây dựng một backend thương mại điện tử có khả năng mở rộng, bảo mật và quan sát được — không tốn chi phí khi nhàn rỗi và tự động mở rộng quy mô dưới tải — mà không cần quản lý bất kỳ máy chủ nào.



### 3. Mục tiêu

1. Thiết kế và triển khai API thương mại điện tử serverless cấp độ sản xuất cho các chức năng sản phẩm, giỏ hàng, đơn hàng và thanh toán.
2. Xây dựng quy trình xử lý đơn hàng bất đồng bộ theo hướng sự kiện bằng EventBridge và SQS, nhằm tách riêng khâu tạo đơn hàng khỏi khâu xử lý hậu kỳ.
3. Thực thi xác thực và phân quyền bằng Amazon Cognito, đồng thời kiểm tra JWT token tại lớp API.
4. Triển khai toàn bộ hạ tầng dưới dạng code bằng AWS CDK với TypeScript, đảm bảo khả năng triển khai lặp lại và kiểm soát được.
5. Bảo mật ứng dụng phía khách hàng bằng AWS WAF, CloudFront, TLS 1.2+ và IAM theo nguyên tắc tối thiểu hóa quyền.
6. Đảm bảo khả năng quan sát đầy đủ thông qua CloudWatch Dashboard, cảnh báo vận hành, tracing bằng X-Ray và thông báo qua SNS.
7. Cung cấp giao diện người dùng React 19 single-page có khả năng phản hồi, được phân phối qua CloudFront với cơ chế cache tài nguyên theo content-hash.
8. Minh họa tính phù hợp với tất cả năm trụ cột của AWS Well-Architected Framework.



### 4. Giải pháp đề xuất

Giải pháp là một nền tảng thương mại điện tử cloud-native được tổng hợp từ các stack cơ sở hạ tầng có thể triển khai độc lập, mỗi stack chịu trách nhiệm cho một domain được xác định rõ ràng.

#### Triết lý kiến trúc

Hệ thống tuân theo mô hình thiết kế **serverless-first, event-driven**. Không có EC2 instance, không có container và không có tiến trình máy chủ nào liên tục. Mọi đơn vị tính toán đều là Lambda function được gọi theo yêu cầu. Mọi tài nguyên cơ sở hạ tầng đều được định nghĩa bằng TypeScript sử dụng AWS CDK và triển khai qua CloudFormation.

#### Luồng khách hàng

Khách hàng tương tác với ứng dụng React 19 single-page được phân phối thông qua CloudFront. Các yêu cầu API được chuyển từ trình duyệt đến API Gateway, nơi JWT Cognito được xác thực và yêu cầu được định tuyến đến Lambda function phù hợp. Lambda tương tác với DynamoDB theo mô hình single-table để đọc và ghi dữ liệu. Khi khách hàng đặt hàng, Order Service phát sự kiện `OrderCreated` lên EventBridge để kích hoạt quy trình xử lý bất đồng bộ qua SQS. Order Processor tiêu thụ tin nhắn, cập nhật trạng thái đơn hàng trong vòng đời `PENDING → PROCESSING → COMPLETED` và phát lại sự kiện trạng thái về EventBridge. Ứng dụng phía client thực hiện polling đối với endpoint chi tiết đơn hàng theo chu kỳ 3 giây để hiển thị trạng thái mới nhất một cách trực tiếp.

#### Các quyết định thiết kế chính

- **Thiết kế DynamoDB single-table** — tất cả các thực thể (sản phẩm, mục giỏ hàng, đơn hàng) chia sẻ một bảng với access pattern dựa trên GSI, loại bỏ nhu cầu join và cho phép đọc theo key O(1) cho tất cả các đường dẫn hot.
- **HTTP 202 Accepted khi tạo đơn hàng** — Order Service phản hồi ngay lập tức sau khi lưu trữ đơn hàng PENDING và xuất bản sự kiện, giữ thời gian phản hồi API xác định bất kể độ phức tạp xử lý downstream.
- **Tiêm cấu hình runtime** — ID pool Cognito và URL API được tiêm tại thời điểm triển khai qua file `/config.json` được phục vụ từ CloudFront, ngăn các giá trị đặc thù theo môi trường được baked vào JavaScript bundle.
- **WAF tại us-east-1** — WebACL WAF được triển khai tại us-east-1 theo yêu cầu đối với WebACL có phạm vi CloudFront, và ARN của nó được truyền cho FrontendStack như biến môi trường để giải quyết giới hạn cross-region của CloudFormation export.



### 5. Tổng quan kiến trúc AWS


{{< img src="images/2-Proposal/diagram.png" alt="AWS Serverless Architecture" >}}


### 6. Dịch vụ AWS

| Dịch vụ | Vai trò trong nền tảng |
|---------|----------------------|
| **Amazon Cognito** | Đăng ký và xác thực người dùng. Hỗ trợ đăng ký qua email có xác minh, luồng SRP và USER_PASSWORD, nhóm CUSTOMER và ADMIN, cùng JWT token cho các yêu cầu API được bảo vệ. |
| **Amazon API Gateway** | Điểm vào REST API với throttling, authorizer Cognito JWT, logging có cấu trúc và các route công khai/được bảo vệ cho catalog, giỏ hàng, đơn hàng và thanh toán. |
| **AWS Lambda** | Hệ thống hiện đang dùng năm function: ProductServiceFunction, CartServiceFunction, OrderServiceFunction, PaymentServiceFunction và OrderProcessorFunction. Chúng chạy trên ARM64 Graviton2 (Node.js 22), được đóng gói bằng esbuild và bật tracing bằng AWS X-Ray. |
| **Amazon DynamoDB** | Thiết kế single-table với thanh toán PAY_PER_REQUEST, PITR, TTL cho giỏ hàng hết hạn và các GSI phục vụ truy vấn catalog, lịch sử đơn hàng và truy cập theo người dùng. |
| **Amazon EventBridge** | Event bus tùy chỉnh `EcommerceEventBus` dùng để phát hành sự kiện `OrderCreated` và kích hoạt xử lý đơn hàng bất đồng bộ. |
| **Amazon SQS** | `EcommerceOrderQueue` và `EcommerceOrderDLQ` đảm bảo xử lý tin nhắn theo cơ chế retry, buffering và tách lỗi khỏi luồng chính. |
| **Amazon Secrets Manager** | Lưu trữ thông tin xác thực VNPay cho PaymentServiceFunction, giúp cấu hình nhạy cảm không bị hardcode trong mã nguồn. |
| **Amazon S3** | Bucket riêng tư lưu trữ build frontend React, có mã hóa, versioning, HTTPS-only và chỉ được truy cập qua CloudFront OAC. |
| **Amazon CloudFront** | CDN toàn cầu phân phát frontend với OAC, TLS 1.2+, HTTP/2 và HTTP/3, fallback SPA routing và các policy cache cho file build của Vite. |
| **AWS WAF v2** | WebACL gắn trên CloudFront ở us-east-1 để chặn IP đáng ngờ, pattern tấn công OWASP, input độc hại và giới hạn tốc độ theo IP. |
| **Amazon CloudWatch** | Dashboard, alarm và log aggregation cho API Gateway, Lambda, EventBridge và SQS. |
| **Amazon SNS** | `EcommerceAlarmsTopic` gửi thông báo từ CloudWatch alarm tới kênh email vận hành. |
| **AWS CDK (TypeScript)** | Toàn bộ hạ tầng được định nghĩa bằng code trên tám stack: AuthStack, DatabaseStack, EventStack, ApiStack, MonitoringStack, SecurityStack, FrontendStack và InfrastructureStack. |
| **AWS X-Ray** | Bật tracing trên các Lambda function để theo dõi toàn bộ luồng request từ API xuống DynamoDB và EventBridge. |



### 7. Cân nhắc theo AWS Well-Architected Framework

#### Xuất sắc vận hành (Operational Excellence)
Toàn bộ cơ sở hạ tầng được định nghĩa dưới dạng code trong AWS CDK TypeScript. Mỗi stack có thể triển khai độc lập bằng `npx cdk deploy <StackName>`. CloudWatch cung cấp dashboard vận hành năm hàng bao gồm tất cả các tầng dịch vụ. Sáu alarm với `TreatMissingData=NOT_BREACHING` ngăn cảnh báo sai trong các giai đoạn yên tĩnh. Lambda source map cho phép đọc stack trace trong CloudWatch. X-Ray active tracing trên tất cả các function cung cấp khả năng hiển thị yêu cầu từ đầu đến cuối.

#### Bảo mật (Security)
Xác thực được thực thi qua Amazon Cognito JWT trên tất cả các route không công khai. Cognito authorizer của API Gateway cache kết quả năm phút, giảm độ trễ mà không ảnh hưởng bảo mật. Chính sách IAM tuân theo quyền tối thiểu — mỗi Lambda function có role riêng chỉ với các action DynamoDB cụ thể mà nó cần (ví dụ OrderProcessorFunction chỉ có `GetItem` và `UpdateItem`, không có `PutItem` hay `Scan`). S3 là private với HTTPS được thực thi. CloudFront chuyển hướng tất cả HTTP sang HTTPS. WAF chặn các IP độc hại đã biết, pattern tấn công OWASP Top 10 và client lạm dụng tốc độ. CORS sử dụng allowed origin rõ ràng (domain CloudFront) thay vì wildcard. Không có thông tin đăng nhập được hardcode ở bất kỳ đâu trong codebase — tất cả giá trị nhạy cảm được đọc từ biến môi trường tại thời điểm CDK synth hoặc từ `config.json` runtime được tiêm tại thời điểm triển khai.

#### Độ tin cậy (Reliability)
SQS `maxReceiveCount=3` đảm bảo các lần thử xử lý đơn hàng thất bại được thử lại trước khi định tuyến đến DLQ. `visibilityTimeout=180s` là sáu lần Lambda timeout (30s), tuân theo best practice AWS. DLQ lưu trữ 14 ngày cung cấp đủ thời gian để điều tra và phát lại. `ConditionExpression: attribute_not_exists(PK)` khi tạo đơn hàng ngăn đơn hàng trùng lặp trong điều kiện retry. Order Processor kiểm tra `status !== 'PENDING'` trước khi xử lý, cung cấp idempotency trước việc SQS giao hàng trùng lặp. DynamoDB PITR cho phép khôi phục point-in-time đến bất kỳ giây nào trong 35 ngày qua.

#### Hiệu năng (Performance Efficiency)
Tất cả Lambda function chạy trên ARM64 Graviton2, cung cấp khoảng 20% hiệu năng trên đơn vị giá tốt hơn so với x86. Node.js 22 với esbuild bundling và minification giảm kích thước cold-start package. AWS SDK v3 được bundle trực tiếp, loại bỏ phụ thuộc phiên bản runtime. DynamoDB PAY_PER_REQUEST scale về zero khi không có traffic và xử lý bất kỳ burst nào tự động. Behavior CachingOptimized của CloudFront với filename content-hashed Vite cho phép cache trình duyệt giữ tài nguyên vô thời hạn, giảm tải origin. Behavior CACHING_DISABLED trên `index.html` và `config.json` đảm bảo việc triển khai được phản ánh ngay lập tức. HTTP/2 và HTTP/3 giảm chi phí kết nối cho SPA.

#### Tối ưu chi phí (Cost Optimization)
Toàn bộ nền tảng là serverless — không có chi phí khi nhàn rỗi. DynamoDB PAY_PER_REQUEST có nghĩa là không có capacity unit được cung cấp dư thừa. Lambda tính phí theo từng 100ms gọi. CloudFront PRICE_CLASS_200 giới hạn edge location ở Mỹ, Châu Âu và Israel, giảm chi phí CDN mà không ảnh hưởng đáng kể đến độ trễ cho region mục tiêu (ap-southeast-1 là origin). Các CloudWatch log group có chính sách lưu trữ 30 ngày rõ ràng, ngăn chi phí lưu trữ không giới hạn. Không có EC2, RDS, ALB và chi phí NAT Gateway.

#### Bền vững (Sustainability)
Bộ xử lý ARM64 Graviton2 tiêu thụ ít năng lượng hơn trên mỗi đơn vị tính toán so với x86. CloudFront caching giảm số lần gọi Lambda và đọc DynamoDB cần thiết để phục vụ các yêu cầu lặp lại. PAY_PER_REQUEST có nghĩa là tài nguyên tính toán chỉ được cấp phát khi thực sự cần, không tiêu thụ khi nhàn rỗi.



### 8. Lộ trình triển khai

| Giai đoạn | Mô tả | Thời gian |
|-----------|-------|----------|
| **Giai đoạn 1** | Nền tảng hạ tầng: thiết lập dự án CDK, Cognito, DynamoDB single-table, API Gateway và khung Lambda ban đầu | Tuần 1 |
| **Giai đoạn 2–4** | Phát triển API lõi: ProductServiceFunction, CartServiceFunction, OrderServiceFunction và PaymentServiceFunction; thiết lập EventBridge và SQS | Tuần 1–2 |
| **Giai đoạn 5–6** | Xử lý đơn hàng theo hướng sự kiện: EventBridge custom bus, hàng đợi OrderQueue + DLQ, OrderProcessorFunction và tracing bằng X-Ray | Tuần 2 |
| **Giai đoạn 7** | Phát triển frontend: React 19 + Vite SPA, FrontendStack (S3 + CloudFront OAC), tiêm cấu hình runtime qua `config.json` và tích hợp Cognito SDK | Tuần 2–3 |
| **Giai đoạn 8** | Tăng cường bảo mật: WAF WebACL (SecurityStack tại us-east-1), IP reputation, OWASP CRS, Known Bad Inputs và rate limiting | Tuần 3 |
| **Giai đoạn 9** | Quan sát hệ thống: MonitoringStack (CloudWatch Dashboard, alarm, SNS email subscription) và cấu hình logging | Tuần 3 |
| **Giai đoạn 10** | Cải tiến UX và tối ưu hóa: tìm kiếm, sắp xếp giá, polling trạng thái đơn hàng và dọn dẹp frontend | Tuần 4 |
| **Hoàn thiện** | Kiểm thử, hardening backend/frontend, xử lý bảo mật và hoàn thiện tài liệu | Tuần 4 |


### 9. Ước tính chi phí

Để tham khảo, tệp dự toán hỗ trợ có thể tải xuống tại đây: <a href="/files/my-estimate.csv" download>Tải xuống my-estimate.csv</a>.

Tất cả ước tính chi phí sử dụng region **AWS Asia Pacific (Singapore) ap-southeast-1** và phản ánh mức sử dụng dự kiến cho nền tảng ở quy mô demo với lưu lượng vừa phải (khoảng 1.000 yêu cầu API mỗi ngày). Số liệu được tính theo **AWS Pricing Calculator** (ap-southeast-1, tháng 7/2025).

| Dịch vụ | Giả định sử dụng | Chi phí ước tính/tháng |
|---------|----------------|----------------------|
| AWS Lambda | 30.000 lần gọi/tháng, ARM64, 512 MB, 200ms | ~$0,00 (free tier: 1M lần gọi) |
| Amazon API Gateway | 30.000 lời gọi REST API/tháng | ~$0,13 |
| Amazon DynamoDB | PAY_PER_REQUEST, 0,1 GB storage, PITR 1 GB | ~$0,27 |
| Amazon SQS | ~1.000 tin nhắn đơn hàng/tháng | ~$0,00 (free tier: 1M request) |
| Amazon EventBridge | ~3.000 sự kiện tùy chỉnh/tháng | ~$0,00 (free tier: 1M sự kiện) |
| Amazon Secrets Manager | 1 secret, thời gian lưu trữ 30 ngày, ~100 API calls/tháng | ~$0,40 |
| Amazon S3 | ~10 MB tài nguyên SPA, 100 PUT request | ~$0,00 |
| Amazon CloudFront | ~1 GB truyền dữ liệu/tháng, PRICE_CLASS_200 | ~$0,14 |
| **AWS WAF** | 1 WebACL, 1 custom rule, 3 managed rule groups, ~30.000 request | **~$9,02** |
| Amazon Cognito | <50.000 MAU, Lite Tier | ~$0,01 |
| Amazon CloudWatch | 1 dashboard, 6 alarm, 0,5 GB log/tháng | ~$0,95 |
| Amazon SNS | <1.000 thông báo email/tháng | ~$0,00 |
| **Tổng cộng** | | **$10,92/tháng (~$131,07/năm)** |

Chi phí lớn nhất là **AWS WAF (~$9,02/tháng, chiếm khoảng 83% tổng chi phí)** gồm: $5,00 phí WebACL cố định + $3,00 cho 3 managed rule group + $1,00 cho 1 custom rate-limit rule. Ở mức zero traffic, nền tảng tốn khoảng **$9,10/tháng** (chỉ phí cố định WAF + CloudWatch), còn Secrets Manager chỉ làm tăng chi phí rất nhỏ.

Đối với triển khai quy mô sản xuất với 100.000 yêu cầu/ngày và 10.000 người dùng hoạt động hàng tháng, chi phí ước tính sẽ vào khoảng **$40–$70/tháng** — vẫn thấp hơn đáng kể so với các triển khai dựa trên máy chủ tương đương.



### 10. Đánh giá rủi ro

#### Ma trận rủi ro

| Rủi ro | Khả năng xảy ra | Mức độ ảnh hưởng | Biện pháp giảm thiểu |
|--------|----------------|-----------------|---------------------|
| Lambda cold start ảnh hưởng độ trễ cảm nhận | Trung bình | Thấp | ARM64 Graviton2 giảm thời gian cold start; esbuild bundling thu nhỏ kích thước package; DynamoDB PAY_PER_REQUEST loại bỏ thời gian khởi động |
| DynamoDB hot partition khi ghi đồng thời cao | Thấp | Cao | Thiết kế single-table sử dụng `orderId` ngẫu nhiên (UUID v4) làm tiền tố partition key, phân phối lệnh ghi đều các partition |
| Lỗi xử lý SQS message khiến đơn hàng bị kẹt | Thấp | Cao | DLQ lưu trữ 14 ngày và `maxReceiveCount=3` ngăn mất message; visibility timeout 6× ngăn requeue quá sớm; CloudWatch alarm bật khi có tin nhắn DLQ đầu tiên |
| JWT Cognito hết hạn gây lỗi 401 trong phiên dài | Trung bình | Trung bình | Axios response interceptor thực hiện silent token refresh qua Cognito SDK khi nhận 401; chuyển hướng đến login chỉ khi refresh token cũng hết hạn |
| WAF false positive chặn yêu cầu hợp lệ | Thấp | Trung bình | Managed rule group được đặt ở chế độ block nhưng có thể chuyển sang COUNT mode qua CDK override nếu phát hiện false positive |
| Phụ thuộc WAF ARN cross-region (SecurityStack → FrontendStack) | Thấp | Trung bình | WAF ARN được truyền qua biến môi trường (`WAF_WEB_ACL_ARN`) thay vì `Fn.importValue` để giải quyết giới hạn cross-region của CloudFormation export; được tài liệu hóa trong `.env.example` |
| Cấu hình sai ALLOWED_ORIGIN gây lỗi CORS | Trung bình | Cao | `ALLOWED_ORIGIN` phải được đặt thành CloudFront URL sau khi deploy FrontendStack; được tài liệu hóa trong `.env.example`; API fallback về `localhost:5173` nếu để trống |
| Cạn kiệt Lambda concurrency cấp tài khoản | Thấp | Trung bình | Nền tảng serverless không có reserved concurrency chia sẻ pool tài khoản; thêm reserved concurrency cho OrderProcessorFunction sẽ cô lập pipeline xử lý đơn hàng |

#### Kế hoạch dự phòng

- Tất cả stack có thể triển khai lại độc lập qua `npx cdk deploy <StackName>`.
- DynamoDB PITR cho phép khôi phục đến bất kỳ giây nào trong 35 ngày qua.
- Thư mục build `dist/` và tất cả CDK stack đều có thể tái tạo hoàn toàn từ source code.



### 11. Kết quả kỳ vọng

#### Sản phẩm kỹ thuật

1. **Tám CDK stack có thể triển khai độc lập** bao gồm cotoàn bộ nền tảng — xác thực, cơ sở dữ liệu, API, xử lý sự kiện, giám sát, bảo mật và phân phối frontend.

2. **Ba Lambda function cấp sản xuất** xử lý duyệt catalog sản phẩm công khai (với lọc danh mục, tìm kiếm text, sắp xếp theo giá và URL theo slug) cùng quản lý giỏ hàng và đơn hàng có xác thực.

3. **Pipeline xử lý đơn hàng theo hướng sự kiện** chứng minh kiến trúc bất đồng bộ: API tách biệt khỏi xử lý, SQS là bộ đệm tin cậy, DLQ để cô lập lỗi và chuyển đổi trạng thái idempotent trong DynamoDB.

4. **SPA hướng đến khách hàng bảo mật** với xác thực Cognito, polling trạng thái đơn hàng thời gian thực (interval 3 giây với phát hiện trạng thái kết thúc) và thiết kế responsive.

5. **Stack quan sát đầy đủ** với CloudWatch Dashboard năm hàng, sáu alarm vận hành, thông báo email qua SNS và X-Ray trace trên tất cả Lambda function.

6. **Bảo vệ WAF** bao gồm IP reputation, OWASP Top 10, các pattern khai thác đã biết và giới hạn tốc độ theo IP.

#### Kết quả học tập

- Áp dụng AWS CDK TypeScript cho infrastructure-as-code trên 8 stack và khoảng 2.000 dòng định nghĩa CDK.
- Triển khai thiết kế DynamoDB single-table vớiláy access pattern dựa trên GSI và không có thao tác Scan.
- Xây dựng pipeline xử lý bất đồng bộ theo hướng sự kiện với EventBridge, SQS và Lambda.
- Áp dụng AWS Well-Architected Framework trên tất cả năm trụ cột trong triển khai thực tế.
- Hoàn thành ứng dụng fullstack serverless từ tài khoản AWS trống đến URL CloudFront có thể truy cập công khai.

#### Đánh giá theo AWS Well-Architected Framework

Hệ thống triển khai cuối cùng đạt được các tiêu chí sau theo AWS Well-Architected Framework:

| Trụ cột | Trạng thái |
|---------|-----------|
| Xuất sắc vận hành | ✅ IaC, structured logging, CloudWatch Dashboard, 6 alarm, X-Ray |
| Bảo mật | ✅ Cognito JWT, IAM quyền tối thiểu, WAF, S3 private, TLS 1.2+, không dùng wildcard |
| Độ tin cậy | ✅ DLQ, retry logic, PITR, idempotency, visibility timeout theo best practice SQS |
| Hiệu năng | ✅ ARM64, Node.js 22, esbuild, PAY_PER_REQUEST, CloudFront caching |
| Tối ưu chi phí | ✅ Zero idle cost, không có tài nguyên được cấp phát dư thừa, chính sách lưu trữ log |
| Bền vững | ✅ ARM64 tiết kiệm năng lượng, caching giảm tính toán, scale-to-zero |