# Danh sách lớp v0 — G08

Mỗi lớp được thiết kế để giải quyết bài toán nghiệp vụ cụ thể cho hệ thống xe thông minh: **model cần nhận ra cái này để làm gì?**

## DMS (Driver Monitoring System)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng DMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `eye_state` | open / closed / unknown | Phát hiện tài xế buồn ngủ hoặc vi ngủ (microsleep) khi mắt nhắm liên tục > 1.5 giây | Có, trừ khi tròng kính bị đốm phản xạ NIR che lóa |
| 2 | `gaze_zone` | road / cluster / center / mirror_l / mirror_r / mirror_c / lap / other | Phát hiện tài xế xao nhãng, nhìn lệch khỏi làn đường lái xe quá thời gian an toàn | Có, kết hợp hướng mắt và góc nghiêng đầu |
| 3 | `head_pose` | forward / left / right / downward / upward | Ước lượng hướng nhìn tổng thể khi tài xế đeo kính râm tối màu hoặc mắt bị che khuất | Có, hình khối xương sọ và đầu rất rõ trên NIR |
| 4 | `phone_interaction` | held / mounted / lap / none | Phát hiện và cảnh báo hành vi sử dụng điện thoại bất hợp pháp khi đang điều khiển xe | Có, nhận diện qua biên dạng chữ nhật của máy và tư thế tay |
| 5 | `hands_on_wheel` | both_hands / single_hand / no_hands | Giám sát mức độ sẵn sàng tiếp quản vô lăng trong hệ thống hỗ trợ lái tự động ADAS L2/L3 | Có, nhận diện bàn tay chạm tiếp xúc vào vành vô lăng |
| 6 | `seatbelt_status` | worn_correct / worn_incorrect / not_worn | Kích hoạt chuông cảnh báo an toàn và phục vụ phân tích dữ liệu hộp đen khi va chạm | Có, dải phản xạ quang học của dây an toàn nổi bật trên áo |

## OMS (Occupant Monitoring System)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng OMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `occupancy_state` | empty / adult / child / object | Xác định số lượng người ngồi để điều chỉnh nhiệt độ điều hòa và kích hoạt túi khí | Có, dựa trên độ chiếm chỗ và biên dạng cơ thể |
| 2 | `infant_seat` | rear_facing / front_facing / none | Tự động ngắt túi khí ghế phụ trước khi nôi trẻ em quay ngược chiều để tránh chấn thương | Có, phân biệt qua hình khối nôi và hướng đặt quai |
| 3 | `child_seat` | empty / occupied / none | Phát hiện và phát cảnh báo chống bỏ quên trẻ nhỏ trong xe (hệ thống CPD chống sốc nhiệt) | Có, dựa trên hình khối ghế an toàn có tựa lưng cao |
| 4 | `seatbelt_buckle` | buckled / unbuckled | Kiểm tra hành khách hàng ghế sau đã cài chốt an toàn trước khi xe lăn bánh | Có, chốt kim loại và dây đai phản xạ độ tương phản cao |
| 5 | `left_behind_object` | bag / box / cup / other | Phát thông báo nhắc nhở tài xế/hành khách không để quên hành lý, balo khi tắt máy rời xe | Có, nhận dạng hình học vật thể trên đệm ghế |
| 6 | `out_of_position` | normal / leaning / lying_down | Điều chỉnh lực bung hoặc hướng bung của túi khí rèm khi hành khách ngồi sai tư thế | Có, phân tích dáng người toàn thân trong khoang cabin |

## 2 câu hỏi leo thang nghiệp vụ

1. **Câu hỏi:** Khi ghế trẻ em có em bé hoặc hình nộm ngồi bên trong (`child_seat` có người), guideline hiện tại chỉ cho phép chọn `occupied = no` hoặc `unknown` mà không có giá trị `yes`, và cấm gán nhãn `person` cho trẻ em. Hệ thống phiên bản tiếp theo sẽ phân loại trẻ em như thế nào để phục vụ tính năng chống bỏ quên trẻ em (Child Presence Detection - CPD)?  
   - **Khung ví dụ:** `S02_ir.png` (ghế sau bên trái xe `rear_left`).  
   - **Cách tạm gán hiện tại:** Khi gán đúng thực tế `occupied = yes` cho ghế có hình nộm, script `cabin_check.py` ban đầu báo lỗi do schema gốc thiếu giá trị này. Theo hướng dẫn của Coach trên lớp, em đã cập nhật thêm giá trị `"yes"` vào file `scripts/cabin_schema.py` để script chấm hợp lệ hóa và chấp nhận trường hợp ghế trẻ em có người ngồi.

2. **Câu hỏi:** Khi tài xế đeo kính có tráng phủ quang học chống tia hồng ngoại (NIR-blocking/anti-reflective coating) hoặc đèn LED chiếu thẳng vào mắt kính gây lóa trắng xóa, camera không thể phân biệt mắt đang nhắm hay mở. Trong trường hợp này, quy chuẩn có cho phép model tự động hạ mức tin cậy (*confidence score*) và chuyển sang suy luận dựa trên tư thế đầu (`head_pose`) hay không?  
   - **Khung ví dụ:** `D06.png` (tài xế đeo kính gọng đen có đốm lóa lớn).  
   - **Cách tạm gán hiện tại:** Gán `eye_left = unknown`, `eye_right = unknown` kèm cờ `occluded = 1` cho các điểm mốc mắt trên skeleton `face13`, tuyệt đối không đoán là `closed` để tránh làm nhiễu dữ liệu huấn luyện phát hiện tài xế buồn ngủ.
