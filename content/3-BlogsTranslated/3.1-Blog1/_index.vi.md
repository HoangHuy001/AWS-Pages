---
title: "Blog 1"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# Xác thực kép (Dual-token Authentication) cho Nakama Game Server với Amazon Cognito trên AWS

Trong các hệ thống game trực tuyến hiện đại, việc quản lý **định danh người chơi (Identity)** và **phiên làm việc trong game (Game Session)** thường được xử lý bởi hai cơ chế khác nhau. Nếu không được thiết kế hợp lý, người chơi có thể phải xác thực nhiều lần hoặc gặp gián đoạn khi chuyển giữa các dịch vụ.

Để giải quyết vấn đề này, AWS giới thiệu mô hình **Dual-token Authentication**, kết hợp **Amazon Cognito** và **Nakama Game Server** nhằm xây dựng quy trình xác thực an toàn, đồng thời vẫn đảm bảo trải nghiệm đăng nhập liền mạch cho người chơi.

Giải pháp sử dụng **Amazon Cognito** để quản lý danh tính người dùng và **Nakama** để quản lý phiên chơi. Một **Go Runtime Hook** sẽ xác thực JWT do Cognito cấp, sau đó tạo Session Token cho Nakama để người chơi có thể sử dụng trong suốt quá trình kết nối.

---

## Tổng quan kiến trúc

Kiến trúc được chia thành nhiều lớp nhằm tách biệt quá trình xác thực, định tuyến lưu lượng và xử lý kết nối thời gian thực.

> *Hình 1. Kiến trúc Dual-token Authentication trên AWS.*

Luồng xử lý hoạt động như sau:

- Người chơi đăng nhập thông qua **Amazon Cognito** và nhận JWT Access Token.
- Toàn bộ lưu lượng đi qua **Amazon CloudFront**.
- **AWS WAF** kiểm tra và lọc các request trước khi chuyển đến backend.
- Các HTTP API được chuyển đến **Application Load Balancer (ALB)**.
- Các kết nối **WebSocket** được chuyển trực tiếp đến **Network Load Balancer (NLB)**.
- Nakama chạy trên **Amazon ECS (AWS Fargate)** xác thực JWT và tạo Session Token.
- Sau khi xác thực thành công, Nakama sử dụng Session Token để quản lý toàn bộ phiên chơi.

---

## Các thành phần chính

### Amazon Cognito

Amazon Cognito chịu trách nhiệm quản lý người dùng và xác thực đăng nhập.

Sau khi người chơi đăng nhập thành công bằng cơ chế **USER_SRP_AUTH**, Cognito sẽ phát hành **JWT Access Token** mà không cần gửi mật khẩu lên máy chủ.

---

### Go Runtime Hook trên Nakama

Go Runtime Hook đóng vai trò cầu nối giữa Cognito và Nakama.

Chức năng chính:

- Kiểm tra tính hợp lệ của JWT.
- Trích xuất thông tin người dùng (sub claim).
- Tạo Session Token tương ứng trong Nakama.
- Chuyển quyền quản lý phiên chơi sang Nakama.

Nhờ đó, Cognito chỉ quản lý danh tính, trong khi Nakama tập trung xử lý gameplay và các kết nối thời gian thực.

---

### Amazon CloudFront

CloudFront đóng vai trò là điểm truy cập HTTPS duy nhất của hệ thống.

CloudFront tự động phân loại lưu lượng:

- HTTP API → Application Load Balancer.
- WebSocket → Network Load Balancer.

Điều này giúp người dùng chỉ cần truy cập một endpoint duy nhất nhưng vẫn đảm bảo mỗi loại kết nối được xử lý đúng cách.

---

### Application Load Balancer (ALB)

ALB xử lý toàn bộ các HTTP API của hệ thống.

Vai trò chính:

- Định tuyến request theo URL.
- Chỉ cho phép các endpoint được cấu hình.
- Tăng cường bảo mật bằng cách giới hạn các route được phép truy cập.

---

### Network Load Balancer (NLB)

NLB được sử dụng cho các kết nối WebSocket.

Không giống ALB, NLB hoạt động ở tầng TCP nên:

- Không can thiệp vào dữ liệu truyền tải.
- Giữ nguyên kết nối thời gian thực.
- Giảm độ trễ trong quá trình chơi game.

Nakama sẽ trực tiếp xác thực Session Token khi người chơi thiết lập kết nối WebSocket.

---

### Amazon ECS trên AWS Fargate

Máy chủ Nakama được triển khai trên Amazon ECS sử dụng AWS Fargate.

Việc sử dụng Fargate giúp:

- Không cần quản lý máy chủ.
- Dễ dàng mở rộng khi số lượng người chơi tăng.
- Tự động triển khai và vận hành container.

---

## Tại sao cần cả ALB và NLB?

Trong kiến trúc này, AWS sử dụng đồng thời hai Load Balancer vì mỗi loại phục vụ một mục đích khác nhau.

| Thành phần | Chức năng |
|------------|-----------|
| Application Load Balancer | Xử lý HTTP API, kiểm tra route và áp dụng các chính sách truy cập |
| Network Load Balancer | Xử lý WebSocket bằng TCP passthrough với độ trễ thấp |

CloudFront sẽ tự động chuyển tiếp request đến đúng Load Balancer dựa trên loại kết nối.

---

## Lợi ích của Dual-token Authentication

Giải pháp mang lại nhiều ưu điểm cho các hệ thống game trực tuyến:

- Tách biệt giữa xác thực người dùng và quản lý phiên chơi.
- Tăng cường bảo mật với hai lớp token độc lập.
- Hỗ trợ WebSocket ổn định cho game thời gian thực.
- Dễ dàng mở rộng khi lượng người chơi tăng.
- Tận dụng các dịch vụ quản lý của AWS để giảm chi phí vận hành.

---

## Kết luận

Dual-token Authentication là một kiến trúc hiện đại giúp kết hợp **Amazon Cognito** và **Nakama** để xây dựng hệ thống xác thực an toàn và linh hoạt cho game online.

Việc sử dụng **Amazon CloudFront**, **AWS WAF**, **Application Load Balancer**, **Network Load Balancer** và **Amazon ECS trên AWS Fargate** giúp hệ thống vừa đảm bảo hiệu năng, vừa tăng cường khả năng bảo mật và mở rộng.

Đối với các nhà phát triển game multiplayer trên AWS, đây là một mô hình đáng tham khảo khi xây dựng hạ tầng xác thực và quản lý phiên chơi trong môi trường sản xuất.