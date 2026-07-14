---
title: "Worklog Tuần 7"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---



### Mục tiêu Tuần 7:

* Nghiên cứu và áp dụng mô hình kiến trúc hướng sự kiện (Event-Driven Architecture) trong việc liên kết các thành phần hệ thống.
* Tìm hiểu và thực hành các giải pháp bảo mật nâng cao bao gồm quản lý khóa mã hóa dữ liệu và tường lửa bảo vệ ứng dụng web trên AWS.

### Công việc thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu về kiến trúc hướng sự kiện (Event-Driven Architecture) <br> - Nghiên cứu dịch vụ Amazon SQS (Simple Queue Service): <br>&emsp; + Cơ chế hoạt động của Message Queue <br>&emsp; + Phân biệt Standard Queue và FIFO Queue | 01/06/2026  | 01/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Nghiên cứu dịch vụ Amazon SNS (Simple Notification Service): <br>&emsp; + Mô hình Pub/Sub (Publish/Subscribe) <br>&emsp; + Cách cấu hình Topics và Subscriptions <br> - Thực hành kết hợp SQS và SNS để tối ưu luồng xử lý | 02/06/2026 | 02/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Nghiên cứu giải pháp bảo mật và quản lý khóa mã hóa dữ liệu: <br>&emsp; + Dịch vụ AWS KMS (Key Management Service) <br>&emsp; + Phân biệt giữa Customer Managed Keys và AWS Managed Keys <br>&emsp; + Cơ chế mã hóa Envelope Encryption | 03/06/2026 | 03/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu giải pháp bảo vệ ứng dụng web nâng cao: <br>&emsp; + Dịch vụ tường lửa ứng dụng AWS WAF (Web Application Firewall) <br>&emsp; + Cách cấu hình Web ACLs, Rules và các bộ quy tắc bảo mật tự động (Managed Rules) | 04/06/2026 | 04/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Thực hành tổng hợp: <br>&emsp; + Cấu hình AWS WAF để ngăn chặn các cuộc tấn công phổ biến như SQL Injection, Cross-Site Scripting (XSS) <br>&emsp; + Thiết lập mã hóa dữ liệu lưu trữ trên Amazon S3 sử dụng khóa tạo từ AWS KMS | 05/06/2026 | 05/06/2026      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được trong Tuần 7:

Thấu hiểu tư duy thiết kế hệ thống bất đồng bộ (Asynchronous) thông qua việc sử dụng hàng đợi tin nhắn Amazon SQS và dịch vụ thông báo Amazon SNS.

Nắm vững các phương thức mã hóa dữ liệu cả khi lưu trữ (At Rest) lẫn khi truyền tải (In Transit) bằng cách làm chủ dịch vụ quản lý khóa AWS KMS.

Triển khai thành công lớp bảo mật tường lửa AWS WAF, bảo vệ tài nguyên ứng dụng web khỏi các lỗ hổng bảo mật nghiêm trọng trong danh mục OWASP Top 10.

Nâng cao khả năng phối hợp giữa các dịch vụ thông báo và lưu trữ để xây dựng một kiến trúc hạ tầng đám mây an toàn, bảo mật và có tính sẵn sàng cao.

