# ADR-0001: Kiến Trúc Microservices

## Trạng Thái
Đã Chấp Nhận

## Bối Cảnh
Hệ thống cần xử lý một nền tảng gọi xe với nhiều domain nghiệp vụ bao gồm quản lý người dùng, quản lý tài xế, quản lý chuyến đi, ghép nối, và thông báo. Hệ thống phải:
- **Khả năng mở rộng**: Xử lý lưu lượng cao, đặc biệt cho các thao tác ghép nối
- **Hiệu suất**: Độ trễ thấp cho các thao tác thời gian thực như ghép nối tài xế
- **Độ tin cậy**: Chịu lỗi với giao tiếp bất đồng bộ
- **Dễ bảo trì**: Tách biệt rõ ràng các mối quan tâm và triển khai độc lập

Hệ thống bao gồm nhiều bounded contexts:
- user-service: Quản lý người dùng (xác thực, phân quyền, hồ sơ người dùng)
- driver-service: Quản lý tài xế (hồ sơ tài xế, trạng thái làm việc, theo dõi vị trí)
- trip-service: Quản lý chuyến đi (vòng đời chuyến đi, tính cước)
- matching-service: Ghép nối (ghép nối tài xế-chuyến đi thời gian thực sử dụng truy vấn địa lý)
- notification-service: Thông báo (thông báo theo sự kiện đến ứng dụng di động)

## Quyết Định
Chúng ta sẽ áp dụng **Kiến Trúc Microservices** với các dịch vụ sau:

1. **User-service** (ASP.NET Core)
   - Quản lý tài khoản người dùng, xác thực, phân quyền
   - Xử lý yêu cầu đăng ký tài xế
   - Cơ sở dữ liệu: `user-service-db` (MSSQL)

2. **Driver-service** (ASP.NET Core)
   - Quản lý hồ sơ tài xế và trạng thái làm việc
   - Triển khai Event Sourcing cho các thay đổi trạng thái làm việc
   - Theo dõi vị trí tài xế trong Redis
   - Cơ sở dữ liệu: `driver-service-db` (MSSQL)

3. **Trip-service** (Spring Boot)
   - Quản lý toàn bộ vòng đời chuyến đi (PENDING → ACCEPTED → ONGOING → COMPLETED/CANCELLED)
   - Triển khai Event Sourcing cho các sự kiện chuyến đi
   - Xuất bản sự kiện lên Kafka
   - Cơ sở dữ liệu: `trip-service-db` (MSSQL)

4. **Matching-service** (Spring Boot)
   - Xử lý ghép nối tài xế-chuyến đi thời gian thực
   - Sử dụng truy vấn Redis Geospatial để tìm kiếm gần đây
   - Tiêu thụ sự kiện chuyến đi từ Kafka
   - Không có cơ sở dữ liệu (stateless)

5. **Notification-service** (Spring Boot)
   - Tiêu thụ sự kiện từ Kafka
   - Gửi thông báo đẩy qua FCM
   - Không có cơ sở dữ liệu (stateless)

6. **API Gateway** (Spring Cloud Gateway)
   - Điểm truy cập duy nhất cho tất cả yêu cầu bên ngoài
   - Xử lý định tuyến, xác thực, CORS
   - Định tuyến đến các microservices phù hợp

## Hệ Quả

### Tích Cực
- **Triển Khai Độc Lập**: Mỗi dịch vụ có thể được triển khai độc lập
- **Đa Dạng Công Nghệ**: Các dịch vụ có thể sử dụng các tech stack khác nhau (ASP.NET Core, Spring Boot)
- **Khả Năng Mở Rộng**: Các dịch vụ có thể mở rộng độc lập dựa trên tải (ví dụ: matching-service có thể mở rộng nhiều hơn)
- **Cô Lập Lỗi**: Lỗi trong một dịch vụ không lan truyền sang các dịch vụ khác
- **Tự Chủ Nhóm**: Các nhóm khác nhau có thể sở hữu các dịch vụ khác nhau

### Tiêu Cực
- **Độ Phức Tạp**: Nhiều thành phần hơn, lời gọi mạng, giao dịch phân tán
- **Nhất Quán Dữ Liệu**: Nhất quán cuối cùng giữa các dịch vụ
- **Chi Phí Vận Hành**: Nhiều dịch vụ hơn để giám sát, triển khai và bảo trì
- **Độ Trễ Mạng**: Giao tiếp giữa các dịch vụ tăng thêm độ trễ
- **Độ Phức Tạp Kiểm Thử**: Kiểm thử tích hợp phức tạp hơn

### Giảm Thiểu
- Sử dụng **Event Sourcing** và **Kafka** cho giao tiếp bất đồng bộ đáng tin cậy
- Triển khai **Circuit Breakers** và **Retry Policies** để tăng khả năng phục hồi
- Sử dụng **gRPC** cho giao tiếp giữa các dịch vụ độ trễ thấp
- Triển khai **giám sát** và **ghi log** toàn diện
- Sử dụng **API Gateway** để đơn giản hóa tương tác với client
