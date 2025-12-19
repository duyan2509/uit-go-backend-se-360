# ADR-0003: Mẫu Database per Service

## Trạng Thái
Đã Chấp Nhận

## Bối Cảnh
Trong kiến trúc microservices, quyền sở hữu và cô lập dữ liệu là quan trọng. Hệ thống có nhiều dịch vụ với các domain dữ liệu riêng biệt:
- **User-service**: Tài khoản người dùng, dữ liệu xác thực, yêu cầu đăng ký tài xế
- **Driver-service**: Hồ sơ tài xế, sự kiện trạng thái làm việc
- **Trip-service**: Thực thể chuyến đi, sự kiện chuyến đi, tính toán cước

Yêu cầu:
- **Cô Lập Dữ Liệu**: Mỗi dịch vụ nên sở hữu dữ liệu của nó
- **Mở Rộng Độc Lập**: Mở rộng cơ sở dữ liệu nên độc lập theo từng dịch vụ
- **Linh Hoạt Công Nghệ**: Các dịch vụ nên chọn công nghệ cơ sở dữ liệu phù hợp
- **Nhất Quán Dữ Liệu**: Xử lý nhất quán cuối cùng giữa các dịch vụ
- **Hiệu Quả Chi Phí**: Tối ưu hóa chi phí cơ sở dữ liệu

## Quyết Định
Chúng ta sẽ triển khai mẫu **Database per Service**:

### Cơ Sở Dữ Liệu Dịch Vụ
Mỗi dịch vụ có cơ sở dữ liệu riêng trên Azure SQL Server:

1. **user-service-db** (MSSQL)
   - Bảng: `ApplicationUser`, `DriverRegister`
   - Sở hữu bởi: User-service
   - Truy cập: Chỉ user-service có thể truy cập trực tiếp cơ sở dữ liệu này

2. **driver-service-db** (MSSQL)
   - Bảng: `Driver`, `DriverWorkStatusEvent`
   - Sở hữu bởi: Driver-service
   - Truy cập: Chỉ driver-service có thể truy cập trực tiếp cơ sở dữ liệu này

3. **trip-service-db** (MSSQL)
   - Bảng: `trips`, `trip_events`
   - Sở hữu bởi: Trip-service
   - Truy cập: Chỉ trip-service có thể truy cập trực tiếp cơ sở dữ liệu này

### Hạ Tầng Dùng Chung
- Tất cả cơ sở dữ liệu chạy trên **cùng một instance Azure SQL Server** (logical server)
- Mỗi cơ sở dữ liệu được cô lập nhưng chia sẻ hạ tầng để tiết kiệm chi phí
- Private endpoints cho truy cập an toàn
- Connection strings cụ thể cho từng dịch vụ và được lưu trữ an toàn

### Dịch Vụ Stateless
- **Matching-service**: Không có cơ sở dữ liệu (sử dụng Redis cho truy vấn địa lý)
- **Notification-service**: Không có cơ sở dữ liệu (stateless event consumer)

## Chi Tiết Triển Khai

### Tạo Cơ Sở Dữ Liệu
```terraform
resource "azurerm_mssql_database" "service_dbs" {
  for_each = toset([
    "user-service-db",
    "driver-service-db", 
    "trip-service-db"
  ])
  
  name      = each.key
  server_id = azurerm_mssql_server.main.id
  sku_name  = "Basic"
}
```

### Connection Strings
Mỗi dịch vụ nhận connection string riêng qua biến môi trường:
- `DB_CONNECTION_STRING` chứa tên cơ sở dữ liệu cụ thể cho dịch vụ
- Credentials được lưu trong Azure Key Vault

### Chia Sẻ Dữ Liệu
- Các dịch vụ **không bao giờ** truy cập trực tiếp cơ sở dữ liệu của dịch vụ khác
- Chia sẻ dữ liệu qua:
  - **Lời gọi gRPC** cho truy vấn đồng bộ
  - **Sự kiện Kafka** cho truyền bá dữ liệu bất đồng bộ
  - **Event Sourcing** cho audit trails và nhất quán cuối cùng

## Hệ Quả

### Tích Cực
- **Quyền Sở Hữu Dữ Liệu**: Ranh giới quyền sở hữu rõ ràng, mỗi dịch vụ sở hữu dữ liệu của nó
- **Mở Rộng Độc Lập**: Có thể mở rộng cơ sở dữ liệu độc lập dựa trên tải
- **Linh Hoạt Công Nghệ**: Có thể chọn các loại cơ sở dữ liệu khác nhau cho mỗi dịch vụ (mặc dù hiện tại tất cả đều MSSQL)
- **Cô Lập Lỗi**: Vấn đề cơ sở dữ liệu trong một dịch vụ không ảnh hưởng đến các dịch vụ khác
- **Bảo Mật**: Giảm bề mặt tấn công, các dịch vụ không thể truy cập dữ liệu trái phép
- **Tự Chủ Nhóm**: Các nhóm có thể quản lý schema và migration cơ sở dữ liệu của riêng họ

### Tiêu Cực
- **Giao Dịch Phân Tán**: Không thể sử dụng giao dịch ACID giữa các dịch vụ
- **Nhất Quán Dữ Liệu**: Yêu cầu nhất quán cuối cùng, không có đảm bảo nhất quán ngay lập tức
- **Độ Phức Tạp Truy Vấn**: Không thể join dữ liệu giữa các dịch vụ trực tiếp
- **Chi Phí**: Nhiều cơ sở dữ liệu có thể tăng chi phí (giảm thiểu bằng server dùng chung)
- **Chi Phí Vận Hành**: Nhiều cơ sở dữ liệu hơn để quản lý, sao lưu và giám sát

### Giảm Thiểu
- Sử dụng **Event Sourcing** để duy trì audit trails và cho phép nhất quán cuối cùng
- Triển khai **Mẫu Saga** cho giao dịch phân tán (ví dụ: luồng tạo chuyến đi)
- Sử dụng **CQRS** khi phù hợp (read models có thể được denormalize)
- **Sự kiện Kafka** cho đồng bộ hóa dữ liệu giữa các dịch vụ
- **API composition** ở mức API Gateway cho truy vấn cross-service
- Triển khai **database connection pooling** để tối ưu hóa sử dụng tài nguyên
- Sử dụng **read replicas** cho các dịch vụ read-heavy nếu cần
