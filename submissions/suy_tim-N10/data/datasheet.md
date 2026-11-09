\# Datasheet — Heart Failure 30-day Readmission Dataset



\## 1. Tên dataset

hf\_model\_dataset\_final.csv.gz



\## 2. Nguồn dữ liệu

MIMIC-IV Clinical Database v3.1, PhysioNet.

Module sử dụng chính: hosp.



\## 3. Mục đích

Xây dựng mô hình dự báo nguy cơ tái nhập viện do mọi nguyên nhân

trong vòng 30 ngày ở bệnh nhân suy tim.



\## 4. Quần thể

\- 28,376 bệnh nhân

\- 70,066 lượt nhập viện

\- Tuổi ≥18

\- Suy tim: ICD-9 428\* hoặc ICD-10 I50\*

\- Sống khi xuất viện

\- LOS ≥1 ngày



\## 5. Outcome

readmit\_30d:

\- 1: có lần nhập viện tiếp theo trong vòng 30 ngày sau xuất viện

\- 0: không ghi nhận lần tái nhập viện trong vòng 30 ngày trong dữ liệu MIMIC-IV



Prediction time T0: thời điểm xuất viện.



\## 6. Phân bố outcome

\- Positive: 17,522 admissions

\- Negative: 52,544 admissions

\- Prevalence: 25.01%



\## 7. Predictors

Dataset có 52 cột tổng cộng.

Sau khi loại ID, time, target, fold và 5 biến thiếu >60%:

\- 41 predictors được sử dụng

\- 111 model features sau preprocessing



Chi tiết: xem data\_dictionary.csv



\## 8. Missing data

Loại:

\- egfr\_last

\- sbp\_last

\- dbp\_last

\- bmi\_last

\- weight\_kg\_last



Các biến số còn lại:

\- median imputation fit trên Train



Biến phân loại:

\- most-frequent imputation fit trên Train



days\_since\_last\_discharge:

\- missing có tính cấu trúc

\- xử lý kết hợp với has\_prior\_admission



\## 9. Data split

Patient-level split theo subject\_id:

\- Train: 42,045 admissions / 16,904 patients

\- Validation: 14,010 / 5,745

\- Test: 14,011 / 5,727



Không có subject\_id trùng giữa các tập.



\## 10. Quyền sử dụng và riêng tư

MIMIC-IV là credentialed-access dataset trên PhysioNet.

Không phân phối công khai dữ liệu row-level hoặc dữ liệu dẫn xuất chứa

thông tin bệnh nhân.



subject\_id và hadm\_id không được sử dụng làm predictors.



\## 11. Giới hạn

\- Dữ liệu từ một hệ thống y tế.

\- Có thể không quan sát được readmission xảy ra ngoài hệ thống.

\- Chưa có external validation.

\- Một số biến lâm sàng có tỷ lệ missing cao.

\- Hiệu năng ở bệnh nhân ≥85 tuổi thấp hơn các nhóm tuổi khác.



\## 12. Recommended use

Dùng cho nghiên cứu và đánh giá mô hình dự báo nội bộ.

Không sử dụng trực tiếp để ra quyết định lâm sàng nếu chưa có external validation.



\## 13. Không khuyến nghị

Không dùng model như công cụ chẩn đoán hoặc quyết định điều trị tự động.

