# Đề xuất 3 bộ dữ liệu — Bài tập 1
### Môn Khai phá dữ liệu — Trường Đại học Giao thông Vận tải TP.HCM

---

# ĐỀ XUẤT 1: Brazilian E-Commerce Public Dataset (Olist)

## 1. Nguồn, giấy phép, quy mô

| Mục | Chi tiết |
|---|---|
| Đường dẫn | https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce |
| Đơn vị công bố | Olist (nền tảng thương mại điện tử lớn nhất Brazil), công bố công khai trên Kaggle |
| Ngày tải | *(ghi ngày nhóm thực tế tải xuống)* |
| Số bản ghi | ~99.441 đơn hàng (bảng `orders`), tổng cộng phân bố trên 9 bảng CSV liên kết qua khóa `order_id` / `customer_id` / `product_id` / `seller_id` |
| Số thuộc tính | 54 cột khi gộp toàn bộ 9 bảng qua các phép join |
| Giấy phép | Dữ liệu thương mại thật đã được ẩn danh hóa (tên đối tác/khách hàng thay bằng tên hư cấu); Olist công bố miễn phí cho mục đích học thuật/nghiên cứu trên Kaggle (CC BY-NC-SA 4.0 theo trang dataset) |
| Cấu trúc | 9 bảng quan hệ: `customers`, `orders`, `order_items`, `order_payments`, `order_reviews`, `products`, `sellers`, `geolocation`, `product_category_name_translation` |

> **Lưu ý bắt buộc:** dữ liệu này là dạng quan hệ (relational), nhóm phải tự `merge`/`join` các bảng theo khóa chung để tạo ra bảng phân tích duy nhất trước khi khai phá — đây cũng chính là một phần công việc tiền xử lý của Bài 1.

## 2. Từ điển dữ liệu (rút gọn — các thuộc tính chính sau khi join)

| Tên thuộc tính | Ý nghĩa | Kiểu | Thang đo | Miền giá trị |
|---|---|---|---|---|
| `order_id` | Mã đơn hàng | Chuỗi | Định danh | Duy nhất mỗi đơn |
| `customer_unique_id` | Mã khách hàng (không đổi qua các đơn) | Chuỗi | Định danh | — |
| `order_status` | Trạng thái đơn hàng | Hạng mục | Định danh | delivered, shipped, canceled, unavailable... |
| `order_purchase_timestamp` | Thời điểm đặt hàng | Ngày giờ | Khoảng | 2016–2018 |
| `order_delivered_customer_date` | Thời điểm giao hàng thực tế | Ngày giờ | Khoảng | Có thể rỗng nếu chưa giao |
| `order_estimated_delivery_date` | Thời điểm giao hàng dự kiến | Ngày giờ | Khoảng | — |
| `price` | Giá sản phẩm (mỗi item) | Số thực | Tỉ lệ | > 0, đơn vị Real (BRL) |
| `freight_value` | Phí vận chuyển | Số thực | Tỉ lệ | ≥ 0 |
| `payment_type` | Hình thức thanh toán | Hạng mục | Định danh | credit_card, boleto, voucher, debit_card |
| `payment_installments` | Số kỳ trả góp | Số nguyên | Khoảng | 1–24 |
| `payment_value` | Tổng giá trị thanh toán | Số thực | Tỉ lệ | ≥ 0 |
| `review_score` | Điểm đánh giá đơn hàng | Số nguyên | Thứ tự | 1–5 |
| `product_category_name` | Ngành hàng sản phẩm | Hạng mục | Định danh | ~70 ngành hàng (tiếng Bồ Đào Nha, có bảng dịch sang tiếng Anh) |
| `product_weight_g`, `product_length_cm`, `product_height_cm`, `product_width_cm` | Kích thước/khối lượng sản phẩm | Số thực | Tỉ lệ | > 0 |
| `seller_state`, `customer_state` | Bang của người bán/khách hàng | Hạng mục | Định danh | 27 bang Brazil |
| `customer_city`, `seller_city` | Thành phố | Hạng mục | Định danh | — |
| `geolocation_lat`, `geolocation_lng` | Tọa độ địa lý theo mã bưu chính | Số thực | Khoảng | Phạm vi lãnh thổ Brazil |

*(Nhóm bổ sung đầy đủ ~54 cột còn lại khi nộp bài, dựa theo mô tả từng bảng gốc trên Kaggle.)*

## 3. Điều tra tri thức lĩnh vực

**Bối cảnh nghiệp vụ:** Olist là mô hình marketplace trung gian — kết nối người bán nhỏ lẻ khắp Brazil với khách hàng qua một nền tảng chung, tự xử lý logistics. Toàn bộ hành trình một đơn hàng được ghi lại: từ lúc khách đặt hàng, thanh toán, người bán giao cho đơn vị vận chuyển, đến khi khách nhận hàng và để lại đánh giá. Đây là bài toán "vòng đời đơn hàng thương mại điện tử" rất phổ biến trong thực tế kinh doanh.

**Ý nghĩa và liên hệ giữa các thuộc tính:**
- Chênh lệch giữa `order_estimated_delivery_date` và `order_delivered_customer_date` phản ánh việc giao hàng trễ hay đúng hẹn — một chỉ số vận hành quan trọng.
- `review_score` thường có tương quan nghịch với độ trễ giao hàng và tương quan với `freight_value`/`price` — khách trả phí vận chuyển cao mà nhận hàng trễ dễ đánh giá thấp.
- `payment_installments` phản ánh thói quen tiêu dùng đặc trưng của thị trường Brazil (mua trả góp ngay cả với đơn giá trị nhỏ), liên hệ tới `product_category_name` (mặt hàng giá trị cao thường trả góp nhiều kỳ hơn).
- Từng `order_id` có thể có nhiều `order_item_id` (nhiều sản phẩm trong một đơn) → dữ liệu giao dịch tự nhiên phù hợp cho luật kết hợp.

**Lập luận khả năng khai phá:**

| Kỹ thuật | Có khả thi không? | Vì sao |
|---|---|---|
| Phân lớp | **Có** | Biến mục tiêu rời rạc rõ ràng và có ý nghĩa nghiệp vụ: (a) giao hàng trễ/đúng hạn (nhị phân, suy ra từ so sánh 2 cột ngày), (b) đánh giá tích cực/tiêu cực (gộp `review_score` 1–2 sao = tiêu cực, 4–5 sao = tích cực). Cả hai đều là bài toán mà doanh nghiệp thật sự cần dự đoán sớm để can thiệp. |
| Luật kết hợp | **Có** | Mỗi `order_id` là một "giỏ hàng" tự nhiên chứa nhiều `product_category_name` (qua bảng `order_items`) — đúng cấu trúc dữ liệu giao dịch kinh điển cho Apriori/FP-Growth. Ngoài ra có thể khai phá luật giữa `payment_type`, `payment_installments` (đã rời rạc hóa) và ngành hàng. |
| Gom cụm | **Có** | Khách hàng (theo `customer_unique_id`) có thể phân nhóm theo hành vi mua: tần suất mua, giá trị đơn trung bình, thời gian mua gần nhất (mô hình RFM — Recency, Frequency, Monetary) — một bài toán phân khúc khách hàng có ý nghĩa thực tế trong marketing. |

## 4. Đề xuất 2 kỹ thuật sẽ thực hiện trên bộ này

→ **Luật kết hợp** (sản phẩm/ngành hàng mua cùng nhau trong một đơn) và **Gom cụm** (phân khúc khách hàng theo hành vi mua — RFM).
*(Phân lớp giao hàng trễ để dành làm phương án dự phòng hoặc mở rộng nếu nhóm muốn làm cả 3 kỹ thuật trên bộ này.)*

## 5. Điều tra tiền xử lý (Mục 3.3)

> Các con số cụ thể (tỉ lệ %, ngưỡng) dưới đây là **giả thuyết cần kiểm chứng** trên dữ liệu thật nhóm tải về — không dùng làm số liệu báo cáo cuối cùng.

### 5.1 Nhiễu và ngoại lai

| Thuộc tính | Cách phát hiện | Nghi vấn cần kiểm tra | Quyết định dự kiến |
|---|---|---|---|
| `price`, `freight_value` | IQR/boxplot theo từng `product_category_name` | Có đơn giá bằng 0 hoặc cực lớn bất thường (ví dụ sản phẩm vài Real nhưng phí ship gấp chục lần giá) | Giữ nhưng gắn cờ; chỉ loại nếu xác định là lỗi nhập liệu rõ ràng (giá = 0 với sản phẩm không phải khuyến mãi) |
| `product_weight_g`, kích thước sản phẩm | Thống kê mô tả + đối chiếu tri thức lĩnh vực (khối lượng/kích thước có hợp lý với ngành hàng không) | Có sản phẩm nặng 0g hoặc kích thước 0 cm — lỗi nhập liệu vì vật lý không cho phép | Loại hoặc điền theo trung vị của cùng `product_category_name` |
| `payment_installments` | Thống kê tần suất | Giá trị 0 kỳ trả góp có hợp lệ không (có thể là thanh toán ví/voucher) | Giữ, đối chiếu với `payment_type` để xác nhận không phải lỗi |
| Thời gian giao hàng (`order_delivered_customer_date` − `order_purchase_timestamp`) | IQR trên khoảng thời gian tính được | Đơn giao "âm ngày" (ngày giao trước ngày đặt) là lỗi logic chắc chắn | Loại các dòng vi phạm ràng buộc logic thời gian |

**Ảnh hưởng dự kiến:** ngoại lai về giá/phí vận chuyển ảnh hưởng trực tiếp đến mô hình phân lớp trễ hạn (Bài 2) nếu không xử lý; ngoại lai thời gian giao hàng cần loại trước khi tính nhãn "trễ/đúng hạn".

### 5.2 Giá trị thiếu

| Thuộc tính | Cơ chế thiếu dự kiến | Cách xử lý dự kiến |
|---|---|---|
| `order_delivered_customer_date` | Có hệ thống — thiếu khi đơn chưa giao/bị hủy (liên hệ trực tiếp với `order_status` ≠ delivered) | Không điền — loại các đơn chưa hoàn tất khỏi tập huấn luyện phân lớp trễ hạn (vì chưa có nhãn thật) |
| `product_category_name`, kích thước/khối lượng sản phẩm | Có thể ngẫu nhiên (một số sản phẩm hiếm khi cập nhật catalogue) | Điền mode/trung vị theo nhóm sản phẩm tương tự, hoặc gắn nhãn "unknown" nếu tỉ lệ thiếu nhỏ |
| `review_comment_message` (không dùng cho khai phá số) | Có hệ thống — khách không bắt buộc viết bình luận, chỉ chọn điểm số | Không điền — không dùng cột text tự do trong phạm vi các kỹ thuật đã chọn |
| Tọa độ `geolocation_lat/lng` sau khi join | Ngẫu nhiên — một số mã bưu chính không khớp bảng geolocation | Loại các dòng không join được nếu dùng phân tích không gian, hoặc bỏ qua yêu cầu tọa độ nếu không cần |

### 5.3 Thêm / xóa / biến đổi thuộc tính

- **Xóa:** các mã định danh không mang ý nghĩa phân tích trực tiếp (`order_id`, `customer_id` thô — chỉ giữ lại `customer_unique_id` để gom nhóm khách hàng); cột review text tự do nếu không dùng NLP.
- **Thêm (feature engineering):**
  - `delivery_delay_days` = `order_delivered_customer_date` − `order_estimated_delivery_date` → nhãn cho bài toán phân lớp trễ hạn.
  - `order_item_count` = số dòng `order_item_id` trên mỗi `order_id` → phục vụ cả gom cụm (đơn hàng lớn/nhỏ) và tiền xử lý cho luật kết hợp.
  - Đặc trưng RFM (Recency, Frequency, Monetary) theo `customer_unique_id` → bắt buộc cho gom cụm phân khúc khách hàng.
- **Biến đổi:**
  - Mã hóa one-hot cho `payment_type`, `customer_state` (số lượng giá trị vừa phải, phù hợp one-hot).
  - Chuẩn hóa (scaling) `price`, `freight_value`, các đặc trưng RFM trước khi đưa vào gom cụm dựa trên khoảng cách (k-means).
  - Rời rạc hóa `product_category_name` thành "giỏ hàng" nhị phân hóa (one-hot theo từng đơn) để chạy Apriori/FP-Growth.

---

# ĐỀ XUẤT 2: Inside Airbnb — Detailed Listings

## 1. Nguồn, giấy phép, quy mô

| Mục | Chi tiết |
|---|---|
| Đường dẫn | http://insideairbnb.com/get-the-data/ (chọn 1 thành phố, ví dụ: Rio de Janeiro, Barcelona, hoặc thành phố có ≥ 25.000 listing) |
| Đơn vị công bố | Inside Airbnb (dự án phi lợi nhuận, tổng hợp dữ liệu công khai từ Airbnb) |
| Ngày tải | *(ghi ngày nhóm thực tế tải xuống — dữ liệu được cập nhật định kỳ hàng tháng/quý)* |
| Số bản ghi | 20.000 – 90.000+ listing tùy thành phố chọn (khuyến nghị chọn thành phố lớn để đạt ≥ 10.000 bản ghi) |
| Số thuộc tính | 74 cột (file `listings.csv` bản chi tiết — không dùng bản rút gọn `listings_summary.csv`) |
| Giấy phép | Dữ liệu công khai cho mục đích nghiên cứu phi thương mại (theo chính sách Inside Airbnb) |

## 2. Từ điển dữ liệu (rút gọn — các nhóm thuộc tính chính)

| Tên thuộc tính | Ý nghĩa | Kiểu | Thang đo | Miền giá trị |
|---|---|---|---|---|
| `id` | Mã định danh listing | Chuỗi/số | Định danh | Duy nhất |
| `host_id` | Mã định danh chủ nhà | Số | Định danh | — |
| `host_since` | Ngày host tham gia Airbnb | Ngày | Khoảng | — |
| `host_response_rate` | Tỉ lệ phản hồi tin nhắn của host | Chuỗi (dạng %, cần chuyển số) | Tỉ lệ | 0–100% |
| `host_is_superhost` | Host có đạt danh hiệu Superhost | Nhị phân | Định danh | t / f |
| `host_listings_count` | Số lượng listing host đang quản lý | Số nguyên | Tỉ lệ | ≥ 1 |
| `neighbourhood_cleansed` | Khu vực (đã chuẩn hóa theo bản đồ hành chính) | Hạng mục | Định danh | Tùy thành phố |
| `latitude`, `longitude` | Tọa độ | Số thực | Khoảng | Phạm vi thành phố |
| `property_type` | Loại hình chỗ ở | Hạng mục | Định danh | Apartment, House, Loft... |
| `room_type` | Loại phòng | Hạng mục | Định danh | Entire home/apt, Private room, Shared room, Hotel room |
| `accommodates` | Sức chứa tối đa | Số nguyên | Tỉ lệ | ≥ 1 |
| `bathrooms`, `bedrooms`, `beds` | Số phòng tắm/ngủ/giường | Số thực/nguyên | Tỉ lệ | ≥ 0 |
| `amenities` | Danh sách tiện nghi (dạng chuỗi JSON) | Chuỗi (đa trị) | Định danh (tập hợp) | Wifi, Kitchen, Pool, Air conditioning... |
| `price` | Giá thuê mỗi đêm | Chuỗi (dạng "$xxx.xx", cần chuyển số) | Tỉ lệ | > 0 |
| `minimum_nights`, `maximum_nights` | Số đêm tối thiểu/tối đa | Số nguyên | Tỉ lệ | ≥ 1 |
| `availability_365` | Số ngày còn trống trong năm | Số nguyên | Tỉ lệ | 0–365 |
| `number_of_reviews` | Tổng số đánh giá | Số nguyên | Tỉ lệ | ≥ 0 |
| `review_scores_rating` | Điểm đánh giá trung bình | Số thực | Khoảng | 0–100 (hoặc 0–5 tùy phiên bản) |
| `review_scores_cleanliness`, `review_scores_location`, `review_scores_value`... | Điểm đánh giá theo từng khía cạnh | Số thực | Khoảng | 0–10 |
| `instant_bookable` | Có cho đặt phòng tức thì không | Nhị phân | Định danh | t / f |
| `reviews_per_month` | Số đánh giá trung bình mỗi tháng | Số thực | Tỉ lệ | ≥ 0 |

*(Nhóm bổ sung đầy đủ 74 cột dựa theo Data Dictionary chính thức của Inside Airbnb khi nộp bài.)*

## 3. Điều tra tri thức lĩnh vực

**Bối cảnh nghiệp vụ:** Airbnb là mô hình kinh tế chia sẻ trong lĩnh vực lưu trú — chủ nhà (host) đăng cho thuê chỗ ở, khách đặt phòng, sau đó để lại đánh giá. Dữ liệu Inside Airbnb là bản chụp (snapshot) thị trường lưu trú ngắn hạn của một thành phố tại một thời điểm.

**Ý nghĩa và liên hệ giữa các thuộc tính:**
- `price` chịu ảnh hưởng đồng thời bởi vị trí (`neighbourhood_cleansed`, `latitude/longitude`), sức chứa (`accommodates`, `bedrooms`), loại phòng (`room_type`), và uy tín host (`host_is_superhost`, `review_scores_rating`).
- `host_is_superhost` thường liên hệ với `host_response_rate` cao và `review_scores_rating` cao — phản ánh chất lượng dịch vụ nhất quán.
- `amenities` là danh sách tiện nghi tự khai báo — các tiện nghi thường xuất hiện theo "cụm" tự nhiên (ví dụ: Kitchen + Wifi + Air conditioning hay đi cùng nhau ở căn hộ trung-cao cấp; Pool + Hot tub hay đi cùng ở biệt thự nghỉ dưỡng).
- `availability_365` thấp kết hợp `number_of_reviews` cao gợi ý listing đang hoạt động sôi động (không phải listing "ma"/bỏ hoang).

**Lập luận khả năng khai phá:**

| Kỹ thuật | Có khả thi không? | Vì sao |
|---|---|---|
| Phân lớp | **Có** | Biến mục tiêu rời rạc có ý nghĩa: dự đoán `host_is_superhost` (t/f) từ các đặc điểm vận hành, hoặc phân loại `price` thành các phân khúc (thấp/trung/cao) — hữu ích cho host mới định giá phòng. |
| Luật kết hợp | **Có** | `amenities` là tập nhiều giá trị trên mỗi listing → "giỏ tiện nghi" tự nhiên, đúng cấu trúc bài toán luật kết hợp kinh điển (giống giỏ hàng siêu thị). |
| Gom cụm | **Có** | Có thể phân nhóm listing theo đặc điểm giá, vị trí, sức chứa để nhận diện các "phân khúc thị trường" tự nhiên (ví dụ: căn hộ studio giá rẻ trung tâm, biệt thự cao cấp ngoại ô...). |

## 4. Đề xuất 2 kỹ thuật sẽ thực hiện trên bộ này

→ **Phân lớp** (dự đoán Superhost hoặc phân khúc giá) và **Gom cụm** (phân khúc thị trường lưu trú).
*(Luật kết hợp trên tập `amenities` để dành làm phương án dự phòng/mở rộng.)*

## 5. Điều tra tiền xử lý (Mục 3.3)

> Các con số cụ thể dưới đây là **giả thuyết cần kiểm chứng** trên dữ liệu thật nhóm tải về — không dùng làm số liệu báo cáo cuối cùng.

### 5.1 Nhiễu và ngoại lai

| Thuộc tính | Cách phát hiện | Nghi vấn cần kiểm tra | Quyết định dự kiến |
|---|---|---|---|
| `price` | Boxplot theo `room_type`/`neighbourhood_cleansed`; IQR | Có listing giá 0 hoặc giá cực cao bất thường (host để giá "chặn" không cho đặt) | Loại giá = 0; xem xét loại/gắn cờ các giá trị vượt xa phân vị 99% nếu không có mô tả hợp lý (villa siêu sang) |
| `minimum_nights` | Thống kê tần suất, đối chiếu tri thức lĩnh vực | Giá trị bất thường như 365, 1000 đêm (host cố tình chặn đặt phòng ngắn hạn) | Giữ nguyên nhưng cần lưu ý khi coi đây là "outlier hợp lệ" chứ không phải lỗi — quyết định giữ vì có ý nghĩa nghiệp vụ thật (host không muốn cho thuê ngắn hạn) |
| `bedrooms`, `beds`, `accommodates` | So sánh chéo (accommodates nhỏ hơn bedrooms là vô lý) | Vi phạm ràng buộc logic (bedrooms > accommodates) | Loại hoặc điền lại theo nhóm property_type tương tự |
| `availability_365` | Thống kê mô tả | Giá trị 0 kéo dài có thể là listing ngừng hoạt động chứ không phải nhiễu | Giữ, không coi là ngoại lai — có ý nghĩa nghiệp vụ (listing tạm ngưng) |

### 5.2 Giá trị thiếu

| Thuộc tính | Cơ chế thiếu dự kiến | Cách xử lý dự kiến |
|---|---|---|
| `host_response_rate`, `host_acceptance_rate` | Có hệ thống — thiếu ở host mới chưa có đủ lịch sử phản hồi | Điền theo nhóm (mode/trung vị theo host_listings_count), hoặc tạo cờ nhị phân "host mới/có lịch sử" thay vì điền số |
| `review_scores_*` (toàn bộ nhóm điểm đánh giá) | Có hệ thống — thiếu khi `number_of_reviews` = 0 (listing chưa có đánh giá) | Không điền trung bình toàn cục (sẽ sai lệch); tách riêng nhóm "chưa có đánh giá" khi phân lớp/gom cụm |
| `bathrooms`, `bedrooms` | Ngẫu nhiên — một số host không khai đầy đủ | Điền theo trung vị của cùng `property_type` + `accommodates` |
| `neighbourhood` (khác `neighbourhood_cleansed`) | Có hệ thống — trường tự khai báo, một số host bỏ trống | Ưu tiên dùng `neighbourhood_cleansed` (đã geocode chuẩn hóa), bỏ cột tự khai nếu trùng lặp thông tin |

### 5.3 Thêm / xóa / biến đổi thuộc tính

- **Xóa:** các cột văn bản tự do dài (`name`, `description`, `neighborhood_overview`) nếu không dùng NLP; cột trùng lặp thông tin địa lý (`neighbourhood` tự khai vs. `neighbourhood_cleansed` đã chuẩn hóa — giữ 1 trong 2); các URL/ID không mang ý nghĩa phân tích (`listing_url`, `picture_url`).
- **Thêm (feature engineering):**
  - `price_per_person` = `price` / `accommodates` → so sánh giá công bằng hơn giữa các loại chỗ ở khác nhau.
  - `amenity_count` = số lượng tiện nghi trong `amenities` → đặc trưng số cho gom cụm/phân lớp.
  - `host_experience_years` = năm hiện tại − năm trong `host_since` → đo độ "thâm niên" của host.
  - Nhóm tiện nghi nhị phân hóa riêng (has_wifi, has_pool, has_kitchen...) tách từ `amenities` → chuẩn bị cho luật kết hợp.
- **Biến đổi:**
  - Chuyển `price`, `host_response_rate`, `host_acceptance_rate` từ chuỗi (có ký hiệu $, %) sang số thực.
  - Chuẩn hóa/co giãn (`StandardScaler`/`MinMaxScaler`) cho `price`, `accommodates`, `bedrooms`... trước khi gom cụm bằng thuật toán dựa trên khoảng cách.
  - Mã hóa hạng mục `room_type`, `property_type` (one-hot, vì số lượng giá trị vừa phải).
  - Rời rạc hóa `price` thành các phân khúc (thấp/trung/cao) nếu dùng làm biến mục tiêu phân lớp.

---

# ĐỀ XUẤT 3: US Accidents (2016–2023)

## 1. Nguồn, giấy phép, quy mô

| Mục | Chi tiết |
|---|---|
| Đường dẫn | https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents |
| Đơn vị công bố | Sobhan Moosavi và cộng sự (nghiên cứu công bố tại hội nghị ACM SIGSPATIAL 2019), thu thập qua nhiều API giao thông thời gian thực |
| Ngày tải | *(ghi ngày nhóm thực tế tải xuống)* |
| Số bản ghi | ~7,7 triệu bản ghi toàn quốc (khuyến nghị nhóm lọc mẫu con vài trăm nghìn dòng theo 1–2 bang để dễ xử lý, vẫn đạt ≥ 10.000 bản ghi) |
| Số thuộc tính | 46 cột |
| Giấy phép | **Chỉ dùng cho mục đích nghiên cứu/học thuật phi thương mại** — Creative Commons Attribution-NonCommercial-ShareAlike 4.0 (CC BY-NC-SA 4.0) |
| Phạm vi thời gian | Tháng 2/2016 – tháng 3/2023, phủ 49 bang nước Mỹ |

## 2. Từ điển dữ liệu (rút gọn — các nhóm thuộc tính chính)

| Tên thuộc tính | Ý nghĩa | Kiểu | Thang đo | Miền giá trị |
|---|---|---|---|---|
| `ID` | Mã định danh vụ tai nạn | Chuỗi | Định danh | Duy nhất |
| `Severity` | Mức độ nghiêm trọng (ảnh hưởng đến giao thông) | Số nguyên | Thứ tự | 1 (nhẹ) – 4 (nghiêm trọng) |
| `Start_Time`, `End_Time` | Thời điểm bắt đầu/kết thúc ảnh hưởng | Ngày giờ | Khoảng | — |
| `Start_Lat`, `Start_Lng` | Tọa độ vị trí xảy ra | Số thực | Khoảng | Phạm vi lãnh thổ Mỹ |
| `City`, `County`, `State` | Đơn vị hành chính | Hạng mục | Định danh | — |
| `Temperature(F)` | Nhiệt độ tại thời điểm xảy ra | Số thực | Khoảng | Có thể âm |
| `Humidity(%)` | Độ ẩm | Số thực | Tỉ lệ | 0–100 |
| `Visibility(mi)` | Tầm nhìn | Số thực | Tỉ lệ | ≥ 0 |
| `Wind_Speed(mph)` | Tốc độ gió | Số thực | Tỉ lệ | ≥ 0 |
| `Precipitation(in)` | Lượng mưa | Số thực | Tỉ lệ | ≥ 0 |
| `Weather_Condition` | Điều kiện thời tiết mô tả | Hạng mục | Định danh | Clear, Rain, Snow, Fog... (~100+ giá trị) |
| `Amenity`, `Bump`, `Crossing`, `Give_Way`, `Junction`, `No_Exit`, `Railway`, `Roundabout`, `Station`, `Stop`, `Traffic_Calming`, `Traffic_Signal`, `Turning_Loop` | Đặc điểm hạ tầng đường tại vị trí tai nạn | Nhị phân | Định danh | True / False |
| `Sunrise_Sunset` | Trạng thái sáng/tối | Nhị phân | Định danh | Day / Night |
| `Distance(mi)` | Chiều dài đoạn đường bị ảnh hưởng | Số thực | Tỉ lệ | ≥ 0 |

*(Nhóm bổ sung đầy đủ 46 cột theo mô tả gốc của tác giả khi nộp bài.)*

## 3. Điều tra tri thức lĩnh vực

**Bối cảnh nghiệp vụ:** Dữ liệu mô tả các vụ tai nạn giao thông thu thập tự động từ cảm biến, camera giao thông, và báo cáo của cơ quan quản lý đường bộ Mỹ. Đây là bài toán an toàn giao thông — hiểu điều kiện nào (thời tiết, hạ tầng, thời điểm) làm tăng nguy cơ và mức độ nghiêm trọng của tai nạn.

**Ý nghĩa và liên hệ giữa các thuộc tính:**
- `Severity` (mức độ nghiêm trọng) có xu hướng liên hệ với điều kiện thời tiết xấu (`Weather_Condition` = Rain/Snow/Fog, `Visibility(mi)` thấp) và thời điểm (`Sunrise_Sunset` = Night thường nguy hiểm hơn).
- Các cờ hạ tầng nhị phân (`Junction`, `Traffic_Signal`, `Crossing`...) phản ánh đặc điểm điểm đen giao thông — giao lộ và đèn tín hiệu thường là nơi tai nạn tập trung nhưng mức độ nghiêm trọng có thể thấp hơn (do tốc độ thấp) so với đường cao tốc không giao lộ.
- Nhiệt độ, độ ẩm, gió, mưa là các yếu tố môi trường có tương tác với nhau (mưa lớn thường đi kèm độ ẩm cao, tầm nhìn giảm) — dữ liệu thời tiết tự động nên có tỉ lệ thiếu đáng kể, cần điều tra kỹ ở Bài 1.

**Lập luận khả năng khai phá:**

| Kỹ thuật | Có khả thi không? | Vì sao |
|---|---|---|
| Phân lớp | **Có** | `Severity` là biến mục tiêu rời rạc, thứ tự, có ý nghĩa thực tế rõ ràng (dự đoán mức độ nghiêm trọng giúp phân bổ nguồn lực cứu hộ). |
| Luật kết hợp | **Có** | Sau khi rời rạc hóa các thuộc tính số (nhiệt độ, tầm nhìn theo khoảng) và kết hợp với các cờ nhị phân hạ tầng/thời tiết, mỗi vụ tai nạn trở thành một "giỏ điều kiện" — có thể khai phá luật kiểu "Rain + Night + Junction → Severity cao". |
| Gom cụm | **Có** | Có thể phân nhóm tai nạn theo đặc điểm địa lý — thời tiết — hạ tầng để nhận diện các "hồ sơ tai nạn" điển hình (ví dụ: cụm tai nạn đô thị giờ cao điểm, cụm tai nạn cao tốc trời xấu ban đêm...). |

## 4. Đề xuất 2 kỹ thuật sẽ thực hiện trên bộ này

→ **Phân lớp** (dự đoán mức độ nghiêm trọng `Severity`) và **Luật kết hợp** (điều kiện thời tiết/hạ tầng đi cùng tai nạn nghiêm trọng).
*(Gom cụm hồ sơ tai nạn để dành làm phương án dự phòng/mở rộng.)*

## 5. Điều tra tiền xử lý (Mục 3.3)

> Các con số cụ thể dưới đây là **giả thuyết cần kiểm chứng** trên dữ liệu thật nhóm tải về — không dùng làm số liệu báo cáo cuối cùng.

### 5.1 Nhiễu và ngoại lai

| Thuộc tính | Cách phát hiện | Nghi vấn cần kiểm tra | Quyết định dự kiến |
|---|---|---|---|
| `Temperature(F)`, `Wind_Chill(F)` | IQR/boxplot theo mùa/bang | Giá trị cực đoan phi vật lý (ví dụ nhiệt độ vài trăm độ do lỗi cảm biến) | Loại các giá trị ngoài khoảng vật lý hợp lý (ví dụ ngoài [-60°F, 130°F] cho lục địa Mỹ) |
| `Visibility(mi)` | Thống kê mô tả | Giá trị âm hoặc bằng 0 kéo dài bất thường (có thể lỗi cảm biến khác với sương mù dày thật) | Đối chiếu với `Weather_Condition` (nếu ghi "Fog/Heavy Fog" thì hợp lý, nếu "Clear" mà Visibility = 0 thì là lỗi) |
| `Distance(mi)` | IQR theo `Severity` | Đoạn đường ảnh hưởng dài bất thường (hàng chục dặm) cho một vụ tai nạn đơn lẻ | Kiểm tra xem có phải lỗi trùng lặp bản ghi hoặc lỗi đo — cân nhắc loại nếu vượt ngưỡng phi thực tế |
| `Start_Time`/`End_Time` | Kiểm tra logic | Thời gian kết thúc trước thời gian bắt đầu | Loại các dòng vi phạm ràng buộc logic thời gian |

### 5.2 Giá trị thiếu

| Thuộc tính | Cơ chế thiếu dự kiến | Cách xử lý dự kiến |
|---|---|---|
| Các cột thời tiết (`Temperature(F)`, `Humidity(%)`, `Wind_Speed(mph)`, `Precipitation(in)`...) | Có hệ thống — dữ liệu lấy từ trạm thời tiết gần nhất, một số khu vực/thời điểm trạm không ghi nhận | Điền theo trung vị của cùng trạm thời tiết gần nhất (`Airport_Code`) và cùng khung thời gian, hoặc loại nếu tỉ lệ thiếu quá lớn ở một số cột phụ |
| `Precipitation(in)` | Có thể vừa hệ thống (trạm không đo được) vừa có ý nghĩa thật (= 0 khi trời không mưa) | Cẩn trọng: phân biệt "thiếu do không đo" và "= 0 vì không mưa" trước khi điền, tránh làm sai lệch bản chất dữ liệu |
| `Wind_Chill(F)` | Có hệ thống — chỉ tính khi nhiệt độ đủ thấp | Không điền tùy tiện — có thể tạo cờ nhị phân "có áp dụng gió lạnh" thay vì điền số |
| `City`, `Zipcode` (một phần nhỏ) | Ngẫu nhiên — lỗi geocode | Loại các dòng thiếu định danh vị trí nếu cần phân tích theo khu vực |

### 5.3 Thêm / xóa / biến đổi thuộc tính

- **Xóa:** `Description` (văn bản tự do, không dùng nếu không làm NLP); `Country` (hằng số — luôn là US, không mang thông tin phân biệt); các cột trùng lặp thông tin thời gian ánh sáng (`Civil_Twilight`, `Nautical_Twilight`, `Astronomical_Twilight`) nếu nhóm chỉ cần `Sunrise_Sunset` làm đại diện.
- **Thêm (feature engineering):**
  - `Hour_of_Day`, `Day_of_Week` tách từ `Start_Time` → phục vụ cả phân lớp (giờ cao điểm) và luật kết hợp (rời rạc hóa theo khung giờ).
  - `Is_Weekend` (nhị phân) từ `Day_of_Week`.
  - `Infra_Feature_Count` = tổng số cờ hạ tầng = True (Junction, Crossing, Traffic_Signal...) trên mỗi vụ → đặc trưng số cho gom cụm.
  - `Weather_Severity_Group` gộp `Weather_Condition` thành nhóm lớn (Clear / Rain / Snow / Fog / Other) — giảm số lượng giá trị hạng mục quá nhiều (~100+) trước khi mã hóa.
- **Biến đổi:**
  - Rời rạc hóa các thuộc tính số liên tục (`Temperature(F)`, `Visibility(mi)`, `Wind_Speed(mph)`) thành khoảng (thấp/trung bình/cao) để phục vụ luật kết hợp.
  - Chuẩn hóa/co giãn các thuộc tính số trước khi gom cụm dựa trên khoảng cách.
  - Mã hóa các cờ hạ tầng nhị phân (True/False → 1/0) — đã sẵn dạng phù hợp cho luật kết hợp.
  - Giảm chiều/gộp nhóm `Weather_Condition` (như trên) để tránh bùng nổ số chiều khi one-hot encode.

---

## Bảng tổng hợp — Ma trận bộ dữ liệu × kỹ thuật (Mục 2.1)

| Bộ dữ liệu | Phân lớp | Luật kết hợp | Gom cụm |
|---|:---:|:---:|:---:|
| Bộ 1: Olist E-Commerce | ✓ (dự phòng) | ✓ (chọn) | ✓ (chọn) |
| Bộ 2: Inside Airbnb Listings | ✓ (chọn) | ✓ (dự phòng) | ✓ (chọn) |
| Bộ 3: US Accidents | ✓ (chọn) | ✓ (chọn) | ✓ (dự phòng) |

→ Cả ba cột đều có ít nhất một dấu ✓ "chọn" — đáp ứng yêu cầu **phủ đủ cả ba kỹ thuật** ở Mục 2 của hướng dẫn bài tập. Mỗi bộ đều làm được ≥ 2 kỹ thuật, dư so với yêu cầu tối thiểu.

---

## Ghi chú khi hoàn thiện bài nộp

1. Đây là bản đề xuất khung — nhóm cần **tự tải dữ liệu thật**, xác nhận lại chính xác số bản ghi/số thuộc tính, và bổ sung từ điển dữ liệu đầy đủ 100% (không chỉ bản rút gọn ở trên).
2. Phần "Điều tra tri thức lĩnh vực" nên được viết lại bằng ngôn ngữ và cách hiểu của chính nhóm sau khi đã thực sự khảo sát dữ liệu — tránh sao chép nguyên văn, vì đây là phần chấm điểm cốt lõi của Bài 1.
3. Trước khi nộp, kiểm tra lại giấy phép và trích dẫn nguồn đầy đủ (đặc biệt bộ US Accidents chỉ được dùng phi thương mại — cần ghi rõ trong báo cáo).
4. **Mục 5 (Điều tra tiền xử lý)** của mỗi đề xuất chỉ nêu **giả thuyết và kế hoạch khảo sát** dựa trên hiểu biết chung về từng loại dữ liệu. Nhóm bắt buộc phải chạy khảo sát thật (tính tỉ lệ thiếu chính xác theo từng cột, vẽ boxplot/IQR thật, thống kê số lượng ngoại lai cụ thể) trên dữ liệu đã tải về, rồi thay các nhận định giả thuyết này bằng số liệu và kết luận thật của nhóm — đây là yêu cầu bắt buộc của Mục 3.3, không được giữ nguyên các giả thuyết trong tài liệu này khi nộp bài.