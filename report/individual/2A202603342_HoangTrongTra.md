# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Hoàng Trọng Trà
- MSSV: 2A202603342
- Email: trahoangdev@gmail.com
- Lớp: 2B
- Link repo bài làm: [GitHub](https://github.com/trahoangdev/K5-L2B-DAY02-HoangTrongTra-2A202603342-ObjectDetection)
- Link Colab đã chạy: [Notebook Colab đã chạy](https://colab.research.google.com/drive/1U0jC_pM6QEiqYfd4WMpypBf-qWMiI_4M); [bản notebook lưu trong repo](../../notebooks/day2_colab_executed.ipynb).

## 2. Schema và mini guideline

Áp dụng [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md), giữ ID car=0, truck=1, bus=2, van=3. Ba ca cụ thể: `drive_001` — xe con bị cột che, box bao phần nhìn thấy; `drive_025` — xe con cắt cạnh dưới, box dừng ở rìa ảnh; `drive_050` — xe bán tải thuộc car theo schema. Ca xe cứu hộ `drive_019` được ghi lại để giảng viên làm rõ car/truck.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file, 653 box: 533 car, 46 truck, 20 bus, 54 van.
- Kết quả checker và lỗi đã xử lý: `python tools/check_yolo_labels.py` đạt 50/50 file hợp lệ; không còn lỗi cấu trúc.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001`, `drive_004`, `drive_008`, `drive_009`, `drive_012`, `drive_019`, `drive_024`, `drive_025`, `drive_028`, `drive_032`, `drive_040`, `drive_050`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Bổ sung xe sát cạnh dưới trong `drive_004`/`drive_008`, xe buýt `drive_050` và dãy xe đỗ `drive_028`; kiểm lại các ca car/van. Bản nhãn trong workspace có thêm hai chỉnh sửa hình học ở `drive_025`/`drive_040`. [Nhật ký self-QC](assets/SELF_QC_NOTES.md) và [nhật ký chỉnh box](assets/geometry_review/changes.json).
- Commit khóa nhãn ban đầu: `c98e91d4934097e8e327054386a7861f110222d4` — `Lock initial training annotations`.

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 435 | 653 |
| FP | 628 | 0 |
| FN | 218 | 0 |
| Precision | 0,4092 | 1,0000 |
| Recall | 0,6662 | 1,0000 |
| F1 | 0,5070 | 1,0000 |
| mean_iou_of_matches | 0,7070 | 0,9996 |
| passed | false | true |

Kết quả tại [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json); [kết quả ban đầu](../../outputs/annotation_evaluation_initial.json) được giữ để đối chiếu. Học viên xác nhận rework trên ảnh/CVAT sau lần chấm đầu. Tại workspace, box taxi `drive_025` được siết cạnh phải/dưới để bớt nền; box sedan đen `drive_040` được siết cạnh trên và nới cạnh phải để bao phần xe nhìn thấy. Giữ nguyên lớp, số vật thể và gold; [nhật ký chỉnh sửa](assets/geometry_review/changes.json). Bảng trên phản ánh nhãn workspace; [QC kèm phiên Colab](assets/yolo26_runs/colab_annotation_evaluation.json) thuộc commit `86e632d`, trước hai chỉnh sửa này.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab, GPU Tesla T4 14913 MiB, Python 3.13.16, Ultralytics 8.4.208, Torch 2.14.1+cu130. Notebook clone commit `86e632d`, chưa chứa hai chỉnh sửa hiện tại ở `drive_025`/`drive_040`; metrics dưới đây thuộc phiên bản nhãn tại commit đó.
- Các tham số thay đổi so với notebook nếu có và lý do: Giữ nguyên `yolo26n.pt`, 50 epoch, `imgsz=640`, `batch=-1`, `device=0`, `seed=20260909` và split 50 train/10 eval. AutoBatch chọn batch 45; runtime sử dụng 2 dataloader workers theo log.
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0,6330 |
| Recall | 0,5662 |
| mAP50 | 0,6417 |
| mAP50–95 | 0,4358 |

Đã giải nén [assets/eval_predictions/](assets/eval_predictions/visuals/) và [assets/yolo26_runs/](assets/yolo26_runs/) từ `day2_colab_outputs.zip`, không kèm checkpoint. Trong [drive_051](assets/eval_predictions/visuals/drive_051.jpg), vật thể mang nhãn bus ở phía trên bên phải, sau cột, không có dự đoán bus ở confidence 0,25. [JSON metrics](assets/yolo26_runs/model_eval_metrics.json), [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv) và [metadata nguồn Colab](assets/yolo26_runs/colab_run_metadata.json) ghi nhận số liệu và nguồn dữ liệu.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa? `bus` có recall thấp nhất, bằng 0,000 theo bảng validation trên 2 vật thể eval. [drive_051](assets/eval_predictions/visuals/drive_051.jpg) minh họa bus ground truth phía trên bên phải không được dự đoán đúng lớp ở confidence 0,25.
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box? Cần kiểm độ sát và vị trí các cạnh box dự đoán, nhất là xe nhỏ, bị che hoặc cắt khung. Lần chạy này mAP50 = 0,6417, mAP50–95 = 0,4358; box có thể đạt IoU 0,50 nhưng chưa đạt các ngưỡng cao hơn.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét? [Confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix.png) có 3 vật thể truck được dự đoán car và 2 vật thể bus bị nhầm sang car/van. Cần rà tiêu chí phân lớp; chưa đủ bằng chứng kết luận nguyên nhân do guideline vì train chỉ có 20 bus so với 533 car.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì? Giữ eval riêng khỏi cập nhật trọng số để đánh giá nhất quán với split của bài. Trong notebook, 10 ảnh này dùng validation và chọn best.pt nên không phải test set độc lập.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| 10/11/2026 — runtime Colab | Ban đầu Colab báo lỗi cấp runtime. | Chạy lại notebook khi Colab cấp được GPU Tesla T4. | Đã chạy đủ 50 epoch, đánh giá và xuất 10 ảnh eval; phiên chạy dùng commit `86e632d`. |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): đã có 50 file nhãn đúng định dạng, mini guideline, self-QC, gold evaluation đạt ngưỡng, link Colab chạy đủ 50 epoch, metrics và 10 ảnh prediction. Phiên Colab dùng commit `86e632d`; cần đồng bộ hai chỉnh sửa `drive_025`/`drive_040` và chạy lại Colab nếu nộp đúng bản nhãn hiện tại. Chưa có nhật ký chi tiết từng thao tác rework CVAT hoặc xác nhận riêng của giảng viên cho ca xe cứu hộ.

## 8. Công cụ AI và tài liệu tham khảo

Công cụ: CVAT, Google Colab, Ultralytics/PyTorch và Python/Pillow. Codex hỗ trợ đọc tài liệu.

Nguồn ngoài repo: [Ultralytics Train](https://docs.ultralytics.com/modes/train/), [YOLO26](https://docs.ultralytics.com/models/yolo26/).
