# Họ và tên: Nguyễn Công Nhật Tân
# MSSV: 2A202600141


# Báo cáo Thực hành LAB 16 (Phương án Dự phòng CPU)

**1. Giải thích lý do sử dụng CPU thay cho GPU:**
Do hạn chế từ chính sách đối với tài khoản mới hoặc Free Tier của GCP (mặc định quota GPU = 0), việc yêu cầu tăng quota thường mất thời gian xét duyệt hoặc dễ bị từ chối nếu tài khoản chưa có lịch sử thanh toán. Do đó, tôi đã lựa chọn phương án dự phòng sử dụng cấu hình CPU `n2-standard-8` (8 vCPU, 32GB RAM). Phương án này giúp tôi vẫn làm chủ được quy trình triển khai hạ tầng Infrastructure-as-Code qua Terraform, cũng như hoàn thành vòng lặp Machine Learning trên Cloud mà không bị gián đoạn.

**2. Đánh giá sơ bộ kết quả Benchmark:**
Với mô hình LightGBM chạy trên bộ dữ liệu `Credit Card Fraud Detection`, thời gian training diễn ra vô cùng nhanh (chỉ mất ~1.3 giây). Mặc dù F1-Score và Precision có vẻ thấp do đặc trưng mất cân bằng dữ liệu của bài toán (tỷ lệ gian lận cực nhỏ), nhưng AUC-ROC đạt mức rất khả quan (~0.906). Đặc biệt, về mặt inference, độ trễ (latency) trung bình rất thấp (~0.4ms/row) và throughput có thể xử lý hơn 1,26 triệu rows mỗi giây. Điều này chứng tỏ với các bài toán dữ liệu dạng bảng (tabular data), một CPU hiệu năng cao hoàn toàn có thể đáp ứng tốt yêu cầu Inference trong thực tế mà không cần dùng đến GPU giúp tiết kiệm được chi phí rất lớn.

**3. Bảng Kết quả Benchmark trên `n2-standard-8`:**

| Metric | Kết quả |
|---|---|
| **Thời gian load data** | 2.2143 giây |
| **Thời gian training** | 1.2951 giây |
| **Best iteration** | 28 |
| **AUC-ROC** | 0.906252 |
| **Accuracy** | 0.964555 |
| **F1-Score** | 0.075973 |
| **Precision** | 0.03977 |
| **Recall** | 0.846939 |
| **Inference latency (1 row)** | 0.4088 ms (mean) / 0.8705 ms (p99) |
| **Inference throughput** | ~1,260,999.3 rows/sec |
