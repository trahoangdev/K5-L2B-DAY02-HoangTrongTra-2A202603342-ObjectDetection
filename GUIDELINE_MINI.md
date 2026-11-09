# Mini guideline — bài làm cá nhân

> Bản guideline có AI hỗ trợ. Ngày 09/11/2026, Codex đã rà hai lượt trên cùng 12 ảnh train, gồm lượt đối chiếu sau khi học viên thông báo cập nhật nhãn. Nhiều xe bỏ sót đã được bổ sung; vẫn còn các cạnh box và lớp cần kiểm lại. Chưa ghi nhận xác nhận của giảng viên cho ca xe cứu hộ.

> Cập nhật sau rework với gold: học viên xác nhận tự chỉnh trên ảnh/CVAT. Checker đạt 50/50; F1 và mean IoU kiểm lại đều 1,0000. Các số dòng và quan sát trong hai lượt self-QC dưới đây thuộc bản trước gold; xem kết quả hiện tại tại mục 4 của [báo cáo cá nhân](report/individual/2A202603342_HoangTrongTra.md). Bộ nhãn hiện có 653 box và trùng gold về lớp/tọa độ trên 50 file.

## Định nghĩa lớp

| ID | Lớp | Định nghĩa theo schema của bài | Ví dụ/điểm phân biệt |
| ---: | --- | --- | --- |
| 0 | `car` | Xe con, sedan, SUV, taxi và xe bán tải. | Xe bán tải vẫn là `car` theo quy định bài, dù có thùng phía sau; xe đang đỗ vẫn được gán nhãn. |
| 1 | `truck` | Xe tải chở hàng và xe đầu kéo. | Phân biệt xe tải có kết cấu chở hàng với xe bán tải thuộc `car`; không chọn lớp chỉ dựa vào kích thước trong ảnh. |
| 2 | `bus` | Xe buýt và minibus chở khách theo schema. | Xe buýt lớn thuộc `bus`; van/minivan chở người không tự động trở thành `bus`. Trường hợp khó phân biệt phải ghi lại và hỏi giảng viên. |
| 3 | `van` | Xe van, minivan chở hàng hoặc chở người. | Phân biệt với xe con thông thường và xe buýt; không suy lớp chỉ từ màu xe hay số người nhìn thấy. |

Giữ nguyên thứ tự ID khi tạo lớp trong công cụ annotation và khi xuất YOLO. Lớp thể hiện loại xe, không thể hiện trạng thái di chuyển, đỗ hay bị che.

## Quy tắc chung

- **Box bám sát vật thể:** dùng hình chữ nhật nhỏ nhất bao quanh phần xe nhìn thấy; không cố ý thêm lề, bóng đổ, phản chiếu hoặc nền đường. Mỗi xe hợp lệ có một box riêng, không gộp hai xe đang sát nhau.
- **Vật thể bị che khuất:** nếu vẫn nhận diện được lớp, vẽ một box bao quanh các phần nhìn thấy của chính xe đó; không suy đoán phần bị che. Box có thể chứa khoảng giữa các phần nhìn thấy do dạng hình chữ nhật, nhưng không mở rộng theo xe hay vật cản phía trước.
- **Vật thể bị cắt bởi rìa ảnh:** nếu nhận diện được lớp, chỉ bao phần xe còn trong ảnh; cạnh box trùng rìa ảnh tại nơi xe bị cắt, không kéo box ra ngoài ảnh.
- **Vật thể rất nhỏ/mơ hồ:** không đặt ngưỡng kích thước tùy ý. Nếu không đủ thông tin xác định lớp, ghi tên ảnh và vị trí vật thể, hỏi giảng viên trước khi chốt; không đoán lớp hoặc coi đây là quyết định bỏ qua vĩnh viễn.
- **Xe đang đỗ:** vẫn gán theo loại xe; chuyển động không phải điều kiện để gán nhãn.
- **Ngoài phạm vi:** không gán người, xe đạp, xe máy/mô tô, biển báo, đèn giao thông, hình xe trên quảng cáo hoặc phản chiếu của xe.
- **Xuất YOLO detection:** mỗi ảnh có một file `.txt` cùng basename; mỗi dòng đúng 5 giá trị `class_id x_center y_center width height`, tọa độ chuẩn hóa trong `[0, 1]`, rộng/cao lớn hơn 0 và box nằm trong ảnh. Ảnh thực sự không có xe hợp lệ vẫn có file rỗng.
- **Phạm vi dữ liệu:** gán nhãn 50 ảnh trong `dataset/images/train/`; giữ nguyên tên ảnh và schema. Không dùng nhãn eval hoặc gold train để suy đáp án cho bước này.
- **Quét ảnh trước khi lưu:** kiểm tra cả hai bên đường, dãy xe đang đỗ và bốn cạnh ảnh. Xe lớn ở sát cạnh dưới hoặc bị cắt khung vẫn phải có box nếu nhận diện được lớp; không chỉ gán các xe ở giữa ảnh.
- **Kiểm lớp trước khi xuất:** kiểm tra riêng xe buýt và các xe có thân van/minivan. Không giữ nhãn `car` mặc định cho mọi xe; nếu `car`/`van`/`bus` chưa rõ thì ghi lại để hỏi giảng viên.
- **Xe chồng lấn:** xác định rõ box thuộc xe trước hay xe sau trước khi sửa lớp hoặc thêm box. Ví dụ `drive_028` có hai xe trắng chồng lấn ở lề trái; box phải bám phần nhìn thấy của từng xe, không bao cả hai vào một box.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

Đối chiếu dưới đây dựa trên file YOLO đang có trong repo. Số dòng tính từ 1 trong file `.txt` tương ứng, có thể thay đổi sau khi xuất lại từ CVAT.

| Ảnh | Tình huống và vị trí để tìm lại | Quy tắc áp dụng | Lý do theo schema | Quan sát trên nhãn hiện tại |
| --- | --- | --- | --- | --- |
| [drive_001.jpg](dataset/images/train/drive_001.jpg) | Xe con màu trắng gần giữa ảnh về phía phải, bị cột lớn phía trước che một phần. | Gán `car` (0), box bao phần xe nhìn thấy; không suy đoán hình dạng xe sau cột. | Vẫn nhận diện được xe con; vật thể bị che một phần vẫn cần gán theo phần nhìn thấy. | Bản xuất lượt 2 có box `car` ở dòng 2. Giữ nguyên nguyên tắc xử lý che khuất. |
| [drive_025.jpg](dataset/images/train/drive_025.jpg) | Xe con màu trắng ở sát cạnh dưới, bên trái trung tâm, bị cắt khỏi khung ảnh. | Gán `car` (0); cạnh dưới box chạm cạnh dưới ảnh, các cạnh khác ôm phần xe nhìn thấy. | Xe bị cắt khung nhưng còn nhận diện được lớp; không bỏ xe và không ước lượng phần ngoài ảnh. | Bản xuất lượt 2 có box `car` dòng 7, chạm cạnh dưới ảnh. |
| [drive_008.jpg](dataset/images/train/drive_008.jpg) | Xe buýt dài màu vàng/xám ở nửa phải ảnh có phần khớp nối giữa thân xe. | Gán một box `bus` (2) cho toàn bộ phần xe buýt nhìn thấy; không tách thành hai xe theo khớp nối. | Khớp nối là bộ phận của cùng một xe; quy tắc là mỗi xe một box. | Bản xuất lượt 2 có một box `bus` dòng 20; xe con trắng lớn phía dưới đã được bổ sung box `car` dòng 23. Cần kéo các cạnh box sát hơn. |
| [drive_050.jpg](dataset/images/train/drive_050.jpg) | Xe bán tải màu cam/nâu ở nửa phải ảnh, phía dưới khu đất phủ lưới xanh, gần người đứng cạnh xe. | Gán `car` (0), box chỉ bao xe, không lấy thêm người đứng cạnh. | Schema của bài xếp xe bán tải vào `car`; người không thuộc bốn lớp cần gán. | Bản xuất lượt 2 có xe bán tải `car` dòng 20 và đã bổ sung xe buýt lớn góc dưới trái, `bus` dòng 14. Xe tối màu phía dưới có box `car` dòng 18; cần kéo sát box. |

## Ca cần giảng viên làm rõ trước khi chốt lớp

| Ảnh | Vị trí và điểm chưa rõ | Cách xử lý hiện tại |
| --- | --- | --- |
| [drive_019.jpg](dataset/images/train/drive_019.jpg) | Xe chuyên dụng màu trắng/xanh ở giữa phần dưới ảnh, có thiết bị kéo/cẩu phía sau cabin. Schema chưa mô tả riêng xe cứu hộ. | Bản xuất hiện gán `car` (0) ở dòng 27. Đây là nhãn đang có, chưa phải xác nhận lớp đúng; cần hỏi giảng viên trước khi chốt `car` hay `truck`. |

## Cập nhật sau self-QC hỗ trợ lần 1

- Đã rà 10 ảnh chọn ngẫu nhiên với seed `20261109`, thêm 2 ảnh để đối chiếu các ca trong guideline: tổng cộng 12 ảnh. Danh sách, số box và ảnh đối chiếu nằm trong [mục 3 của báo cáo](report/individual/2A202603342_HoangTrongTra.md#3-annotation-và-self-qc).
- Các lỗi cần ưu tiên: thiếu xe ở cạnh dưới trong `drive_004` và `drive_008`; thiếu xe buýt trong `drive_050`; bỏ sót dãy xe đỗ bên phải trong `drive_028`; nhiều box còn dư nền. Một số xe có thân van/minivan đang mang nhãn `car`, cần kiểm lại lớp.
- Checker đạt 50/50 chỉ xác nhận cấu trúc. Chưa kết luận bộ nhãn đạt chất lượng và chưa có gold-set evaluation. Lượt rà này chưa chỉnh sửa các file nhãn.

## Cập nhật sau self-QC hỗ trợ lần 2

- Học viên đã thông báo cập nhật nhãn. Checker tiếp tục đạt 50/50; tổng số box tăng từ 1.038 lên 1.063 (`car`: 910; `truck`: 13; `bus`: 90; `van`: 50).
- Đã xác nhận xe cạnh dưới trong `drive_004`, xe trắng lớn trong `drive_008` và xe buýt trong `drive_050` có box; dãy xe đỗ lề phải trong `drive_028` cũng đã được bổ sung.
- Cần sửa tiếp độ sát box, ví dụ xe trắng `drive_008` dòng 23; xe buýt `drive_009` dòng 1; hai xe phía dưới `drive_012` dòng 9/10; xe `drive_032` dòng 1/2; xe trắng `drive_040` dòng 18 và xe tối màu `drive_050` dòng 18.
- Tại `drive_028`, phân biệt xe trắng phía trước (`car`, dòng 11) và xe van phía sau (`van`, dòng 15); box dòng 15 hiện còn rộng, bao sang xe phía trước. Khi xem phóng to, chưa đủ cơ sở kết luận hai box là bản sao của cùng một xe; cần chỉnh theo đúng từng vật thể.
- Ca cứu hộ `drive_019` vẫn là `car` dòng 27, chưa có xác nhận của giảng viên. Chi tiết và ảnh mới nằm trong [báo cáo](report/individual/2A202603342_HoangTrongTra.md#3-annotation-và-self-qc), thư mục `assets/self_qc_v2/`. Ảnh lượt 1 được giữ để đối chiếu lịch sử.

## Ghi nhận việc sử dụng AI

- Codex hỗ trợ tổng hợp quy tắc từ `README.md`/`GUIDE.md`, soạn guideline, tạo ảnh đối chiếu từ nhãn YOLO do học viên xuất và rà hai lượt trên 12 ảnh train.
- Học viên đã thông báo sửa và cập nhật nhãn sau lượt rà đầu; các phát hiện lượt 2 và ca cần hỏi giảng viên vẫn cần xác nhận. Việc sử dụng AI được ghi tại mục 8 của báo cáo cá nhân.
