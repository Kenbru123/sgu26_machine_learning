# RT_IOT2022 — EDA và tiền xử lý dữ liệu

## Mục tiêu
Chuẩn bị dữ liệu cho bài toán phân loại 12 loại hoạt động/tấn công mạng IoT bằng Random Forest (Classification) và MLP (Neural Network). Phù hợp phần 3.2 của đề tài cuối kỳ môn Máy học.

## Cách chạy
1. Đặt `RT_IOT2022.csv` cùng thư mục với `RT_IOT2022_EDA_TienXuLy.ipynb`.
2. Cài Python và các thư viện: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`, `joblib`, `jupyter`.
3. Mở Jupyter Notebook, chạy **Run All**.
4. Đọc kết quả trực quan trong notebook; đầu ra sẽ nằm trong `rt_iot_eda_outputs/`.

## Các bước làm sạch đã thực hiện
- Xóa `Unnamed: 0` vì chỉ là chỉ mục từ CSV.
- Bỏ 5.195 dòng trùng hoàn toàn khi không xét chỉ mục.
- Bỏ 13 dòng có đặc trưng giống hệt nhưng nhãn `Attack_type` khác nhau (6 nhóm mâu thuẫn; không có cơ sở xác định nhãn đúng).
- Xóa `bwd_URG_flag_count` là cột hằng.
- Không thấy NaN hoặc vô cực; không xóa ngoại lệ dựa đơn thuần vào IQR, do đây có thể là tín hiệu tấn công.
- Dữ liệu sạch còn 117.909 dòng và 83 cột (82 đặc trưng, 1 nhãn).
- Chia 80/20 phân tầng với `random_state=42`: train 94.327, test 23.582 dòng. Cả hai có đủ 12 lớp.
- Mã hóa biến `proto`/`service` bằng One-Hot Encoding.
- Chuẩn bị hai luồng: Random Forest (numerical passthrough), MLP (`log1p` + `StandardScaler`).

## Nội dung các file
- `RT_IOT2022_EDA_TienXuLy.ipynb`: notebook **đã thực thi**, gồm thống kê, đồ thị và các bước giải thích.
- `rt_iot_eda_outputs/train.csv.gz`, `test.csv.gz`: hai tập dữ liệu đã được làm sạch và chia; vẫn giữ các cột gốc có ý nghĩa và `Attack_type`, **chưa đưa qua One-Hot hoặc StandardScaler**.
- `rt_iot_eda_outputs/rf_preprocessor.joblib`, `mlp_preprocessor.joblib`: bộ xử lý fit bằng train để tái sử dụng khi suy luận/demo.
- `rt_iot_eda_outputs/metadata.json`: thống kê làm sạch, tên cột, phân phối nhãn và seed.
- `rt_iot_eda_outputs/figures/*.png`: biểu đồ EDA.
- `rt_iot_eda_outputs/nhan_goc.csv`: thống kê phân bố 12 lớp ở dữ liệu gốc.

## Sử dụng ở bước xây dựng mô hình
```python
import pandas as pd
train = pd.read_csv('rt_iot_eda_outputs/train.csv.gz')
test = pd.read_csv('rt_iot_eda_outputs/test.csv.gz')
X_train = train.drop(columns='Attack_type')
y_train = train['Attack_type']
X_test = test.drop(columns='Attack_type')
y_test = test['Attack_type']
```

**Rất quan trọng:** Với `GridSearchCV`/`RandomizedSearchCV`, tạo **preprocessor chưa fit** (xem `make_preprocessors()` trong notebook) và đặt vào `sklearn.pipeline.Pipeline` với bộ phân loại. Điều này khiến mỗi fold fit lại preprocessing chỉ trên training fold, tránh data leakage. Không dùng các bản `.joblib` đã fit toàn bộ train để biến đổi dữ liệu trước cross-validation. Chỉ dùng test đánh giá kết quả cuối cùng, không dùng test chọn mô hình/tune.

Do mất cân bằng nhãn đáng kể (lớp lớn nhất khoảng 77%), ưu tiên **macro-F1**, recall theo lớp, confusion matrix bên cạnh Accuracy. Chỉ áp dụng cân bằng dữ liệu/trọng số khi huấn luyện, không làm trên test.

## Hạn chế
- Xóa dòng mâu thuẫn là lựa chọn bảo thủ vì không có thông tin nguồn để xác định nhãn đúng; cần ghi nhận trong báo cáo.
- Đây là chia ngẫu nhiên sau khử trùng; chưa xác thực khả năng tổng quát theo thời gian, thiết bị hoặc phiên kết nối vì dataset chưa cung cấp nhóm độc lập tương ứng.
- Phân tích ngoại lệ bằng ngưỡng chỉ hỗ trợ hiểu dữ liệu; giá trị cao trong tấn công mạng không mặc nhiên là lỗi.
