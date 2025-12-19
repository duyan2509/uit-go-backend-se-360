# ADR-0004: Di Chuyển Chức Năng Bắt Chuyến từ Driver sang Trip Service (DDD)

## Trạng Thái
Đã Chấp Nhận

## Bối Cảnh
Hệ thống cần xử lý chức năng "bắt chuyến" (accept trip) nơi tài xế chấp nhận yêu cầu chuyến đi. Ban đầu, chức năng này được coi là một phần của driver-service vì nó liên quan đến hành động của tài xế.

Tuy nhiên, từ góc độ **Domain-Driven Design (DDD)**:
- **Trip Aggregate**: Chuyến đi là aggregate trung tâm trong domain chuyến đi
- **Vòng Đời Chuyến Đi**: Chuyển đổi trạng thái chuyến đi (PENDING → ACCEPTED → ONGOING → COMPLETED) thuộc về domain chuyến đi
- **Quy Tắc Nghiệp Vụ**: Chấp nhận chuyến đi liên quan đến logic nghiệp vụ cụ thể của chuyến đi (cơ chế khóa, xác thực trạng thái, tính cước)
- **Nhất Quán Dữ Liệu**: Trạng thái chuyến đi phải được cập nhật nguyên tử với dữ liệu chuyến đi

Driver-service nên tập trung vào:
- Quản lý hồ sơ tài xế
- Trạng thái làm việc của tài xế (On/Off/InTrip)
- Theo dõi vị trí tài xế

Trip-service nên sở hữu:
- Toàn bộ vòng đời chuyến đi
- Chuyển đổi trạng thái chuyến đi
- Quy tắc nghiệp vụ chuyến đi

## Quyết Định
Chúng ta sẽ di chuyển chức năng **bắt chuyến (accept trip)** từ driver-service sang **trip-service**.

### Triển Khai Hiện Tại
Phương thức `acceptTrip` được triển khai trong `TripService` (trip-service) và xử lý:
1. **Khóa Nguyên Tử**: Sử dụng Redis `trip:lock:{tripId}` để ngăn race conditions
2. **Xác Thực Tài Xế**: Kiểm tra xem tài xế có sẵn sàng trong `drivers:geo:free`
3. **Chuyển Đổi Trạng Thái**: Di chuyển tài xế từ `drivers:geo:free` sang `drivers:geo:intrip`
4. **Cập Nhật Chuyến Đi**: Cập nhật trạng thái chuyến đi thành `ACCEPTED` và đặt `driverId`
5. **Thông Báo**: Gửi thông báo đến rider qua notification-service

### API Endpoint
- **Endpoint**: `PUT /api/trips/{id}/accept`
- **Service**: trip-service
- **Request Body**: `{ "driverId": "..." }`
- **Xác Thực**: Tài xế phải được xác thực (JWT token)

### Ranh Giới Domain

**Trip Service (Trip Aggregate)**
- Sở hữu logic chấp nhận chuyến đi
- Quản lý chuyển đổi trạng thái chuyến đi
- Phối hợp với driver-service (qua gRPC) để xác thực tài xế nếu cần
- Cập nhật Redis geospatial sets (theo dõi vị trí tài xế)

**Driver Service (Driver Aggregate)**
- Quản lý trạng thái làm việc của tài xế
- Theo dõi cập nhật vị trí tài xế
- KHÔNG xử lý chấp nhận chuyến đi trực tiếp
- Nhận sự kiện/thông báo về phân công chuyến đi

## Chi Tiết Triển Khai

### Luồng
1. Driver App → API Gateway → Trip-service: `PUT /api/trips/{tripId}/accept`
2. Trip-service xác thực tài xế (kiểm tra Redis để xem có sẵn sàng)
3. Trip-service lấy khóa: `trip:lock:{tripId}`
4. Trip-service cập nhật Redis: di chuyển tài xế từ `drivers:geo:free` sang `drivers:geo:intrip`
5. Trip-service cập nhật cơ sở dữ liệu: đặt `driverId` và trạng thái thành `ACCEPTED`
6. Trip-service xuất bản sự kiện lên Kafka (tùy chọn)
7. Trip-service gửi thông báo đến rider

### Vị Trí Code
- **Service**: `TripService.acceptTrip()` trong trip-service
- **Controller**: `TripController.acceptTrip()` trong trip-service
- **Thao Tác Redis**: Được xử lý trong trip-service (cập nhật geospatial set)

## Hệ Quả

### Tích Cực
- **Căn Chỉnh Domain**: Chấp nhận chuyến đi là một phần của domain chuyến đi, không phải domain tài xế
- **Nguồn Sự Thật Duy Nhất**: Trạng thái chuyến đi được quản lý ở một nơi (trip-service)
- **Nhất Quán**: Chuyển đổi trạng thái chuyến đi là nguyên tử trong trip aggregate
- **Logic Nghiệp Vụ**: Các quy tắc cụ thể của chuyến đi (cơ chế khóa, xác thực) được tập trung
- **Khả Năng Bảo Trì**: Quyền sở hữu rõ ràng của logic vòng đời chuyến đi
- **Khả Năng Kiểm Thử**: Dễ kiểm thử các kịch bản chấp nhận chuyến đi trong cô lập

### Tiêu Cực
- **Thao Tác Cross-Domain**: Trip-service cần cập nhật vị trí tài xế trong Redis
- **Coupling**: Trip-service phụ thuộc vào schema Redis cho vị trí tài xế
- **Phối Hợp**: Có thể cần phối hợp với driver-service cho các xác thực phức tạp

### Giảm Thiểu
- **Schema Redis Dùng Chung**: Định nghĩa các pattern key Redis rõ ràng (được tài liệu hóa trong ADR-0005)
- **Cập Nhật Theo Sự Kiện**: Driver-service có thể lắng nghe sự kiện chuyến đi để cập nhật trạng thái của nó
- **Lời Gọi gRPC**: Nếu cần, trip-service có thể gọi driver-service để xác thực
- **Hợp Đồng Rõ Ràng**: Tài liệu hóa schema Redis và hợp đồng giữa các dịch vụ
- **Event Sourcing**: Sử dụng sự kiện để duy trì tính nhất quán giữa các dịch vụ

### Cân Nhắc Tương Lai
- Cân nhắc di chuyển các thao tác Redis geospatial sang một dịch vụ dùng chung hoặc driver-service
- Sử dụng sự kiện để thông báo cho driver-service về phân công chuyến đi
- Triển khai CQRS nếu cần tách biệt đọc/ghi
