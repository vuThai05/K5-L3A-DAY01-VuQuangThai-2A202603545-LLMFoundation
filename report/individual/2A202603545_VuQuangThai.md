# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM

> Đổi tên file này thành `MSSV_HoTen.md` của bạn, ví dụ `2A20260000_NguyenVanAn.md`, rồi điền từng mục. Xoá các dòng hướng dẫn in nghiêng khi xong.

- **MSSV:** 2A202603545
- **Họ và tên:** Vũ Quang Thái
- **SEED** (in ở ô đầu notebook): 8765

## 1. Bằng chứng

| Tệp | Có? |
| --- | --- |
| [`day01_lab.ipynb`](day01_lab.ipynb) — notebook đã chạy, còn output | ☐ |
| [`lab1_report.json`](lab1_report.json) — do ô "Báo cáo cuối" sinh ra | ☐ |
| [`hand_calc.jpg`](hand_calc.jpg) — ảnh tính tay có các bước | ☐ |

## 2. Kết quả chính

                                F1 test thường (SUB)  F1 50 case khó  
1. BoW + LogReg                                0.889           0.368   
2. BoW + MLP                                   0.947           0.378   
3a. Emb + kNN                                  0.976           0.759   
3b. Emb + LogReg                               0.974           0.612   
3c. Emb + MLP head                             1.000           0.692   
4a. LLM nhỏ zero-shot                          0.000           0.000   
4d. LLM nhỏ + prompt phòng thủ                 0.000           0.000   

## 3. Dự đoán và thực tế

predict("llm_models", "Model lớn dự kiến có F1 cao hơn model nhỏ khoảng 0.05–0.10; tuy nhiên có chi phí cao hơn gấp 5–10 lần. LLM có thể không thắng mô hình 3b vì embedding giúp mô hình tận dụng ngữ nghĩa và ngữ cảnh tốt hơn.")
Dự đoán LLM sẽ phân loại tốt nhờ khả năng hiểu ngôn ngữ, nhưng thực tế kết quả trên 50 case khó chỉ đạt accuracy 42%.

predict("hard_eval", "BoW dự kiến đạt accuracy khoảng 60–70%, LLM khoảng 75–85%. Nhóm khó nhất với BoW là obfuscation vì việc thay chữ bằng số, ký tự đặc biệt hoặc chèn dấu câu khiến từ khóa không được nhận diện. Với LLM, nhóm khó nhất là prompt injection vì tin nhắn cố tình đưa chỉ dẫn giả để thao túng mô hình, ngoài ra nhóm ham_looks_spam cũng dễ gây nhầm lẫn.")
Dự đoán sẽ giảm prompt injection, nhưng thực tế vẫn chỉ nhận diện đúng 17% nhóm này.

predict("llm_json", "JSON dự kiến cho F1 tương đương bản trả một từ vì vẫn sử dụng cùng model để phân loại. Tuy nhiên, số token và chi phí sẽ tăng do phải sinh thêm phần giải thích. JSON có ưu điểm là dễ kiểm tra và giải thích kết quả, nhưng không thực sự cần thiết nếu chỉ phân loại spam/ham.")
Dự đoán F1 tương đương bản một từ, nhưng thực tế JSON đạt F1 = 1.000, trong khi bản một từ đạt 0.000. Chi phí tăng 2.12 lần.

## 4. Phân tích 50 case khó

BoW + LogReg giảm F1 mạnh nhất, từ 0.889 xuống 0.368. Nhóm khó nhất là spam giọng tự nhiên và né từ khóa vì mô hình chủ yếu dựa vào từ xuất hiện. Ví dụ, tin nhắn C0ngr4tulat10ns! Y0u h4ve w0n a £1000 g1ft c4rd khiến BoW khó nhận diện do từ khóa bị biến đổi ký tự. Một trường hợp khác là Hi, I'm working from home and making about £300 a day, dễ bị nhầm thành tin nhắn bình thường vì nội dung được diễn đạt tự nhiên, không có từ khóa spam rõ ràng.

## 5. Thử thách cá nhân

Em chọn thử thách chuyển đầu ra LLM từ một từ sang JSON gồm nhãn và lý do. Kết quả, bản JSON đạt F1 = 1.000, trong khi bản một từ đạt 0.000. Chi phí tăng 2.12 lần, tổng 701 token và không có JSON không hợp lệ. JSON giúp kết quả dễ giải thích hơn nhưng tốn thêm chi phí.

## 6. Phương án cho sản phẩm thật

Chọn Encoder (MiniLM) + MLP head vì đạt F1 = 1.000 trên tập SUB và đáp ứng yêu cầu p95 dưới 50 ms. Có thể kết hợp thêm rule-based để nhận diện OTP, tin giao dịch hợp lệ và hạn chế chặn nhầm. Những tin có độ tin cậy thấp sẽ được chuyển sang bước kiểm tra bổ sung. Đánh đổi là cần tài nguyên tính toán và phải kiểm tra khả năng xử lý 116 tin/giây trước khi triển khai thực tế.

## 7. AI log và điều đã học

Em sử dụng AI để giải thích các khái niệm Embedding, LLM, hỗ trợ viết và sửa code, xử lý lỗi API và xây dựng prompt phân loại spam. Em đã đối chiếu kết quả AI với thực nghiệm và điều chỉnh code khi phát hiện sai lệch. Qua bài lab, em hiểu rằng accuracy cao không đồng nghĩa mô hình hoạt động tốt trên dữ liệu thực tế. Việc lựa chọn mô hình cần cân bằng giữa chất lượng, độ trễ và chi phí. Ngoài ra, LLM có khả năng hiểu ngôn ngữ tốt nhưng vẫn có thể bị đánh lừa bởi prompt injection và những tin nhắn spam được viết tự nhiên.
