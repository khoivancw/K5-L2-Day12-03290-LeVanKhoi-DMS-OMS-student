# Tự đánh giá — Lê Văn Khởi · G08

## 1. Tự rà soát 4 cảnh

Loại lỗi: `seat_flip`, `wrong_class`, `guess`, `box`, `missing`, `extra`, `lr_flip` (trái/phải mặt), `flag` (thiếu cờ điểm), `ok`.

| Ảnh | Đối tượng | Loại lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|
| S01_rgb.png | object (cốc nước) | seat_flip | Lúc đầu để seat mặc định là driver, đã sửa lại thành rear_middle | Đã sửa |
| S02_ir.png | child_seat (ghế có hình nộm) | wrong_class | Gán occupied=yes bị script ban đầu báo lỗi; theo hướng dẫn của coach đã cập nhật cabin_schema.py thêm giá trị "yes" để script chấm chấp nhận | Đã sửa |
| D01.png | face (khuôn mặt tài xế) | guess | Tài xế ngửa đầu uống nước; gaze_zone ban đầu để road, sau sửa thành other | Đã sửa |
| D06.png | face (mắt dưới đốm lóa kính) | flag | Ban đầu đoán mắt nhắm (closed); đã sửa lại thành unknown và tick occluded trên face13 | Đã sửa |

## 2. Lỗi mình lặp lại nhiều nhất hôm nay

Lỗi: Thuộc tính mặc định của CVAT (như seat=driver hoặc eye=open) dễ bị trôi nếu không kiểm tra kỹ bảng DETAILS ngay sau khi vẽ bounding box.

Vì sao mình mắc: Do thao tác vẽ box liên tục nên có lúc quên mở tab DETAILS để chỉnh lại thuộc tính tương ứng với đối tượng.

Lần sau tránh bằng cách: Tuân thủ quy trình kỹ sư: vẽ xong box nào thì mở ngay tab DETAILS chốt chính xác các thuộc tính rồi mới chuyển sang đối tượng tiếp theo.

## 3. Ghi chú về disagreement_log

Một dòng bạn của mình đúng và mình sai: Ở ảnh S01_rgb.png, mình vẽ box object nhưng quên chỉnh seat nên bị gán là driver, bạn Vũ Tiến Vinh gán đúng là rear_middle.

Mình học được gì từ dòng đó: Camera OMS chỉ nhìn hàng ghế sau xe nên không bao giờ có ghế driver; việc review chéo giúp phát hiện ngay các lỗi sơ suất về thuộc tính mặc định.
