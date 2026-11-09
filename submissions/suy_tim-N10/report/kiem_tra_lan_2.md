# Bài kiểm tra lần 2 — N10 — suy_tim

Fork: https://github.com/lthuyhang235-chj/hmyt-project · Nhánh: `gd5-proposed-model`

Thành viên (MSSV hoặc alias) và vai trò:

* 20233815 — Đồng thực hiện; tham gia xử lý dữ liệu, xây dựng/đánh giá mô hình và hoàn thiện báo cáo. Phân công chi tiết giữa hai thành viên chưa được ghi chính thức trong tài liệu hiện có.
* 20233749 — Đồng thực hiện; tham gia tổng quan y văn, xử lý dữ liệu, đánh giá mô hình và hoàn thiện báo cáo. Phân công chi tiết giữa hai thành viên chưa được ghi chính thức trong tài liệu hiện có.

## A. Literature Review và research gaps

### A1. Chiến lược tìm kiếm

|Nguồn (PubMed, Scopus, IEEE Xplore, Google Scholar…)|Từ khóa / truy vấn|Khoảng năm|Số kết quả|Số bài giữ lại|
|-|-|-|-|-|
|PubMed|`("Heart failure" OR "HF") AND ("Readmission" OR "30-day") AND ("Machine learning" OR "XGBoost")`|2018–2026, ưu tiên 2023–2026|142|5|
|IEEE Xplore|"heart failure" AND readmission AND "machine learning"|2018–2026|29|0|
|Google Scholar|`("Heart failure" OR "HF") ("30-day readmission") ("Machine learning" OR "XGBoost" OR "SHAP")`|2018–2026, ưu tiên 3–5 năm gần nhất|Khoảng 3.970|6|

Ngày tìm kiếm: 26/10/2026.

Tiêu chí chọn / loại bài:

* Chọn nghiên cứu liên quan đến suy tim, tái nhập viện, tử vong hoặc phân tầng nguy cơ có giá trị phương pháp cho bài toán.
* Ưu tiên nghiên cứu dự báo tái nhập viện 30 ngày ở bệnh nhân suy tim.
* Ưu tiên các nghiên cứu 3–5 năm gần nhất, nhưng giữ một số bài cũ có giá trị nền tảng hoặc so sánh trực tiếp.
* Ưu tiên nghiên cứu sử dụng dữ liệu bệnh án điện tử, dữ liệu lâm sàng và các mô hình Logistic Regression, Random Forest, XGBoost, LightGBM hoặc mô hình học máy có thể so sánh.
* Ưu tiên bài có báo cáo AUROC/AUC, AUPRC/PR-AUC, sensitivity, specificity, F1 hoặc calibration.
* Ưu tiên bài có SHAP/XAI, phân tích khả năng khái quát hóa hoặc kiểm định ngoài.
* Loại nghiên cứu không liên quan đến suy tim hoặc không có bài toán dự báo nguy cơ.
* Loại bài không có thông tin hiệu năng đủ để phục vụ đối chiếu.
* Loại bản trùng lặp của cùng một nghiên cứu.

### A2. Ma trận y văn (≥ 10 bài, ưu tiên 3–5 năm gần nhất)

|#|Bài (tác giả, năm)|DOI / link|Dataset (n, nguồn)|Phương pháp|Kết quả chính (metric, giá trị)|Kiểm định ngoài? (có / không)|Hạn chế chính|
|-|-|-|-|-|-|-|-|
|1|van Es et al., 2025, ESC Heart Failure|10.1002/ehf2.15395|N=65 bệnh nhân sau ADHF; 426 chuỗi theo dõi 21 ngày bằng cảm biến đeo|Cosinor + LSTM; LOOCV|AUC=0.74; Sens=74%; Spec=73%; NPV=97%; PPV=20%; F1=31%; AUPRC=0.21|Không|Cỡ mẫu rất nhỏ; mất cân bằng lớp; PPV thấp; 24% dữ liệu mất do không đeo thiết bị; thiếu kiểm định ngoài|
|2|Zhai et al., 2026, BMC Medical Informatics and Decision Making|10.1186/s12911-026-03682-z|MIMIC-IV N=1.308; eICU-CRD N=365|37 biến; MICE; SMOTE-Tomek; LR/RF/LightGBM/XGBoost/MLP/KNN|Nội bộ XGB: AUC=0.968, Acc=90.6%, F1=90.6%, Recall=91.7%; ngoại viện AUC=0.718, Acc=76.3%, Recall=38.3%|Có|Hiệu năng giảm mạnh khi sang eICU; minh họa rõ distribution shift giữa bệnh viện|
|3|Wang et al., 2025, Frontiers in Cardiovascular Medicine|10.3389/fcvm.2025.1673159|N=870 bệnh nhân cao tuổi có T2DM và HF|Stacking Ensemble + SHAP|AUC=0.867 cho tái nhập viện 30 ngày|Không|Quần thể rất đặc thù: cao tuổi + T2DM + HF; khó khái quát sang HF chung|
|4|Maese-Calvo et al., 2026, Medical \& Biological Engineering \& Computing|10.1007/s11517-026-03657-2|Registry nông thôn kéo dài 20 năm, N≈5.000|RF, XGBoost, SVM so với Logistic Regression|RF AUC=0.812; baseline LR AUC=0.686|Không|Dữ liệu kéo dài 20 năm có nguy cơ thay đổi phác đồ/case-mix; cần kiểm định ngoài|
|5|Zhang et al., 2024, iScience|10.1016/j.isci.2024.110281|N=2.232 bệnh nhân acute HF; readmission 30 ngày 6.72%|VIF + feature selection + 5-fold CV; LR/RF/CatBoost/LightGBM/XGBoost; SHAP|XGBoost AUC=0.763 (95% CI 0.703–0.824); Acc=70.9%; Sens=66.0%; Spec=71.3%; Brier=0.197|Không|Một trung tâm; chỉ 150 biến cố; nguy cơ leakage ở preprocessing/feature selection chưa được loại trừ rõ; chưa external validation|
|6|Golas et al., 2018, BMC Medical Informatics and Decision Making|10.1186/s12911-018-0620-z|20.301 admissions từ 10.052 bệnh nhân HF, EMR|DNN so với RF, Logistic Regression và LACE|DNN AUC=0.705; Acc=65.4%; Sens=63.8%; Spec=67.1%; Precision=44.8%; LR AUC=0.664|Không|DNN phức tạp nhưng cải thiện AUC nhỏ so với LR; thiếu explainability|
|7|Awan et al., 2019, ESC Heart Failure|10.1002/ehf2.12419|N=10.757 bệnh nhân ≥65 tuổi; readmission/death 30 ngày 23.6%|Class-balanced MLP; Weighted RF/SVM; Logistic Regression|MLP AUC=0.62; AUPRC=0.46; Sens=48%; Spec=70%; LR AUC=0.58|Không|Sensitivity thấp; nhiều FN; chủ yếu dữ liệu hành chính/ICD; thiếu marker lâm sàng sâu|
|8|Pikatza-Huerga et al., 2025, Postgraduate Medical Journal|10.1093/postmj/qgaf102|Bệnh nhân acute HF; 16% readmission, 84% non-readmission|Ensemble ML; SMOTE; so với Cox và Logistic Regression|Ensemble AUC=0.81; Cox AUC=0.58; LR AUC=0.50|Không|Phụ thuộc SMOTE; thiếu tập kiểm định độc lập|
|9|Luo et al., 2024, Scientific Reports|10.1038/s41598-024-67844-7|N=8.396 bệnh nhân HF cao tuổi; 73 đặc trưng|XGBoost/LASSO/RF feature selection + CatBoost + SHAP|AUC=0.836; F1=0.735; Brier=0.165|Không|Một bệnh viện; nguy cơ overfitting; dự báo 1 năm khác bài toán 30 ngày|
|10|Soliman et al., 2023, JMIR Medical Informatics|10.2196/46934|N=15.612 admissions HF; readmission 35.85%|Deep Learning so với explainable shallow ML + feature engineering|Explainable ML đạt hiệu năng tương đương deep learning và vượt các thang điểm lâm sàng truyền thống|Không|Dự báo 100 ngày, không phải 30 ngày; feature engineering thủ công phức tạp|
|11|Shakya et al., 2025, JMIR Medical Informatics|10.2196/73020|MIMIC-IV; HF; tuổi TB≈73; readmission 30 ngày≈19%|Code-based embeddings vs text-based embeddings; LR/XGBoost/ANN|Code-based embeddings AUROC≈0.65; one-hot baseline≈0.54|Không|Chưa kiểm định đa cơ sở; cần temporal/graph/multimodal modeling; cohort và feature representation khác dự án nhóm|

### A3. Research gaps

|Mã|Khoảng trống (1–2 câu)|Bằng chứng (số # trong A2)|Dự án của nhóm giải quyết bằng cách nào|
|-|-|-|-|
|G1|Khả năng khái quát hóa ra ngoài cơ sở phát triển còn hạn chế; hiệu năng nội bộ cao có thể giảm đáng kể khi chuyển bệnh viện.|#2, #4, #5, #8, #9, #11|Nhóm em giữ Test độc lập ở mức bệnh nhân, báo cáo rõ đây chỉ là internal held-out evaluation và không tuyên bố external validation; nêu external/temporal validation là hướng phát triển.|
|G2|Nhiều mô hình có khả năng dự báo nhưng thiếu khả năng giải thích, đặc biệt giải thích từng bệnh nhân.|#1, #6, #7, #11|Nhóm em dùng SHAP toàn cục bằng bar/beeswarm và SHAP cục bộ bằng waterfall cho TP/FP/FN/TN.|
|G3|Mất cân bằng lớp khiến Accuracy/AUROC đơn thuần có thể gây hiểu nhầm; sensitivity, AUPRC và FN cần được chú ý.|#1, #7, #8|Nhóm em ưu tiên PR-AUC, Sensitivity, F1 và phân tích chi phí FN; threshold được chọn trên Validation thay vì dùng mặc định 0.5.|
|G4|Việc chia dữ liệu và tiền xử lý không chặt chẽ có thể gây leakage, đặc biệt khi một bệnh nhân có nhiều admissions; nhiều bài trong review không mô tả rõ patient-level separation.|#5, #6, #11|Nhóm em chia Train/Validation/Test theo `subject_id`; mọi admission của cùng bệnh nhân chỉ thuộc một tập; imputation/scaling/OHE chỉ fit trên Train.|
|G5|Phần lớn nghiên cứu tập trung discrimination; calibration, uncertainty, subgroup fairness, error analysis và clinical utility ít được báo cáo đồng thời.|#1–#11; #5 và #9 có báo Brier nhưng chưa thấy nghiên cứu nào trong ma trận đánh giá đầy đủ fairness + error analysis + DCA|Nhóm em báo cáo bootstrap 95% CI, calibration curve/intercept/slope, Brier, fairness theo gender/age/race, error analysis và Decision Curve Analysis.|

### A4. Thẩm định 1 bài quan trọng nhất trong A2 bằng PROBAST+AI

Bài số #: **5 — Zhang et al., 2024, iScience** · Lý do chọn: Đây là bài gần nhất với đề tài của nhóm em về quần thể suy tim, outcome tái nhập viện 30 ngày, sử dụng XGBoost và SHAP, đồng thời có các metric có thể đối chiếu trực tiếp.

|Miền|Nguy cơ sai lệch (thấp / cao / không rõ)|Căn cứ (trích dẫn hoặc số trang trong bài)|
|-|-|-|
|Người tham gia và nguồn dữ liệu|Thấp|2.232 bệnh nhân AHF, tiêu chí chọn/loại và thời gian thu thập 2013–2021 được mô tả rõ.<br />STAR Methods, tr.11|
|Biến dự báo|Thấp|Predictors gồm nhân khẩu học, tiền sử, thuốc, xét nghiệm và siêu âm tim; biến thiếu <30% được giữ và điền bằng KNN.<br />STAR Methods, tr.12|
|Kết cục|Không rõ|Outcome là tái nhập viện ngoài kế hoạch trong 30 ngày; có 150/2.232 ca tái nhập viện (6,72%). Tuy nhiên bài chưa làm rõ cách ghi nhận readmission ngoài hệ thống.<br />Results, tr.2|
|Phân tích|Cao|Chỉ có 150 biến cố nhưng còn 43 predictors; bài không nói rõ KNN imputation và feature selection có chỉ thực hiện trên Train hay không. SMOTE + 5-fold CV cũng chưa mô tả đủ để loại trừ leakage.<br />STAR Methods, tr.12|

Nguy cơ sai lệch tổng thể: CAO, chủ yếu do miền Phân tích.



Tính áp dụng cho bài toán của nhóm em: Khá cao vì cùng dự báo tái nhập viện 30 ngày, cùng dùng XGBoost và SHAP. Tuy nhiên không thể so sánh trực tiếp vì bài nghiên cứu acute HF, tỷ lệ readmission chỉ 6,72%, bộ biến khác và chưa có external validation. Tác giả cũng thừa nhận khả năng khái quát hóa cần được kiểm chứng thêm.



### B1. Kết quả hiện có

|Mô hình|Tập đánh giá (cách chia, n)|Metric chính (giá trị, 95% CI nếu có)|Metric phụ|Nguồn số liệu (file / notebook / W\&B)|
|-|-|-|-|-|
|Baseline 1 (Mốc 2) — Logistic Regression|Validation; patient-level split; n=14.010 admissions, 5.745 patients; prevalence≈25.0%|ROC-AUC=0.6586; 95% CI: chưa tính cho baseline|PR-AUC=0.4063; tại threshold 0.50: Sens=0.0991, Spec=0.9762, Precision=0.5812, F1=0.1693; best F1 tại threshold 0.21: Sens=0.6957, Spec=0.5167, F1=0.4424|`03_train_baseline.py`; kết quả Validation|
|Mô hình đề xuất (Mốc 3) — Tuned XGBoost|Final Test; patient-level holdout; n=14.011 admissions, 5.727 patients; prevalence=24.99%; threshold=0.30 khóa từ Validation|ROC-AUC=0.6633 \[0.6492–0.6772]; PR-AUC=0.4143 \[0.3831–0.4459]|Sens=0.6519 \[0.6282–0.6746]; Spec=0.5707 \[0.5569–0.5852]; Precision=0.3360; NPV=0.8311; F1=0.4435; Accuracy=0.5910; Brier=0.1791; calibration slope=1.0102; intercept=-0.3749|`07_tune_xgboost.py`, `09_final_test_xgboost.py`, `10_evaluate_ci_calibration.py`; `xgboost_final_test_metrics_95ci.csv`|

### B2. Ablation (Mốc 3)

|Cấu hình (bỏ / đổi thành phần nào)|Metric chính|Chênh lệch so với mô hình đầy đủ|Nguồn|
|-|-|-|-|
|Chưa thực hiện — nhóm em hiện mới thực hiện model comparison<br />LR/RF/XGBoost/LightGBM, chưa có ablation study trên cùng mô hình.|chưa có|chưa có |Chưa có|

### B3. Đối chiếu với y văn

Mô hình cuối của nhóm em đạt ROC-AUC=0.6633, PR-AUC=0.4143, Sensitivity=65.19%, Specificity=57.07% và Accuracy=59.10%.

So với bài #5 Zhang et al. 2024, XGBoost của bài báo đạt AUC=0.763, Accuracy=70.9%, Sensitivity=66.0%, Specificity=71.3%. Mô hình của nhóm em có sensitivity gần tương đương nhưng discrimination, specificity và accuracy thấp hơn. Tuy nhiên phép so sánh **không công bằng** vì bài #5 dùng acute HF, prevalence chỉ 6.72%, có nhiều biến siêu âm/EF và cách chọn feature khác.

So với bài #6 Golas et al. 2018, DNN đạt AUC=0.705, Accuracy=65.4%, Sensitivity=63.8%, Specificity=67.1%; Logistic Regression của bài này AUC=0.664. AUC của XGBoost nhóm em (0.6633) gần với Logistic Regression trong bài #6 nhưng thấp hơn DNN. Phép so sánh **không công bằng** vì dataset, feature set, model architecture, threshold và cách chia dữ liệu khác nhau.

So với bài #11 Shakya et al. 2025, cùng sử dụng MIMIC-IV và outcome 30-day HF readmission, code-based embeddings đạt AUROC≈0.65. Mô hình nhóm em đạt AUROC=0.6633, nhỉnh hơn về AUROC. Tuy nhiên nhóm em vẫn **không kết luận mô hình tốt hơn trực tiếp** vì cohort definition, prevalence (≈19% so với 25%), cách biểu diễn feature và protocol đánh giá khác nhau.

Kết luận đối chiếu: mô hình của nhóm em nằm trong vùng hiệu năng thường gặp của các nghiên cứu tái nhập viện suy tim, có kết quả nội bộ ổn định nhưng chưa đạt mức tốt nhất trong y văn. Điểm mạnh của dự án nằm ở patient-level leakage control, calibration, bootstrap CI, SHAP, fairness, error analysis và DCA được thực hiện đồng thời.

### B4. Timeline đến khi hoàn thành

|Việc|Phụ trách (MSSV / vai trò)|Mốc (M3 tuần 10 · M4 tuần 12 · Final tuần 15)|Ngày dự kiến|Trạng thái (Chưa / Đang / Xong + bằng chứng)|
|-|-|-|-|-|
|Hoàn thành model comparison, tuning XGBoost, chọn threshold|20233815 + 20233749 — đồng thực hiện|M3 tuần 10|08/11/2026|Xong — `03` đến `09`, Validation/Test results|
|95% CI + calibration|20233815 + 20233749 — đồng thực hiện|M3 tuần 10|08/11/2026|Xong — `10_evaluate_ci_calibration.py`|
|SHAP global/local|20233815 + 20233749 — đồng thực hiện|M3 tuần 10|08/11/2026|Xong — `11_shap_final_xgboost.py`|
|Fairness + error analysis|20233815 + 20233749 — đồng thực hiện|M3 tuần 10|08/11/2026|Xong — `12_fairness_analysis.py`, `13_error_analysis.py`|
|Decision Curve Analysis|20233815 + 20233749 — đồng thực hiện|M3 tuần 10|08/11/2026|Xong — `14_decision_curve_analysis.py`; Net Benefit tại 0.30 = 0.02496|
|Hoàn thiện FAIR: data dictionary, checksum, datasheet|20233815 + 20233749 — đồng thực hiện|M4 tuần 12|22/11/2026 — dự kiến theo lịch làm việc của nhóm|Xong — `data_dictionary.csv`, `datasheet.md`, SHA-256 của `hf_model_dataset_final.csv.gz` đã được tạo và lưu đầy đủ trong `checksums.txt`.|
|Điền checklist TRIPOD+AI chính thức|20233815 + 20233749 — đồng thực hiện|M4 tuần 12|22/11/2026 — dự kiến theo lịch làm việc của nhóm|Xong — `TRIPOD_AI_checklist.xlsx`, 52/52 tiểu mục đã được điền trạng thái|
|Audit PROBAST+AI cuối|20233815 + 20233749 — đồng thực hiện|M4 tuần 12|22/11/2026 — dự kiến|Xong — đã hoàn thiện 4 miền PROBAST+AI và dẫn chứng theo file/dòng code|
|Hoàn thiện báo cáo, slide, README và tái lập repo|20233815 + 20233749 — đồng thực hiện|M4 tuần 12|22/11/2026 — dự kiến|Đang — báo cáo đã cập nhật kết quả cuối; slide đang bổ sung Results/XAI/DCA|
|Nộp final + trình bày|20233815 + 20233749 — đồng thực hiện|Final tuần 15|13/12/2026 — dự kiến theo lịch làm việc của nhóm|Chưa — chờ hoàn thiện repo và slide cuối|

### B5. Rủi ro đang vướng / câu hỏi cho GV

* Mô hình hiện mới được kiểm định nội bộ trên MIMIC-IV, chưa có external/temporal validation.
* Hiệu năng dự báo ở mức trung bình (ROC-AUC = 0.6633; Precision = 33.60%).
* Phân tích fairness cho thấy nhóm ≥85 tuổi có Sensitivity thấp hơn (55.43%), cần được lưu ý khi diễn giải kết quả.
* Câu hỏi cho GV: Ablation study có bắt buộc ở Mốc 3 khi nhóm đã thực hiện so sánh LR/RF/XGBoost/LightGBM, tuning, SHAP, fairness, error analysis và DCA hay không?C. Tự đánh giá dự án theo Phụ lục FAIR, TRIPOD+AI, PROBAST+AI

### C1. FAIR — dữ liệu

Ethics/IRB: MIMIC-IV là cơ sở dữ liệu đã được khử định danh.

Việc thu thập thông tin bệnh nhân và xây dựng nguồn dữ liệu MIMIC-IV đã được Institutional Review Board của Beth Israel Deaconess Medical Center xem xét; yêu cầu informed consent được miễn đối với việc tạo và chia sẻ nguồn dữ liệu nghiên cứu. Nhóm em thực hiện phân tích thứ cấp trên dữ liệu đã khử định danh thông qua credentialed access của PhysioNet.

|Mục|Trả lời|Bằng chứng|
|-|-|-|
|Nguồn, phiên bản, ngày tải; checksum file dùng để huấn luyện|Nhóm em sử dụng MIMIC-IV Clinical Database v3.1, module `hosp`.Dữ liệu nguồn của MIMIC-IV được thu thập tại BIDMC trong giai đoạn<br />2008–2022. Ngày nhóm em tải dữ liệu chưa được lưu chính xác trong nhật ký dự án nên nhóm em không tự suy đoán. SHA-256 của `hf_model_dataset_final.csv.gz` đã được tạo và lưu đầy đủ trong `checksums.txt`.|`processed/hf_model_dataset_final.csv.gz`; `checksums.txt`; MIMIC-IV v3.1|
|Giấy phép / DUA, căn cứ được phép dùng|Dữ liệu credentialed-access qua PhysioNet; nhóm em có quyền truy cập. Nhóm em không công khai dữ liệu MIMIC-IV row-level.|Quyền truy cập PhysioNet; Data Availability statement trong báo cáo|
|Datasheet: quần thể, tiêu chí chọn/loại, định nghĩa và đơn vị biến, giới hạn|Đạt. Nhóm em đã hoàn thành `datasheet.md`, mô tả nguồn dữ liệu, quần thể, tiêu chí chọn/loại, outcome, predictors, missing data, split, quyền sử dụng, giới hạn và recommended use. `data_dictionary.csv` mô tả 52 cột dataset.|`datasheet.md`; `data_dictionary.csv`|
|Không có định danh bệnh nhân|Không có định danh trực tiếp. `subject_id` và `hadm_id` là mã đã khử định danh của MIMIC-IV và được loại khỏi predictors. Nhóm em không public row-level dataset.|Các script model loại `subject_id`, `hadm_id`; Data Availability statement|

### C2. Hướng dẫn báo cáo

* Câu hỏi nghiên cứu của nhóm em:  
**Làm thế nào để xây dựng mô hình học máy sử dụng dữ liệu bệnh án điện tử nhằm dự báo xác suất bệnh nhân suy tim tái nhập viện do mọi nguyên nhân trong vòng 30 ngày sau xuất viện, đồng thời giải thích các yếu tố đóng góp vào dự đoán và đánh giá tính ổn định, công bằng và lợi ích lâm sàng tiềm năng của mô hình?**
* Hướng dẫn báo cáo phù hợp (TRIPOD+AI / STARD-AI / DECIDE-AI) và lý do (Phụ lục §3.2):  
**TRIPOD+AI**, vì đây là nghiên cứu phát triển và đánh giá một mô hình dự đoán tiên lượng lâm sàng. Nhóm em không chọn STARD-AI vì dự án không đánh giá độ chính xác của một xét nghiệm AI so với chuẩn tham chiếu; không chọn DECIDE-AI vì chưa phải nghiên cứu đánh giá triển khai lâm sàng giai đoạn sớm của hệ hỗ trợ quyết định.
* Số mục checklist đã đáp ứng / tổng số mục; các mục còn thiếu:  
Nhóm em đã **điền toàn bộ 52/52 tiểu mục của checklist TRIPOD+AI chính thức** trong `TRIPOD_AI_checklist.xlsx`. Theo trạng thái hiện tại: **27 tiểu mục Đạt, 12 Đạt một phần, 2 Chưa và 11 Không áp dụng có lý do**. Các nội dung còn cần hoàn thiện chủ yếu là funding/COI, repo/package versions và một số chi tiết reporting/open-science.
* Kết cục, nhãn, thời điểm dự đoán T0, các biến có sẵn tại T0:  
Outcome = **all-cause hospital readmission trong vòng 30 ngày sau xuất viện**.  
Nhãn: `readmit_30d=1` nếu có tái nhập viện ≤30 ngày; `0` nếu không ghi nhận tái nhập viện ≤30 ngày trong dữ liệu.  
T0 = **thời điểm xuất viện**.  
Predictors chỉ sử dụng thông tin có trước hoặc tại T0: demographics, thông tin admission, LOS, lịch sử admission, comorbidities, thuốc, procedures, các xét nghiệm gần xuất viện và discharge location. Nhóm em không sử dụng thông tin xảy ra sau T0 làm predictor.

### C3. PROBAST+AI — tự thẩm định dự án của nhóm

|Miền|Nguy cơ sai lệch (thấp / cao / không rõ)|Căn cứ trong repo (file, dòng)|Việc sẽ làm để giảm nguy cơ|
|-|-|-|-|
|Người tham gia và nguồn dữ liệu|Thấp|`01_build_hf_preprocessing.py`, dòng 24–31 và 42–60: đọc dữ liệu MIMIC-IV; dòng 63–70: xác định HF bằng ICD-9 428* / ICD-10 I50*; dòng 100–103: tạo cohort đủ điều kiện; dòng 214–223: tạo fold theo `subject_id` và kiểm tra mỗi bệnh nhân chỉ thuộc một fold.|Ghi rõ đây là dữ liệu một hệ thống y tế; không tuyên bố khả năng áp dụng ngoài MIMIC-IV; hướng phát triển là temporal/external validation.|
|Biến dự báo|Thấp|`01_build_hf_preprocessing.py`, dòng 168–182: chỉ lấy xét nghiệm trong khoảng admission–discharge; `07_tune_xgboost.py`, dòng 110–158: loại ID, target, fold và 5 biến missing cao khỏi predictors; dòng 196–223: median imputation, scaling và one-hot encoding.|Hoàn thiện/duy trì data dictionary; kiểm tra timing của predictor; diễn giải thận trọng `race_UNKNOWN` và missingness.|
|Kết cục (chú ý incorporation bias)|Không rõ|`01_build_hf_preprocessing.py`, dòng 73–80: xác định lần nhập viện tiếp theo theo `subject_id`, tính khoảng cách từ discharge đến next admission và tạo `readmit_30d=1` nếu khoảng cách >0 và ≤30 ngày.|Diễn giải `Y=0` là “không ghi nhận readmission ≤30 ngày trong dữ liệu”; external validation nếu có nguồn dữ liệu khác. |
|Phân tích (chia theo bệnh nhân, fit chỉ trên train, cỡ mẫu, dữ liệu thiếu)|Thấp|`07_tune_xgboost.py`, dòng 75–102: chia Train/Validation/Test và kiểm tra không có `subject_id` trùng; dòng 160–236: preprocessing nằm trong pipeline; dòng 240–308: internal CV và tuning chỉ trên Train. `10_evaluate_ci_calibration.py`, dòng 332–339: preprocessing fit trên Train và chỉ transform Test; dòng 350–374: huấn luyện mô hình khóa và dự đoán Test; dòng 415–517: bootstrap theo `subject_id` để tính 95% CI.|Không retune sau khi xem Test; ghi limitation về chưa có formal sample-size calculation; nếu phát triển model mới cần holdout mới/temporal/external set.|

Tính áp dụng (quần thể, bối cảnh dùng mô hình):  
Mô hình hiện phù hợp để nghiên cứu **phân tầng nguy cơ tái nhập viện 30 ngày ở bệnh nhân suy tim người lớn tại thời điểm xuất viện trong dữ liệu tương tự MIMIC-IV**. Nhóm em chưa có đủ bằng chứng để triển khai ở bệnh viện khác hoặc tại Việt Nam. Mô hình chưa có external validation và chưa được đánh giá tác động lâm sàng thực tế.

### C4. Bảng kiểm nhanh (Phụ lục §7)

|Mục|Đạt / Chưa|Bằng chứng|
|-|-|-|
|Datasheet|Đạt|`datasheet.md` đã hoàn thành; có quần thể, tiêu chí chọn/loại, outcome, predictors, missing data, split, privacy, giới hạn và recommended use|
|Phiên bản + checksum dữ liệu|Đạt|MIMIC-IV v3.1;<br />`checksums.txt` đã lưu SHA-256 đầy đủ của<br />`hf_model_dataset_final.csv.gz`|
|Không định danh; căn cứ được phép dùng dữ liệu|Đạt|MIMIC-IV de-identified; credentialed access qua PhysioNet; ID được loại khỏi predictors; không public row-level data|
|Chọn đúng hướng dẫn báo cáo + checklist đã điền|Đạt|Chọn TRIPOD+AI; `TRIPOD_AI_checklist.xlsx` đã điền 52/52 tiểu mục, không để mục không áp dụng trống|
|Định nghĩa kết cục, nhãn, T0|Đạt|`readmit_30d`, all-cause ≤30 ngày; T0=discharge; predictor timing đã xác định|
|Chia theo bệnh nhân; tiền xử lý fit chỉ trên train|Đạt|Patient-level Train/Validation/Test; zero overlap; Imputer/Scaler/OHE fit Train only|
|Mô hình mốc hồi quy logistic (hoặc mốc phù hợp, nêu lý do)|Đạt|Logistic Regression baseline: ROC-AUC=0.6586 trên Validation|
|AUC + calibration (đường cong, in-the-large, slope) + Brier, kèm 95% CI|Đạt|Final Test ROC-AUC=0.6633 \[0.6492–0.6772]; Brier=0.1791 \[0.1755–0.1826]; calibration intercept=-0.3749 \[-0.4427–-0.3107]; slope=1.0102 \[0.9392–1.0786]; calibration curve đã lưu|
|Cỡ mẫu có đủ không; giới hạn về tính áp dụng|Đạt một phần|Cohort lớn: 70.066 admissions, 17.522 events; chưa có formal sample-size calculation a priori. Nhóm em đã ghi rõ limitation và giới hạn external applicability do MIMIC-IV đơn nguồn|

## D. Khai báo dùng AI

|Công cụ|Dùng cho phần nào|Nhóm đã kiểm chứng lại bằng cách nào|
|-|-|-|
|ChatGPT (OpenAI)|Nhóm em sử dụng để hỗ trợ xây dựng/kiểm tra cấu trúc code Python, giải thích metric, gợi ý cách trình bày FAIR/TRIPOD+AI/PROBAST+AI, hỗ trợ soạn báo cáo/slide và tổng hợp nội dung từ dữ liệu nhóm cung cấp.|Nhóm em chạy lại toàn bộ script trên môi trường local với dữ liệu MIMIC-IV; các số liệu chỉ lấy từ output thật CSV/terminal/PNG; metric được đối chiếu với confusion matrix và file kết quả; DOI/y văn được kiểm tra lại bằng file review/bài báo; nhóm em chịu trách nhiệm cuối cùng về nội dung|



