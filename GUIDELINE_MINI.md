# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con, sedan, SUV, taxi và xe bán tải. | ID 0. Xe bán tải thuộc car; xe đang đỗ vẫn gán theo loại xe. |
| `truck` | Xe tải chở hàng và xe đầu kéo. | ID 1. Phân biệt với xe bán tải thuộc car; không chọn lớp chỉ theo kích thước trong ảnh. |
| `bus` | Xe buýt và minibus chở khách theo schema. | ID 2. Phân biệt với van/minivan; trường hợp chưa rõ cần hỏi giảng viên. |
| `van` | Xe van, minivan chở hàng hoặc chở người. | ID 3. Phân biệt với xe con thông thường và xe buýt. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: hình chữ nhật nhỏ nhất bao quanh phần xe nhìn thấy; mỗi xe một box riêng, không lấy thêm nền đường, bóng đổ hoặc xe bên cạnh.
- Vật thể bị che khuất: vẫn gán nếu nhận diện được lớp; box bao phần nhìn thấy của chính xe đó, không suy đoán phần bị che.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán nếu nhận diện được lớp; box dừng tại rìa ảnh và chỉ bao phần xe còn trong khung.
- Vật thể rất nhỏ/mơ hồ: không đoán lớp hoặc tự đặt ngưỡng kích thước; ghi tên ảnh, vị trí và hỏi giảng viên trước khi chốt.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001.jpg` | Xe con trắng gần giữa ảnh về phía phải bị cột che một phần. | Gán car (0), box bao phần xe nhìn thấy. | Nhận diện được xe con; không bỏ xe vì che khuất và không đoán phần sau cột. |
| `drive_025.jpg` | Xe con trắng sát cạnh dưới bị cắt khỏi khung ảnh. | Gán car (0), cạnh dưới box chạm rìa ảnh. | Xe bị cắt khung vẫn nhận diện được lớp; không ước lượng phần ngoài ảnh. |
| `drive_050.jpg` | Xe bán tải cam/nâu ở nửa phải ảnh, gần người đứng cạnh. | Gán car (0), box chỉ bao xe. | Schema xếp xe bán tải vào car; người đứng cạnh không thuộc bốn lớp cần gán. |
