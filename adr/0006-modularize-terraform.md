# ADR-0006: Mô-đun Hóa Mã Hạ Tầng Terraform

## Trạng Thái
Đã Chấp Nhận

## Bối Cảnh
Mã hạ tầng ban đầu được viết trong cấu trúc monolithic trong thư mục `azure-infra/` với tất cả tài nguyên được định nghĩa trong các file đơn:
- `main.tf` - Hạ tầng cốt lõi
- `database.tf` - Tài nguyên cơ sở dữ liệu
- `services.tf` - Container dịch vụ
- `messaging.tf` - Kafka/Event Hubs
- `container_infra.tf` - Hạ tầng container

**Vấn Đề:**
1. **Khả Năng Tái Sử Dụng**: Không thể tái sử dụng pattern hạ tầng giữa các môi trường
2. **Khả Năng Bảo Trì**: File lớn với các mối quan tâm hỗn hợp
3. **Kiểm Thử**: Khó kiểm thử các thành phần riêng lẻ
4. **Nhất Quán**: Rủi ro cấu hình không nhất quán giữa các môi trường
5. **Onboarding**: Thành viên nhóm mới gặp khó khăn với các file lớn, phức tạp

## Quyết Định
Chúng ta sẽ **mô-đun hóa** mã Terraform thành các module có thể tái sử dụng:

### Cấu Trúc Module
```
modules/
├── network/              # VNet, subnets, private endpoints
├── database/             # SQL Server, databases, private DNS
├── messaging/            # Event Hubs (Kafka), authorization rules
├── secrets/              # Key Vault, secrets management
├── container_registry/   # ACR setup
├── container_app_env/    # Container App Environment
├── service_container/   # Định nghĩa container dịch vụ có thể tái sử dụng
└── self_service/        # Module nền tảng self-service
```

### Nguyên Tắc Thiết Kế Module
1. **Trách Nhiệm Đơn**: Mỗi module xử lý một mối quan tâm hạ tầng
2. **Khả Năng Tái Sử Dụng**: Module có thể được sử dụng giữa các môi trường khác nhau (dev, staging, prod)
3. **Khả Năng Kết Hợp**: Module có thể được kết hợp để xây dựng hạ tầng hoàn chỉnh
4. **Input/Output**: Biến và output rõ ràng cho hợp đồng module
5. **Tài Liệu**: Mỗi module có tài liệu rõ ràng

### Ví Dụ Module

**Module Database:**
```terraform
module "database" {
  source = "./modules/database"
  
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  db_admin_username   = var.db_admin_username
  db_admin_password   = var.db_admin_password
  service_databases   = ["user-service-db", "driver-service-db", "trip-service-db"]
  db_subnet_id       = module.network.db_subnet_id
}
```

**Module Service Container:**
```terraform
module "services" {
  source = "./modules/service_container"
  
  services                    = local.services
  container_app_environment_id = module.container_env.id
  resource_group_name         = azurerm_resource_group.main.name
  acr_login_server           = module.acr.login_server
  identity_id                = azurerm_user_assigned_identity.aca_identity.id
  db_hostname                = module.database.hostname
  # ... other variables
}
```

## Chi Tiết Triển Khai

### Cấu Trúc Module
Mỗi module tuân theo cấu trúc Terraform chuẩn:
```
modules/{module_name}/
├── main.tf       # Định nghĩa tài nguyên
├── variables.tf  # Biến đầu vào
├── outputs.tf    # Giá trị đầu ra
└── README.md     # Tài liệu (tùy chọn)
```

### Sử Dụng Trong Cấu Hình Chính
```terraform
# azure-infra/main.tf hoặc provision/complete_demo/main.tf
module "network" {
  source = "../modules/network"
  # ... variables
}

module "database" {
  source = "../modules/database"
  # ... variables
  depends_on = [module.network]
}

module "services" {
  source = "../modules/service_container"
  # ... variables
  depends_on = [module.database, module.network]
}
```

### Lợi Ích
- **Khả Năng Tái Sử Dụng**: Cùng module được sử dụng trong `azure-infra/` và `provision/complete_demo/`
- **Nhất Quán**: Cùng pattern hạ tầng giữa các môi trường
- **Kiểm Thử**: Có thể kiểm thử module độc lập
- **Khả Năng Bảo Trì**: Thay đổi trong module lan truyền đến tất cả cách sử dụng

## Hệ Quả

### Tích Cực
- **Khả Năng Tái Sử Dụng**: Module có thể được tái sử dụng giữa các dự án và môi trường
- **Khả Năng Bảo Trì**: Module nhỏ hơn, tập trung dễ hiểu và sửa đổi hơn
- **Nhất Quán**: Pattern hạ tầng chuẩn hóa
- **Kiểm Thử**: Có thể kiểm thử module trong cô lập
- **Tài Liệu**: Hợp đồng module rõ ràng (variables/outputs)
- **Hợp Tác Nhóm**: Nhiều người có thể làm việc trên các module khác nhau
- **Phiên Bản**: Có thể phiên bản module độc lập

### Tiêu Cực
- **Độ Phức Tạp**: Nhiều file và cấu trúc hơn để điều hướng
- **Đường Cong Học Tập**: Nhóm cần hiểu cấu trúc module
- **Quản Lý Phụ Thuộc**: Cần quản lý phụ thuộc module cẩn thận
- **Tái Cấu Trúc**: Thay đổi interface module ảnh hưởng đến tất cả cách sử dụng

### Giảm Thiểu
- **Tài Liệu**: README rõ ràng cho mỗi module giải thích mục đích và cách sử dụng
- **Ví Dụ**: Cung cấp ví dụ sử dụng trong thư mục `modules/*/examples/`
- **Phiên Bản**: Sử dụng phiên bản module cho thay đổi breaking
- **Kiểm Thử**: Kiểm thử module trong cô lập trước khi tích hợp
- **Code Review**: Xem xét thay đổi module cẩn thận cho tính ổn định interface
- **Hướng Dẫn Migration**: Tài liệu migration từ cấu trúc monolithic sang modular

### Trạng Thái Hiện Tại
Codebase đã được mô-đun hóa với các module sau:
- `modules/network/` - Hạ tầng mạng
- `modules/database/` - Tài nguyên cơ sở dữ liệu
- `modules/messaging/` - Event Hubs/Kafka
- `modules/secrets/` - Key Vault
- `modules/container_registry/` - ACR
- `modules/container_app_env/` - Container App Environment
- `modules/service_container/` - Container dịch vụ có thể tái sử dụng
- `modules/self_service/` - Nền tảng self-service

ADR này tài liệu hóa quyết định và lý do cho cấu trúc mô-đun này.
