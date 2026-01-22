# Tổng quan : Azure Cost Management (tương đương AWS CE/Budgets)

## Mục tiêu
- **Thu thập và phân tích metrics sử dụng thực tế**: Sử dụng công cụ như Docker stats hoặc Azure Monitor để ghi nhận dữ liệu tài nguyên (vCPU-seconds, Memory GB-seconds) cho từng microservices (UserService, TripService, DriverService, api-gateway, matching-service, notification-service), nhằm phản ánh chính xác tình trạng active/idle và tránh over-provisioning.
- **Áp dụng bảng giá Azure để mô phỏng chi phí**: Nhân metrics thu thập được với đơn giá cập nhật từ Azure Pricing Calculator (region East US, năm 2025), bao gồm các yếu tố điều chỉnh như free grants (dành cho Azure Container Apps - ACA), active/idle rate, Spot discount, và overhead (control plane, node), để tính toán chi phí chính xác mà không cần deploy thực tế.
- **So sánh các giải pháp Azure**: Đánh giá chi phí giữa các dịch vụ như Azure Container Apps (ACA), Azure Container Instances (ACI), Azure Kubernetes Service (AKS) với Spot và On-Demand, hỗ trợ quyết định kiến trúc tối ưu (ví dụ: sử dụng ACA cho toàn hệ thống ở giai đoạn prototype đến scale trung bình, chuyển sang AKS cho service matching và notification khi quy mô lớn).
- **Tối ưu hóa chi phí và linh hoạt**: Giúp tiết kiệm thời gian, chi phí thử nghiệm bằng cách chạy local hoặc môi trường phát triển, đồng thời dự báo chi phí monthly dựa trên hệ số peak/off-peak (ví dụ: nhân với 720 giờ/tháng), phù hợp cho nghiên cứu FinOps và phát triển MVP.

## Vai trò của các folder (folder `cost-optimization-simulation`)
- `comparison`: So sánh chi phí và báo cáo
- `cost-calculator`: Công cụ tính toán chi phí cốt lõi
- `demo`: Bộ điều phối tự động hóa toàn bộ quy trình
- `metrics-collector`: Thu thập metrics từ Docker
- `workload-runner`: Chạy kiểm tra tải (load testing)
- `results`: Thư mục lưu kết quả đầu ra
