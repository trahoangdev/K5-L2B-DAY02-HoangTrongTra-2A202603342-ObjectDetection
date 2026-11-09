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

Ca cần giảng viên làm rõ: xe cứu hộ trong `drive_019` hiện mang nhãn `car`, dòng 27; chưa ghi nhận xác nhận cách xếp `car`/`truck`. Những xe khó phân biệt `car`/`van` còn được liệt kê trong nhật ký self-QC. Số dòng nêu trên thuộc bản xuất được kiểm ở lượt 2 và có thể thay đổi khi xuất lại.

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file trong `dataset/labels/train/`, tổng cộng 1.063 box: 910 `car`, 13 `truck`, 90 `bus`, 50 `van`.
- **Kết quả checker và lỗi đã xử lý:** `python tools/check_yolo_labels.py` cho kết quả **ĐẠT cấu trúc: 50/50 file nhãn hợp lệ**. Chưa ghi nhận lỗi định dạng ở hai lượt kiểm. Checker không đánh giá độ đầy đủ, độ sát box hoặc lớp đúng của vật thể.
- **Danh sách ít nhất 10 ảnh đã rà bằng mắt:** Codex hỗ trợ rà trực quan 12 ảnh: `drive_001`, `drive_004`, `drive_008`, `drive_009`, `drive_012`, `drive_019`, `drive_024`, `drive_025`, `drive_028`, `drive_032`, `drive_040`, `drive_050`. Trong đó 10 ảnh chọn ngẫu nhiên với seed `20261109`, thêm `drive_008` và `drive_025` để đối chiếu guideline. Học viên đã thông báo cập nhật nhãn sau lượt rà đầu; phần rà có AI hỗ trợ được ghi rõ tại mục 8.
- **Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:** sau cập nhật, đã xác nhận bổ sung xe ở cạnh dưới trong `drive_004`, xe trắng lớn trong `drive_008`, xe buýt trong `drive_050` và dãy xe đỗ lề phải trong `drive_028`. Tổng số box tăng từ 1.038 lên 1.063. Còn box dư nền ở một số ảnh, box `van` dòng 15 trong `drive_028` bao sang xe phía trước và các lớp cần xác nhận. Những điểm này cần tiếp tục chỉnh trong CVAT, xuất lại và kiểm tra trước khi khóa nhãn.
- **Commit khóa nhãn ban đầu:** chưa ghi nhận; chưa xác nhận hoàn tất các vấn đề còn lại của self-QC.

Chi tiết từng ảnh, số box trước/sau và ảnh đối chiếu nằm trong [nhật ký self-QC](assets/SELF_QC_NOTES.md). Ảnh lượt 1 ở `assets/self_qc/`, lượt 2 ở `assets/self_qc_v2/`. Đây là ảnh kiểm nhãn, không phải ảnh prediction của model.

## 4. Gold QC và rework

Chưa có gold train và `outputs/annotation_evaluation.json` trong workspace; chưa có kết quả chấm annotation với gold để báo cáo.

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | Chưa chạy | Chưa có |
| FP | Chưa chạy | Chưa có |
| FN | Chưa chạy | Chưa có |
| Precision | Chưa chạy | Chưa có |
| Recall | Chưa chạy | Chưa có |
| F1 | Chưa chạy | Chưa có |
| mean_iou_of_matches | Chưa chạy | Chưa có |
| passed | Chưa xác định | Chưa xác định |

Chưa thực hiện rework dựa trên gold. Các thay đổi ở mục 3 là sửa nhãn sau self-QC trước khi khóa, không phải rework sau gold evaluation. Sau khi nhận gold đúng mốc, cần lưu kết quả thật tại `outputs/annotation_evaluation.json`, ghi số liệu trước/sau và lý do sửa hoặc giữ nhãn. Điều kiện sang bước train là F1 ≥ 0,80 tại IoU 0,50 và `mean_iou_of_matches` ≥ 0,70.

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

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md), chưa tự quy đổi thành điểm:

| Tiêu chí | Trạng thái và bằng chứng |
|---|---|
| Hoàn thành và định dạng YOLO | Đủ 50 file, checker đạt 50/50. |
| Đầy đủ vật thể | Đã sửa các ca bỏ sót được xác nhận trong mẫu rà; chưa kết luận đầy đủ trên toàn bộ 50 ảnh. |
| Độ bám sát và hình học box | Còn các box dư nền nêu trong self-QC lượt 2. |
| Phân lớp nhất quán | Giữ mapping bốn lớp; còn các ca cần kiểm/xác nhận. |
| Mini guideline và quyết định mơ hồ | Có định nghĩa lớp, quy tắc box và ít nhất ba ca cụ thể; ca cứu hộ còn chờ làm rõ. |
| Gold-set annotation QC | Chưa có kết quả gold evaluation, chưa xác nhận đạt ngưỡng F1/IoU. |
| Pipeline YOLO và phân tích | Chưa có kết quả Colab, metrics hoặc ảnh prediction; các nhận xét về model chưa thể hoàn thiện. |

## 8. Công cụ AI và tài liệu tham khảo

- **Công cụ:** CVAT để gán nhãn và xuất YOLO; Codex hỗ trợ đọc tài liệu. Python/Pillow dùng tạo ảnh đối chiếu từ nhãn đã xuất.
- **Phần đã tự thực hiện/kiểm tra được ghi nhận:** học viên thông báo hoàn thành gán 50 ảnh trên CVAT, chạy checker đạt 50/50 và cập nhật nhãn sau góp ý self-QC. Lượt kiểm tiếp theo xác nhận các box bổ sung nêu ở mục 3; các vấn đề còn lại và ca cần hỏi giảng viên chưa được xác nhận hoàn tất.
- **Phạm vi AI hỗ trợ:** thống kê và góp ý kiểm nhãn, trình bày báo cáo; Codex không thay đổi file nhãn trong hai lượt rà. Ảnh self-QC không phải đầu ra của model phát hiện vật thể.
- **Tài liệu tham khảo:** [README](../../README.md), [GUIDE](../../GUIDE.md), [RULES](../../RULES.md), [CHECKPOINTS](../../CHECKPOINTS.md), [RUBRIC](../../RUBRIC.md), [SUBMISSION](../../SUBMISSION.md) và mẫu báo cáo được cung cấp. Lượt rà sử dụng ảnh/nhãn train trong repo, không sử dụng gold train hoặc nguồn nhãn bên ngoài.
