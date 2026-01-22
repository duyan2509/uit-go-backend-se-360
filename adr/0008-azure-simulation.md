# ADR-0008: Mô Phỏng Chi Phí Azure Bằng Chạy Local và Bảng Giá

## Trạng Thái
Đã Chấp Nhận

## Bối Cảnh
Trong quá trình đánh giá và so sánh chi phí triển khai hệ thống UIT Go Backend trên các dịch vụ Azure khác nhau (như Azure Container Apps, Azure Container Instances, Azure Kubernetes Service với Spot và On-Demand), việc triển khai thực tế trên từng resource có thể tốn kém về thời gian, chi phí và tài nguyên. Phần 5.2.3.5 trong báo cáo đã mô tả cách hiện thực hóa các giải pháp bằng cách thu thập metrics sử dụng thực tế và áp dụng bảng giá Azure để dự báo. Để tối ưu hóa quy trình này, chúng ta cần một phương pháp thay thế không yêu cầu triển khai trực tiếp trên Azure, mà vẫn có thể ước lượng chi phí chính xác dựa trên dữ liệu local.

**Vấn Đề:**
1. **Cấu Hình Thủ Công**: Mỗi dịch vụ mới yêu cầu cập nhật Terraform thủ công
2. **Không Nhất Quán**: Các dịch vụ khác nhau có thể được cấu hình khác nhau
3. **Dễ Sai Sót**: Dễ mắc lỗi trong cấu hình
4. **Onboarding Chậm**: Mất thời gian để thêm dịch vụ mới
5. **Trùng Lặp Template**: Mã Terraform tương tự lặp lại cho mỗi dịch vụ

## Quyết Định
Thay vì triển khai hệ thống trên các Azure resource khác nhau để kiểm tra chi phí, chúng ta sẽ:
1. Chạy hệ thống local (sử dụng Docker hoặc môi trường phát triển cục bộ).
2. Đo lường tài nguyên tiêu thụ (như vCPU-seconds, Memory GB-seconds) bằng công cụ như Docker stats hoặc script thu thập định kỳ.
3. Lấy bảng giá cập nhật của từng Azure resource từ Azure Pricing Calculator (ví dụ: giá per-second cho ACA, ACI, AKS).
4. Tính toán chi phí bằng cách nhân metrics đo được với đơn giá tương ứng, bao gồm các yếu tố như free grants, active/idle rate, Spot discount, và overhead (control plane, node).
5. Dự báo chi phí monthly bằng cách nhân với hệ số peak/off-peak và thời gian (ví dụ: 720 giờ/tháng).


## Hệ Quả

### Tích Cực
- **Tiết kiệm chi phí và thời gian**: Không cần tạo tài khoản Azure thử nghiệm hoặc chịu phí thực tế cho các deployment tạm thời.
- **Dễ dàng lặp lại**: Có thể chạy nhiều kịch bản workload khác nhau local mà không ảnh hưởng đến môi trường production.
- **Tăng tính linh hoạt**: Phù hợp cho giai đoạn prototype và nghiên cứu FinOps mà không cần cam kết resource đám mây.

### Tiêu Cực
- **Độ chính xác có thể thấp hơn**: Metrics local có thể không phản ánh đầy đủ overhead của môi trường Azure thực tế (như network latency hoặc scaling behavior).
- **Phụ thuộc vào workload giả lập**: Nếu dữ liệu đo không đại diện cho traffic thực (spiky như ứng dụng gọi xe), ước lượng có thể sai lệch.


### Giảm Thiểu
- **Sử dụng workload realistic**: Áp dụng công cụ như JMeter hoặc Locust để simulate traffic peak/off-peak giống thực tế.
- **So sánh với dữ liệu Azure Monitor**: Sau khi deploy MVP, validate metrics local bằng Azure Container Insights để điều chỉnh hệ số.
- **Cập nhật bảng giá định kỳ**: Sử dụng Azure Pricing API hoặc calculator để đảm bảo dữ liệu giá mới nhất (ví dụ: cập nhật 2025).
