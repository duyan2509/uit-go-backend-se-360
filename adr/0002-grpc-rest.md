# ADR-0002: gRPC cho Giao Tiếp Nội Bộ, REST cho API Bên Ngoài

## Trạng Thái
Đã Chấp Nhận

## Bối Cảnh
Hệ thống cần hỗ trợ hai loại giao tiếp:
1. **API Bên Ngoài**: Ứng dụng di động (Rider/Driver) và web clients cần tương tác với hệ thống
2. **Giao Tiếp Nội Bộ**: Các microservices cần giao tiếp với nhau

Yêu cầu:
- **Độ Trễ Thấp**: Các lời gọi giữa các dịch vụ nội bộ phải nhanh
- **An Toàn Kiểu**: Hợp đồng mạnh giữa các dịch vụ
- **Hiệu Quả**: Tối thiểu hóa kích thước payload và overhead mạng
- **Tương Thích**: Clients bên ngoài cần API HTTP/REST chuẩn
- **Trải Nghiệm Nhà Phát Triển**: Dễ sử dụng và bảo trì

## Quyết Định
Chúng ta sẽ sử dụng **cách tiếp cận kết hợp**:

### REST cho Giao Tiếp Bên Ngoài
- **API Gateway** expose các REST endpoints (`/api/user/**`, `/api/trips/**`, `/api/drivers/**`)
- Tất cả clients bên ngoài (ứng dụng di động, web) giao tiếp qua REST
- HTTP/1.1 chuẩn với JSON payloads
- Hỗ trợ xác thực chuẩn (JWT tokens)
- Dễ kiểm thử với các công cụ HTTP chuẩn (curl, Postman)

### gRPC cho Giao Tiếp Nội Bộ
- Tất cả **giao tiếp giữa các dịch vụ** sử dụng gRPC
- Các dịch vụ expose gRPC endpoints trên các cổng riêng (ví dụ: driver-service:28082)
- Protocol Buffers (protobuf) cho serialization message
- HTTP/2 cho multiplexing và hiệu quả
- Hợp đồng kiểu mạnh qua các file `.proto`

**Ví dụ:**
- `user-service` gọi `driver-service` qua gRPC để lấy thông tin tài xế
- `trip-service` gọi `matching-service` qua gRPC để tìm tài xế gần đây
- `trip-service` gọi `notification-service` qua gRPC để gửi thông báo

## Chi Tiết Triển Khai

### Cấu Hình gRPC
- Mỗi dịch vụ expose gRPC trên một cổng riêng (ví dụ: `:28082` cho driver-service)
- REST endpoints vẫn ở các cổng chuẩn (ví dụ: `:8082` cho driver-service)
- gRPC clients được cấu hình với hỗ trợ HTTP/2

### Protocol Buffers
- Các file `.proto` dùng chung trong thư mục `proto-contracts/`
- Các dịch vụ tạo code client/server từ định nghĩa proto
- Hợp đồng có phiên bản để tương thích ngược

## Hệ Quả

### Tích Cực
- **Hiệu Suất**: gRPC sử dụng HTTP/2 multiplexing và protobuf nhị phân, giảm độ trễ
- **An Toàn Kiểu**: Kiểm tra kiểu tại thời điểm biên dịch qua protobuf
- **Hiệu Quả**: Kích thước payload nhỏ hơn so với JSON
- **Streaming**: gRPC hỗ trợ streaming hai chiều (hữu ích cho cập nhật thời gian thực)
- **REST Chuẩn**: Clients bên ngoài sử dụng REST API quen thuộc
- **Công Cụ**: Hỗ trợ công cụ tốt cho cả REST và gRPC

### Tiêu Cực
- **Độ Phức Tạp**: Cần duy trì cả REST và gRPC endpoints
- **Đường Cong Học Tập**: Nhóm cần hiểu gRPC và protobuf
- **Giới Hạn Trình Duyệt**: gRPC-web cần thiết cho browser clients (không cần cho ứng dụng di động)
- **Debugging**: gRPC yêu cầu các công cụ chuyên biệt (grpcurl) so với công cụ HTTP chuẩn

### Giảm Thiểu
- Sử dụng **API Gateway** để ẩn độ phức tạp gRPC nội bộ khỏi clients bên ngoài
- Duy trì **tài liệu rõ ràng** cho cả REST và gRPC APIs
- Sử dụng **hợp đồng proto dùng chung** để đảm bảo tính nhất quán
- Triển khai **gRPC health checks** cho service discovery
- Cung cấp **gRPC reflection** cho phát triển và debugging
