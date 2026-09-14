# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Bùi Thành Long<br>
**MSSV:** 2A202602147<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** `SOLO`

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`
- Bốn mã ảnh: `drive_008`, `drive_022`, `drive_033`, `drive_038`
- Số vật thể thực tế: 89 (car 62, van 12, bus 10, truck 5) — sau khi tự kiểm và xoá 37 hộp không đủ căn cứ (xem mục 3); vẫn cao hơn mục tiêu 40–60 nhưng đã giảm mạnh so với 126 hộp ban đầu.
- Mã SHA-256 của gói YOLO của bạn: `f0dc22a76731cdf8de342fd84fae325e8d82c8acf480f3c163f62155ada98948`
- Mã SHA-256 của gói CVAT gốc của bạn: `4c7f2b6ff363724ff6a352209668442e758fa950dc8a38f1fc860eec8cfceb09`
- Nguồn đối chiếu: bộ nhãn đối chiếu do Lab Coach cấp (làm cá nhân)
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b` (đã đối chiếu khớp với `comparison_export_sha256` trong `comparison_summary.json` thật)
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: `day2-reference-4img-v1` (theo `release-manifest.json`), nhận sau khi hai gói xuất của tôi đã được kiểm (`my_export_audit.json`, `my_native_export_audit.json`)

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Tôi gán nhãn, tự kiểm tra và xuất cả hai gói (YOLO và CVAT gốc) từ công việc CVAT của mình trước khi mở bộ nhãn đối chiếu. Hai gói xuất của tôi có mã SHA-256 khác hoàn toàn với gói đối chiếu, và tôi chỉ tải bộ tham chiếu sau khi đã kiểm bài riêng theo `guideline-mini-sheet.md`, nên các quyết định phân lớp và hình học trong bài là của tôi, không bị ảnh hưởng bởi bộ tham chiếu.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_008 #27 | `truck` | thùng/sàn hàng chữ nhật rõ phía sau cabin | Gán `truck` khi thấy thùng/ben/sàn hàng rõ ràng |
| drive_008 #29 | `bus` | thân dài, nhiều hàng cửa sổ liên tiếp | Gán `bus` khi thân khách dài, nhiều cửa sổ/hàng ghế |
| drive_038 #13 | `van` | thân hộp kín, không có khoang hàng tách biệt | Gán `van` khi thân hộp nhỏ kín, dùng chở người/hàng |
| drive_022 #1 | `car` | dáng sedan, kích thước nhỏ, không thùng hàng | Gán `car` khi là sedan/hatchback/SUV/taxi |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Hộp `drive_008 #5` và hộp `drive_008 #18` đều thuộc lớp `car`, nhưng `#5` có `visibility=occluded` (bị xe khác che một phần) còn `#18` có `visibility=clear`. Lớp mô tả loại phương tiện (câu hỏi "đây là gì"), còn thuộc tính `visibility` mô tả mức bằng chứng nhìn thấy hộp đó (câu hỏi "tôi nhìn thấy nó rõ đến đâu") — hai xe có thể cùng lớp nhưng khác thuộc tính, hoặc ngược lại khác lớp nhưng cùng thuộc tính.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| `drive_033` có 12 hộp `car` gán ở dãy xe mờ phía xa | phạm vi/thuộc tính | Phóng 100% thấy các hộp quá nhỏ để phân biệt hình dạng, phần lớn đã gắn `visibility=unclear`; chỉ nhận ra "là một phương tiện", không đủ căn cứ khẳng định là `car` | Xoá cả 12 hộp thay vì giữ nguyên lớp `car` đã đoán trước đó — vì schema chỉ có 4 lớp cố định, không có lớp "phương tiện chung chung" để gán tạm, nên theo đúng quy tắc mục 1 "không đoán", hộp không đủ căn cứ phân lớp thì không được giữ trong bộ nhãn |

- Số hộp `needs_review` trước và sau khi kiểm: **22/126 hộp** trước khi kiểm (tập trung ở `drive_033` và `drive_038`) → **0/89 hộp** sau khi kiểm. Tôi đã xoá 37 hộp không đủ căn cứ phân lớp (đa số là các hộp cực nhỏ/mờ ở nền xa) và xử lý dứt điểm toàn bộ hộp `needs_review` còn lại (xác nhận lớp/thuộc tính rồi chuyển `review_state=confident`, hoặc xoá nếu vẫn không đủ căn cứ), sau đó xuất lại cả hai gói.
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Các hộp như `drive_038 #41–#55` (kích thước ~6×8 px, `visibility=unclear`) nằm ở dãy xe đậu rất xa trong ảnh; tôi không đủ căn cứ để khẳng định lớp cụ thể ngay cả khi phóng 100%. Cách xử lý: giữ nguyên `needs_review`, ghi lại toạ độ trong nhật ký quyết định, và hỏi Lab Coach xem có nên loại các hộp dưới một ngưỡng kích thước nhất định ra khỏi phạm vi gán nhãn hay không.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `0 0.265406 0.568070 0.128719 0.063766` (ảnh `drive_022`)
- Tên lớp và tọa độ điểm ảnh `xyxy`: `car`, xấp xỉ `(129.0, 343.2, 210.1, 384.0)` trên ảnh 640×640
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Định dạng YOLO chỉ kiểm tra được kiểu dữ liệu và việc toạ độ nằm trong [0,1] — nó không biết vật thể thật sự là xe gì, có nên được gán nhãn hay không, hay hộp có ôm sát vật thể hay không. Một dòng `0 0.5 0.5 0.1 0.1` vẫn "đúng định dạng" dù người gán nhãn gán nhầm một chiếc `truck` thành `car` (sai lớp), gán nhãn cho một xe máy ngoài phạm vi bốn lớp (sai phạm vi), hoặc vẽ hộp lệch/quá rộng so với vật thể thật (sai hình học). Vì vậy định dạng đúng là điều kiện cần, không phải điều kiện đủ.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`
- Mã ảnh thẩm định: `drive_008`
- Mô tả một dự đoán trong `detect_result.jpg`: Thực tế mô hình không đưa ra dự đoán nào đạt ngưỡng `conf=0.25` trên ảnh thẩm định `drive_008` — ảnh lưu ra không có hộp hay nhãn nào được vẽ đè lên, dù ảnh có nhiều xe rõ ràng (kể cả một xe bus lớn ở giữa khung hình).
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Việc không phát hiện được vật thể nào phù hợp với quy mô huấn luyện: chỉ 3 ảnh, 8 epoch, và `freeze=10` (đóng băng phần lớn backbone) — quá ít để mô hình học được đặc trưng bốn lớp xe. Đây là dấu hiệu về giới hạn của lần huấn luyện thử, không phải bằng chứng cho thấy nhãn của tôi sai.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Nếu huấn luyện lại với nhiều epoch hơn hoặc không đóng băng backbone mà mô hình vẫn không phát hiện được cả những vật thể lớn, rõ ràng (như xe bus giữa ảnh), thì nhận định "do thiếu dữ liệu/epoch" sẽ bị bác bỏ, và cần nghi ngờ lỗi cấu hình `data.yaml` hoặc đường dẫn ảnh/nhãn khi huấn luyện thay vì đổ lỗi cho quy mô dữ liệu.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?

Bộ dữ liệu chỉ có bốn ảnh (ba huấn luyện, một thẩm định), huấn luyện 8 epoch với mô hình đóng băng phần lớn backbone — quy mô này quá nhỏ để ước lượng độ chính xác, độ phủ hay khả năng khái quát của mô hình. Kết quả chỉ dùng để phát hiện lỗi rõ ràng trong dữ liệu/nhãn (chẩn đoán), không phải chỉ số hiệu năng có thể dùng để quyết định triển khai mô hình trong thực tế.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 47
- IoU trung bình và trung vị: trung bình 0.8533, trung vị 0.8701
- Mức đồng thuận lớp: 0.6809 (~68,1% các cặp hộp ghép được có cùng lớp)
- Số hộp phía bạn không ghép được: 42 (trong tổng 89 hộp của tôi, giảm từ 79 trước khi kiểm — phần lớn là các hộp mà bộ tham chiếu không gán ở cùng vị trí, xem mục 3)
- Số hộp phía đối chiếu không ghép được: 3
- Một điểm khác biệt cụ thể: Trong 47 hộp ghép được, 32 hộp trùng lớp và **15 hộp khác lớp** — gần như toàn bộ 15 hộp khác lớp đó đều là nhầm lẫn qua lại trong đúng ba lớp `truck/bus/van` (ví dụ `drive_022` hộp #2: tôi gán `truck`, đối chiếu gán `bus`, IoU=0.870; `drive_033` hộp #2: tôi gán `bus`, đối chiếu gán `van`, IoU=0.983), chỉ 2 trường hợp lẫn sang `car`. Ngược lại, mọi hộp `car` đều đồng thuận tuyệt đối. Đây không phải lỗi ngẫu nhiên mà là nhầm lẫn có hệ thống giữa ba lớp xe cỡ lớn/trung.
- Quy tắc hoặc hành động sửa phát sinh: Ranh giới `truck/bus/van` trong `guideline-mini-sheet.md` cần bổ sung tiêu chí cho xe nhỏ/xa trong ảnh — khi không thấy rõ thùng hàng (truck) hay hàng cửa sổ dài (bus), nên dựa thêm vào tỉ lệ khung thân xe (dài/cao) thay vì chỉ dựa vào chi tiết bề mặt. Tôi sẽ rà lại toàn bộ 15 hộp này trong CVAT, phóng to hết cỡ từng hộp để quyết định lại, thay vì chỉ sửa một hộp đơn lẻ.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

Đối chiếu chỉ đo mức độ hai bộ nhãn (của tôi và bộ tham chiếu) khớp nhau về hình học và lớp; nếu cả hai cùng mắc một lỗi hệ thống (ví dụ cùng bỏ sót một loại xe khuất, hoặc cùng hiểu sai một quy tắc ranh giới), điểm đồng thuận vẫn cao dù cả hai đều sai so với thực tế khách quan. IoU/đồng thuận là tín hiệu chẩn đoán về khả năng tái lập quy tắc, không phải bằng chứng về tính đúng đắn tuyệt đối của nhãn.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất là việc đối chiếu hai gói xuất (YOLO và CVAT gốc) từ cùng một công việc cho thấy đúng 89 hộp khớp nhau tuyệt đối về lớp và hình học (IoU tối thiểu 0.9999), chứng tỏ quy trình xuất dữ liệu nhất quán ngay cả sau khi sửa nhãn. Câu hỏi còn lại cho Lab Coach: nhóm nhầm lẫn có hệ thống giữa ba lớp `truck/bus/van` (15/47 hộp đối chiếu được) vẫn còn sau khi sửa — có tiêu chí khách quan nào (ví dụ tỉ lệ khung thân xe) để phân biệt ba lớp này khi xe nhỏ/xa, thay vì chỉ dựa vào chi tiết bề mặt hay không?
