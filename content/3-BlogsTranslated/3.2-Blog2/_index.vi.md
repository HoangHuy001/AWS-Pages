---
title: "Blog 2"
date: 2026-06-15
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Hạn chế truy cập AWS Management Console bằng Sign-in Resource-based Policies và Resource Control Policies (RCPs)

Việc bảo vệ quyền truy cập vào môi trường đám mây là một trong những yêu cầu quan trọng đối với mọi doanh nghiệp. Nhiều tổ chức cần đảm bảo rằng người dùng chỉ có thể đăng nhập vào **AWS Management Console** từ các mạng đáng tin cậy như mạng nội bộ, VPN của doanh nghiệp hoặc các Amazon VPC đã được cấu hình trước.

Để đáp ứng nhu cầu này, AWS đã giới thiệu **Sign-in Resource-based Policies** và **Resource Control Policies (RCPs)**. Hai tính năng này giúp kiểm soát quyền đăng nhập ngay từ bước xác thực, đồng thời cho phép quản lý chính sách tập trung trên nhiều tài khoản AWS.

---

## Tổng quan giải pháp

Giải pháp cho phép doanh nghiệp thiết lập các chính sách giới hạn nguồn truy cập khi người dùng đăng nhập vào AWS Management Console.

Thay vì cho phép truy cập từ bất kỳ vị trí nào, quản trị viên có thể chỉ định các địa chỉ IP, mạng VPN hoặc Amazon VPC được phép sử dụng. Nếu yêu cầu đăng nhập đến từ một mạng không nằm trong danh sách cho phép, AWS sẽ tự động từ chối trước khi người dùng có thể truy cập vào hệ thống.

Cách tiếp cận này giúp xây dựng một **network perimeter** vững chắc, đồng thời đáp ứng các yêu cầu về bảo mật và tuân thủ.

> *Hình 1. Kiến trúc kiểm soát đăng nhập AWS Management Console bằng Sign-in Resource-based Policies.*

---

## Các thành phần chính

### Sign-in Resource-based Policies

Sign-in Resource-based Policies giúp kiểm soát quyền đăng nhập dựa trên các điều kiện được định nghĩa trước.

Quản trị viên có thể cấu hình các điều kiện như:

- Địa chỉ IP của mạng nội bộ doanh nghiệp.
- Mạng VPN của công ty.
- Amazon VPC hoặc VPC Endpoint.
- Các tài khoản quản trị được miễn áp dụng chính sách.

Nhờ đó, chỉ những yêu cầu đăng nhập đáp ứng đầy đủ điều kiện mới được phép truy cập AWS Management Console.

---

### Resource Control Policies (RCPs)

Đối với các tổ chức sử dụng **AWS Organizations**, Resource Control Policies giúp áp dụng cùng một chính sách bảo mật cho nhiều tài khoản AWS.

Thay vì phải cấu hình riêng lẻ trên từng tài khoản, quản trị viên có thể quản lý tập trung, giúp đảm bảo tính nhất quán và giảm thiểu sai sót trong quá trình vận hành.

---

### AWS CloudTrail

AWS CloudTrail ghi lại toàn bộ các sự kiện đăng nhập, bao gồm cả những lần đăng nhập thành công và các yêu cầu bị từ chối.

Các nhật ký này hỗ trợ doanh nghiệp:

- Theo dõi hoạt động đăng nhập.
- Phát hiện các hành vi truy cập bất thường.
- Phục vụ điều tra sự cố bảo mật.
- Đáp ứng các yêu cầu kiểm toán và tuân thủ.

---

## Ví dụ sử dụng

Giả sử một tổ chức tài chính yêu cầu tăng cường bảo mật cho hệ thống AWS.

Doanh nghiệp đặt ra các yêu cầu như:

- Nhân viên chỉ được đăng nhập từ mạng văn phòng, VPN hoặc Amazon VPC đã được phê duyệt.
- Các yêu cầu từ Wi-Fi công cộng hoặc mạng cá nhân phải bị từ chối.
- Một tài khoản quản trị vẫn được phép truy cập trong mọi trường hợp để tránh bị khóa hệ thống.
- Mọi lần đăng nhập đều phải được ghi nhận để phục vụ kiểm toán.

Với Sign-in Resource-based Policies kết hợp cùng RCPs, các yêu cầu này có thể được triển khai và quản lý một cách hiệu quả.

---

## Lợi ích của giải pháp

Giải pháp mang lại nhiều lợi ích cho doanh nghiệp:

- Chỉ cho phép đăng nhập từ các mạng đáng tin cậy.
- Giảm nguy cơ truy cập trái phép vào AWS Management Console.
- Quản lý chính sách tập trung trên nhiều tài khoản AWS.
- Hỗ trợ kiểm toán và tuân thủ thông qua AWS CloudTrail.
- Xây dựng ranh giới bảo mật mạng (Network Perimeter) chặt chẽ hơn.

---

## Kết luận

Sign-in Resource-based Policies và Resource Control Policies bổ sung thêm một lớp bảo mật ngay trong quá trình đăng nhập vào AWS Management Console.

Kết hợp với **AWS Organizations** và **AWS CloudTrail**, giải pháp giúp doanh nghiệp kiểm soát nguồn truy cập, quản lý chính sách tập trung và tăng cường khả năng giám sát các hoạt động đăng nhập.

Đối với các tổ chức vận hành nhiều tài khoản AWS, đây là một giải pháp hiệu quả để nâng cao bảo mật, giảm rủi ro truy cập trái phép và đáp ứng các yêu cầu về quản trị cũng như tuân thủ.