---
title: "Worklog Tuần 11"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---


### Mục tiêu Tuần 11:

* Phân tích yêu cầu, thiết kế kiến trúc tổng thể cho dự án thực tập tốt nghiệp áp dụng các dịch vụ đã học trên AWS.
* Triển khai xây dựng hạ tầng mạng bảo mật và cấu hình hệ thống lưu trữ, cơ sở dữ liệu nền tảng cho ứng dụng.

### Công việc thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Nhận đề tài và phân tích yêu cầu bài toán dự án cuối khóa <br> - Khảo sát và lựa chọn mô hình kiến trúc phù hợp (Microservices/Serverless) <br> - Vẽ sơ đồ kiến trúc hệ thống tổng thể trên AWS | 29/06/2026  | 29/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Sử dụng AWS CDK/CloudFormation để khởi tạo hạ tầng mạng: <br>&emsp; + Thiết lập Amazon VPC đa vùng (Multi-AZ) <br>&emsp; + Cấu hình hệ thống Public/Private Subnets <br>&emsp; + Thiết lập NAT Gateway và Security Groups | 30/06/2026 | 30/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Triển khai phân lớp lưu trữ và tầng dữ liệu: <br>&emsp; + Khởi tạo cụm cơ sở dữ liệu quan hệ Amazon RDS (Multi-AZ) <br>&emsp; + Cấu hình Amazon DynamoDB cho các dữ liệu phi cấu trúc <br>&emsp; + Tạo S3 Buckets lưu trữ media | 01/07/2026 | 01/07/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Triển khai tầng ứng dụng và dịch vụ xử lý logic: <br>&emsp; + Cấu hình Amazon ECS (Fargate) để chạy các dịch vụ container hóa <br>&emsp; + Viết và cấu hình các hàm AWS Lambda xử lý tác vụ bất đồng bộ | 02/07/2026 | 02/07/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Thực hành tổng hợp và kiểm tra: <br>&emsp; + Kiểm tra kết nối phân tầng giữa ứng dụng (ECS/Lambda) xuống Database (RDS/DynamoDB) <br>&emsp; + Cấu hình và kiểm tra quyền truy cập IAM Role cho các tài nguyên dự án | 03/07/2026 | 03/07/2026      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được trong Tuần 11:

Hoàn thành bản thiết kế kiến trúc hệ thống tổng thể cho dự án cuối khóa, đảm bảo tính tối ưu theo khung chuẩn AWS Well-Architected.

Triển khai thành công toàn bộ hạ tầng mạng biệt lập và bảo mật, phân tách rõ ràng luồng dữ liệu public và private.

Khởi tạo và kết nối thành công các dịch vụ cơ sở dữ liệu nền tảng (RDS, DynamoDB) và lưu trữ dữ liệu (S3), sẵn sàng cho ứng dụng kết nối.

Vận hành thử nghiệm thành công các container ứng dụng trên ECS Fargate và các hàm xử lý logic Lambda trong môi trường mạng nội bộ an toàn.

