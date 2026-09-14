# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Bùi Thanh Minh Hoàng[cite: 8]<br>
**MSSV:** 2A202602054[cite: 8]<br>
**Hình thức:** Cá nhân[cite: 8]<br>
**Mã cặp:** SOLO[cite: 8]

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

- Ảnh và mã vật thể: `drive_008.jpg` / vật thể chạm mép trên ảnh (tọa độ ytl="0.00")[cite: 2]
- Dấu hiệu nhìn thấy: Có dạng hình hộp vuông vắn, một phần bị viền ảnh cắt đi[cite: 2]
- Quy tắc áp dụng: Xét phần thân có đặc điểm của thân xe khách dài nhiều cửa sổ hay chỉ là thùng hộp nhỏ[cite: 7]
- Quyết định: Gán nhãn `bus` với thuộc tính `truncated`[cite: 2]
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đặt thuộc tính `review_state` thành `needs_review` và xin hỗ trợ từ Lab Coach[cite: 2, 7]

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038.jpg` / vật thể góc trái (tọa độ xtl="342.14", ytl="0.00")[cite: 2]
- Dấu hiệu nhìn thấy: Phần lộ ra có cấu trúc tách biệt giữa khoang lái và khoang hàng[cite: 2]
- Quy tắc áp dụng: Thùng, ben hoặc sàn hàng phân tách rõ ràng sẽ gán là `truck`[cite: 7]
- Quyết định: Gán nhãn `truck`[cite: 2]
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu `visibility` là `unclear` và chuyển sang `needs_review` để thảo luận[cite: 2, 7]

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_038.jpg` / ô tô ở rìa (tọa độ xtl="480.70", ytl="0.00")[cite: 2]
- Dấu hiệu nhìn thấy khi phóng 100%: Vật thể nằm ở vị trí quá xa và đồng thời bị cắt bớt bởi giới hạn mép khung hình[cite: 2]
- Giá trị `visibility`: `unclear`[cite: 2]
- Giá trị `boundary`: `truncated`[cite: 2]
- Trạng thái `review_state`: `needs_review`[cite: 2]
- Lý do: Bức ảnh không cung cấp đủ chi tiết hình ảnh để nhận diện rõ ràng bản chất, và vật thể chạm mép nên không nguyên vẹn[cite: 2]

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 137[cite: 8] — 40–60 là mục tiêu khối lượng, không phải điểm cắt.