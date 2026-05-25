# Mô phỏng hệ thống tách kênh FDM & Lọc thích nghi LMS

Đây là mã nguồn MATLAB cho Bài tập lớn môn **Xử lý tín hiệu số**, thực hiện mô phỏng hệ thống ghép/tách kênh phân chia theo tần số (FDM) trong môi trường nhiễu trắng (AWGN). Đồ án tập trung so sánh hiệu năng của các bộ lọc tĩnh kinh điển và đề xuất giải pháp tối ưu hóa bằng thuật toán tự học.

##  Nội dung thực hiện
1. **Khối phát (Transmitter):** Trộn 3 tín hiệu sóng mang (500 Hz, 1500 Hz, 3000 Hz) tạo thành tín hiệu FDM.
2. **Kênh truyền (Channel):** Cộng thêm nhiễu trắng ngẫu nhiên (AWGN).
3. **Khối thu (Receiver) - Trích xuất Kênh 2 (1500 Hz):**
   - Lọc tĩnh phương án 1: Bộ lọc IIR Butterworth (Bậc 6).
   - Lọc tĩnh phương án 2: Bộ lọc FIR Hamming (Bậc 100).
   - **Tối ưu hóa :** Cấu trúc Lọc thích nghi LMS (Bậc 50) với tín hiệu tham chiếu nội bộ.

##  Tóm tắt Kết quả (Hiệu năng SNR)
Thuật toán lọc thích nghi LMS cho thấy sự vượt trội hoàn toàn khi phá vỡ giới hạn dải thông cố định của bộ lọc tĩnh, nâng cao đáng kể chất lượng tín hiệu khôi phục:

| Cấu trúc bộ lọc | Bậc bộ lọc | SNR đạt được (dB) | Đánh giá |
| :--- | :---: | :---: | :--- |
| IIR Butterworth | 6 | 7.1 | Tối ưu tài nguyên, hiệu quả khá |
| FIR Hamming | 100 | 8.7 | Dải chắn sâu, tốn tài nguyên |
| **Thích nghi LMS** | **50** | **14.2** | **Hiệu năng bứt phá, khử nhiễu tối ưu** |
