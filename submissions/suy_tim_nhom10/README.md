# Heart Failure 30-Day Readmission Prediction

Dự án xây dựng mô hình học máy nhằm dự báo nguy cơ **tái nhập viện do mọi nguyên nhân trong vòng 30 ngày sau xuất viện** ở bệnh nhân suy tim, sử dụng dữ liệu bệnh án điện tử từ **MIMIC-IV Clinical Database v3.1**.

Mục tiêu của dự án không chỉ là xây dựng mô hình dự báo mà còn đánh giá khả năng giải thích, độ ổn định, tính công bằng giữa các nhóm bệnh nhân và lợi ích lâm sàng tiềm năng của mô hình.

---

## 1. Dataset

Dự án sử dụng:

- **MIMIC-IV Clinical Database v3.1**
- Nguồn: PhysioNet
- Module chính: `hosp`
- Thời điểm dự đoán `T0`: **thời điểm xuất viện**
- Kết cục: **all-cause 30-day readmission**

Dữ liệu MIMIC-IV gốc và các bộ dữ liệu dẫn xuất ở mức từng bệnh nhân **không được công khai trên repository**.

Repository chỉ chứa:

- Source code
- Notebook phân tích
- Tài liệu mô tả dữ liệu
- Kết quả tổng hợp
- Hình ảnh và metric không chứa dữ liệu mức bệnh nhân

---

## 2. Cohort Summary

Bộ dữ liệu cuối trước khi đưa vào mô hình gồm:

| Thông tin | Giá trị |
|---|---:|
| Số bệnh nhân suy tim | 28,376 |
| Số lượt nhập viện | 70,066 |
| Tái nhập viện trong 30 ngày | 17,522 |
| Không tái nhập viện trong 30 ngày | 52,544 |
| Tỷ lệ tái nhập viện | 25.01% |
| Số cột trong dataset cuối | 52 |
| Predictor ban đầu | 46 |
| Predictor sử dụng cho mô hình | 41 |

Một số biến OMR có tỷ lệ thiếu lớn hơn 60% được loại khỏi mô hình:

- `egfr_last`
- `sbp_last`
- `dbp_last`
- `bmi_last`
- `weight_kg_last`

Sau tiền xử lý và One-Hot Encoding, mô hình sử dụng **111 đặc trưng**.

---

## 3. Pipeline

### 3.1. `01_build_hf_preprocessing.py`

Xây dựng cohort suy tim và các đặc trưng cơ bản.

Chức năng chính:

- Đọc dữ liệu MIMIC-IV v3.1.
- Xác định bệnh nhân suy tim:
  - ICD-9: `428*`
  - ICD-10: `I50*`
- Chỉ giữ bệnh nhân từ 18 tuổi trở lên.
- Loại trường hợp tử vong trong viện.
- Chỉ giữ thời gian nằm viện từ 1 ngày trở lên.
- Tạo nhãn `readmit_30d`.
- Tạo các đặc trưng lịch sử nhập viện.
- Tạo số lượng chẩn đoán, thủ thuật và thuốc.
- Lấy các xét nghiệm gần thời điểm xuất viện.
- Tạo patient-level folds bằng `StratifiedGroupKFold`.

---

### 3.2. `02_add_clinical_features.py`

Bổ sung các đặc trưng lâm sàng.

Bao gồm:

- Bệnh đồng mắc.
- Nhóm thuốc.
- OMR features.
- Kiểm tra chất lượng dữ liệu.
- Chuẩn hóa cấu trúc bộ dữ liệu.
- Tạo bộ dữ liệu cuối trước mô hình:

```text
hf_model_dataset_final.csv.gz
```

---

### 3.3. `MIMIC_IV_v3_1_HF_EDA.ipynb`

Phân tích dữ liệu khám phá bao gồm:

- Tổng quan cohort.
- Phân bố nhãn.
- Phân bố tuổi và giới tính.
- So sánh nhóm Readmission và Non-readmission.
- Phân tích missing data.
- Correlation analysis.
- Data quality checks.
- Khảo sát các đặc trưng lâm sàng chính.

---

## 4. Patient-Level Data Split

Dữ liệu được chia theo `subject_id` để tránh một bệnh nhân xuất hiện ở nhiều tập khác nhau.

| Tập | Admissions | Patients | Positive rate |
|---|---:|---:|---:|
| Train | 42,045 | 16,904 | 25.01% |
| Validation | 14,010 | 5,745 | 25.00% |
| Test | 14,011 | 5,727 | 24.99% |

Quy ước:

```text
Fold 0       -> Test
Fold 1       -> Validation
Fold 2,3,4   -> Train
```

Không có `subject_id` trùng giữa Train, Validation và Test.

---

## 5. Leakage Control

Các biện pháp kiểm soát data leakage được áp dụng xuyên suốt pipeline.

### Patient-level split

Tất cả admissions của cùng một bệnh nhân chỉ thuộc **một tập duy nhất**.

### Predictor timing

Chỉ sử dụng thông tin có trước hoặc tại thời điểm xuất viện.

Các xét nghiệm được giới hạn trong khoảng:

```text
admittime <= charttime <= dischtime
```

### Preprocessing

Các bước phụ thuộc dữ liệu:

- Median imputation
- Most-frequent imputation
- StandardScaler
- One-Hot Encoding

chỉ được `fit` trên Train.

Validation và Test chỉ được `transform` bằng các tham số đã học từ Train.

### Hyperparameter tuning

Tuning XGBoost chỉ được thực hiện trên Train bằng internal cross-validation.

Validation và Test không được sử dụng trong quá trình tuning.

---

## 6. Models

Các mô hình đã được đánh giá:

- Logistic Regression
- Random Forest
- XGBoost
- LightGBM

Mô hình cuối được lựa chọn:

```text
Tuned XGBoost
```

Hyperparameters:

```text
n_estimators      = 400
learning_rate     = 0.03
max_depth         = 3
min_child_weight  = 8
subsample         = 0.9
colsample_bytree  = 0.7
reg_alpha         = 0.01
reg_lambda        = 0.5
scale_pos_weight  = 1.5
```

Threshold cuối:

```text
0.30
```

Threshold được lựa chọn trên Validation và được khóa trước khi mở Final Test.

---

## 7. Final Test Results

Kết quả trên Final Test:

| Metric | Giá trị |
|---|---:|
| Sensitivity | 0.6519 |
| Specificity | 0.5707 |
| Precision | 0.3360 |
| NPV | 0.8311 |
| F1-score | 0.4435 |
| Accuracy | 0.5910 |
| ROC-AUC | 0.6633 |
| PR-AUC | 0.4143 |
| Brier Score | 0.1791 |

Confusion matrix:

```text
TN = 5998
FP = 4511
FN = 1219
TP = 2283
```

---

## 8. Uncertainty and Calibration

95% confidence intervals được tính bằng **patient-level cluster bootstrap với 1000 lần lặp**.

Một số kết quả chính:

| Metric | Estimate | 95% CI |
|---|---:|---:|
| ROC-AUC | 0.6633 | 0.6492–0.6772 |
| PR-AUC | 0.4143 | 0.3831–0.4459 |
| Sensitivity | 0.6519 | 0.6282–0.6746 |
| Specificity | 0.5707 | 0.5569–0.5852 |
| Brier Score | 0.1791 | 0.1755–0.1826 |

Calibration:

```text
Calibration intercept = -0.3749
Calibration slope     = 1.0102
```

Calibration slope gần 1 cho thấy độ dốc tương đối tốt, trong khi intercept âm cho thấy mô hình có xu hướng dự báo nguy cơ cao hơn thực tế.

---

## 9. Explainable AI

Dự án sử dụng **SHAP** để giải thích mô hình.

Bao gồm:

- Global feature importance.
- SHAP bar plot.
- SHAP beeswarm plot.
- Local waterfall plots.
- Phân tích TP / FP / FN / TN.

Một số đặc trưng có ảnh hưởng lớn:

- `prior_admissions_1y`
- `prior_admissions_90d`
- `hemoglobin_last`
- `prior_admissions`
- `sodium_last`
- `bun_last`
- `creatinine_last`
- `los_days`
- `days_since_last_discharge`

---

## 10. Fairness Analysis

Mô hình được đánh giá theo:

- Gender
- Age
- Race

Kết quả cho thấy hiệu năng giữa nam và nữ tương đối gần nhau.

Tuy nhiên, nhóm bệnh nhân:

```text
Age >= 85
```

có:

```text
Sensitivity = 55.43%
FNR         ≈ 44.57%
```

cho thấy đây là nhóm cần được lưu ý khi diễn giải và triển khai mô hình.

---

## 11. Error Analysis

Phân tích False Positive và False Negative được thực hiện nhằm tìm hiểu các trường hợp mô hình dự đoán sai.

Mô hình có xu hướng phụ thuộc đáng kể vào:

- Lịch sử nhập viện.
- Tần suất nhập viện gần đây.
- Các chỉ số xét nghiệm.
- Mức độ sử dụng dịch vụ y tế trước đó.

Error analysis được sử dụng để hỗ trợ diễn giải hạn chế của mô hình thay vì chỉ dựa vào các metric tổng quát.

---

## 12. Decision Curve Analysis

Decision Curve Analysis được sử dụng để đánh giá lợi ích lâm sàng tiềm năng.

Tại threshold:

```text
0.30
```

kết quả:

```text
Model Net Benefit = 0.02496
Treat-all          = -0.07151
Treat-none         = 0
```

Mô hình có Net Benefit cao hơn cả chiến lược Treat-all và Treat-none tại threshold đã khóa.

Kết quả này chỉ cho thấy **clinical utility tiềm năng**, không đồng nghĩa với việc mô hình đã được chứng minh có lợi ích lâm sàng trong triển khai thực tế.

---

## 13. Reproducibility and Reporting

Project sử dụng:

- FAIR data documentation
- Datasheet
- Data dictionary
- SHA-256 checksum
- TRIPOD+AI
- PROBAST+AI

Các file tài liệu chính:

```text
datasheet.md
data_dictionary.csv
checksums.txt
TRIPOD_AI_checklist.xlsx
```

SHA-256 của dataset cuối được lưu trong:

```text
checksums.txt
```

---

## 14. Repository Structure

```text
.
├── 01_build_hf_preprocessing.py
├── 02_add_clinical_features.py
├── 03_train_baseline.py
├── 04_train_random_forest.py
├── 05_train_xgboost.py
├── 06_train_lightgbm.py
├── 07_tune_xgboost.py
├── 08_tune_logistic_regression.py
├── 09_final_test_xgboost.py
├── 10_evaluate_ci_calibration.py
├── 11_shap_final_xgboost.py
├── 12_fairness_analysis.py
├── 13_error_analysis.py
├── 14_decision_curve_analysis.py
├── MIMIC_IV_v3_1_HF_EDA.ipynb
├── datasheet.md
├── data_dictionary.csv
├── checksums.txt
├── TRIPOD_AI_checklist.xlsx
├── requirements.txt
└── README.md
```

---

## 15. Limitations

Một số hạn chế chính:

- Chưa có external validation.
- Chưa có temporal validation độc lập.
- Dữ liệu đến từ một hệ thống y tế.
- Có thể không ghi nhận readmission tại bệnh viện ngoài hệ thống.
- ROC-AUC và Precision còn ở mức trung bình.
- Hiệu năng khác nhau giữa một số nhóm tuổi.
- Chưa thực hiện formal sample-size calculation a priori.
- Chưa thực hiện ablation study chính thức.

Do đó, mô hình hiện phù hợp cho mục đích **nghiên cứu và đánh giá phương pháp**, chưa nên sử dụng trực tiếp trong quyết định lâm sàng.

---

## 16. Data Privacy

MIMIC-IV là cơ sở dữ liệu có kiểm soát truy cập.

Repository này **không chứa**:

- Raw MIMIC-IV data.
- Dữ liệu mức từng bệnh nhân.
- `subject_id` / `hadm_id` ở dạng dataset công khai.
- Hồ sơ bệnh án cá nhân.

Người dùng muốn tái lập project cần tự đăng ký quyền truy cập MIMIC-IV thông qua PhysioNet và tuân thủ các điều khoản sử dụng dữ liệu.

---

## 17. Disclaimer

Project được thực hiện cho mục đích học tập và nghiên cứu.

Mô hình không phải là thiết bị y tế và không được sử dụng độc lập để đưa ra quyết định chẩn đoán, điều trị hoặc xuất viện.
