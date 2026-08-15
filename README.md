# Data Mining Project – Olist E-Commerce

Dự án môn **Khai phá dữ liệu (Data Mining)**, sử dụng **Python** để tiền xử lý và phân tích bộ dữ liệu thương mại điện tử thực tế **Brazilian E-Commerce Public Dataset by Olist**.

## Mục tiêu

Thực hiện quy trình xử lý dữ liệu:

```text
Dữ liệu thô
    ↓
Tích hợp dữ liệu
    ↓
Khám phá dữ liệu
    ↓
Xử lý dữ liệu thiếu
    ↓
Xử lý ngoại lai
    ↓
Chuẩn hóa / mã hóa
    ↓
Tạo đặc trưng RFM
    ↓
Dữ liệu sạch
```

---

# Công cụ sử dụng

* **Python**
* **Visual Studio Code**
* **Jupyter Notebook**
* **Git + GitHub**

### Thư viện Python

```text
pandas
numpy
scikit-learn
matplotlib
seaborn
mlxtend
ipykernel
```

Các thư viện được quản lý trong `requirements.txt`.

---

# Cấu trúc project

```text
data_mining_project/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│
├── .gitignore
├── README.md
└── requirements.txt
```

### `data/raw/`

Chứa dữ liệu thô ban đầu.

### `data/processed/`

Chứa dữ liệu sau từng bước xử lý.

### `notebooks/`

Chứa các Jupyter Notebook của dự án.

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

Mở file `.ipynb` trong VS Code.

Ở góc trên bên phải, chọn Python Kernel:

```text
Python 3.x ('venv')
```

Nếu chưa thấy kernel, chạy:

```powershell
python -m ipykernel install --user --name=data-mining-venv
```

Sau đó chọn lại kernel trong VS Code.

Có thể chạy từng cell bằng:

```text
Shift + Enter
```

---

# 8. Quy trình xử lý dữ liệu

Các bước xử lý dự kiến:

```text
01. Data Integration
        ↓
02. EDA / Exploration
        ↓
03. Missing Values
        ↓
04. Outliers
        ↓
05. Data Encoding
        ↓
06. RFM Feature Engineering
```

Các file trung gian sẽ được lưu trong:

```text
data/processed/
```

Ví dụ:

```text
step1_merged.csv
step3_nomissing.csv
step4_nooutliers.csv
step5_encoded.csv
olist_cleaned.csv
```

> Tên notebook và quy trình có thể được điều chỉnh khi nhóm thống nhất phương pháp xử lý dữ liệu.

---

# 9. Quy tắc Git

## Không làm việc trực tiếp trên `main`

Mỗi thành viên tạo branch riêng:

```powershell
git checkout -b feature/ten-cong-viec
```

Ví dụ:

```powershell
git checkout -b feature/data-processing
```

Sau khi hoàn thành:

```powershell
git status
git add .
git commit -m "Describe your changes"
git push -u origin feature/ten-cong-viec
```

Sau đó tạo **Pull Request** trên GitHub để kiểm tra và merge vào `main`.

---

# 10. Cập nhật code mới nhất

Trước khi bắt đầu làm việc:

```powershell
git checkout main
git pull origin main
```

Sau đó chuyển lại branch của mình:

```powershell
git checkout feature/ten-cong-viec
```

---

# 11. Một số lỗi thường gặp

### Python không hoạt động

```powershell
python --version
```

### Chưa kích hoạt môi trường ảo

```powershell
.\venv\Scripts\activate
```

### Thiếu thư viện

```powershell
pip install -r requirements.txt
```

### Không tìm thấy file CSV

Kiểm tra file có nằm đúng trong:

```text
data/raw/
```

hay không.

### Không tìm thấy module trong Jupyter

Kiểm tra Notebook đang sử dụng đúng:

```text
venv
```

Kernel.

---

# Lưu ý chung

* Sử dụng **đường dẫn tương đối**, không sử dụng đường dẫn riêng trên máy cá nhân.
* Không commit `venv/`.
* Không commit dữ liệu trong `data/raw/`.
* Không tự ý đổi cấu trúc project.
* Không làm việc trực tiếp trên `main`.
* Trao đổi với nhóm trước khi thay đổi cấu trúc dữ liệu hoặc notebook của người khác.

---

## Kết quả hướng tới

Xây dựng quy trình tiền xử lý dữ liệu bằng **Python**, từ dữ liệu thương mại điện tử thô đến tập dữ liệu sạch phục vụ cho các bước **khai phá và phân tích dữ liệu tiếp theo**.
