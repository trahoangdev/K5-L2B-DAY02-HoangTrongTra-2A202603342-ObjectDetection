# Nhật ký self-QC — Hoàng Trọng Trà

Chi tiết hai lượt rà ngày 09/11/2026. Các quan sát được giữ theo từng lượt để đối chiếu trước/sau; số dòng YOLO có thể đổi khi xuất lại từ CVAT. Công cụ sử dụng được ghi tại mục 8 của [báo cáo cá nhân](../2A202603342_HoangTrongTra.md).

- Số file nhãn train hiện tại: **50 file**, tổng cộng **1.063 box** (`car`: 910; `truck`: 13; `bus`: 90; `van`: 50). Lượt rà đầu có 1.038 box (`car`: 889; `truck`: 13; `bus`: 88; `van`: 48); bản cập nhật tăng 25 box. Các số liệu này được kiểm ngày 09/11/2026.
- Kết quả checker: chạy `python tools/check_yolo_labels.py`, kết quả **ĐẠT cấu trúc: 50/50 file nhãn hợp lệ**. Chưa có lỗi cấu trúc cần sửa trong lượt kiểm này.
- Cách lấy mẫu: chọn ngẫu nhiên 10 ảnh từ danh sách tên ảnh đã sắp xếp bằng `random.Random(20261109).sample(..., 10)`; thêm `drive_008` và `drive_025` để đối chiếu guideline. Tổng cộng **12 ảnh được rà qua hai lượt** bằng cách đặt ảnh gốc cạnh ảnh vẽ box từ nhãn YOLO. Sau lượt 1, nhãn được cập nhật; lượt 2 kiểm lại bản xuất mới.
- Mẫu ngẫu nhiên: `drive_001`, `drive_004`, `drive_009`, `drive_012`, `drive_019`, `drive_024`, `drive_028`, `drive_032`, `drive_040`, `drive_050`.
- Commit khóa nhãn ban đầu: **chưa ghi nhận; còn các vấn đề cần kiểm/sửa trước khi khóa**.

Số `#` trên ảnh đối chiếu là số dòng trong file nhãn của từng lượt rà, tính từ 1, không phải object ID của CVAT. Số này đã thay đổi sau khi xuất lại nhãn. Các ảnh dưới đây là ảnh hỗ trợ kiểm annotation, không phải kết quả prediction của YOLO.

**Lượt 1 — giữ lại để đối chiếu lịch sử:** số dòng và các vấn đề trong bảng này thuộc bản nhãn trước cập nhật, không mô tả đầy đủ trạng thái hiện tại.

| Ảnh đối chiếu | Số box | Quan sát và việc cần làm |
|---|---:|---|
| [drive_001](self_qc/drive_001_review.jpg) | 16 | Xe con bị cột che và xe bạc phía dưới đã có box. Xe thân van trắng bên trái, dòng 10, đang là `car`: cần đối chiếu `car`/`van`. |
| [drive_004](self_qc/drive_004_review.jpg) | 16 | Xe đỏ/nâu lớn bị cắt ở cạnh dưới chưa có box. Cần bổ sung box theo phần nhìn thấy và kiểm lớp của xe; không bỏ xe chỉ vì bị cắt khung. |
| [drive_008](self_qc/drive_008_review.jpg) | 23 | Xe con trắng lớn ở phần dưới chưa có box. Xe đen cắt góc dưới phải cũng cần kiểm/bổ sung nếu nhận diện được lớp. Xe buýt khớp nối đã có một box `bus`, dòng 21. |
| [drive_009](self_qc/drive_009_review.jpg) | 32 | Xe buýt đỏ lớn phía trước có box `bus`, dòng 8; box còn chứa nền ở cạnh trái/trên, cần kéo sát phần xe nhìn thấy. |
| [drive_012](self_qc/drive_012_review.jpg) | 39 | Hai xe lớn phía dưới có box, nhưng box dòng 6 và 7 còn dư nền quanh xe; cần chỉnh độ sát. Kiểm riêng các xe có thân van/minivan trước khi chốt lớp. |
| [drive_019](self_qc/drive_019_review.jpg) | 32 | Xe cứu hộ đang là `car`, dòng 27; cần giảng viên xác nhận lớp. Xe thân van trắng bên trái, dòng 6, cũng đang là `car`, cần rà lại. |
| [drive_024](self_qc/drive_024_review.jpg) | 32 | Xe van bạc lớn phía trước có box `van`, dòng 30. Xe tối màu phía sau đang là `van`, dòng 31, cần kiểm lại `car`/`van` theo hình dáng; rà các cạnh box để bớt nền. |
| [drive_025](self_qc/drive_025_review.jpg) | 8 | Xe trắng bị cắt cạnh dưới đã có box `car`, dòng 3, chạm rìa ảnh; giữ nguyên nguyên tắc này. Kiểm lại độ sát các cạnh, không coi quan sát này là xác nhận toàn ảnh hoàn hảo. |
| [drive_028](self_qc/drive_028_review.jpg) | 10 | Dãy xe đỗ dọc lề phải ảnh chưa có box; cần rà và bổ sung từng xe nhận diện được lớp. Xe thân van trắng ở lề trái, dòng 8, đang là `car`, cần kiểm lại lớp. |
| [drive_032](self_qc/drive_032_review.jpg) | 6 | Các xe chính đã có box; dòng 2 (xe trắng sát cạnh dưới) và dòng 3 (xe tối màu giữa ảnh) có nhiều nền hai bên, cần kéo sát thân xe. |
| [drive_040](self_qc/drive_040_review.jpg) | 24 | Xe trắng lớn phía dưới có box `car`, dòng 1, nhưng còn dư nền, nhất là cạnh trái. Xe buýt/xe tải chính đã có box riêng; cần kiểm tiếp độ sát. |
| [drive_050](self_qc/drive_050_review.jpg) | 34 | Xe buýt lớn góc dưới trái chưa có box. Cả 34 box hiện đều là `car`, cần rà riêng các xe có thân van/minivan; xe bán tải cam/nâu dòng 6 là `car` theo schema. |

**Kết luận lượt 1:** có các vấn đề về completeness, tightness và lớp cần xác nhận. Lượt rà này giữ nguyên file nhãn; bản xuất được cập nhật sau đó.

**Lượt 2 — kiểm lại sau cập nhật:** checker tiếp tục đạt 50/50. Dùng lại 12 ảnh trên để đối chiếu các vấn đề đã ghi nhận.

| Ảnh đối chiếu mới | Box lượt 1 → lượt 2 | Kết quả kiểm lại |
|---|---:|---|
| [drive_001](self_qc_v2/drive_001_review.jpg) | 16 → 16 | Xe bị cột che có box `car`, dòng 2. Xe thân van trắng bên trái vẫn mang `car`, dòng 8; cần kiểm đúng vật thể và lớp trước khi chốt. |
| [drive_004](self_qc_v2/drive_004_review.jpg) | 16 → 17 | Xe đỏ/nâu cắt cạnh dưới đã được thêm box `car`, dòng 2. Box còn rộng bên trái/phải và phía trên, cần kéo sát phần xe nhìn thấy. |
| [drive_008](self_qc_v2/drive_008_review.jpg) | 23 → 24 | Xe trắng lớn phía dưới đã có box `car`, dòng 23; box còn nhiều nền quanh xe. Rà thêm xe đen chỉ lộ một phần ở góc dưới phải trước khi quyết định có đủ thông tin gán lớp hay không. |
| [drive_009](self_qc_v2/drive_009_review.jpg) | 32 → 33 | Đã bổ sung box ở cạnh phải. Box xe buýt đỏ lớn, dòng 1, vẫn dư nền phía trái/trên; cần chỉnh sát xe. |
| [drive_012](self_qc_v2/drive_012_review.jpg) | 39 → 41 | Có thêm box cho xe ở mép ảnh. Hai xe lớn phía dưới, dòng 9/10, vẫn có box dư nền; cần chỉnh cạnh. |
| [drive_019](self_qc_v2/drive_019_review.jpg) | 32 → 32 | Xe cứu hộ vẫn là `car`, dòng 27, chưa có xác nhận giảng viên. Xe trắng thân van bên trái, dòng 7, cần kiểm lại lớp. |
| [drive_024](self_qc_v2/drive_024_review.jpg) | 32 → 35 | Đã bổ sung xe ở phần trên ảnh. Xe tối màu sau van bạc vẫn là `van`, dòng 31, cần đối chiếu `car`/`van`; các box phía trước còn nền dư. |
| [drive_025](self_qc_v2/drive_025_review.jpg) | 8 → 8 | Xe trắng cắt cạnh dưới có box `car`, dòng 7, chạm rìa ảnh. Giữ nguyên nguyên tắc vẽ theo phần nhìn thấy. |
| [drive_028](self_qc_v2/drive_028_review.jpg) | 10 → 20 | Dãy xe đỗ lề phải đã có box. Ở lề trái có hai xe trắng chồng lấn: xe trước `car` dòng 11, xe sau `van` dòng 15. Box dòng 15 đang bao rộng sang xe trước, cần chỉnh theo phần nhìn thấy của xe sau; không kết luận là nhãn trùng chỉ từ hai box chồng nhau. Xem [ảnh phóng to](self_qc_v2/drive_028_left_detail.jpg). |
| [drive_032](self_qc_v2/drive_032_review.jpg) | 6 → 6 | Box xe tối màu giữa ảnh, dòng 1, và xe trắng sát cạnh dưới, dòng 2, vẫn dư nền hai bên; cần kéo sát. |
| [drive_040](self_qc_v2/drive_040_review.jpg) | 24 → 29 | Có thêm box cho xe xa phía trên ảnh. Xe trắng lớn phía dưới, dòng 18, vẫn có box rộng, đặc biệt cạnh trái. |
| [drive_050](self_qc_v2/drive_050_review.jpg) | 34 → 36 | Xe buýt góc dưới trái đã có box `bus`, dòng 14. Xe tối màu phía dưới, giữa bus và xe đỏ, có box `car`, dòng 18; cần siết box để bỏ nền và phần taxi phía trên. Xem [ảnh phóng to](self_qc_v2/drive_050_bottom_detail.jpg). |

**Kết luận lượt 2:** đã xác nhận nhiều xe bỏ sót được bổ sung. Cần xử lý tiếp độ sát box và các lớp chưa rõ trước khi khóa nhãn. Chưa có gold để tính F1/IoU ở thời điểm này. Nhãn được giữ nguyên trong lượt kiểm; các ảnh lượt 1/2 lưu riêng để đối chiếu. [Manifest lượt 2](self_qc_v2/manifest.json) ghi số box và SHA-256 của 12 file nhãn được rà, giúp nhận biết ảnh đối chiếu có còn khớp với bản xuất hiện tại.
