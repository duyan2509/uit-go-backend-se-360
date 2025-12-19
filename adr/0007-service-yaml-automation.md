# ADR-0007: Cấu Hình Dịch Vụ qua service.yml và Tự Động Hóa

## Trạng Thái
Đã Chấp Nhận

## Bối Cảnh
Hệ thống cần hỗ trợ onboarding dịch vụ mới dễ dàng và triển khai tự động. Trước đây, thêm một dịch vụ mới yêu cầu:
1. Thay đổi mã Terraform thủ công trong `azure-infra/services.tf`
2. Cập nhật thủ công GitHub Actions workflows
3. Cấu hình thủ công các cài đặt cụ thể cho dịch vụ (ports, databases, dependencies)
4. Rủi ro không nhất quán và lỗi con người

**Vấn Đề:**
1. **Cấu Hình Thủ Công**: Mỗi dịch vụ mới yêu cầu cập nhật Terraform thủ công
2. **Không Nhất Quán**: Các dịch vụ khác nhau có thể được cấu hình khác nhau
3. **Dễ Sai Sót**: Dễ mắc lỗi trong cấu hình
4. **Onboarding Chậm**: Mất thời gian để thêm dịch vụ mới
5. **Trùng Lặp Template**: Mã Terraform tương tự lặp lại cho mỗi dịch vụ

## Quyết Định
Chúng ta sẽ triển khai hệ thống **cấu hình dựa trên service.yml** với **cung cấp hạ tầng tự động**:

### File Cấu Hình Dịch Vụ
Mỗi dịch vụ có file `service.yml` trong thư mục gốc của nó:

```yaml
service:
  name: "my-service"
  display_name: "My Service"
  description: "Description of my service"
  version: "1.0.0"
  
  build:
    language: "maven"  # maven, dotnet, node, python, go
    dockerfile: "Dockerfile"
    
  runtime:
    port: 8080
    health_check_path: "/actuator/health"
    cpu: 0.5
    memory: "1.0Gi"
    min_replicas: 0
    max_replicas: 3
    
  networking:
    external: false
    cors_enabled: false
    
  dependencies:
    database: false
    kafka: false
    redis: false
    
  env:
    CUSTOM_VAR: "value"
    
  tags:
    team: "backend"
    environment: "dev"
```

### Thành Phần Tự Động Hóa

1. **GitHub Actions Workflows**
   - Tự động phát hiện file `service.yml`
   - Build và push Docker images
   - Triển khai lên Azure Container Apps
   - Cập nhật hạ tầng qua Terraform

2. **Tự Động Hóa Terraform**
   - Loại bỏ service template hardcode
   - Tạo cấu hình Terraform từ file `service.yml`
   - Sử dụng `for_each` để lặp qua các dịch vụ được phát hiện
   - Tạo tài nguyên động dựa trên cấu hình dịch vụ

3. **Phát Hiện Dịch Vụ**
   - Scripts quét file `service.yml`
   - Tạo registry/bản đồ dịch vụ
   - Truyền vào Terraform như biến

### Triển Khai

**Vị Trí Template Dịch Vụ:**
- Template: `.github/templates/service-template/service.yml`
- Dịch vụ mới sao chép template này và tùy chỉnh

**Tích Hợp Terraform:**
```terraform
# Đọc cấu hình dịch vụ
locals {
  services = {
    for service_file in fileset("${path.module}/../", "*/service.yml") :
    dirname(service_file) => yamldecode(file("${path.module}/../${service_file}")).service
  }
}

# Sử dụng trong module
module "services" {
  source = "./modules/service_container"
  
  services = {
    for name, config in local.services : name => {
      port     = config.runtime.port
      db_name  = config.dependencies.database ? "${name}-db" : null
      external = config.networking.external
      # ... map other fields
    }
  }
}
```

**GitHub Actions:**
- Workflow: `.github/workflows/deploy-service.yml`
- Kích hoạt khi service.yml thay đổi
- Xác thực cấu hình dịch vụ
- Build và triển khai dịch vụ
- Cập nhật hạ tầng nếu cần

## Hệ Quả

### Tích Cực
- **Onboarding Dễ Dàng**: Dịch vụ mới chỉ cần file `service.yml`
- **Nhất Quán**: Tất cả dịch vụ tuân theo cùng cấu trúc cấu hình
- **Tự Động Hóa**: Giảm công việc thủ công, triển khai nhanh hơn
- **Self-Service**: Các nhóm có thể thêm dịch vụ mà không cần nhóm hạ tầng
- **Kiểm Soát Phiên Bản**: Cấu hình dịch vụ được theo dõi trong git
- **Xác Thực**: Có thể xác thực service.yml trước khi triển khai
- **Tài Liệu**: service.yml phục vụ như tài liệu

### Tiêu Cực
- **Đường Cong Học Tập**: Nhóm cần hiểu định dạng service.yml
- **Xác Thực**: Cần xác thực mạnh mẽ file service.yml
- **Migration**: Dịch vụ hiện có cần được migrate sang service.yml
- **Độ Phức Tạp**: Tự động hóa thêm độ phức tạp vào pipeline CI/CD
- **Debugging**: Vấn đề trong tự động hóa có thể khó debug hơn

### Giảm Thiểu
- **Template**: Cung cấp template rõ ràng với ví dụ
- **Script Xác Thực**: `scripts/validate-service-config.sh` để xác thực service.yml
- **Tài Liệu**: Tài liệu rõ ràng về schema service.yml
- **Ví Dụ**: File service.yml ví dụ trong tài liệu
- **Thông Báo Lỗi**: Thông báo lỗi rõ ràng cho cấu hình không hợp lệ
- **Migration Dần Dần**: Migrate dịch vụ hiện có từng cái một
- **Kiểm Thử**: Kiểm thử tự động hóa với dịch vụ mẫu

### Triển Khai Hiện Tại
Hệ thống đã có:
- Template `service.yml` trong `.github/templates/service-template/`
- Dịch vụ ví dụ: `demo-service/service.yml`
- GitHub Actions workflows cho triển khai dịch vụ
- Scripts cho xác thực và cung cấp dịch vụ

### Cải Tiến Tương Lai
- **Service Registry**: Registry trung tâm của tất cả dịch vụ
- **Dependency Graph**: Trực quan hóa phụ thuộc dịch vụ
- **Giám Sát Sức Khỏe**: Cấu hình health check tự động
- **Theo Dõi Chi Phí**: Phân bổ chi phí theo dịch vụ
- **Tự Động Mở Rộng**: Mở rộng động dựa trên cấu hình service.yml
