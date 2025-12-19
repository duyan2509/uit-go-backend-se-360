# ADR-0005: Tái Cấu Trúc Schema Redis cho Theo Dõi Vị Trí Tài Xế

## Trạng Thái
Đề Xuất

## Bối Cảnh
Schema Redis hiện tại cho theo dõi vị trí tài xế sử dụng cấu trúc phẳng:
- `drivers:geo:free` - GeoSet chứa tất cả tài xế rảnh với vị trí của họ
- `drivers:geo:intrip` - GeoSet chứa tất cả tài xế hiện đang trong chuyến đi với vị trí của họ

**Schema Hiện Tại:**
```
drivers:geo:free -> { driverId1: (lat, lng), driverId2: (lat, lng), ... }
drivers:geo:intrip -> { driverId1: (lat, lng), driverId2: (lat, lng), ... }
```

**Vấn Đề:**
1. **Liên Kết Chuyến Đi**: Không thể dễ dàng xác định tài xế đang ở chuyến đi nào
2. **Ngữ Cảnh Rider**: Không có cách trực tiếp để truy vấn "tài xế nào đang xử lý chuyến đi cho rider X"
3. **Khả Năng Mở Rộng**: Tất cả tài xế trong một set có thể trở thành nút thắt cổ chai
4. **Độ Phức Tạp Truy Vấn**: Cần duy trì các set riêng biệt và di chuyển tài xế giữa chúng

## Quyết Định
Chúng ta sẽ tái cấu trúc schema Redis để tập trung vào chuyến đi hơn và hỗ trợ truy vấn tốt hơn:

### Schema Mới

**Tài Xế Rảnh:**
```
driver:free:{driverId} -> (latitude, longitude)
```
- Key-value đơn giản với dữ liệu địa lý
- Dễ kiểm tra xem tài xế có rảnh không
- Có thể sử dụng lệnh Redis GEO trên các key riêng lẻ hoặc duy trì một GeoSet riêng

**Tài Xế Trong Chuyến Đi:**
```
driver:intrip:{riderId} -> (latitude, longitude)
```
- Được key bởi `riderId` thay vì `driverId`
- Cho phép tra cứu nhanh: "Tài xế nào đang xử lý chuyến đi của rider X?"
- Hỗ trợ một tài xế cho mỗi rider (một chuyến đi hoạt động cho mỗi rider)

**Cách Tiếp Cận Thay Thế (Được Khuyến Nghị):**
Duy trì cả hai cấu trúc để linh hoạt:

1. **GeoSet cho Truy Vấn Gần Đây:**
   ```
   drivers:geo:free -> { driverId1: (lat, lng), driverId2: (lat, lng), ... }
   driver:intrip:{riderId} -> driverId (Giá trị String)
   driver:free:{driverId} -> (lat, lng) (GeoHash hoặc entry GeoSet riêng)
   ```

2. **Liên Kết Chuyến Đi:**
   ```
   driver:intrip:{riderId} -> driverId
   trip:driver:{tripId} -> driverId
   ```

### Schema Cuối Cùng Đề Xuất

**Cho Tài Xế Rảnh:**
- `drivers:geo:free` (GeoSet) - Tất cả tài xế rảnh cho truy vấn gần đây
- `driver:free:{driverId}` (GeoHash) - Vị trí tài xế riêng lẻ (tùy chọn, cho tra cứu nhanh)

**Cho Tài Xế Trong Chuyến Đi:**
- `driver:intrip:{riderId}` (String) - Ánh xạ riderId sang driverId
- `driver:location:{driverId}` (GeoHash) - Vị trí hiện tại của tài xế (bất kể trạng thái)

**Cho Khóa Chuyến Đi:**
- `trip:lock:{tripId}` (String) - Giá trị khóa cho chấp nhận chuyến đi nguyên tử (hiện có)

## Chi Tiết Triển Khai

### Chiến Lược Migration
1. **Giai Đoạn 1**: Thêm các key mới cùng với các key hiện có (dual-write)
2. **Giai Đoạn 2**: Cập nhật tất cả dịch vụ để sử dụng schema mới
3. **Giai Đoạn 3**: Xóa schema cũ sau khi xác thực

### Pattern Key
```
# Tài xế rảnh (cho matching)
drivers:geo:free -> GeoSet[driverId -> (lat, lng)]

# Tra cứu tài xế trong chuyến đi
driver:intrip:{riderId} -> driverId (String)

# Vị trí tài xế (luôn được cập nhật)
driver:location:{driverId} -> (lat, lng) (GeoHash)

# Khóa chuyến đi (hiện có)
trip:lock:{tripId} -> lockValue (String)
```

### Thao Tác

**Khi Tài Xế Trở Thành Rảnh:**
1. Thêm vào GeoSet `drivers:geo:free`
2. Xóa khỏi `driver:intrip:{riderId}` (nếu tồn tại)
3. Cập nhật `driver:location:{driverId}`

**Khi Tài Xế Chấp Nhận Chuyến Đi:**
1. Xóa khỏi GeoSet `drivers:geo:free`
2. Đặt `driver:intrip:{riderId}` = `driverId`
3. Cập nhật `driver:location:{driverId}`

**Truy Vấn: "Tìm tài xế rảnh gần đây"**
- Sử dụng `GEORADIUS drivers:geo:free <lat> <lng> <radius>`

**Truy Vấn: "Tài xế nào đang xử lý chuyến đi của rider X?"**
- Sử dụng `GET driver:intrip:{riderId}`

## Hệ Quả

### Tích Cực
- **Liên Kết Chuyến Đi**: Dễ tìm tài xế cho một rider/chuyến đi cụ thể
- **Hiệu Quả Truy Vấn**: Tra cứu key trực tiếp cho liên kết chuyến đi (O(1))
- **Khả Năng Mở Rộng**: Có thể phân vùng theo riderId nếu cần
- **Linh Hoạt**: Hỗ trợ nhiều pattern truy vấn
- **Ngữ Nghĩa Rõ Ràng**: Schema phản ánh domain nghiệp vụ (tập trung vào chuyến đi)

### Tiêu Cực
- **Độ Phức Tạp Migration**: Cần migrate dữ liệu hiện có và cập nhật tất cả dịch vụ
- **Duy Trì Kép**: Có thể cần duy trì cả GeoSet và các key riêng lẻ
- **Overhead Lưu Trữ**: Nhiều key Redis hơn một chút (ảnh hưởng tối thiểu)
- **Nhất Quán**: Cần đảm bảo tất cả key được cập nhật nguyên tử

### Giảm Thiểu
- **Thao Tác Nguyên Tử**: Sử dụng giao dịch Redis (MULTI/EXEC) hoặc Lua scripts
- **Migration Dần Dần**: Triển khai dual-write, sau đó chuyển đổi
- **Giám Sát**: Thêm cảnh báo cho sự không nhất quán schema
- **Tài Liệu**: Tài liệu rõ ràng về pattern key và thao tác
- **Kiểm Thử**: Kiểm thử toàn diện cho tất cả thao tác Redis

### Dịch Vụ Bị Ảnh Hưởng
- **trip-service**: Cập nhật schema khi chuyến đi được chấp nhận
- **driver-service**: Cập nhật vị trí và trạng thái free/intrip
- **matching-service**: Truy vấn `drivers:geo:free` cho tìm kiếm gần đây
- **Tất cả dịch vụ**: Cần hiểu schema mới
