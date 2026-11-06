# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM

- **MSSV:** 2A202603746
- **Họ và tên:** Nguyen Van Hieu
- **SEED:** 1573

## 1. Bằng chứng

| Tệp | Trạng thái |
| --- | --- |
| [`day01_lab.ipynb`](day01_lab.ipynb) — notebook đã chạy, còn output | ✅ Có |
| [`lab1_report.json`](lab1_report.json) — do ô "Báo cáo cuối" sinh ra | ✅ Có |
| [`hand_calc.jpg`](hand_calc.jpg) — ảnh tính tay có các bước | ⚠️ Có ảnh, hiện mới có phép tính baseline accuracy; các phép tính còn lại cần bổ sung |

## 2. Kết quả chính

Số liệu dưới đây lấy từ kết quả đã lưu trong notebook. `USD/1k` là chi phí ước tính cho 1.000 tin; mô hình cục bộ không gọi API nên bằng 0.

| Cách tiếp cận | F1 test | F1 SUB (40 tin) | p95 (ms/tin) | USD/1k tin |
| --- | ---: | ---: | ---: | ---: |
| 1. BoW + Logistic Regression | 0.896 | 0.889 | 0.03 | 0 |
| 2. BoW + MLP | 0.938 | 0.895 | 0.29 | 0 |
| 3a. MiniLM + kNN | 0.888 | 0.950 | 25.93 | 0 |
| 3b. MiniLM + Logistic Regression | 0.944 | 0.923 | 16.70 | 0 |
| 3c. MiniLM + MLP head | 0.945 | 0.950 | 16.31 | 0 |
| 4a/4b. Gemini zero-shot / few-shot | Chưa có kết quả: API trả 429 khi chạy 4A | — | — | — |

## 3. Dự đoán và thực tế

- **Learning rate:** Em đoán `lr=50` làm loss dao động hoặc không hội tụ. Kết quả đã ghi trong notebook là loss giảm nhanh và xuống khoảng 0.007, không phân kỳ như em nghĩ. Em đã đánh giá quá mức rủi ro của learning rate lớn trong lần chạy này; kết quả còn phụ thuộc dữ liệu và gradient.
- **F1 của BoW + Logistic Regression:** Em đoán F1 khoảng 0.85. Kết quả test là 0.896, cao hơn dự đoán 0.046. Em đoán precision cao hơn recall và điều đó đúng: precision = 1.000, recall = 0.812.
- **Thí nghiệm tin nhắn ngụy trang:** Em đoán Encoder + LogReg (3b) sẽ tốt hơn BoW. Trong phần trả lời 3B đã ghi, cả hai đều đúng 5/6 tin, nên kết quả thực tế là hòa chứ không có lợi thế rõ cho 3b trên nhóm nhỏ này.

## 4. Phân tích 50 case khó

Chưa có kết quả để kết luận: ô chạy 50 case khó không có output lưu trong notebook. Vì vậy hiện chưa thể xác định model tụt nhiều nhất, nhóm khó nhất hay nêu hai case sai cụ thể mà không đoán thay kết quả. Cần chạy stress test rồi bổ sung bảng F1 theo model/nhóm và hai ví dụ sai.

## 5. Thử thách cá nhân

Chưa có bài làm được lưu: ô code thử thách đang trống và notebook chưa lưu đề cùng kết quả chạy. Cần chạy ô nhận đề theo SEED 1573, tự viết code và bổ sung kết quả/giải thích vào đây.

## 6. Phương án cho sản phẩm thật

Em chọn **3c. MiniLM + MLP head** nếu ưu tiên F1: kết quả lưu được là F1 test = 0.945, F1 SUB = 0.950, p95 = 16.31 ms/tin, thấp hơn SLA 50 ms. Thông lượng đo được khoảng 208 tin/s, cao hơn mức trung bình 116 tin/s của 10 triệu tin/ngày. Tuy vậy, biên dự phòng không lớn và số đo trên máy lab chưa bảo đảm tải đỉnh ngoài thực tế; cần benchmark trên hạ tầng triển khai và scale thêm nếu cần. Nếu ưu tiên độ đơn giản, 3b có F1 test = 0.944 và p95 = 16.70 ms, gần như ngang 3c.

Chưa đo được chi phí Gemini vì thí nghiệm 4A bị giới hạn API 429. Để có con số tham khảo theo giả định trong bài (gpt-6-luna, 100 token input + 1 token output mỗi tin, giá $0.10/$0.50 trên 1 triệu token), lọc 10 triệu tin/ngày tốn khoảng **$105/ngày, tương đương $3.150 cho 30 ngày**. Đây là ước tính lý thuyết, không phải chi phí đo từ thí nghiệm; prompt thực tế dài hơn có thể làm chi phí tăng.

## 7. AI log và điều đã học

**AI log:** Em sẽ tự điền bảng này sau, gồm câu hỏi đã hỏi, nội dung AI hỗ trợ, và phần em đã sửa/kiểm chứng.

| Phần | Bạn hỏi AI gì (prompt) | AI trả lời/viết gì | Bạn đã sửa / kiểm chứng gì |
| --- | --- | --- | --- |
|  |  |  |  |

Qua bài này, em thấy F1 và tốc độ cần được cân nhắc cùng nhau; accuracy một mình có thể che việc bỏ sót spam. Embedding giúp tận dụng ngữ nghĩa có sẵn, nhưng ở nhóm tin ngụy trang nhỏ kết quả chưa chắc hơn BoW; cần xem từng lỗi và không kết luận quá nhiều từ 40 tin.
