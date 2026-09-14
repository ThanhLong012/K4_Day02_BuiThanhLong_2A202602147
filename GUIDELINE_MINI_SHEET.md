# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Bùi Thành Long<br>
**MSSV:** 2A202602147<br>
**Hình thức:** cá nhân <br>
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

- Ảnh và mã vật thể: `drive_033`, hộp `bus` tại toạ độ ảnh xấp xỉ (0,434)-(193,640) — góc dưới-trái ảnh (số thứ tự hộp đã đổi qua các lần xuất do đã xoá/sửa các hộp khác, nên xác định bằng toạ độ cho chắc)
- Dấu hiệu nhìn thấy: Hộp nằm sát góc trái-dưới ảnh, bị mép ảnh cắt và bị che một phần (`visibility=occluded`, `boundary=truncated`); vẫn thấy được thân xe khá dài với ít nhất hai hàng cửa sổ liên tiếp trên phần thân còn lại trong khung hình.
- Quy tắc áp dụng: Gán `bus` khi thấy thân khách dài, nhiều hàng cửa sổ liên tiếp; không gán `van` vì `van` có thân hộp ngắn hơn và không có dãy cửa sổ hành khách rõ như vậy.
- Quyết định: `bus`
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Nếu không còn thấy được ít nhất một hàng cửa sổ liên tiếp do bị che/cắt quá nhiều, tôi sẽ đặt `review_state=needs_review`, ghi lại toạ độ hộp vào nhật ký quyết định và xin Lab Coach xác nhận trước khi chốt lớp.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038`, hộp `truck` tại toạ độ ảnh xấp xỉ (421,136)-(523,212) (đối chiếu với hộp `van` liền kề tại (492,109)-(581,207))
- Dấu hiệu nhìn thấy: Hộp `truck` có `visibility=clear`, `boundary=inside`; nhìn rõ khoang chở hàng dạng thùng/sàn hở tách biệt với cabin, khác với hộp `van` ngay cạnh có thân hộp kín liền khối.
- Quy tắc áp dụng: Gán `truck` khi thấy thùng/ben/sàn hàng hoặc thiết bị công vụ rõ ràng; không gán `van` vì `van` phải có thân hộp kín một khối, không tách khoang hàng như xe tải.
- Quyết định: `truck`
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Nếu ranh giới cabin/thùng hàng bị che khuất không rõ, tôi sẽ đặt `visibility=unclear`, `review_state=needs_review` thay vì đoán giữa `truck` và `van`.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_033`, một hộp `car` từng gán ở toạ độ xấp xỉ (313,33)-(321,37) — **đã xoá khỏi bộ nhãn cuối cùng**, mô tả dưới đây là trạng thái tại thời điểm phát hiện, trước khi sửa (xem `REPORT.md` mục 3)
- Dấu hiệu nhìn thấy khi phóng 100%: Hộp chỉ rộng khoảng 8×4 điểm ảnh, nằm ở dãy xe đậu rất xa; phóng 100% vẫn không phân biệt được hình dạng thân xe, chỉ thấy một khối mờ.
- Giá trị `visibility` (tại thời điểm phát hiện): `unclear`
- Giá trị `boundary` (tại thời điểm phát hiện): `inside`
- Trạng thái `review_state` (tại thời điểm phát hiện): `needs_review`
- Lý do: Vật thể quá nhỏ và mờ để có căn cứ phân lớp chắc chắn (đúng trường hợp mục 1 "không đoán"). Quyết định cuối cùng sau khi tự kiểm: **xoá hẳn hộp này** (cùng 11 hộp tương tự khác) thay vì giữ `needs_review` vô thời hạn, vì không có lớp "phương tiện chung chung" nào trong schema để gán tạm cho vật thể chỉ nhận ra là xe nhưng không rõ loại.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính. (đã kiểm tự động: cả 89/89 hộp có đủ `visibility`/`boundary`/`review_state` hợp lệ)
- [x] Đã xử lý mọi hộp `needs_review`. (22 → 0, đã kiểm tự động trên gói CVAT gốc mới nhất)
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 89 (sau khi xoá 37 hộp không đủ căn cứ, giảm từ 126 ban đầu) — 40–60 là mục tiêu khối lượng, không phải điểm cắt; vẫn cao hơn mục tiêu nên bạn tự cân nhắc có cần rà thêm phạm vi không trước khi tick hộp này.
