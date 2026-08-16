# Data Mining Project – Olist E-Commerce

Dự án môn **Khai phá dữ liệu (Data Mining)**, thực hiện quy trình khai phá dữ liệu từ **Brazilian E-Commerce Public Dataset by Olist** sử dụng Python.

## Mục tiêu

Xây dựng quy trình **khai phá dữ liệu hoàn chỉnh** từ dữ liệu thô đến insights kinh doanh:

```text
Dữ liệu thô
    ↓
[GIAI ĐOẠN 1] Tiền Xử Lý (6 bước)
├─ 01. Tích hợp dữ liệu
├─ 02. Khám phá dữ liệu (EDA)
├─ 03. Xử lý thiếu & trùng
├─ 04. Xử lý ngoại lai
├─ 05. Chuẩn hóa & mã hóa
└─ 06. Tạo RFM features
    ↓ (output: olist_cleaned.csv)
[GIAI ĐOẠN 2] Phân Cụm (Clustering)
├─ K-Means
├─ Customer Segmentation
└─ RFM Analysis
    ↓
[GIAI ĐOẠN 3] Phân Loại (Classification)
├─ Decision Tree / Random Forest
├─ Logistic Regression
└─ Model Evaluation
    ↓
[GIAI ĐOẠN 4] Luật Kết Hợp (Association Rules)
├─ Apriori / Eclat
├─ Market Basket Analysis
└─ Product Recommendations
    ↓
Insights & Business Decisions
```

---

# Công cụ sử dụng

* **Python**
* **Visual Studio Code**
* **Jupyter Notebook**
* **Git + GitHub**

### Thư viện Python

```text
# Data Processing
pandas
numpy

# Visualization
matplotlib
seaborn

# Machine Learning
scikit-learn
mlxtend (Association Rules)

# Jupyter
ipykernel
jupyter
```

Các thư viện được quản lý trong `requirements.txt`.

---

# Cấu trúc Project

```text
data_mining_project/
│
├── data/
│   ├── raw/                    # Dữ liệu thô ban đầu
│   └── processed/              # Dữ liệu sau xử lý
│
├── notebooks/
│   ├── 01_data_integration.ipynb        # Tích hợp dữ liệu
│   ├── 02_eda_exploration.ipynb         # Khám phá dữ liệu
│   ├── 03_missing_duplicates.ipynb      # Xử lý thiếu/trùng
│   ├── 04_outliers_handling.ipynb       # Xử lý ngoại lai
│   ├── 05_data_encoding.ipynb           # Mã hóa dữ liệu
│   ├── 06_feature_rfm.ipynb             # Tạo feature RFM
│   │
│   ├── 07_clustering_analysis.ipynb     # Phân cụm khách hàng
│   ├── 08_classification_models.ipynb   # Mô hình phân loại
│   └── 09_association_rules.ipynb       # Luật kết hợp
│
├── .gitignore
├── README.md
├── requirements.txt
└── HUONG_DAN_6_BUOC.md         # Hướng dẫn chi tiết tiền xử lý
```

**`data/raw/`** – Dữ liệu thô gốc từ Olist (4 file CSV)  
**`data/processed/`** – Dữ liệu sau từng bước xử lý  
**`notebooks/`** – Tất cả Jupyter Notebook cho 4 giai đoạn khai phá

---

# Hướng dẫn cài đặt

## 1. Cài Python

Kiểm tra Python:

```powershell
python --version
```

Nếu chưa có Python, tải tại:

https://www.python.org/

---

## 2. Cài Git

Kiểm tra:

```powershell
git --version
```

Nếu chưa có Git, tải tại:

https://git-scm.com/

---

# 3. Clone project

Mở Terminal/PowerShell tại thư mục muốn lưu project:

```powershell
git clone https://github.com/Banh-ntat/data_mining_project.git
```

Sau đó:

```powershell
cd data_mining_project
```

Mở bằng VS Code:

```powershell
code .
```

---

# 4. Tạo môi trường Python

Trong Terminal của VS Code:

```powershell
python -m venv venv
```

Kích hoạt môi trường ảo:

```powershell
.\venv\Scripts\activate
```

Nếu thành công, Terminal sẽ xuất hiện:

```text
(venv)
```

---

# 5. Cài thư viện

Sau khi kích hoạt `venv`:

```powershell
pip install -r requirements.txt
```

Nếu muốn kiểm tra:

```powershell
pip list
```

---

# 6. Chuẩn bị Dataset

Dataset sử dụng:

**Brazilian E-Commerce Public Dataset by Olist**

Sau khi tải và giải nén dataset, có nhiều file CSV. Trong giai đoạn hiện tại, sử dụng 4 file:

```text
olist_orders_dataset.csv
olist_order_items_dataset.csv
olist_customers_dataset.csv
olist_products_dataset.csv
```

Đặt 4 file vào:

```text
data/raw/
```

Cấu trúc:

```text
data/
├── raw/
│   ├── olist_orders_dataset.csv
│   ├── olist_order_items_dataset.csv
│   ├── olist_customers_dataset.csv
│   └── olist_products_dataset.csv
│
└── processed/
```

### Lưu ý

* Không đổi tên các file CSV.
* Không chỉnh sửa trực tiếp dữ liệu trong `data/raw/`.
* Không upload dữ liệu thô lên GitHub.
* `data/raw/` đã được thêm vào `.gitignore`.

Mỗi thành viên cần **tự tải dataset về máy**.

---

# 7. Làm việc với Jupyter Notebook

Mở file `.ipynb` trong VS Code. Chọn Python Kernel ở góc trên bên phải: `Python 3.x ('venv')`

Nếu chưa thấy kernel, chạy lệnh sau:

```powershell
python -m ipykernel install --user --name=data-mining-venv
```

Chạy từng cell bằng **Shift + Enter**. Sau khi hoàn thành, chạy lại toàn bộ notebook (Kernel → Restart & Run All) để kiểm tra không có lỗi.

---

# 8. Quy trình và Cấu trúc Notebook

Dự án được chia thành **4 giai đoạn** khai phá dữ liệu:

## Giai Đoạn 1: Tiền Xử Lý Dữ Liệu (Data Preprocessing)

Chuẩn bị dữ liệu sạch cho phân tích qua 6 bước:

| Bước | Notebook | Mô Tả | Input | Output | Git Branch |
|------|----------|-------|-------|--------|-----------|
| 1 | 01_data_integration.ipynb | Tích hợp 4 bảng gốc | 4 CSV | step1_merged.csv | `feature/data-integration` |
| 2 | 02_eda_exploration.ipynb | Khám phá & phân tích dữ liệu | step1_merged.csv | (Không) | `feature/eda-exploration` |
| 3 | 03_missing_duplicates.ipynb | Xử lý giá trị thiếu & trùng | step1_merged.csv | step3_cleaned.csv | `feature/missing-duplicates` |
| 4 | 04_outliers_handling.ipynb | Xử lý ngoại lai (IQR) | step3_cleaned.csv | step4_nooutliers.csv | `feature/outliers-handling` |
| 5 | 05_data_encoding.ipynb | Chuẩn hóa & mã hóa dữ liệu | step4_nooutliers.csv | step5_encoded.csv | `feature/data-encoding` |
| 6 | 06_feature_rfm.ipynb | Tính toán RFM features | step5_encoded.csv | olist_cleaned.csv | `feature/rfm-features` |

**Kết quả:** `olist_cleaned.csv` – Dataset sạch sẵn sàng cho phân tích

**Lưu ý:** Xem [HUONG_DAN_6_BUOC.md](./HUONG_DAN_6_BUOC.md) để biết chi tiết từng bước, ví dụ code, và checklist bắt buộc.

## Giai Đoạn 2: Phân Cụm (Clustering)

Notebook: `07_clustering_analysis.ipynb`

Phân tích nhóm khách hàng dựa trên RFM:
- Áp dụng K-Means clustering
- Phân loại khách hàng: VIP, Loyal, At-risk, New
- Phân tích hành vi từng segment
- Đề xuất chiến lược marketing cho mỗi nhóm

## Giai Đoạn 3: Phân Loại (Classification)

Notebook: `08_classification_models.ipynb`

Xây dựng mô hình dự đoán:
- Dự đoán tình trạng đơn hàng (Delivered, Cancelled, etc.)
- Phân loại khách hàng repeat buyer vs. one-time buyer
- Các thuật toán: Decision Tree, Random Forest, Logistic Regression
- Đánh giá mô hình, tuning hyperparameters

## Giai Đoạn 4: Luật Kết Hợp (Association Rules)

Notebook: `09_association_rules.ipynb`

Phát hiện pattern mua hàng:
- Áp dụng Apriori/Eclat algorithm
- Tìm các cặp sản phẩm thường mua cùng
- Tính toán support, confidence, lift
- Đề xuất sản phẩm (Product Recommendation)

---

Xem [HUONG_DAN_6_BUOC.md](./HUONG_DAN_6_BUOC.md) để biết chi tiết giai đoạn tiền xử lý.

---

# 9. Quy tắc Git

Không làm việc trực tiếp trên branch `main`. Mỗi thành viên tạo branch riêng cho công việc của mình:

```powershell
git pull origin main
git checkout -b feature/ten-chi-tiet-cong-viec
```

**Ví dụ cho 6 bước tiền xử lý (Giai Đoạn 1):**

```powershell
# Bước 1: Data Integration
git checkout -b feature/data-integration

# Bước 2: EDA Exploration
git checkout -b feature/eda-exploration

# Bước 3: Missing & Duplicates
git checkout -b feature/missing-duplicates

# Bước 4: Outliers Handling
git checkout -b feature/outliers-handling

# Bước 5: Data Encoding
git checkout -b feature/data-encoding

# Bước 6: RFM Features
git checkout -b feature/rfm-features
```

Sau khi hoàn thành, commit và push code:

```powershell
git add notebooks/*.ipynb data/processed/*.csv
git commit -m "Add step X: [Mo ta chi tiet]"
git push -u origin feature/ten-chi-tiet-cong-viec
```

**Branch naming cho các giai đoạn khác:**

```powershell
# Giai đoạn 2: Clustering
git checkout -b feature/clustering-analysis

# Giai đoạn 3: Classification
git checkout -b feature/classification-models

# Giai đoạn 4: Association Rules
git checkout -b feature/association-rules
```

Tạo **Pull Request** trên GitHub để kiểm tra trước khi merge vào `main`.

Lưu ý: Không commit dữ liệu thô trong `data/raw/` (đã được thêm vào `.gitignore`).

---

# 10. Cập nhật Code Mới Nhất

Trước khi bắt đầu làm việc, cập nhật branch của bạn với mã mới nhất:

```powershell
git checkout main
git pull origin main
git checkout feature/ten-branch-cua-ban
git merge main
```

---

# Lưu ý Quan Trọng

**Quy Trình Xử Lý Dữ Liệu:**
- Luôn bắt đầu từ giai đoạn tiền xử lý (notebooks 01-06) để chuẩn bị dữ liệu
- Không bỏ qua bước EDA – cần hiểu dữ liệu trước khi áp dụng thuật toán
- Các giai đoạn phân cụm, phân loại, luật kết hợp phụ thuộc vào output của tiền xử lý

**Git & Collaboration:**
- Sử dụng đường dẫn tương đối (`../data/processed/...`) thay vì đường dẫn tuyệt đối
- Không commit folder `venv/`, `__pycache__/` hoặc dữ liệu thô trong `data/raw/`
- Chạy Kernel → Restart & Run All trước khi push để kiểm tra notebook chạy không lỗi
- Ghi rõ lý do cho mỗi quyết định xử lý dữ liệu trong comment

**Data Integrity:**
- Không sửa đổi cấu trúc project mà không thảo luận trước
- Lưu giữ tất cả file output trung gian cho debugging
- Không thay đổi file output của người khác

**Documenting Results:**
- Ghi nhận kết quả từng giai đoạn (accuracy, clustering quality, rule metrics)
- Tạo báo cáo tóm tắt insights chính từ từng phân tích

---

Xem [HUONG_DAN_6_BUOC.md](./HUONG_DAN_6_BUOC.md) để có hướng dẫn chi tiết giai đoạn tiền xử lý (notebooks 01-06).

---

## Kết Quả Hướng Tới

Xây dựng quy trình khai phá dữ liệu hoàn chỉnh, từ tiền xử lý dữ liệu đến phân tích sâu sắc, giúp đưa ra những hiểu biết chi tiết về khách hàng, sản phẩm, và xu hướng mua hàng để hỗ trợ quyết định kinh doanh.
