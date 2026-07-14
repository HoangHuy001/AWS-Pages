---
title: "Worklog Tuần 10"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---



### Mục tiêu Tuần 10:

* Nghiên cứu và ứng dụng phương pháp Quản lý hạ tầng bằng mã nguồn (Infrastructure as Code - IaC) để tự động hóa việc khởi tạo tài nguyên.
* Tìm hiểu giải pháp giám sát an ninh hệ thống, phát hiện lỗ hổng và quản lý tuân thủ tiêu chuẩn bảo mật trên AWS.

### Công việc thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu dịch vụ Quản lý hạ tầng bằng mã nguồn AWS CloudFormation: <br>&emsp; + Khái niệm về Templates, Stacks và Change Sets <br>&emsp; + Cách viết cấu trúc file cấu hình bằng định dạng YAML/JSON | 22/06/2026  | 22/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Nghiên cứu bộ công cụ phát triển đám mây AWS Cloud Development Kit (CDK): <br>&emsp; + Khái niệm về Constructs và Stacks trong CDK <br>&emsp; + Cách sử dụng ngôn ngữ lập trình (TypeScript/Python) để định nghĩa hạ tầng | 23/06/2026 | 23/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu các dịch vụ giám sát an ninh và phát hiện mối đe dọa tự động: <br>&emsp; + Dịch vụ phân tích nhật ký hành vi và phát hiện mã độc Amazon GuardDuty <br>&emsp; + Dịch vụ tự động quét lỗ hổng bảo mật Amazon Inspector | 24/06/2026 | 24/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Nghiên cứu giải pháp quản lý cấu hình và đánh giá mức độ tuân thủ: <br>&emsp; + Dịch vụ ghi vết lịch sử thay đổi tài nguyên AWS Config <br>&emsp; + Thiết lập các quy tắc (AWS Config Rules) để tự động hóa kiểm tra an toàn hệ thống | 25/06/2026 | 25/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Thực hành tổng hợp: <br>&emsp; + Viết một kịch bản AWS CDK để tự động khởi tạo một mô hình mạng VPC và cụm máy chủ EC2 hoàn chỉnh <br>&emsp; + Cấu hình kích hoạt Amazon GuardDuty để giám sát các hành vi truy cập bất thường vào hệ thống vừa tạo | 26/06/2026 | 26/06/2026      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được trong Tuần 10:

Hiểu rõ tư duy tự động hóa hạ tầng (IaC), có khả năng xây dựng và tái sử dụng các bản thiết kế hệ thống thông qua AWS CloudFormation.

Thành thạo việc sử dụng AWS CDK để lập trình và khởi tạo tài nguyên Cloud bằng ngôn ngữ lập trình quen thuộc, giúp tối ưu hóa tốc độ triển khai dự án.

Nắm vững cơ chế hoạt động của Amazon GuardDuty và Amazon Inspector trong việc chủ động quét lỗ hổng, phát hiện sớm các nguy cơ tấn công mạng.

Làm chủ dịch vụ AWS Config giúp kiểm soát toàn bộ lịch sử thay đổi cấu hình tài nguyên, đảm bảo hệ thống luôn tuân thủ các quy chuẩn bảo mật nghiêm ngặt.