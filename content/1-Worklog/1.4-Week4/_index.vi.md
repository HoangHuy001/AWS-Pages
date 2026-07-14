---
title: "Worklog Tuần 4"
date: 2026-05-11
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---



### Mục tiêu Tuần 4:

* Hiểu cách xây dựng kiến trúc hệ thống có tính sẵn sàng cao (High Availability), khả năng chịu lỗi và tự động co giãn trên AWS.
* Tìm hiểu giải pháp giám sát hiệu năng hạ tầng và theo dõi mức độ tiêu thụ tài nguyên của ứng dụng.

### Công việc thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Nghiên cứu khái niệm High Availability & Scalability (Mở rộng theo chiều dọc và chiều ngang) <br> - Tìm hiểu hệ thống phân giải tên miền Amazon Route 53 và các chính sách định tuyến tên miền | 11/05/2026  | 11/05/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Nghiên cứu dịch vụ cân bằng tải Elastic Load Balancing (ELB): <br>&emsp; + Phân biệt ALB, NLB và CLB <br>&emsp; + Cơ chế định tuyến lưu lượng dựa trên HTTP/HTTPS | 12/05/2026 | 12/05/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu dịch vụ tự động co giãn hệ thống AWS Auto Scaling: <br>&emsp; + Cấu hình Launch Templates <br>&emsp; + Thiết lập các chính sách co giãn (Scaling Policies) | 13/05/2026 | 13/05/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Nghiên cứu dịch vụ giám sát hệ thống Amazon CloudWatch: <br>&emsp; + Cách theo dõi chỉ số (Metrics) <br>&emsp; + Cấu hình bộ ghi nhật ký (Logs) và bộ lọc | 14/05/2026 | 14/05/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Thực hành tổng hợp: <br>&emsp; + Tạo một Auto Scaling Group kết hợp với Application Load Balancer (ALB) <br>&emsp; + Giả lập quá tải CPU để kiểm tra tính năng tự co giãn <br>&emsp; + Thiết lập CloudWatch Alarms gửi cảnh báo | 15/05/2026 | 15/05/2026      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được trong Tuần 4:

Nắm vững các nguyên lý thiết kế hệ thống phân tán chống chịu thảm họa và cơ chế định tuyến tên miền toàn cầu với Amazon Route 53.

Hiểu rõ cách dịch vụ Elastic Load Balancing phân phối lưu lượng truy cập một cách thông minh đến các cụm máy chủ EC2 phía sau để tối ưu hiệu năng.

Triển khai thành công hệ thống tự động co giãn AWS Auto Scaling giúp tự động thêm/bớt máy chủ linh hoạt theo nhu cầu thực tế của người dùng nhằm tối ưu chi phí hạ tầng.

Thành thạo việc giám sát tài nguyên bằng Amazon CloudWatch, biết cách thiết lập Dashboard quản lý tập trung và cấu hình gửi cảnh báo tự động qua email (SNS) khi hệ thống gặp sự cố.


