# 📊 Hướng Dẫn 6 Bước Xử Lý Dữ Liệu (Data Mining Project)

## 👥 Phân công nhóm 6 người

| Thứ tự | Notebook | Người làm | Trạng thái |
|--------|----------|-----------|-----------|
| 1️⃣ | `01_data_integration.ipynb` | Ngân | Hoàn thành |
| 2️⃣ | `02_eda_exploration.ipynb` | Thư | Hoàn thành |
| 3️⃣ | `03_missing_duplicates.ipynb` | Sang | Chưa xong |
| 4️⃣ | `04_outliers_handling.ipynb` | Phúc | Chưa xong |
| 5️⃣ | `05_data_encoding.ipynb` | Chiến | Chưa xong |
| 6️⃣ | `06_feature_rfm.ipynb` | Hoàng | Chưa xong |

---

## 📋 QUYẾT ĐỊ CHUNG CHO TẤT CẢ THÀNH VIÊN

### ⚠️ Quy tắc Bắt Buộc
- **Làm việc trên branch riêng**, không sửa trực tiếp `main`
- **Mỗi người tạo đúng 1 notebook** theo tên được quy định
- **Dùng đường dẫn tương đối** (`../data/...`), không dùng đường dẫn máy cá nhân
- **Không đưa dữ liệu** trong `data/raw/` lên GitHub
- **Chỉ xử lý phần được giao**, không tự ý làm thay phần của thành viên khác
- Notebook phải có cấu trúc: **Mục tiêu → Kiểm tra → Xử lý → Kiểm tra sau → Nhận xét → Xuất file**
- **Trước khi làm**: `git pull` để lấy phiên bản mới nhất
- **Sau khi hoàn thành**: Chạy lại toàn bộ notebook để chắc chắn không có lỗi

### 🚀 Quy trình Submit Công Việc
1. Lưu notebook
2. Chạy lại toàn bộ cells từ đầu đến cuối (Kernel → Restart & Run All)
3. Kiểm tra output files được tạo
4. Commit: `git add notebooks/0X_*.ipynb && git commit -m "Message"`
5. Push: `git push -u origin feature/branch-name`
6. Tạo **Pull Request** để trưởng nhóm kiểm tra

---

## � CHUỖI DỮ LIỆU & QƯỸ TRÌNH LƯU CHUYỂN

Kết quả của người trước là **input của người sau**. **Phải tuân theo thứ tự**:

```
4 file CSV gốc
      ↓
[TV1] Data Integration
      ↓ Output: step1_merged.csv
      ↓
[TV2] EDA Exploration (không tạo file mới)
      ↓
[TV3] Data Cleaning (Missing + Duplicates)
      ↓ Output: step3_cleaned.csv
      ↓
[TV4] Outliers Handling
      ↓ Output: step4_nooutliers.csv
      ↓
[TV5] Data Encoding (Type Conversion + Encoding)
      ↓ Output: step5_encoded.csv
      ↓
[TV6] Feature RFM (Recency + Frequency + Monetary)
      ↓ Output: olist_cleaned.csv
      ↓
✅ Dataset RFM Sẵn Sàng Cho Phân Tích & Modeling
```

### ⏰ Có thể làm song song không?
**Có thể**, nhưng chỉ ở mức độ **chuẩn bị & nghiên cứu**:
- TV3 có thể viết code khung khi TV2 chưa xong, nhưng **phải chờ** `step1_merged.csv` để chạy
- TV4 có thể chuẩn bị code IQR, nhưng **phải chờ** `step3_cleaned.csv`
- TV5 & TV6 tương tự

**Không được:** Cùng lúc tạo kết quả cuối (output files) → sẽ gây conflict

---

## �📋 Tổng Quan Quy Trình

```
Raw Data 
   ↓
[01] Data Integration (Tích hợp 4 bảng)
   ↓ (output: step1_merged.csv)
[02] EDA Exploration (Khám phá & phân tích)
   ↓ (không có output file)
[03] Data Cleaning ⭐ CẦN LÀM (Missing + Duplicates)
   ↓ (output: step3_cleaned.csv)
[04] Outliers Handling ⭐ CẦN LÀM (IQR + xử lý ngoại lai)
   ↓ (output: step4_nooutliers.csv)
[05] Data Encoding ⭐ CẦN LÀM (Type conversion + encoding)
   ↓ (output: step5_encoded.csv)
[06] Feature RFM ⭐ CẦN LÀM (Recency + Frequency + Monetary)
   ↓ (output: olist_cleaned.csv)
Final Clean Dataset
```

---

# 🔧 CHI TIẾT 4 BƯỚC CÒN LẠI

---

## **BƯỚC 3️⃣: DATA CLEANING (Missing + Duplicates)**
**📝 Notebook:** `03_missing_duplicates.ipynb`  
**👤 Người làm:** Người 1

### 🎯 Mục tiêu
- Xử lý dữ liệu thiếu (missing values)
- Loại bỏ dữ liệu trùng lặp (duplicates)
- Kiểm tra dữ liệu sau xử lý
- Xuất dataset cho bước xử lý ngoại lai

### 📥 Input
- File: `data/processed/step1_merged.csv`

### 📋 Các tác vụ cần làm

#### 1. **Xử lý Missing Values (Dữ liệu thiếu)**
```python
# Kiểm tra dữ liệu thiếu
missing = df.isna().sum()
missing_percent = (df.isna().mean() * 100)

# Tạo summary để phân tích
missing_summary = pd.DataFrame({
    'missing_count': df.isna().sum(),
    'missing_percent': df.isna().mean() * 100
})
print(missing_summary[missing_summary['missing_count'] > 0]
      .sort_values('missing_percent', ascending=False))

# QUAN TRỌNG: Không xử lý máy móc!
# Tùy theo cột:
# - Nếu < 5% thiếu & dữ liệu không thể thay thế: DROP dòng
# - Nếu 5-20% thiếu & cột số: FILL bằng median/mean
# - Nếu > 20% thiếu: Xem xét DROP cột
# - Cột phân loại: Cân nhắc kỹ trước khi xử lý

# Ví dụ:
df = df.dropna(subset=['product_weight_g', 'product_height_cm'])  # Drop dòng có NULL
df['product_category_name'] = df['product_category_name'].fillna('Unknown')
df['product_description_length'] = df['product_description_length'].fillna(df['product_description_length'].median())

# PHẢI GHI RÕ LÝ DO: Tại sao chọn cách xử lý này?
# Ví dụ: "Xóa dòng thiếu product_weight_g vì cần thông tin này cho phân tích"
```

#### 2. **Loại bỏ Duplicates (Dữ liệu trùng)**
```python
# Kiểm tra
dup_count = df.duplicated().sum()
print(f"Số dòng trùng lặp hoàn toàn: {dup_count}")

# ⚠️ CẢNH BÁO: order_id lặp lại là BÌNH THƯỜNG
# Vì một đơn hàng có thể có nhiều sản phẩm
# Chỉ xóa nếu toàn bộ cột giống nhau
df = df.drop_duplicates()

# Kiểm tra lại
print("Duplicate còn lại:", df.duplicated().sum())
```

#### 3. **Xử lý Outliers (Ngoại lai)**
```python
import numpy as np

# Phương pháp 1: IQR (Interquartile Range)
Q1 = df['price'].quantile(0.25)
Q3 = df['price'].quantile(0.75)
IQR = Q3 - Q1
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

print(f"Ngưỡng dưới: {lower_bound}, Ngưỡng trên: {upper_bound}")
outliers = df[(df['price'] < lower_bound) | (df['price'] > upper_bound)]
print(f"Số ngoại lai: {len(outliers)} ({len(outliers)/len(df)*100:.2f}%)")

# ⚠️ CẢNH BÁO: Giá cao không phải lúc nào cũng là ngoại lai!
# Cần giải thích tại sao loại bỏ
# Nếu loại bỏ:
df = df[(df['price'] >= lower_bound) & (df['price'] <= upper_bound)]

# Phương pháp 2: Z-score (cho dữ liệu phân bố chuẩn)
from scipy import stats
z_scores = np.abs(stats.zscore(df['price']))
df = df[z_scores < 3]
```

#### 4. **Kiểm tra Dữ liệu Không Hợp Lệ**
```python
# Giá không được âm
df = df[df['price'] >= 0]
df = df[df['freight_value'] >= 0]

# Số lượng phải dương
df = df[df['order_item_id'] > 0]
```

### 📤 Output
- File: `data/processed/step3_cleaned.csv`
- Lưu bằng: `df.to_csv('../data/processed/step3_cleaned.csv', index=False)`

### ✅ Kiểm tra (Checklist)
- [ ] Xử lý hết missing values (ghi rõ lý do)
- [ ] Loại bỏ duplicates hoàn toàn
- [ ] Kiểm tra kích thước trước/sau xử lý
- [ ] Có nhận xét tóm tắt
- [ ] Lưu file output với tên đúng
- [ ] Chạy lại notebook không có lỗi

⚠️ **LƯU Ý:** Không tự ý xử lý outlier hoặc encoding ở bước này!

---

## **BƯỚC 4️⃣: XỬ LÝ NGOẠI LAI (Outliers)**
**📝 Notebook:** `04_outliers_handling.ipynb`  
**👤 Người làm:** Người 2

### 🎯 Mục tiêu
- Xác định các biến số cần kiểm tra ngoại lai
- Sử dụng phương pháp IQR
- Xác định ngưỡng dưới và ngưỡng trên
- Đánh giá số lượng ngoại lai
- Xử lý ngoại lai theo quy tắc đã chọn

### 📥 Input
- File: `data/processed/step3_cleaned.csv`

### 📋 Các tác vụ cần làm

#### 1. **Chọn Biến Cần Kiểm tra**
```python
# Ưu tiên các biến số liên quan đến phân tích thương mại điện tử
numeric_cols = ['price', 'freight_value']
print(df[numeric_cols].describe())

# ⚠️ QUAN TRỌNG: Không áp dụng IQR máy móc cho mọi cột số
```

#### 2. **Hàm Tính IQR**
```python
def iqr_bounds(series):
    Q1 = series.quantile(0.25)
    Q3 = series.quantile(0.75)
    IQR = Q3 - Q1
    lower = Q1 - 1.5 * IQR
    upper = Q3 + 1.5 * IQR
    return Q1, Q3, IQR, lower, upper
```

#### 3. **Kiểm Tra Outliers (Ví dụ với price)**
```python
Q1, Q3, IQR, lower_bound, upper_bound = iqr_bounds(df['price'])

print("Q1:", Q1)
print("Q3:", Q3)
print("IQR:", IQR)
print("Ngưỡng dưới:", lower_bound)
print("Ngưỡng trên:", upper_bound)

outliers = df[(df['price'] < lower_bound) | (df['price'] > upper_bound)]
print(f"Số dòng ngoại lai: {len(outliers)} ({len(outliers)/len(df)*100:.2f}%)")
```

#### 4. **Vẽ Boxplot Trước Xử Lý**
```python
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure(figsize=(10, 4))
sns.boxplot(x=df['price'])
plt.title('Boxplot price trước xử lý ngoại lai')
plt.xlabel('Price')
plt.show()
```

#### 5. **Xử Lý Outliers**
```python
# ⚠️ CẢNH BÁO: Giá cao không phải lúc nào cũng là ngoại lai!
# Cần giải thích tại sao loại bỏ. Phải ghi rõ LÝ DO trong Markdown

# Nếu nhóm quyết định loại các dòng ngoại lai:
df_cleaned = df[
    (df['price'] >= lower_bound) &
    (df['price'] <= upper_bound)
].copy()

print("Kích thước trước:", df.shape)
print("Kích thước sau:", df_cleaned.shape)
```

#### 6. **Kiểm Tra Sau Xử Lý**
```python
print("Số ngoại lai còn lại:", (
    (df_cleaned['price'] < lower_bound) |
    (df_cleaned['price'] > upper_bound)
).sum())
```

### 📤 Output
- File: `data/processed/step4_nooutliers.csv`
- Lưu bằng: `df.to_csv('../data/processed/step4_nooutliers.csv', index=False)`

### ✅ Kiểm tra (Checklist)
- [ ] Xác định các biến số cần kiểm tra
- [ ] Tính Q1, Q3, IQR, ngưỡng dưới/trên
- [ ] Đếm số lượng & tỷ lệ ngoại lai
- [ ] Vẽ Boxplot trước xử lý
- [ ] Ghi rõ LÝ DO xử lý outliers
- [ ] Kiểm tra kích thước trước/sau
- [ ] Lưu file output với tên đúng
- [ ] Chạy lại notebook không có lỗi

⚠️ **LƯU Ý:** Không tự ý xử lý encoding hoặc RFM ở bước này!

---

## **BƯỚC 5️⃣: CHUẨN HÓA KIỂU DỮ LIỆU & MÃ HÓA**
**📝 Notebook:** `05_data_encoding.ipynb`  
**👤 Người làm:** Người 3

### 🎯 Mục tiêu
- Kiểm tra kiểu dữ liệu
- Chuyển cột thời gian sang kiểu datetime
- Kiểm tra dữ liệu sau chuyển đổi
- Xác định các biến phân loại cần mã hóa
- Thực hiện One-Hot Encoding khi phù hợp
- Xuất dataset cho bước tạo đặc trưng

### 📥 Input
- File: `data/processed/step4_nooutliers.csv`

### 📋 Các tác vụ cần làm

#### 1. **Kiểm Tra Kiểu Dữ Liệu**
```python
df.info()

# Đặc biệt kiểm tra
print(df['order_purchase_timestamp'].dtype)
```

#### 2. **Chuyển Đổi Thời Gian**
```python
df['order_purchase_timestamp'] = pd.to_datetime(
    df['order_purchase_timestamp'],
    errors='coerce'
)

# Kiểm tra kết quả
print(df['order_purchase_timestamp'].dtype)  # Phải là datetime64[ns]

# Kiểm tra có giá trị invalid không
print("Số giá trị thời gian không hợp lệ:", df['order_purchase_timestamp'].isna().sum())
```

#### 3. **Kiểm Tra Biến Phân Loại**
```python
categorical_cols = ['customer_state', 'order_status']

for col in categorical_cols:
    print(f"\n{col}:")
    print(df[col].value_counts().head(10))
```

#### 4. **Encoding Biến Phân Loại**
```python
# ⚠️ QUAN TRỌNG: Không mã hóa máy móc tất cả các cột chữ

# One-Hot Encoding (cho biến có ít giá trị)
df_encoded = pd.get_dummies(df, columns=['customer_state'], drop_first=True)

# Label Encoding (cho biến có nhiều giá trị)
from sklearn.preprocessing import LabelEncoder
le = LabelEncoder()
df_encoded['product_category_encoded'] = le.fit_transform(df_encoded['product_category_name'])

# Hoặc dùng map
state_mapping = {state: idx for idx, state in enumerate(df_encoded['customer_state'].unique())}
df_encoded['customer_state_encoded'] = df_encoded['customer_state'].map(state_mapping)
```

#### 5. **Kiểm Tra Sau Encoding**
```python
print(df_encoded.info())
print(df_encoded.head())

# Kiểm tra số cột trước/sau
print("Số cột trước:", df.shape[1])
print("Số cột sau:", df_encoded.shape[1])

# ⚠️ LƯU Ý: product_category_name có rất nhiều danh mục
# Không tự động One-Hot nếu số lượng cột tăng quá lớn
# Ghi nhận và trao đổi với trưởng nhóm nếu cần
```

### 📤 Output
- File: `data/processed/step5_encoded.csv`
- Lưu bằng: `df.to_csv('../data/processed/step5_encoded.csv', index=False)`

### ✅ Kiểm tra (Checklist)
- [ ] Kiểm tra kiểu dữ liệu tất cả cột
- [ ] Chuyển đổi datetime thành datetime64[ns]
- [ ] Kiểm tra không có giá trị datetime invalid
- [ ] Xác định biến phân loại cần encoding
- [ ] Thực hiện One-Hot hoặc Label Encoding
- [ ] Kiểm tra số cột trước/sau encoding
- [ ] Ghi rõ lý do không encoding những cột có quá nhiều nhóm
- [ ] Lưu file output với tên đúng
- [ ] Chạy lại notebook không có lỗi

⚠️ **LƯU Ý:** Không tự ý tạo RFM features ở bước này!

---

## **BƯỚC 6️⃣: TẠO ĐẶC TRƯNG RFM**
**📝 Notebook:** `06_feature_rfm.ipynb`  
**👤 Người làm:** Người 4

### 🎯 Mục tiêu
Tạo ba chỉ số RFM:
- **Recency** (R): Thời gian từ lần mua gần nhất của khách hàng (số ngày)
- **Frequency** (F): Số lần khách hàng mua hàng (tần suất)
- **Monetary** (M): Tổng giá trị mua hàng của khách hàng (tổng tiền)

Tạo dataset RFM cuối cùng sẵn sàng cho giai đoạn khai phá/phân tích tiếp theo

### 📥 Input
- File: `data/processed/step5_encoded.csv`

### 📋 Các tác vụ cần làm

#### 1. **Nạp Dữ Liệu & Chuẩn Bị**
```python
import pandas as pd
import numpy as np

df = pd.read_csv('../data/processed/step5_encoded.csv')

# Đảm bảo cột thời gian là datetime
df['order_purchase_timestamp'] = pd.to_datetime(
    df['order_purchase_timestamp'],
    errors='coerce'
)

print("Kích thước:", df.shape)
```

#### 2. **Xác Định Ngày Tham Chiếu**
```python
# Ngày tham chiếu = ngày mua gần nhất + 1 ngày
snapshot_date = (
    df['order_purchase_timestamp'].max()
    + pd.Timedelta(days=1)
)

print("Ngày tham chiếu:", snapshot_date)
```

#### 3. **Kiểm Tra Số Lượng Khách Hàng & Đơn Hàng**
```python
print("Số khách hàng:", df['customer_unique_id'].nunique())
print("Số đơn hàng:", df['order_id'].nunique())
```

#### 4. **Tính RFM**
```python
# Recency: thời gian từ lần mua gần nhất (số ngày)
# Frequency: số lần mua
# Monetary: tổng giá trị mua

rfm = df.groupby('customer_unique_id').agg(
    Recency=(
        'order_purchase_timestamp',
        lambda x: (snapshot_date - x.max()).days
    ),
    Frequency=(
        'order_id',
        'nunique'
    ),
    Monetary=(
        'price',
        'sum'
    )
).reset_index()

print(rfm.head())
print(rfm.shape)
```

#### 5. **Kiểm Tra RFM**
```python
# Thống kê mô tả
print(rfm[['Recency', 'Frequency', 'Monetary']].describe())

# Kiểm tra missing values
print(rfm.isna().sum())

# Kiểm tra giá trị bất thường
print("Recency nhỏ nhất:", rfm['Recency'].min())
print("Frequency nhỏ nhất:", rfm['Frequency'].min())
print("Monetary nhỏ nhất:", rfm['Monetary'].min())

# Ý nghĩa:
# - Recency càng nhỏ → khách hàng càng mua gần đây (tốt)
# - Frequency càng lớn → khách hàng mua nhiều lần (tốt)
# - Monetary càng lớn → tổng chi tiêu cao (tốt)
```

#### 6. **Xuất Dataset RFM Cuối Cùng**
```python
rfm.to_csv(
    '../data/processed/olist_cleaned.csv',
    index=False
)

print("✅ Đã tạo olist_cleaned.csv")
```

### 📤 Output
- File: `data/processed/olist_cleaned.csv`
- Lưu bằng: `rfm.to_csv('../data/processed/olist_cleaned.csv', index=False)`

### ✅ Kiểm tra (Checklist)
- [ ] Nạp dữ liệu & chuyển đổi datetime
- [ ] Xác định ngày tham chiếu (snapshot_date)
- [ ] Kiểm tra số khách hàng & đơn hàng
- [ ] Tính toán Recency, Frequency, Monetary
- [ ] Thống kê RFM (mô tả, missing, min max)
- [ ] Ghi rõ ý nghĩa của mỗi chỉ số
- [ ] Lưu file output với tên `olist_cleaned.csv`
- [ ] Chạy lại notebook không có lỗi

📌 **Dataset này sẵn sàng cho các bước khai phá dữ liệu/phân tích tiếp theo!**

---

## 📌 HƯỚNG DẪN CHUNG CHO TẤT CẢ

### ✅ Cấu trúc Notebook Tiêu chuẩn
Mỗi notebook nên có cấu trúc:
```
1. Cell Markdown: Tiêu đề & Mục tiêu
2. Cell Python: Import libraries
3. Cells Python: Nạp dữ liệu input
4. Cells Python: Xử lý dữ liệu (core logic)
5. Cell Markdown: Kết luận
6. Cell Python: Lưu output file
```

### 📚 Libraries Cần Sử dụng
```python
# Chuẩn
import pandas as pd
import numpy as np

# Xử lý dữ liệu
from sklearn.preprocessing import StandardScaler, MinMaxScaler, LabelEncoder

# Visualization (nếu cần)
import matplotlib.pyplot as plt
import seaborn as sns
```

### 📂 Quy ước Tên File
```
Bước 1 (Output): step1_merged.csv
Bước 3 (Output): step3_cleaned.csv
Bước 4 (Output): step4_nooutliers.csv
Bước 5 (Output): step5_encoded.csv
Bước 6 (Output): olist_cleaned.csv (Dataset cuối cùng)
```

### 🚀 Cách Chạy Notebook
```bash
# Bước 1: Kích hoạt virtual environment (nếu chưa kích hoạt)
cd c:\Panh\data_mining_project
.\venv\Scripts\Activate.ps1

# Bước 2: Mở notebook trong VS Code
code notebooks/03_missing_duplicates.ipynb     # Hoặc 04, 05, 06

# Bước 3: Chạy cells lần lượt (Shift + Enter)

# Bước 4: Kiểm tra output file được tạo
ls data/processed/
```

---

## 🌿 QUI TRÌ GIT & BRANCH

### Tạo Branch Riêng
Mỗi thành viên **tạo branch riêng** từ `main`:

```bash
# Cập nhật code mới nhất
git pull origin main

# Tạo branch mới
git checkout -b feature/missing-duplicates       # Người 1
git checkout -b feature/outliers                 # Người 2
git checkout -b feature/encoding                 # Người 3
git checkout -b feature/rfm                      # Người 4
```

### Commit & Push
```bash
# Sau khi hoàn thành và kiểm tra không có lỗi:
git add notebooks/03_missing_duplicates.ipynb
git add data/processed/step3_cleaned.csv
git commit -m "Add data cleaning and duplicate handling"
git push -u origin feature/data-cleaning

# Tương tự cho các bước khác thay notebook name & branch name
# Lưu ý: Không push data/raw/ vào GitHub
```

### Tạo Pull Request
1. Vào GitHub repo → Pull requests
2. Click "New Pull Request"
3. Chọn branch của bạn → main
4. Đợi trưởng nhóm review & merge

---

## ✅ CHECKLIST HOÀN THÀNH (Bắt Buộc)

Mỗi thành viên **PHẢI** kiểm tra trước khi báo hoàn thành:

- [ ] **Tên Notebook đúng** (`03_missing_duplicates.ipynb`, `04_outliers_handling.ipynb`, `05_data_encoding.ipynb`, `06_feature_rfm.ipynb`)
- [ ] **Input file đúng** (từ output của người trước)
- [ ] **Output file đúng** (theo quy ước tên)
- [ ] **Notebook chạy không lỗi** (Kernel → Restart & Run All)
- [ ] **Có giải thích** từng bước xử lý (Markdown)
- [ ] **Có so sánh** kích thước trước/sau xử lý
- [ ] **Có nhận xét cuối** (tóm tắt những gì đã làm)
- [ ] **Không dùng đường dẫn tuyệt đối** (chỉ dùng `../data/...`)
- [ ] **Không push `data/raw/`** lên GitHub
- [ ] **Đã commit & push** branch của mình
- [ ] **Đã tạo Pull Request** để trưởng nhóm kiểm tra

---

## 📅 TIMELINE THỰC HIỆN ĐỀ XUẤT

Nếu nhóm cần hoàn thành trong **5 ngày**:

| Ngày | Công Việc |
|------|-----------|
| **Ngày 1** | TV1 & TV2 hoàn thành; TV3 chuẩn bị code & chờ file |
| **Ngày 2** | TV3 hoàn thành missing_duplicates; TV4 bắt đầu |
| **Ngày 3** | TV4 hoàn thành outliers; TV5 bắt đầu |
| **Ngày 4** | TV5 hoàn thành encoding; TV6 bắt đầu |
| **Ngày 5** | TV6 hoàn thành RFM; Trưởng nhóm kiểm tra toàn bộ |

### Lưu ý quan trọng:
- **Trưởng nhóm** dành ngày cuối để:
  1. Chạy lại toàn bộ 6 notebook **theo đúng thứ tự** (01 → 06)
  2. Kiểm tra tên file, đường dẫn, kích thước dữ liệu
  3. Xác nhận kết quả cuối cùng (olist_cleaned.csv) có ý nghĩa
  4. Review Pull Request trước khi merge

---

## 🎯 NHỮNG LƯỚI TRÁNH THƯỜNG GẶP

### ❌ KHÔNG NÊN LÀM
```
- Xử lý dữ liệu một cách máy móc mà không giải thích lý do
- Xóa dữ liệu chỉ vì nó là outlier mà không cân nhắc
- Sử dụng đường dẫn máy cá nhân (C:\Users\TenMay\...)
- Tự ý làm thêm phần của thành viên khác
- Chạy notebook trên venv người khác (mỗi người venv riêng)
- Push toàn bộ data lên GitHub
- Commit trực tiếp vào main mà không merge từ PR
- Không kiểm tra lỗi trước khi push
```

### ✅ NÊN LÀM
```
- Luôn ghi rõ LÝ DO xử lý dữ liệu
- So sánh kích thước trước/sau mỗi bước
- Dùng đường dẫn tương đối (../data/processed/...)
- Chỉ xử lý phần được giao
- Sử dụng virtual environment chung của dự án
- Chỉ push dữ liệu processed, không raw
- Luôn tạo PR cho trưởng nhóm review
- Chạy Restart & Run All trước khi push
```

--
