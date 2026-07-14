---
title: "Nhật ký làm việc Tuần 12"
date: 2026-07-09
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu Tuần 12:

* Hoàn thiện các lớp bảo mật, tự động hóa quy trình CI/CD và cấu hình phân phối ứng dụng toàn cầu cho dự án cuối khóa.
* Đánh giá hiệu năng, dọn dẹp các tài nguyên thử nghiệm để tối ưu chi phí và hoàn thành báo cáo thực tập tổng kết.

### Công việc thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Tích hợp các dịch vụ bảo mật và phân phối cho dự án cuối khóa: <br>&emsp; + Cấu hình Amazon CloudFront kết nối với S3 Bucket để phân phối giao diện ứng dụng (Frontend) <br>&emsp; + Thiết lập AWS WAF bảo vệ Amazon API Gateway | 06/07/2026  | 06/07/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Xây dựng luồng tự động hóa hoàn chỉnh: <br>&emsp; + Thiết lập AWS CodePipeline để tự động cập nhật mã nguồn ứng dụng lên cụm Amazon ECS Fargate mỗi khi có thay đổi | 07/07/2026 | 07/07/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Nghiệm thu, kiểm thử tải và rà soát hệ thống: <br>&emsp; + Chạy thử nghiệm toàn bộ luồng hoạt động từ Client đến Backend và Database <br>&emsp; + Kiểm tra báo cáo bảo mật từ Amazon GuardDuty | 08/07/2026 | 08/07/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tối ưu hóa chi phí và dọn dẹp hạ tầng: <br>&emsp; + Hủy bỏ (Delete Stacks/Terminate) các tài nguyên thử nghiệm không còn sử dụng để tránh phát sinh chi phí ngoài ý muốn <br>&emsp; + Đóng gói toàn bộ mã nguồn dự án | 09/07/2026 | 09/07/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Hoàn thiện hồ sơ thực tập: <br>&emsp; + Tổng hợp số liệu, kết quả đạt được từ Tuần 1 đến Tuần 12 <br>&emsp; + Viết và hoàn thiện file Báo cáo thực tập tốt nghiệp gửi đơn vị và nhà trường | 10/07/2026 | 10/07/2026      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được trong Tuần 12:

Tích hợp thành công giải pháp phân phối nội dung toàn cầu Amazon CloudFront, tối ưu tốc độ tải trang frontend và bảo mật API Gateway toàn diện qua lớp tường lửa AWS WAF.

Vận hành trơn tru luồng CI/CD tự động (AWS CodePipeline), giúp các thay đổi về mã nguồn được kiểm thử và cập nhật tự động lên Amazon ECS mà không gây gián đoạn hệ thống.

Hoàn thành việc nghiệm thu dự án tổng hợp cuối khóa, đảm bảo hệ thống vận hành đúng nghiệp vụ yêu cầu và đạt các tiêu chuẩn bảo mật cơ bản.

Thực hiện rà soát và giải phóng các tài nguyên dư thừa một cách an toàn, kiểm soát tốt ngân sách AWS Budgets. Hoàn thiện toàn bộ nội dung tài liệu và file Báo cáo thực tập tốt nghiệp theo đúng tiến độ của nhà trường.