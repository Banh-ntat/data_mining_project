# Data Mining Project – Nhóm 3

Dự án môn **Khai phá dữ liệu**, thực hiện 4 bài tập thực hành (tiền xử lý, phân lớp, luật kết hợp, gom cụm) trên **3 bộ dữ liệu**:

| Bộ | Dữ liệu | Kỹ thuật thực hiện |
|----|---------|---------------------|
| Bộ 1 | Olist Brazilian E-Commerce | Phân lớp, Gom cụm |
| Bộ 2 | Inside Airbnb – New York City | Luật kết hợp, Gom cụm |
| Bộ 3 | US Accidents | Phân lớp, Luật kết hợp |

> Ghi chú: Luật kết hợp ban đầu dự kiến làm trên Olist, nhưng khảo sát
> thực tế (Mục 1.3 của `khao-sat.ipynb`) cho thấy đa số đơn hàng Olist
> chỉ có 1 sản phẩm/ngành hàng — không đủ đa dạng để sinh luật kết hợp
> có ý nghĩa. Vì vậy nhóm chuyển Luật kết hợp sang Airbnb (mỗi listing
> có nhiều tiện nghi cùng lúc, phù hợp hơn cho Apriori/FP-Growth).

Phân công 6 thành viên: xem chi tiết trong [`HUONG_DAN_LAM_BAI.md`](./HUONG_DAN_LAM_BAI.md).

---

## 1. Cài Python

Kiểm tra đã có Python chưa (PowerShell):

```powershell
python --version
```

Nếu chưa có, tải tại: https://www.python.org/ (chọn bản ≥ 3.10, khi cài nhớ tick **Add Python to PATH**).

---

## 2. Cài Git

```powershell
git --version
```

Nếu chưa có, tải tại: https://git-scm.com/

---

## 3. Clone project

```powershell
git clone https://github.com/<ten-repo-cua-nhom>.git
cd <ten-repo-cua-nhom>
code .
```

---

## 4. Tạo môi trường ảo Python

Trong Terminal của VS Code (PowerShell):

```powershell
python -m venv venv
.\venv\Scripts\activate
```

Nếu kích hoạt thành công, đầu dòng lệnh sẽ hiện `(venv)`.

> Nếu PowerShell báo lỗi không cho chạy script, mở PowerShell với quyền Admin và chạy 1 lần:
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
> ```

---

## 5. Cài thư viện

```powershell
pip install -r requirements.txt
```

Kiểm tra:

```powershell
pip list
```

`requirements.txt` gồm: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`, `mlxtend`, `jupyter`, `ipykernel`.

---

## 6. Chuẩn bị dữ liệu (BẮT BUỘC – mỗi thành viên tự tải về máy)

**Dữ liệu thô KHÔNG được đưa lên GitHub** (đã thêm `data/raw/` vào `.gitignore`). Mỗi người phải tự tải và đặt đúng vị trí bên dưới trước khi chạy notebook.

> ⚠️ Tên thư mục dưới đây phải khớp **chính xác** (kể cả dấu gạch dưới
> `_`) với đường dẫn mà `khao-sat.ipynb` dùng để đọc file
> (`RAW_DIR = '../data/raw'`). Đặt sai tên thư mục (ví dụ dùng gạch
> ngang `-` thay vì gạch dưới `_`) sẽ khiến notebook báo lỗi
> `FileNotFoundError` khi chạy.

### 6.1. Bộ 1 – Olist Brazilian E-Commerce

- Nguồn: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce
- Cần đăng nhập Kaggle → nút **Download** → giải nén.
- Copy toàn bộ các file `.csv` vào:

```text
data/raw/olist/
```

### 6.2. Bộ 2 – Inside Airbnb, New York City

- Nguồn: http://insideairbnb.com/get-the-data/
- Chọn thành phố **New York City** trong danh sách.
- Tải file **`listings.csv`** bản chi tiết (không dùng bản `listings_summary.csv` vì bị rút gọn thuộc tính).
- Có thể tải thêm `calendar.csv`, `reviews.csv` nếu nhóm cần cho luật kết hợp/gom cụm.
- Giấy phép: Creative Commons Attribution 4.0 International (CC BY 4.0).
- Copy vào:

```text
data/raw/airbnb/
```

### 6.3. Bộ 3 – US Accidents

- Nguồn: https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents
- Cần đăng nhập Kaggle → **Download** → giải nén (đặt tên file: `US_Accidents.csv`).
- Đặt file gốc chưa lọc vào:

```text
data/raw/us_accidents/
```

### 6.4. Lưu ý chung

- Không đổi tên file gốc tải về.
- Không sửa trực tiếp dữ liệu trong `data/raw/`.
- Không upload bất kỳ file nào trong `data/raw/` lên Git.
- Dữ liệu sau xử lý (`data/processed/`) là dữ liệu nhẹ hơn, **có thể** commit nếu nhóm thống nhất, hoặc dùng script tái tạo (`bai1.../khao-sat.ipynb` chạy lại được).

---

## 7. Cấu trúc thư mục

```text
ten-nhom/
├── README.md                       # file này
├── HUONG_DAN_LAM_BAI.md             # hướng dẫn chi tiết làm từng bài
├── requirements.txt
├── .gitignore
│
├── data/
│   ├── raw/                        # dữ liệu gốc — KHÔNG commit
│   │   ├── olist/
│   │   ├── airbnb/
│   │   └── us_accidents/
│   └── processed/                  # dữ liệu sau tiền xử lý
│       ├── olist/
│       ├── airbnb/
│       └── us_accidents/
│
├── bai1-du-lieu-tien-xu-ly/
│   ├── khao-sat.ipynb              # 1 notebook gộp cả 3 bộ (hàm dùng chung + 3 phần Bộ 1/2/3 + ma trận)
│   └── bao-cao-bai1.pdf
│
├── bai2-phan-lop/                  # Olist + US Accidents
│   ├── phan-lop.ipynb
│   └── bao-cao-bai2.pdf
│
├── bai3-luat-ket-hop/              # Airbnb + US Accidents
│   ├── luat-ket-hop.ipynb
│   └── bao-cao-bai3.pdf
│
└── bai4-gom-cum/                   # Olist + Airbnb
    ├── gom-cum.ipynb
    └── bao-cao-bai4.pdf
```

---

## 8. Làm việc với Jupyter Notebook trong VS Code

1. Mở file `.ipynb`.
2. Ở góc trên bên phải, chọn kernel `Python 3.x ('venv')`.
3. Nếu chưa thấy kernel, chạy:

```powershell
python -m ipykernel install --user --name=data-mining-venv
```

4. Chạy từng ô bằng **Shift + Enter**.
5. Trước khi commit/push: **Kernel → Restart & Run All** để đảm bảo notebook chạy lại được từ đầu, không lỗi.

---

## 9. Git & làm việc nhóm

Trước khi bắt đầu làm việc, luôn cập nhật branch từ `main`:

```powershell
git checkout main
git pull origin main
git checkout feature/ten-branch-cua-ban
git merge main
```

Quy tắc:

- Dùng đường dẫn tương đối (`../data/processed/...`), không dùng đường dẫn tuyệt đối.
- Không commit `venv/`, `__pycache__/`, hoặc bất kỳ file nào trong `data/raw/`.
- Không sửa file/notebook của người khác khi chưa trao đổi.
- Mỗi bước tiền xử lý/thuật toán ghi rõ lý do trong comment hoặc markdown cell.
- Đặt tên branch theo mẫu: `feature/<ten>-<bai>` (vd: `feature/an-bai2-airbnb`).

---

## 10. Tài liệu liên quan

- [`HUONG_DAN_LAM_BAI.md`](./HUONG_DAN_LAM_BAI.md) — phân công thành viên, checklist chi tiết cho từng bài, tiêu chí báo cáo.
