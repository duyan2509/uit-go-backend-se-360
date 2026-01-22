# Architecture Decision Records (ADRs)

Thư mục này chứa các Architecture Decision Records (ADRs) cho hệ thống UIT Go Backend. ADRs tài liệu hóa các quyết định kiến trúc quan trọng, bối cảnh và hệ quả của chúng.

## ADR là gì?

Architecture Decision Records là các tài liệu ghi lại các quyết định kiến trúc quan trọng cùng với bối cảnh và hệ quả của chúng. Chúng giúp:
- Tài liệu hóa lý do quyết định được đưa ra
- Cung cấp ngữ cảnh cho các nhà phát triển tương lai
- Theo dõi sự tiến hóa của kiến trúc hệ thống
- Tránh xem xét lại các quyết định đã được đưa ra

## Mục Lục ADR

| ADR | Tiêu Đề | Trạng Thái |
|-----|---------|------------|
| [0001](0001-architect.md) | Kiến Trúc Microservices | Đã Chấp Nhận |
| [0002](0002-grpc-rest.md) | gRPC cho Giao Tiếp Nội Bộ, REST cho API Bên Ngoài | Đã Chấp Nhận |
| [0003](0003-db-per-service.md) | Mẫu Database per Service | Đã Chấp Nhận |
| [0004](0004-move-catch-trip-to-trip-service.md) | Di Chuyển Chức Năng Bắt Chuyến từ Driver sang Trip Service (DDD) | Đã Chấp Nhận |
| [0005](0005-redis-schema-refactor.md) | Tái Cấu Trúc Schema Redis cho Theo Dõi Vị Trí Tài Xế | Đề Xuất |
| [0006](0006-modularize-terraform.md) | Mô-đun Hóa Mã Hạ Tầng Terraform | Đã Chấp Nhận |
| [0007](0007-service-yaml-automation.md) | Cấu Hình Dịch Vụ qua service.yml và Tự Động Hóa | Đã Chấp Nhận |
| [0008](0008-azure-simulation.md) | Mô Phỏng Chi Phí Azure Bằng Chạy Local và Bảng Giá | Đề Xuất |

## Trạng Thái ADR

- **Đã Chấp Nhận**: Quyết định đã được đưa ra và triển khai
- **Đề Xuất**: Quyết định đang được xem xét
- **Lỗi Thời**: Quyết định đã bị thay thế
- **Từ Chối**: Quyết định đã được xem xét nhưng không được áp dụng

## Cách Tạo ADR Mới

1. Tạo file mới: `adr/XXXX-tieu-de-mo-ta.md` (trong đó XXXX là số tiếp theo)
2. Sử dụng template sau:

```markdown
# ADR-XXXX: Tiêu Đề

## Trạng Thái
Đề Xuất

## Bối Cảnh
[Mô tả bối cảnh và vấn đề]

## Quyết Định
[Mô tả quyết định]

## Hệ Quả

### Tích Cực
[Hệ quả tích cực]

### Tiêu Cực
[Hệ quả tiêu cực]

### Giảm Thiểu
[Cách giảm thiểu hệ quả tiêu cực]
```

3. Cập nhật README này với entry ADR mới
4. Gửi để xem xét

## Tài Liệu Liên Quan

- [Tổng Quan Kiến Trúc](../ARCHITECTURE.md)
- [Hướng Dẫn Nền Tảng Self-Service](../docs/SELF_SERVICE_GUIDE.md)
- [Terraform Modules](../modules/README.md)
