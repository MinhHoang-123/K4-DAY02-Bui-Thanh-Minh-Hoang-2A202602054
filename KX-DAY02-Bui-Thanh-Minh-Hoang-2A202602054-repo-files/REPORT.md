# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Bùi Thanh Minh Hoàng<br>
**MSSV:** 2A202602054<br>
**Hình thức:** Cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `35055f51ed69dd01f1c532945f49f8457f498e4db330577d75a975f95a470b44`   
- Bốn mã ảnh: `drive_008.jpg`, `drive_022.jpg`, `drive_033.jpg`, `drive_038.jpg`[cite: 2, 3]
- Số vật thể thực tế: 137 vật thể (34 ở ảnh 008, 4 ở ảnh 022, 33 ở ảnh 033, 66 ở ảnh 038)
- Mã SHA-256 của gói YOLO của bạn: `a257b2252addfd64e5c784ad17c09168b7d99fe6928fbc4e53b6e33cedd5a006`
- Mã SHA-256 của gói CVAT gốc của bạn: `337d286e111526366168db3ebb6bcfa5527a7ede78b25ec24cf734c70c9aed46`
- Nguồn đối chiếu: bộ tham chiếu Teaching (do người hướng dẫn thực hành cấp)
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: day2-teaching-reference.zip(application/x-zip-compressed) - 330413 bytes, last modified: 14/9/2026

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:
Bài làm của tôi là độc lập vì tôi đã tự thực hiện dán nhãn toàn bộ 4 ảnh trên CVAT bằng tài khoản cá nhân (username: MinhHoang)[cite: 2], sau đó xuất gói dữ liệu ra trước khi nhận bộ tham chiếu Teaching từ giảng viên.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| `drive_008.jpg` / vật thể rìa ảnh[cite: 2] | `car`[cite: 2] | Xe ô tô bị cắt một phần ở mép phải khung hình[cite: 2] | Chọn thuộc tính boundary là `truncated`[cite: 2] |
| `drive_038.jpg` / xe buýt[cite: 2] | `bus`[cite: 2] | Phương tiện chở khách cỡ lớn bị xe khác che mất một phần[cite: 2] | Chọn thuộc tính visibility là `occluded`[cite: 2] |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:
Lớp (class) xác định bản chất của vật thể đó là gì (ví dụ: `car`, `truck`, `bus` hoặc `van`)[cite: 2, 3]. Thuộc tính (attribute) xác định trạng thái hiển thị của vật thể đó trong bức ảnh (ví dụ: nó đang nhìn rõ `clear`, bị che lấp `occluded` hay bị mờ `unclear`)[cite: 2].

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| `2 0.324423 0.376119 0.618689 0.430120` | phạm vi/lớp/hình học/thuộc tính | Kiểm tra lại các nhãn có review_state là `needs_review` | `2 0.316143 0.359550 0.586709 0.396842` |

- Số hộp `needs_review` trước và sau khi kiểm: 45 hộp `needs_review` trước khi kiểm[cite: 2] / 20 (sau khi kiểm).
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Có nhiều đốm nhỏ ở xa trên ảnh `drive_038.jpg` được dán nhãn `car` với thuộc tính `unclear`, rất khó xác nhận đây có phải là ô tô hay không[cite: 2]. Cần hỏi Lab Coach xem có bỏ qua các vật thể quá xa này không.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `2 0.385562 0.723070 0.437875 0.351047`
- Tên lớp và tọa độ điểm ảnh `xyxy`: Lớp `bus` (class 2). Tọa độ pixel là `[106.6, 350.4, 386.9, 575.1]`.
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
Vì định dạng YOLO chỉ lưu trữ các con số tọa độ tỷ lệ và ID của lớp một cách vô tri[cite: 3]. Nếu người dán nhãn chọn nhầm lớp, đánh giá sai mép viền vật thể, hoặc quên không gán thuộc tính bị che khuất, file text xuất ra vẫn đúng chuẩn định dạng nhưng thực tế lại cung cấp dữ liệu huấn luyện sai lệch cho mô hình.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022.jpg`, `drive_033.jpg`, `drive_038.jpg`
- Mã ảnh thẩm định: `drive_008.jpg`
- Mô tả một dự đoán trong `detect_result.jpg`: Trên ảnh `drive_008.jpg`, mô hình phát hiện được 17 vật thể, trong đó phần lớn là các xe ô tô ở xa. Một số xe ở phía rìa bên phải được mô hình dự đoán là `truck` (xe tải) với độ tin cậy cao (khoảng 94%), mặc dù chúng bị cắt một phần bởi khung hình.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Gợi ý rằng mô hình chưa phân biệt rõ ràng giữa `car` (ô tô con) và `truck` (xe tải) khi vật thể ở xa hoặc bị cắt.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Các xe buýt xuất hiện trong tập huấn luyện đều bị che khuất một phần (`occluded`), có thể mô hình học cách nhận diện xe buýt dựa trên ngữ cảnh đường phố hơn là hình dáng thực tế.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?
Vì tập dữ liệu quá nhỏ (chỉ có 4 ảnh)[cite: 2] để mô hình có thể học được các đặc trưng tổng quát. Mô hình có thể chỉ đang "học thuộc lòng" (overfitting) tập dữ liệu này thay vì thực sự biết cách nhận diện ở môi trường bên ngoài.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 47
- IoU trung bình và trung vị: Trung bình 0.864091 / Trung vị 0.877928
- Mức đồng thuận lớp: 0.723404 (tương đương 72.34%)
- Số hộp phía bạn không ghép được: 90
- Số hộp phía đối chiếu không ghép được: 3
- Một điểm khác biệt cụ thể: Cụm xe buýt ở giữa ảnh `drive_038.jpg` (khoảng tọa độ x từ 0.25 đến 0.45, y từ 0.5 đến 0.7).
    - Phía tôi: Dán 1 hộp lớn `bus` (loại 2), thuộc tính `occluded` (bị che).
    - Phía đối chiếu: Dán 3 hộp nhỏ, trong đó 2 hộp là `car` (loại 0) ở phía trước và 1 hộp `bus` (loại 2) ở phía sau, che khuất một phần.
    
    *Lý do: Phía đối chiếu có thể đã phân tách cụm xe thành nhiều phần dựa trên hình dáng, trong khi tôi chọn theo "bản chất" của hành vi di chuyển (một đoàn xe đi cùng nhau).
- Quy tắc hoặc hành động sửa phát sinh: Sau khi đối chiếu, tôi nhận thấy có thể cần tách các xe bị che khuất một phần thành nhiều hộp nếu phần lộ ra đủ lớn, để mô hình học cách nhận diện các thành phần khác nhau của cụm xe.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?
Bởi vì cả hai bên dán nhãn (bạn và nguồn đối chiếu) có thể cùng mắc một lỗi sai giống nhau (ví dụ: cùng hiểu sai định nghĩa về ranh giới hộp bao, hoặc cùng bỏ sót các vật thể bị mờ `unclear` ở xa). 

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:
Câu hỏi còn lại cho Lab Coach: Minh chứng: Ảnh phủ `bbox_overlay.jpg` cho thấy sự khác biệt lớn trong cách tôi và nguồn tham chiếu xử lý cụm xe buýt.   

