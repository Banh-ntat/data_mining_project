# Báo cáo kỹ thuật Bài 2: Phân lớp dữ liệu Olist

## 1. Mục tiêu và phạm vi

Mục tiêu của bài toán là xây dựng mô hình phân lớp nhị phân để dự đoán khả năng giao hàng trễ của đơn hàng thương mại điện tử trên bộ dữ liệu Olist.

- Biến mục tiêu: is_late
- Quy ước nhãn:
  - 0: giao đúng hạn
  - 1: giao trễ

Phạm vi báo cáo tập trung vào quy trình kỹ thuật có thể tái lập: thiết kế dữ liệu đầu vào, kiểm soát rò rỉ nhãn, chọn mô hình, chọn siêu tham số và đánh giá trong bối cảnh mất cân bằng lớp.

## 2. Dữ liệu và đặc tả bài toán

### 2.1. Nguồn dữ liệu

- Tệp sử dụng: data/processed/olist/olist_classification.csv
- Quy mô dữ liệu: 96.476 bản ghi, 11 cột
- Thiếu dữ liệu: 0 giá trị thiếu

### 2.2. Cấu trúc biến

- Biến nhãn: is_late
- Biến định danh không dùng để huấn luyện: order_id
- Biến thời gian gốc không dùng trực tiếp: order_purchase_timestamp
- Tập đặc trưng huấn luyện:
  - purchase_month
  - purchase_dayofweek
  - purchase_hour
  - order_item_count
  - unique_product_count
  - unique_seller_count
  - total_order_price
  - total_freight_value

### 2.3. Đặc điểm phân phối nhãn

- is_late = 0: 89.941 mẫu (93,23%)
- is_late = 1: 6.535 mẫu (6,77%)

Kết luận kỹ thuật: dữ liệu mất cân bằng rõ ràng, do đó độ chính xác tổng thể không phản ánh đầy đủ chất lượng mô hình. Các chỉ số trọng tâm cần ưu tiên là recall và F1 cho lớp giao trễ.

## 3. Thiết kế thí nghiệm và kiểm soát sai lệch

### 3.1. Chia tập dữ liệu

- Tỷ lệ train/test: 80/20
- Dùng stratify theo nhãn để bảo toàn tỷ lệ lớp
- Kích thước sau chia:
  - Train: 77.180
  - Test: 19.296

Lý do kỹ thuật: nếu không stratify, tập test có thể lệch tỷ lệ lớp thiểu số, làm sai lệch đánh giá khả năng phát hiện đơn giao trễ.

### 3.2. Tiền xử lý theo mô hình

- Decision Tree: không cần chuẩn hóa thang đo đặc trưng.
- Logistic Regression và kNN: chuẩn hóa trong pipeline để đảm bảo các đặc trưng có cùng mặt bằng ảnh hưởng.

Lý do kỹ thuật:
- kNN dựa trên khoảng cách nên rất nhạy với thang đo.
- Logistic Regression tối ưu hàm mất mát tốt hơn khi dữ liệu được chuẩn hóa.

### 3.3. Kiểm soát rò rỉ nhãn

- Không đưa biến định danh vào mô hình.
- Không dùng biến hậu nghiệm làm đặc trưng huấn luyện.
- Toàn bộ bước chọn mô hình và tuning thực hiện trên train.
- Tập test chỉ dùng một lần để đánh giá cuối.

Kết quả: quy trình đáp ứng nguyên tắc đánh giá độc lập, giảm nguy cơ lạc quan hóa chỉ số.

## 4. Các mô hình sử dụng và giả định kỹ thuật

### 4.1. Baseline tham chiếu

- Dummy Most Frequent: luôn dự đoán lớp đa số.
- Mục đích: xác lập ngưỡng tối thiểu để tránh kết luận sai do hiệu ứng mất cân bằng lớp.

### 4.2. Decision Tree

- Cơ chế: phân hoạch không gian đặc trưng theo các ngưỡng để tăng độ thuần nút.
- Giả định ngầm: dữ liệu có thể tách thành các vùng quyết định phi tuyến.
- Ưu điểm: dễ diễn giải, bắt được tương tác phi tuyến cơ bản.
- Rủi ro: dễ quá khớp nếu cây sâu và lá quá nhỏ.

### 4.3. Logistic Regression

- Cơ chế: mô hình hóa log-odds tuyến tính theo đặc trưng.
- Giả định chính: quan hệ gần tuyến tính giữa đặc trưng và log-odds của lớp dương.
- Ưu điểm: ổn định, làm đường chuẩn tốt để so sánh.
- Rủi ro: hạn chế khi biên phân tách phi tuyến mạnh.

### 4.4. k-Nearest Neighbors

- Cơ chế: dự đoán theo đa số hàng xóm gần nhất.
- Giả định chính: các điểm gần nhau trong không gian đặc trưng có xu hướng cùng nhãn.
- Ưu điểm: đơn giản, không giả định hàm toàn cục.
- Rủi ro: nhạy với nhiễu, nhạy với mất cân bằng lớp và chi phí dự đoán cao.

## 5. Chiến lược chọn siêu tham số

### 5.1. Phương pháp tuning

- Sử dụng GridSearchCV với cv = 5 trên tập train.
- Chỉ số tối ưu: F1 của lớp is_late = 1.

Lý do chọn F1 lớp thiểu số:
- Precision cao nhưng recall thấp không phù hợp mục tiêu cảnh báo trễ hàng.
- Recall cao nhưng precision quá thấp gây quá nhiều cảnh báo giả.
- F1 cân bằng hai yếu tố trên trong một điểm đo duy nhất.

### 5.2. Không gian tìm kiếm

- Decision Tree:
  - class_weight: None, balanced
  - max_depth: 3, 5, 7, 10, None
  - min_samples_leaf: 20, 50, 100
- Logistic Regression:
  - C: 0.1, 1.0, 10.0
  - class_weight: None, balanced
- kNN:
  - n_neighbors: 3, 5, 7, 9, 11
  - weights: uniform, distance

Ghi chú hiệu năng:
- GridSearch cho kNN được chạy trên mẫu train stratified 40.000 dòng để giảm thời gian tính toán.
- Cấu hình tốt nhất sau đó được đánh giá lại trên tập test đầy đủ.

### 5.3. Cấu hình tối ưu tìm được

- Decision Tree: class_weight = balanced, max_depth = 3, min_samples_leaf = 50, CV F1 = 0,2228
- Logistic Regression: C = 10,0, class_weight = balanced, CV F1 = 0,1502
- kNN: n_neighbors = 3, weights = distance, CV F1 = 0,0753

Nhận xét: Decision Tree vượt trội rõ ràng theo tiêu chí tối ưu đã chọn.

## 6. Hệ chỉ số đánh giá

Hệ chỉ số sử dụng:

- Accuracy
- Precision_late
- Recall_late
- F1_late
- ROC-AUC
- Ma trận nhầm lẫn

Ưu tiên diễn giải:

- Recall_late: khả năng phát hiện đơn thực sự giao trễ.
- F1_late: cân bằng giữa phát hiện đúng và cảnh báo giả.
- ROC-AUC: mức độ tách lớp tổng quát theo ngưỡng xác suất.

## 7. Kết quả thực nghiệm trên tập test

### 7.1. Bảng tổng hợp chỉ số

| Mô hình | Accuracy | Precision_late | Recall_late | F1_late | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Decision Tree (GridSearch) | 0,7483 | 0,1433 | 0,5455 | 0,2270 | 0,6828 |
| Logistic Regression (GridSearch) | 0,5627 | 0,0876 | 0,5792 | 0,1521 | 0,5749 |
| kNN (GridSearch) | 0,9087 | 0,1151 | 0,0520 | 0,0717 | 0,5409 |
| Dummy Baseline (Most Frequent) | 0,9323 | 0,0000 | 0,0000 | 0,0000 | 0,5000 |

### 7.2. Phân tích ma trận nhầm lẫn

- Dummy Baseline: [[17989, 0], [1307, 0]]
  - Không phát hiện được bất kỳ đơn giao trễ nào.
  - Accuracy cao nhưng vô nghĩa về mặt vận hành.
- Decision Tree: [[13727, 4262], [594, 713]]
  - Phát hiện 713/1307 đơn giao trễ, recall đạt 54,55%.
  - Chấp nhận đánh đổi bằng số dương tính giả tương đối cao (4262).
- Logistic Regression: [[10101, 7888], [550, 757]]
  - Recall nhỉnh hơn Decision Tree nhưng precision thấp hơn nhiều.
  - Số cảnh báo giả lớn làm giảm tính khả dụng trong thực tế.
- kNN: [[17466, 523], [1239, 68]]
  - Accuracy cao do nghiêng về lớp đa số.
  - Bỏ sót phần lớn đơn giao trễ, không phù hợp mục tiêu bài toán.

## 8. Thảo luận kỹ thuật và lựa chọn mô hình

### 8.1. Vì sao không chọn theo accuracy

Baseline đã đạt accuracy 93,23% nhưng recall_late bằng 0. Đây là bằng chứng trực tiếp cho thấy accuracy không thể dùng làm tiêu chí chính khi dữ liệu mất cân bằng.

### 8.2. Đánh đổi precision và recall

- Logistic Regression tối đa hóa khả năng bắt lớp trễ nhiều hơn một chút, nhưng đánh đổi bằng lượng cảnh báo giả rất lớn.
- Decision Tree cho điểm cân bằng tốt hơn giữa precision và recall, nên F1_late cao nhất.

### 8.3. Lý do chọn mô hình cuối cùng

Mô hình được đề xuất: Decision Tree với cấu hình tối ưu từ GridSearch.

Lý do:

- F1_late cao nhất trong các mô hình khảo sát.
- ROC-AUC cao nhất, cho thấy chất lượng tách lớp tốt hơn tổng thể.
- Độ phức tạp vừa phải và thuận lợi khi giải thích quyết định mô hình.

## 9. Khả năng tái lập cho cả nhóm

Để các thành viên tái lập đúng kết quả, cần thống nhất:

- Cố định random_state cho train/test split và các mô hình có thành phần ngẫu nhiên.
- Giữ nguyên đặc trưng đầu vào như mục 2.2.
- Giữ nguyên chiến lược stratify và quy trình tuning theo F1_late.
- Không dùng tập test trong bất kỳ bước điều chỉnh tham số nào.

Nếu có sai khác nhẹ giữa các máy, kiểm tra theo thứ tự:

- Phiên bản thư viện trong requirements.
- Random seed có đồng nhất hay không.
- Cách lấy mẫu 40.000 dòng cho kNN có stratified và cố định seed hay không.

## 10. Đối chiếu yêu cầu kỹ thuật của bài

Báo cáo đáp ứng các tiêu chí bắt buộc:

- Nêu rõ bài toán, dữ liệu, tỷ lệ mất cân bằng lớp.
- So sánh từ hai thuật toán trở lên, có baseline tham chiếu.
- Chọn siêu tham số có phương pháp bằng GridSearchCV.
- Dùng bộ chỉ số phù hợp với dữ liệu mất cân bằng.
- Kết luận dựa trên số liệu, có phân tích đánh đổi kỹ thuật.

## 11. Hướng cải tiến ở bước tiếp theo

- Tối ưu ngưỡng xác suất theo mục tiêu vận hành cụ thể, không cố định ngưỡng 0,5.
- Bổ sung đường cong Precision-Recall và chỉ số PR-AUC.
- Thử các mô hình ensemble như Random Forest, Gradient Boosting, Balanced Random Forest.
- Mở rộng đặc trưng hành vi đơn hàng theo chu kỳ thời gian và tỷ lệ phí vận chuyển.

## 12. Kết luận

Quy trình phân lớp đã được xây dựng đúng chuẩn kỹ thuật và có khả năng tái lập. Trong các mô hình thử nghiệm, Decision Tree là lựa chọn phù hợp nhất cho bài toán dự đoán giao trễ trên dữ liệu Olist, dựa trên F1_late và ROC-AUC cao nhất, đồng thời giữ được mức diễn giải tốt để cả nhóm triển khai và thảo luận tiếp ở các bước sau.
