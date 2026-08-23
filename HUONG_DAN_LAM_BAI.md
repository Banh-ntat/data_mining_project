# Hướng dẫn làm bài chi tiết — Bài 2, 3, 4

Tài liệu này phân công cụ thể cho 6 thành viên (mỗi người 1 việc = 1 bài ×
1 bộ dữ liệu).

Input của cả 6 việc đều lấy từ `data/processed/` — sản phẩm Bài 1 đã tiền xử
lý xong, **có thể cần chạy lại `khao-sat.ipynb`**.

> Yêu cầu chung cho cả 3 bài (Mục 7 đề bài — áp dụng cho mọi việc bên dưới):
> - Nêu **giả định** của mỗi thuật toán, giải thích cơ chế bằng ngôn ngữ của mình.
> - So sánh **≥ 2 thuật toán hoặc cấu hình**.
> - Chọn tham số **có phương pháp** (grid/thử tay + lý do), không để mặc định mà không nói.
> - Dùng độ đo phù hợp với kỹ thuật, nêu vì sao chọn độ đo đó.
> - Báo cáo kỹ thuật (`bao-cao-baiX.pdf`) dài 2–4 trang, nhận xét ở **mức kỹ
>   thuật** (con số nói gì về mô hình) — diễn giải sâu theo bài toán thực tế để
>   dành cho đồ án, không lặp lại ở đây.

---

## Việc 1 — Bài 2: Phân lớp US Accidents — **Chiến**

**File input:** `data/processed/us_accidents/us_accidents_classification.csv`
**Quy mô:** 80.000 dòng × 23 cột. **Target:** `Severity` (1–4).

### Đặc thù cần biết trước khi làm
- Phân phối target **rất mất cân bằng**: Severity=1: 0.62%, =2: 81.61%,
  =3: 16.88%, =4: 0.89%. Một mô hình luôn đoán "2" đã đạt ~82% accuracy mà
  **vô dụng** — đây là lý do đề bài nhấn mạnh "với dữ liệu mất cân bằng, chỉ
  báo accuracy là không đạt".
- 2,3 triệu dòng là rất lớn — kNN và SVM có thể quá chậm hoặc tốn RAM. Nên:
  - Thử trên **mẫu stratified** (giữ nguyên tỉ lệ 4 lớp) khoảng 200.000–300.000
    dòng khi đang tinh chỉnh siêu tham số, sau đó chạy lại trên toàn bộ (hoặc
    mẫu lớn hơn) cho kết quả cuối, và nói rõ trong báo cáo đã làm vậy.
  - Cây quyết định và Logistic Regression xử lý tốt trên toàn bộ dữ liệu.

### Các bước bắt buộc (theo Mục 4 đề bài)
1. Kiểm tra lại phân phối `Severity`, xác nhận mất cân bằng (đã có ở Bài 1,
   nhắc lại 1 dòng trong báo cáo Bài 2).
2. Tách train/test (nên dùng `stratify=y` để giữ tỉ lệ 4 lớp ở cả 2 tập).
3. Xử lý mất cân bằng — chọn **một hoặc kết hợp**:
   - `class_weight='balanced'` (Logistic Regression, cây quyết định, SVM).
   - Undersampling lớp đa số (khả thi hơn SMOTE vì dữ liệu đã 2,3 triệu dòng,
     oversampling tới hàng triệu dòng sẽ rất tốn tài nguyên).
   - Gộp lớp: ví dụ gộp Severity 1+2 → "Nhẹ", 3+4 → "Nặng" nếu nhóm muốn bài
     toán 2 lớp đỡ mất cân bằng hơn (nêu rõ lý do và đánh đổi trong báo cáo).
4. ≥ 2 thuật toán, ví dụ: Decision Tree + Logistic Regression, hoặc thêm
   Naive Bayes để so sánh. Nêu giả định từng thuật toán (vd: Naive Bayes giả
   định các đặc trưng độc lập có điều kiện — với các cột hạ tầng tương quan
   nhau, giả định này bị vi phạm, nên bàn ngắn ảnh hưởng).
5. Siêu tham số: với Decision Tree ít nhất thử `max_depth` ở vài giá trị.
6. Độ đo: **precision/recall/F1 theo từng lớp** (không chỉ trung bình), ma
   trận nhầm lẫn 4×4, `classification_report`. Cân nhắc F1-macro làm chỉ số
   tổng hợp chính vì nó không bị lớp đa số (82%) làm lu mờ các lớp hiếm.
7. Nhận xét: mô hình có "ăn gian" bằng cách đoán toàn lớp 2 không, các lớp
   hiếm (1, 4) có bị bỏ sót nhiều không.

---

## Việc 2 — Bài 3: Luật kết hợp US Accidents — **Phúc**

**File input:** `data/processed/us_accidents/us_accidents_association_transactions.csv`
**Quy mô:** 80.000 giao dịch × 32 item (đã nhị phân hóa sẵn ở Bài 1: cờ hạ
tầng + khung giờ + ngày thường/cuối tuần + nhóm nhiệt độ + nhóm thời tiết +
ngày/đêm + mức độ nghiêm trọng). Trung bình 6,37 item/giao dịch, ≥2 item 100%.

### Đặc thù cần biết trước khi làm
- 2,3 triệu giao dịch chạy Apriori trực tiếp **sẽ rất chậm**. Nhóm nên:
  - Lấy mẫu ngẫu nhiên 300.000–500.000 giao dịch (đã gợi ý sẵn trong
    `khao-sat.ipynb`), hoặc
  - Dùng `fpgrowth` của `mlxtend` thay vì `apriori` (nhanh hơn nhiều với
    dữ liệu dày đặc item).
- Một số item có tần suất rất cao (`LoaiNgay_NgayThuong` 83.51%,
  `MucDo_Nhe` 82.23%, `AnhSang_Day` 66.76%) — luật chứa các item này rất dễ
  đạt support/confidence cao nhưng **tầm thường** (gần như luôn đúng với mọi
  tập dữ liệu, không phản ánh quan hệ đặc trưng). Bắt buộc lọc bằng **lift**
  (ví dụ lift > 1.2) để loại các luật kiểu này.
- Vì `Severity` đã được đưa vào làm item (`MucDo_Nang`/`MucDo_Nhe`), nhóm có
  thể tìm luật dạng "điều kiện X, Y → MucDo_Nang" — đây là hướng khai phá có
  giá trị kỹ thuật rõ ràng nhất của bộ này.

### Các bước bắt buộc (theo Mục 5 đề bài)
1. Giải thích lại ngắn gọn cách rời rạc hóa đã làm ở Bài 1 (không cần làm
   lại, chỉ dẫn lại vì đề bài yêu cầu "giải trình cách rời rạc hóa" trong
   Bài 3).
2. Apriori và/hoặc FP-Growth; nếu chỉ dùng 1 thuật toán, **so sánh ≥ 2 cấu
   hình ngưỡng** (ví dụ min_support 1% vs 3%) và bàn số luật sinh ra khác
   nhau thế nào.
3. Chọn ngưỡng support/confidence có lý do (ngưỡng quá thấp trên 2,3 triệu
   dòng sẽ sinh ra hàng nghìn luật vô nghĩa — đề bài cảnh báo đúng điều này).
4. Lọc luật: bỏ luật trùng lặp/dư thừa (luật cha đã bao hàm ý nghĩa của luật
   con), bỏ luật lift ≈ 1.
5. Nêu 5–10 luật nổi bật nhất (ưu tiên luật liên quan `MucDo_Nang`), giải
   thích ý nghĩa kỹ thuật (support/confidence/lift nói gì).

---

## Việc 3 — Bài 4: Gom cụm Airbnb — **Thư**

**File input:** `data/processed/airbnb/airbnb_clustering.csv`
**Quy mô:** 30.259 dòng × 16 đặc trưng số (giá, sức chứa, số phòng, đánh giá,
availability, số tiện nghi, hoạt động host...).

### Đặc thù cần biết trước khi làm
- Các đặc trưng có **thang đo rất khác nhau**: `price_num` (có thể vài trăm
  đến vài nghìn USD), `review_scores_*` (0–5), `amenity_count` (0–89). Bắt
  buộc `StandardScaler` (hoặc `MinMaxScaler`) trước khi tính khoảng cách —
  đề bài yêu cầu giải trình rõ vì sao ("co giãn/chuẩn hóa là bắt buộc với các
  thuật toán dựa trên khoảng cách").
- `price_num` và `host_listings_count` có khả năng lệch phải mạnh (một số
  listing/host giá trị rất cao). Cân nhắc log-transform các cột này trước khi
  scale, hoặc dùng RobustScaler, để tránh outlier chi phối khoảng cách.
- Các giá trị `price_num`, `bedrooms`, `bathrooms`... từng thiếu nhiều (đã
  điền bằng median theo `room_type` ở Bài 1) — nếu muốn, có thể nêu ngắn
  trong báo cáo Bài 4 rằng dữ liệu đầu vào không còn missing nhờ bước impute
  ở Bài 1.

### Các bước bắt buộc (theo Mục 6 đề bài)
1. Chuẩn hóa toàn bộ 16 đặc trưng số bằng `StandardScaler`.
2. ≥ 2 thuật toán, ví dụ K-Means + Hierarchical (Agglomerative), hoặc thêm
   DBSCAN để so sánh cách xử lý outlier/nhiễu (DBSCAN không ép mọi điểm vào
   1 cụm, phù hợp minh họa khi dữ liệu Airbnb có nhiều listing "dị biệt").
   Nêu giả định từng thuật toán (K-Means giả định cụm hình cầu, kích thước
   tương đồng; DBSCAN dựa vào mật độ, không cần chọn trước số cụm).
3. Chọn số cụm bằng elbow (inertia) và/hoặc silhouette score, trình bày biểu
   đồ và điểm gãy đã chọn.
4. Đánh giá bằng silhouette score và/hoặc Davies–Bouldin index.
5. Profiling từng cụm: trung bình `price_num`, `accommodates`,
   `review_scores_rating`... mỗi cụm — mô tả ở mức kỹ thuật (đặc trưng nổi
   bật của cụm), không cần diễn giải sâu theo nghiệp vụ du lịch (để dành đồ án).

---

## Việc 4 — Bài 2: Phân lớp Olist — Hoàng

**File input:** `data/processed/olist/olist_classification.csv`
**Quy mô:** 96.476 dòng × 11 cột. **Target:** `is_late` (True: giao trễ 6.77%,
False: đúng hạn 93.23%).

### Đặc thù cần biết trước khi làm
- Mất cân bằng nhẹ hơn US Accidents nhiều (93/7 so với 82/17/1/1 của 4 lớp),
  nhưng **vẫn phải xử lý** — đừng chỉ báo accuracy, vẫn cần precision/recall/F1
  cho lớp `True` (giao trễ), vì đây thường là lớp quan trọng hơn về mặt
  nghiệp vụ dù ít gặp.
- Chỉ 11 cột, 96k dòng — nhẹ hơn US Accidents rất nhiều lần, có thể chạy đủ
  cả kNN/SVM mà không lo hiệu năng, và có dư thời gian để làm grid search kỹ
  hơn cho siêu tham số.

### Các bước bắt buộc (theo Mục 4 đề bài)
1. Kiểm tra lại danh sách 11 cột trong file (in `df.columns.tolist()`) để
   biết chính xác đặc trưng đầu vào là gì trước khi encode/scale.
2. Train/test split có `stratify=y`.
3. Cân nhắc `class_weight='balanced'` cho lớp `is_late=True` (hoặc thử
   không dùng để so sánh, vì mất cân bằng nhẹ có thể không cần).
4. ≥ 2 thuật toán, ví dụ Decision Tree + kNN, hoặc Logistic Regression làm
   baseline dễ diễn giải hệ số.
5. Grid nhỏ cho siêu tham số (vd: `max_depth` ∈ {3,5,7,10} cho cây, `k` ∈
   {3,5,7,9,11} cho kNN) — vì dataset nhỏ nên chạy grid nhanh, tận dụng.
6. Độ đo: accuracy + precision/recall/F1 cho từng lớp + ma trận nhầm lẫn;
   thêm ROC-AUC vì bài toán này là nhị phân, ROC-AUC rất phù hợp.

---

## Việc 5 — Bài 3: Luật kết hợp Airbnb —  Sang

**File input:** `data/processed/airbnb/airbnb_association.csv`
**Quy mô:** 30.218 giao dịch × 182 item (tiện nghi). Trung bình 27,8
item/giao dịch, ≥2 item 99.93%.

### Đặc thù cần biết trước khi làm
- Số giao dịch nhỏ hơn US Accidents rất nhiều (30k so với 2,3 triệu) → chạy
  Apriori/FP-Growth **nhanh**, không lo vấn đề hiệu năng như Việc 2.
- Thách thức chính ở đây là **182 item + trung bình 27,8 item/giao dịch**
  (giao dịch rất "dày đặc") — nếu chọn min_support quá thấp, số tập phổ biến
  và số luật sẽ bùng nổ (hàng chục nghìn luật). Nên bắt đầu với min_support
  cao (ví dụ 15–20%) rồi hạ dần, quan sát số luật tăng thế nào.
- Một số tiện nghi gần như có mặt ở mọi listing (ví dụ Wifi, Smoke alarm) —
  luật chứa các item này thường tầm thường (giống lý do ở Việc 2). Cân nhắc
  loại các item có support > 90–95% trước khi mining, hoặc lọc luật bằng lift.

### Các bước bắt buộc (theo Mục 5 đề bài)
1. Vì dữ liệu đã ở dạng nhị phân sẵn (mỗi cột = 1 tiện nghi), phần "giải
   trình cách rời rạc hóa" chỉ cần nhắc lại ngắn: đơn vị giao dịch = 1
   listing, item = 1 tiện nghi trong `amenities`.
2. FP-Growth nên nhanh hơn Apriori ở mật độ item cao này — có thể dùng để so
   sánh tốc độ giữa 2 thuật toán như một điểm nhận xét kỹ thuật.
3. So sánh ≥ 2 ngưỡng min_support, quan sát bùng nổ số luật.
4. Lọc luật trùng lặp/dư thừa và luật tầm thường (lift thấp, hoặc chứa item
   gần như luôn có mặt).
5. Nêu 5–10 luật nổi bật nhất, ưu tiên các cặp tiện nghi ít phổ biến hơn
   (thể hiện "gu" thực sự của một phân khúc listing, thay vì tiện nghi cơ bản
   ai cũng có).

---

## Việc 6 — Bài 4: Gom cụm Olist (RFM) — **Ngân**

**File input:** `data/processed/olist/olist_clustering.csv`
**Quy mô:** 96.096 dòng × 4 cột (`customer_unique_id`, `recency_days`,
`frequency`, `monetary`) — chỉ 3 đặc trưng số thực sự dùng để gom cụm.

### Đặc thù cần biết trước khi làm
- Đây là bài toán **RFM clustering kinh điển** — tài liệu tham khảo rất
  nhiều, quy trình đơn giản, phù hợp nếu thành viên phụ trách ít kinh nghiệm
  hơn hoặc muốn làm nhanh để có thời gian hỗ trợ các việc khác.
- `monetary` có khả năng lệch phải mạnh (trung vị 108 nhưng max tới 13.664 —
  chênh lệch rất lớn). Nên cân nhắc log-transform `monetary` trước khi
  scale, để tránh vài khách hàng chi tiêu cực lớn kéo lệch toàn bộ cụm.
- `customer_unique_id` **không đưa vào mô hình** — chỉ dùng để định danh/gán
  nhãn cụm về sau.

### Các bước bắt buộc (theo Mục 6 đề bài)
1. Tách `customer_unique_id` ra riêng, chỉ scale 3 cột `recency_days`,
   `frequency`, `monetary` (cân nhắc log-transform `monetary` trước).
2. ≥ 2 thuật toán, ví dụ K-Means + Hierarchical clustering — vì chỉ 3 chiều
   nên có thể trực quan hóa cụm bằng scatter 2D/3D, rất trực quan cho báo cáo.
3. Chọn số cụm bằng elbow/silhouette (với RFM thường ra 3–5 cụm hợp lý).
4. Đánh giá bằng silhouette score.
5. Profiling: bảng trung bình `recency_days`/`frequency`/`monetary` theo
   từng cụm — đây chính là các "phân khúc RFM" kỹ thuật kinh điển (ví dụ cụm
   recency thấp + monetary cao vs cụm recency cao + monetary thấp), mô tả ở
   mức con số, không cần đặt tên phân khúc kiểu marketing (để dành đồ án).

---

## Bảng tổng hợp nhanh

| # | Thành viên | Bài | Bộ | File input | Target/Giao dịch |
|---|---|---|---|---|---|
| 1 | Chiến | Bài 2 | US Accidents | `us_accidents_classification.csv` | `Severity` (1–4) |
| 2 | Phúc | Bài 3 | US Accidents | `us_accidents_association_transactions.csv` | 1 vụ tai nạn = 1 giao dịch |
| 3 | Thư | Bài 4 | Airbnb | `airbnb_clustering.csv` | 1 listing = 1 điểm dữ liệu |
| 4 | Hoàng | Bài 2 | Olist | `olist_classification.csv` | `is_late` (True/False) |
| 5 | Sang | Bài 3 | Airbnb | `airbnb_association.csv` | 1 listing = 1 giao dịch |
| 6 | Ngân | Bài 4 | Olist | `olist_clustering.csv` | 1 khách hàng = 1 điểm dữ liệu (RFM) |