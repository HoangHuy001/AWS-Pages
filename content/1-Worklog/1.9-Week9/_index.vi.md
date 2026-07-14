---
title: "Worklog Tuần 9"
date: 2026-06-15
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---


### Mục tiêu Tuần 9:

* Nghiên cứu và thực hành triển khai các ứng dụng container hóa bằng các dịch vụ quản lý container chuyên nghiệp trên AWS.
* Tìm hiểu giải pháp tự động hóa quy trình tích hợp và triển khai liên tục (CI/CD) trên nền tảng đám mây.

### Công việc thực hiện trong tuần:
| Ngày | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu dịch vụ lưu trữ mã nguồn và quản lý container cơ bản: <br>&emsp; + Trình quản lý kho chứa tài nguyên Amazon Elastic Container Registry (ECR) <br>&emsp; + Cách đóng gói và đẩy Docker Image lên ECR | 15/06/2026  | 15/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Nghiên cứu dịch vụ điều phối container Amazon Elastic Container Service (ECS): <br>&emsp; + Khái niệm về Task Definitions, Tasks và Services <br>&emsp; + Phân biệt hai cơ chế chạy EC2 Launch Type và AWS Fargate | 16/06/2026 | 16/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu bộ công cụ lập trình và quản lý mã nguồn AWS Code Suite: <br>&emsp; + Dịch vụ lưu trữ mã nguồn git Amazon CodeCommit <br>&emsp; + Dịch vụ biên dịch và kiểm thử mã nguồn tự động AWS CodeBuild | 17/06/2026 | 17/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Nghiên cứu giải pháp triển khai và tự động hóa quy trình: <br>&emsp; + Dịch vụ hỗ trợ triển khai mã nguồn tự động AWS CodeDeploy <br>&emsp; + Tạo vòng đời quản lý quy trình kiểm thử với AWS CodePipeline | 18/06/2026 | 18/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Thực hành tổng hợp: <br>&emsp; + Xây dựng một quy trình CI/CD hoàn chỉnh (Pipeline) tự động hóa hoàn toàn <br>&emsp; + Tự động biên dịch mã nguồn từ CodeCommit, đóng gói thành Docker Image đẩy lên ECR <br>&emsp; + Tự động cập nhật phiên bản ứng dụng mới lên cụm máy chủ không máy chủ AWS Fargate (ECS) | 19/06/2026 | 19/06/2026      | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được trong Tuần 9:

Thành thạo việc đóng gói ứng dụng bằng Docker, biết cách quản lý và lưu trữ các phiên bản ảnh ứng dụng (Docker Images) an toàn trên Amazon ECR.

Hiểu rõ cơ chế điều phối và vận hành cụm container với Amazon ECS, tối ưu hóa được chi phí hạ tầng thông qua mô hình tính toán không máy chủ AWS Fargate.

Làm chủ bộ công cụ AWS Code Suite, tự xây dựng được tư duy chuẩn hóa quy trình phát triển phần mềm hiện đại trên môi trường Cloud.

Triển khai thành công hệ thống CI/CD tự động hóa từ đầu đến cuối, giúp tăng tốc độ phân phối ứng dụng, giảm thiểu tối đa các sai sót do thao tác thủ công.

