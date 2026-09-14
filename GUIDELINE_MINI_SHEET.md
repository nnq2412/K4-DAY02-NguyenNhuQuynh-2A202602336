# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Nguyễn Như Quỳnh<br>
**MSSV:** 24026413<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** `SOLO`

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: Ảnh `drive_033`, chiếc xe khách màu trắng ở phía xa.
- Dấu hiệu nhìn thấy: Thân xe dài, có nhiều ô cửa sổ kính liên tiếp.
- Quy tắc áp dụng: Gán lớp `bus` khi có "thân xe khách dài, nhiều cửa sổ hoặc hàng ghế".
- Quyết định: Chọn lớp `bus`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì?: Đánh dấu hộp nhãn thành `needs_review` và tìm giáo viên hướng dẫn để hỏi.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: Ảnh `drive_008`, chiếc xe chở hàng phía bên trái.
- Dấu hiệu nhìn thấy: Phần đuôi là thùng hàng vuông vức nhưng được thiết kế tách rời hẳn với buồng lái phía trước (có khe hở).
- Quy tắc áp dụng: Gán lớp `truck` khi "khoang hàng tách biệt như xe tải".
- Quyết định: Chọn lớp `truck`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì?: Tôi sẽ phóng to hết mức (zoom in) trong CVAT để tìm khe hở giữa cabin và thùng xe. Nếu vẫn không thấy rõ, tôi sẽ áp dụng luật "Không đoán nếu quá mờ" và bỏ qua vật thể này.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: Ảnh `drive_022`, chiếc xe nằm sát mép trái màn hình.
- Dấu hiệu nhìn thấy khi phóng 100%: Chỉ nhìn thấy một phần đuôi xe con, khoảng 30% thân xe, phần còn lại nằm ngoài bức ảnh.
- Giá trị `visibility`: `partially_visible` (bị che/cắt).
- Giá trị `boundary`: `truncated` (chạm mép ảnh).
- Trạng thái `review_state`: `accepted`.
- Lý do: Dù xe bị cắt mất phần lớn, nhưng phần đuôi xe còn lại chứa đủ các đặc điểm rõ ràng để tôi tự tin phân loại nó là một chiếc ô tô con (sedan). Do đó, tôi vẫn vẽ hộp giới hạn và gán nhãn `car` thay vì bỏ qua.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: `71` — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
