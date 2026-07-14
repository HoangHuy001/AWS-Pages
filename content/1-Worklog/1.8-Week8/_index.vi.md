---
title: "Worklog Tuần 8"
date: 2026-06-08
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---


### Mục tiêu Tuần 8:

* Nghiên cứu và thực hành xây dựng ứng dụng dựa trên kiến trúc không máy chủ (Serverless Architecture) hoàn chỉnh.
* Tìm hiểu giải pháp quản lý danh tính người dùng và tăng tốc phân phối nội dung toàn cầu trên AWS.

### Công việc thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Nghiên cứu chuyên sâu về AWS Lambda: <br>&emsp; + Cơ chế hoạt động và Trigger <br>&emsp; + Tìm hiểu về Lambda Layers <br>&emsp; + Cách tối ưu hóa thời gian Cold Start | 08/06/2026  | 08/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu dịch vụ Amazon API Gateway: <br>&emsp; + Cấu hình REST API và HTTP API <br>&emsp; + Thiết lập cơ chế kiểm soát truy cập (Authorizers) <br>&emsp; + Cấu hình giới hạn lưu lượng (Throttling) | 09/06/2026 | 09/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Nghiên cứu dịch vụ quản lý định danh Amazon Cognito: <br>&emsp; + Tìm hiểu và cấu hình User Pools <br>&emsp; + Tìm hiểu và cấu hình Identity Pools để xác thực người dùng | 10/06/2026 | 10/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu mạng lưới phân phối nội dung Amazon CloudFront (CDN): <br>&emsp; + Cơ chế lưu bộ nhớ đệm tại các Edge Location <br>&emsp; + Tích hợp chứng chỉ bảo mật với AWS Certificate Manager (ACM) | 11/06/2026 | 11/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Thực hành tổng hợp: <br>&emsp; + Triển khai một hệ thống Serverless hoàn chỉnh <br>&emsp; + Sử dụng API Gateway tiếp nhận request và kích hoạt Lambda xử lý dữ liệu <br>&emsp; + Lưu trữ vào DynamoDB và bảo mật ứng dụng bằng Cognito | 12/06/2026 | 12/06/2026      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được trong Tuần 8:

Hiểu rõ tư duy thiết kế hệ thống Serverless, biết cách viết và đóng gói mã nguồn tối ưu cho các hàm AWS Lambda xử lý logic nghiệp vụ.

Cấu hình thành công hệ thống API Gateway làm cổng kết nối bảo mật, điều hướng dữ liệu mượt mà từ ứng dụng client xuống các dịch vụ backend.

Làm chủ dịch vụ Amazon Cognito, giải quyết được bài toán đăng ký, đăng nhập và bảo mật phân quyền đa lớp cho người dùng cuối một cách an toàn.

Hiểu rõ cơ chế hoạt động của Amazon CloudFront giúp tối ưu hóa tốc độ tải trang, giảm độ trễ (latency) cho ứng dụng web khi phân phối ra Internet toàn cầu.


