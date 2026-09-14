# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Nguyễn Như Quỳnh<br>
**MSSV:** 24026413<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** `SOLO`

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`
- Bốn mã ảnh: `drive_008`, `drive_022`, `drive_033`, `drive_038`
- Số vật thể thực tế: `71`
- Mã SHA-256 của gói YOLO của bạn: `c34b2b529977ced2f3344aeb047e7da7ce10a109f0d1f028be933a67a4b18e97`
- Mã SHA-256 của gói CVAT gốc của bạn: `143c98a65aad666344eb5ab41ad159f5be4573e4958cda032765e06f433740fb`
- Nguồn đối chiếu: bộ tham chiếu do người hướng dẫn thực hành cấp (`teaching_reference`)
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: `14/09/2026, 3:59 PM`

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Bài của tôi vẫn độc lập vì tôi đã hoàn thành toàn bộ quá trình gán nhãn, tự kiểm tra và xuất gói dữ liệu (CVAT và YOLO) trước khi nhận được bộ tham chiếu từ người hướng dẫn. Các mã băm SHA-256 của gói xuất đóng vai trò như một bằng chứng xác nhận trạng thái dữ liệu đã được chốt và không bị thay đổi sau khi đối chiếu.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| Ảnh drive_038, chiếc xe màu xám bên phải | `van` | Xe có phần đuôi dạng hộp kín liền khối với thân, không có cửa sổ phụ ở khoang hàng | "thân hộp nhỏ, kín, dùng chở người hoặc hàng" |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Lớp (class) dùng để định nghĩa bản chất vật thể là gì (ví dụ: `car` hoặc `truck`). Thuộc tính (attribute) dùng để mô tả thêm trạng thái của vật thể đó (ví dụ: xe đang được hiển thị toàn phần hay bị che khuất).

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Vẽ hộp bọc luôn cả bóng râm đổ xuống mặt đường của chiếc xe tải | hình học | Tắt đi bật lại hộp nhãn để nhìn kỹ và thấy mép dưới bị hở | Thu nhỏ mép dưới sát vào bánh xe. Quy tắc: "Vẽ sát phần vật thể nhìn thấy" |

- Số hộp `needs_review` trước và sau khi kiểm: Trước khi kiểm tra: 3 hộp. Sau khi soát và sửa xong: 0 hộp.
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Có một chiếc xe ở rất xa bị lùm cây che khuất phần lớn, tôi không chắc là `car` hay `van`. Tôi đã xem lại hướng dẫn và áp dụng quy tắc "Không đoán nếu vật thể quá nhỏ hoặc mờ", quyết định không gán nhãn vật thể này.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `0 0.450 0.550 0.120 0.080`
- Tên lớp và tọa độ điểm ảnh `xyxy`: Lớp `car` (0). Tọa độ điểm ảnh thật trên ảnh 640x640: `[x_min: 250, y_min: 326, x_max: 326, y_max: 378]`
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Dòng đúng định dạng chỉ có nghĩa là file xuất ra đúng chuẩn số thực để mô hình YOLO có thể đọc mà không bị sập (lỗi code). Tuy nhiên, máy tính không biết được tọa độ đó là do người dán nhãn vẽ quá to lấn ra ngoài không gian (sai hình học), hoặc người đó phân loại nhầm xe tải thành ô tô con (sai lớp). Do đó, định dạng đúng hoàn toàn không đảm bảo nội dung nhãn có chất lượng cao.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`
- Mã ảnh thẩm định: `drive_008`
- Mô tả một dự đoán trong `detect_result.jpg`: Mô hình dự đoán được một chiếc ô tô với độ tự tin `car 0.82`, hộp chứa tương đối sát đuôi xe nhưng lại nhận diện sót chiếc xe tải ở làn bên cạnh.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào?: Việc nhận sót gợi ý tôi nên xem lại tập huấn luyện xem mình có vô tình bỏ qua (không gán nhãn) các phương tiện đang bị che khuất một phần hay không (lỗi phạm vi).
- Minh chứng nào có thể bác bỏ nhận định của bạn?: Nếu mở tập huấn luyện ra kiểm tra và thấy các xe tải đều đã được tôi gán nhãn đầy đủ, điều này sẽ bác bỏ nghi ngờ do lỗi dữ liệu. Nguyên nhân thực sự lúc này là do mô hình chưa được học đủ nhiều vòng (chỉ mới 8 epoch).
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?: Tập dữ liệu gồm 4 ảnh (3 train, 1 val) là quá nhỏ bé và không thể đại diện cho các điều kiện giao thông đa dạng trong thực tế. Kết quả này chỉ có tác dụng kiểm tra xem luồng mã (code pipeline) có hoạt động thông suốt hay không.

## 6. Đối chiếu nhãn

- Số hộp ghép được: `44`
- IoU trung bình và trung vị: `0.773082` và `0.809331`
- Mức đồng thuận lớp: `0.704545`
- Số hộp phía bạn không ghép được: `27`
- Số hộp phía đối chiếu không ghép được: `6`
- Một điểm khác biệt cụ thể: Nhãn của tôi gán một chiếc xe ở rất xa là `van`, trong khi bộ tham chiếu không gán nhãn cho nó vì vật thể đó quá mờ.
- Quy tắc hoặc hành động sửa phát sinh: Cần tuân thủ chặt chẽ hơn quy tắc "Không đoán nếu vật thể quá nhỏ hoặc mờ". Hành động sửa là xóa bỏ các hộp nhãn không đủ căn cứ này.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?: Mức đồng thuận cao (IoU cao) chỉ chứng minh hai người gán nhãn vẽ hộp giống hệt nhau. Nhưng hoàn toàn có khả năng cả hai đều cùng hiểu sai một quy tắc (ví dụ: cùng thống nhất gán sai một loại xe bán tải thành xe buýt). Do đó, sự đồng thuận không phải là chân lý tuyệt đối.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:
Các file JSON audit (như `my_export_audit.json`) được sinh ra với mã SHA-256 nguyên vẹn, chứng minh tôi đã tự hoàn thành quy trình gán nhãn, kiểm soát chất lượng và chốt dữ liệu (71 vật thể) một cách độc lập trước khi đối chiếu.
