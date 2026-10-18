# Animal Condition Prediction Using SVM
## 1. Bộ dữ liệu
### Nguồn dữ liệu: Animal Condition Prediction – Kaggle
### Tên file: data.csv

## 2. Quy trình thực hiện
### Bước 1: Khám phá dữ liệu (EDA)

- Đọc và hiển thị dữ liệu.

- Kiểm tra kích thước dataset.

- Kiểm tra kiểu dữ liệu của các thuộc tính.

- Thống kê giá trị thiếu và dữ liệu trùng lặp.

- Phân tích phân bố nhãn mục tiêu Dangerous.

- Trực quan hóa số lượng các loài động vật.

- Phân tích các triệu chứng xuất hiện trong dữ liệu.

### Bước 2: Tiền xử lý dữ liệu

- Xử lý các dòng thiếu nhãn mục tiêu.

- Chuẩn hóa cách viết các giá trị dạng chuỗi.

- oại bỏ dữ liệu trùng lặp hoàn toàn.

- Mã hóa các thuộc tính phân loại bằng One-Hot Encoding.

- hia dữ liệu thành hai tập Train và Test theo tỷ lệ 80:20.

- Sử dụng Stratified Sampling để duy trì tỷ lệ nhãn giữa các tập dữ liệu.

### Bước 3: Xây dựng mô hình SVM

- Khởi tạo mô hình bằng SVC() của Scikit-learn.

- Sử dụng Kernel RBF cho mô hình ban đầu.

- Huấn luyện mô hình trên tập Train.

- Dự đoán kết quả trên tập Test.

- Đánh giá mô hình trước khi tinh chỉnh.

### Bước 4: So sánh các Kernel

- Thực hiện huấn luyện và so sánh bốn Kernel:

- Linear
- Polynomial (Poly)
- Radial Basis Function (RBF)
- Sigmoid

- Sử dụng Stratified Cross-Validation và F1 Macro để đánh giá hiệu quả của các Kernel.

### Bước 5: Tinh chỉnh siêu tham số

- Sử dụng GridSearchCV để tìm kiếm tổ hợp tham số phù hợp cho SVM.

- Các tham số được thử nghiệm:

- C: Điều chỉnh mức phạt đối với lỗi phân loại.
- kernel: Lựa chọn hàm nhân.
- gamma: Điều chỉnh ảnh hưởng của từng mẫu trong các Kernel phi tuyến.
- Ngoài ra, sử dụng class_weight="balanced" nhằm giảm tác động của tình trạng mất cân bằng nhãn.

### Bước 6: Đánh giá mô hình

- Sử dụng các chỉ số:
- Accuracy
- Precision
- Recall
- F1-score
- Balanced Accuracy
- Confusion Matrix
- Classification Report

- Thực hiện so sánh kết quả trước và sau tinh chỉnh nhằm đánh giá hiệu quả của quá trình tối ưu hóa.
