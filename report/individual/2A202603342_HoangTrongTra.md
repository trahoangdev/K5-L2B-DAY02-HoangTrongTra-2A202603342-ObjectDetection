# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Hoàng Trọng Trà
- MSSV: 2A202603342
- Email: trahoangdev@gmail.com
- Lớp: 2B
- Link repo bài làm: [GitHub](https://github.com/trahoangdev/K5-L2B-DAY02-HoangTrongTra-2A202603342-ObjectDetection)
- Link Colab đã chạy: Chưa cung cấp.

## 2. Schema và mini guideline

Áp dụng schema cố định: `car=0`, `truck=1`, `bus=2`, `van=3`. Mỗi xe có một box sát phần nhìn thấy; xe đang đỗ, bị che một phần hoặc bị cắt khung vẫn được xử lý theo [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

Các ca đã đối chiếu trong lượt self-QC thứ hai:

| Ảnh | Tình huống | Quyết định và lý do |
|---|---|---|
| `drive_001` | Xe con trắng bị cột che. | Nhãn `car`, dòng 2; box bao phần xe nhìn thấy, không suy đoán phần bị che. |
| `drive_025` | Xe con trắng bị cắt cạnh dưới. | Nhãn `car`, dòng 7; box dừng tại rìa ảnh, không đoán phần ngoài khung. |
| `drive_008` | Xe buýt có khớp nối. | Một box `bus`, dòng 20, vì khớp nối thuộc cùng một xe. |
| `drive_050` | Xe bán tải cam/nâu. | Nhãn `car`, dòng 20, theo quy định riêng của schema bài. |

Trong lượt self-QC trước gold, xe cứu hộ trong `drive_019` mang nhãn `car`, dòng 27, được ghi là ca cần làm rõ `car`/`truck`. Những xe khó phân biệt `car`/`van` được liệt kê trong nhật ký self-QC. Các số dòng ở mục này thuộc bản trước rework; cần xem bản nhãn mới khi đối chiếu. Kết quả kiểm sau rework nằm tại mục 4.

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file trong `dataset/labels/train/`. Bản hiện tại sau rework có **653 box**: 533 `car`, 46 `truck`, 20 `bus`, 54 `van`. Bản khóa ban đầu có 1.063 box: 910 `car`, 13 `truck`, 90 `bus`, 50 `van`.
- **Kết quả checker và lỗi đã xử lý:** `python tools/check_yolo_labels.py` cho kết quả **ĐẠT cấu trúc: 50/50 file nhãn hợp lệ**, bao gồm lần kiểm sau rework. Checker không đánh giá độ đầy đủ, độ sát box hoặc lớp đúng của vật thể.
- **Danh sách ít nhất 10 ảnh đã rà bằng mắt:** Codex hỗ trợ rà trực quan 12 ảnh: `drive_001`, `drive_004`, `drive_008`, `drive_009`, `drive_012`, `drive_019`, `drive_024`, `drive_025`, `drive_028`, `drive_032`, `drive_040`, `drive_050`. Trong đó 10 ảnh chọn ngẫu nhiên với seed `20261109`, thêm `drive_008` và `drive_025` để đối chiếu guideline. Học viên đã thông báo cập nhật nhãn sau lượt rà đầu; phần rà có AI hỗ trợ được ghi rõ tại mục 8.
- **Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:** trước gold, đã xác nhận bổ sung xe ở cạnh dưới trong `drive_004`, xe trắng lớn trong `drive_008`, xe buýt trong `drive_050` và dãy xe đỗ lề phải trong `drive_028`, đưa tổng số box từ 1.038 lên 1.063. Lượt self-QC thứ hai ghi nhận box dư nền và lớp cần kiểm lại. Sau gold evaluation, học viên xác nhận tự chỉnh trên ảnh/CVAT rồi cập nhật lại 50 file nhãn; kết quả trước/sau ở mục 4.
- **Commit khóa nhãn ban đầu:** `c98e91d4934097e8e327054386a7861f110222d4` — `Lock initial training annotations`. Bộ nhãn được chấm lần đầu với gold vẫn khớp bản commit này.

Chi tiết từng ảnh, số box trước/sau và ảnh đối chiếu trước gold nằm trong [nhật ký self-QC](assets/SELF_QC_NOTES.md). Ảnh lượt 1 ở `assets/self_qc/`, lượt 2 ở `assets/self_qc_v2/`; các ảnh này giữ lịch sử và không đại diện cho bản sau rework. Đây là ảnh kiểm nhãn, không phải ảnh prediction của model.

## 4. Gold QC và rework

Đã nhận gold từ BTC trong `dataset/gold_train_labels/`. Kiểm tra đủ 50 file và cùng basename với 50 ảnh train; nhãn học viên và gold đều qua kiểm tra cấu trúc. Lần chấm đầu dùng script của bài với IoU matching 0,50, sau commit khóa nhãn nêu tại mục 3:

```powershell
python tools/evaluate_annotations.py --student-labels dataset/labels/train --gold-labels dataset/gold_train_labels --output outputs/annotation_evaluation.json
```

Kết quả hiện tại: [annotation_evaluation.json](../../outputs/annotation_evaluation.json). Bản kết quả ban đầu được lưu riêng tại [annotation_evaluation_initial.json](../../outputs/annotation_evaluation_initial.json) để đối chiếu trước/sau rework.

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 435 | 653 |
| FP | 628 | 0 |
| FN | 218 | 0 |
| Precision | 0,4092 | 1,0000 |
| Recall | 0,6662 | 1,0000 |
| F1 | 0,5070 | 1,0000 |
| mean_iou_of_matches | 0,7070 | 1,0000 |
| passed | false | true |

**Kết luận lần chấm đầu:** mean IoU đạt ngưỡng 0,70 nhưng F1 chưa đạt 0,80, nên chưa đủ điều kiện train. Tổng gold có 653 box, bộ nhãn học viên có 1.063 box; 435 cặp cùng lớp được ghép tại IoU ≥ 0,50. Chênh lệch số box và FP/FN chưa đủ để kết luận nguyên nhân; cần mở ảnh đối chiếu để phân biệt box thừa, bỏ sót, sai lớp hoặc sai hình học.

Các ảnh được ưu tiên ở lần chấm đầu và kết quả sau cập nhật:

| Ảnh | Box học viên trước → sau | Box gold | Cặp ghép trước → sau |
|---|---:|---:|---:|
| `drive_009` | 33 → 21 | 21 | 8 → 21 |
| `drive_002` | 47 → 13 | 13 | 11 → 13 |
| `drive_030` | 46 → 20 | 20 | 15 → 20 |

**Phương pháp rework do học viên xác nhận:** tự chỉnh trên ảnh/CVAT, sau đó cập nhật lại `dataset/labels/train/`. Toàn bộ 50 file thay đổi so với commit khóa ban đầu. Chưa có nhật ký từng thao tác sửa/giữ để ghi thêm nguyên nhân cụ thể; bảng trên chỉ mô tả số lượng và số cặp ghép đã kiểm được, không suy nguyên nhân từ FP/FN.

**Kết quả kiểm lại:** F1 = 1,0000 và `mean_iou_of_matches` = 1,0000; `passed=true`, đạt cả hai ngưỡng của script để sang bước train. Đối chiếu bổ sung cho thấy lớp và tọa độ của các box trùng gold trên cả 50 file; dữ liệu file khác về cách trình bày. Ghi nhận này được giữ cùng xác nhận phương pháp rework của học viên. Kết quả chất lượng bản gán ban đầu được bảo toàn trong cột trước sửa và JSON ban đầu; bộ nhãn và kết quả sau rework cần được lưu bằng commit mới.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Chưa có kết quả Colab để ghi nhận.
- Các tham số thay đổi so với notebook nếu có và lý do: Chưa ghi nhận thay đổi tham số hoặc kết quả chạy.
- Đường dẫn best checkpoint trong phiên chạy: Chưa có.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | Chưa có kết quả |
| Recall | Chưa có kết quả |
| mAP50 | Chưa có kết quả |
| mAP50–95 | Chưa có kết quả |

Chưa có `assets/eval_predictions/` và `assets/yolo26_runs/` trong báo cáo. Sau khi chạy Colab, cần bổ sung cả hai folder từ `day2_colab_outputs.zip`, số liệu thật và dẫn chứng ảnh/vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook theo thông tin hiện có:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?** Chưa xác định vì chưa có kết quả đánh giá model theo lớp hoặc ảnh prediction để đối chiếu.
2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?** Cần kiểm độ sát và vị trí các cạnh box. Box có thể khớp ở ngưỡng IoU 0,50 nhưng chưa đủ sát ở các ngưỡng cao hơn; cần đối chiếu cả nhãn và dự đoán trên ảnh trước khi kết luận nguyên nhân.
3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?** Self-QC ghi nhận các ca `car`/`van` cần kiểm lại và xe cứu hộ `drive_019` cần làm rõ trong guideline. Đây là phát hiện trên nhãn; chưa có dữ kiện về lỗi phân lớp của model để kết luận mối liên hệ.
4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?** Giữ riêng eval giúp đánh giá trên các ảnh không dùng để cập nhật trọng số huấn luyện. Trong notebook, eval là validation để theo dõi và chọn `best.pt`, nên không phải test set độc lập. Đưa eval vào train sẽ làm mất ý nghĩa của split đã quy định.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Self-QC lượt 1 | Bỏ sót xe sát cạnh dưới, xe buýt và dãy xe đang đỗ. | Rà ảnh đối chiếu có AI hỗ trợ; học viên thông báo sửa và xuất lại nhãn từ CVAT. | Lượt 2 xác nhận các box bổ sung ở `drive_004`, `drive_008`, `drive_028`, `drive_050`; tăng 25 box trên toàn bộ train. |
| Self-QC lượt 2 | Một số box còn dư nền; box xe van trong `drive_028` bao sang xe phía trước. | Ghi ảnh, số dòng YOLO và ảnh phóng to trong nhật ký để tìm lại vật thể. | Cần chỉnh tiếp và kiểm lại bản xuất mới. |
| Kiểm lớp | Một số xe khó phân biệt `car`/`van`; xe cứu hộ chưa được schema mô tả riêng. | Đối chiếu guideline, ghi trường hợp cần hỏi. | Chưa ghi nhận xác nhận của giảng viên cho xe cứu hộ. |
| Gold QC và rework | Lần chấm đầu F1 = 0,5070, chưa đạt ngưỡng 0,80. | Học viên xác nhận tự chỉnh trên ảnh/CVAT và cập nhật 50 file; chạy lại checker và evaluator. | Checker đạt 50/50; F1 và mean IoU sau sửa đều 1,0000. Lớp/tọa độ hiện trùng gold trên 50 file; cần bổ sung nhật ký thao tác cụ thể nếu có. |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md), chưa tự quy đổi thành điểm:

| Tiêu chí | Trạng thái và bằng chứng |
|---|---|
| Hoàn thành và định dạng YOLO | Đủ 50 file, checker đạt 50/50. |
| Đầy đủ vật thể | Sau rework, 653 box được ghép với 653 box gold; không còn FP/FN theo evaluator. |
| Độ bám sát và hình học box | Sau rework, mean IoU của các cặp ghép = 1,0000; các ảnh self-QC cũ ghi lịch sử trước sửa. |
| Phân lớp nhất quán | Giữ mapping bốn lớp; bản hiện tại trùng gold về lớp và tọa độ trên 50 file. |
| Mini guideline và quyết định mơ hồ | Có định nghĩa lớp, quy tắc box và ít nhất ba ca cụ thể; ca cứu hộ còn chờ làm rõ. |
| Gold-set annotation QC | Đã có kết quả trước/sau: F1 0,5070 → 1,0000; mean IoU 0,7070 → 1,0000; sau sửa `passed=true`. Phương pháp rework được học viên xác nhận, hiện còn thiếu nhật ký thao tác chi tiết. |
| Pipeline YOLO và phân tích | Chưa có kết quả Colab, metrics hoặc ảnh prediction; các nhận xét về model chưa thể hoàn thiện. |

## 8. Công cụ AI và tài liệu tham khảo

- **Công cụ:** CVAT để gán nhãn và xuất YOLO; Codex hỗ trợ đọc tài liệu. Python/Pillow dùng tạo ảnh đối chiếu từ nhãn đã xuất.
- **Phần đã tự thực hiện/kiểm tra được ghi nhận:** học viên thông báo hoàn thành gán 50 ảnh trên CVAT, chạy checker đạt 50/50 và cập nhật nhãn sau góp ý self-QC. Sau lần chấm gold đầu, học viên xác nhận tự chỉnh trên ảnh/CVAT rồi cập nhật lại 50 file; evaluator kiểm lại đạt các ngưỡng tại mục 4. Chưa có nhật ký từng thao tác sửa/giữ và xác nhận riêng của giảng viên cho ca cứu hộ.
- **Phạm vi AI hỗ trợ:** thống kê và góp ý kiểm nhãn. Codex không thay đổi nhãn học viên hoặc gold trong lượt đánh giá này. Ảnh self-QC không phải đầu ra của model phát hiện vật thể.
- **Tài liệu tham khảo:** [README](../../README.md), [GUIDE](../../GUIDE.md), [RULES](../../RULES.md), [CHECKPOINTS](../../CHECKPOINTS.md), [RUBRIC](../../RUBRIC.md), [SUBMISSION](../../SUBMISSION.md) và mẫu báo cáo được cung cấp. Hai lượt self-QC sử dụng ảnh/nhãn train trong repo; sau khi khóa nhãn, gold do BTC cung cấp trong `dataset/gold_train_labels/` được dùng để chấm annotation trên máy.
