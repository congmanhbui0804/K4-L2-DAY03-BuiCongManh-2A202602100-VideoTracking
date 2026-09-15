# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Bùi Công Mạnh`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): Không bổ sung thêm — làm đúng theo 4 loại xe nêu trên, không mở rộng phạm vi.

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | 2 giây là khoảng đủ ngắn để chắc chắn không có xe khác kịp thế chỗ, nên giữ ID không rủi ro nhầm lẫn |
| Xe bị che lâu hơn ngưỡng trên | Nếu bị che **hoàn toàn** quá 25 frame: tạo track mới. Nếu vẫn còn **nhìn thấy một phần** thân xe xuyên suốt (ví dụ bị xe buýt che một phần, không che 100%) thì vẫn giữ nguyên ID dù thời gian che dài hơn 25 frame | Che hoàn toàn quá lâu thì không đủ căn cứ để chắc đó vẫn là xe cũ; nhưng nếu còn thấy một phần (đầu/đuôi xe) xuyên suốt thì có thể suy luận liên tục, không cần tách ID |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | Xe đã rời hẳn khung hình nghĩa là mất hoàn toàn thông tin theo dõi; khi xuất hiện lại rất khó khẳng định chắc chắn là cùng một xe, nên tạo ID mới cho an toàn |
| Hai xe cắt nhau / chồng lên nhau | Giữ ID theo hướng di chuyển và vận tốc trước lúc giao cắt; đối chiếu vị trí ngay trước và ngay sau khi hai xe tách nhau để xác nhận không bị hoán đổi ID | Nếu chỉ dựa vào vị trí tức thời lúc chồng lấp rất dễ gán nhầm ID giữa hai xe; dùng quỹ đạo trước/sau đáng tin cậy hơn |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: chỉ cần phân biệt được rõ ràng đây không phải xe máy (có đủ bề rộng thân xe điển hình của ô tô/van/bus/tải) là bắt đầu track, không chờ đến khi nhìn rõ chi tiết |
| Xe đang đỗ, không di chuyển | vẫn gán bbox đầy đủ theo từng frame như xe đang di chuyển, không bỏ qua hay gộp lại — xe đỗ vẫn thuộc phạm vi gán `vehicle` |
| Keyframe đặt dày ở đâu | đặt dày hơn ở: (1) đoạn xe đổi tốc độ hoặc hướng di chuyển đột ngột (xe chạy nhanh), (2) đoạn xe bị che một phần bởi vật cản/xe khác, (3) đoạn gần thời điểm xe vào/ra khung hình hoặc vào/ra vùng bị che khuất tầm nhìn. Đặt thưa hơn ở đoạn xe di chuyển đều, tốc độ ổn định, không bị che |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01`, frame ~73–100, ID 5 và ID 6
- Tình huống: xe buýt lớn chạy ngang che khuất 2 xe ô tô con phía sau trong một khoảng thời gian tương đối dài
- Quyết định: giữ nguyên ID xuyên suốt cho cả hai xe, không tách track mới
- Lý do: tuy bị che lâu nhưng vẫn có thể suy luận vị trí liên tục bằng cách nhìn đuôi xe trước khi bị che và đầu xe khi lộ ra lại phía sau xe buýt, nên tin cậy đủ để giữ cùng một ID thay vì tạo track rác

### Ca 2
- Clip / frame / ID: `clip_01`, ID 4, frame 149–151
- Tình huống: xe di chuyển nhanh gần thời điểm chuẩn bị rời khung hình, khó xác định chính xác frame xe thực sự đã ra khỏi khung hay chưa
- Quyết định: tua từng frame một (không tua nhanh) quanh khu vực nghi ngờ, chỉ bấm outside khi chắc chắn xe đã rời hẳn khung hình
- Lý do: nếu tua nhanh hoặc đoán, dễ để bbox "treo" thêm vài frame sau khi xe đã đi khỏi thật, hoặc ngược lại cắt track sớm hơn thực tế — cả hai đều gây lệch khi chấm so với gold

### Ca 3
- Clip / frame / ID: `clip_01`, ID 6, frame quanh 114–118
- Tình huống: vật cản (không phải xe khác) che khuất một phần tầm nhìn khiến khó quan sát chính xác vị trí xe ở giữa hai keyframe
- Quyết định: tua chậm, quan sát kỹ vật che tầm nhìn, thêm keyframe dày hơn ngay trước và sau đoạn bị che để bbox bám sát vật thể hơn
- Lý do: nếu chỉ đặt keyframe thưa như bình thường, bbox nội suy (interpolate) ở đoạn giữa dễ bị trôi lệch khỏi xe thật khi có vật cản làm gián đoạn quan sát

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Lần nộp này **không thực hiện kiểm chéo** với bạn cùng nhóm, nên phần dưới chỉ là các điểm rút ra từ việc chấm điểm với gold (`outputs/eval_vs_gold.json`):

- Cần quy định rõ ràng hơn thời điểm bấm "outside": số liệu chấm với gold cho thấy phần lớn lỗi false positive (55 FP) đến từ việc còn để bbox "treo" trước khi xe thực sự xuất hiện hoặc sau khi xe đã rời khung (ví dụ ID 4, 5, 6, 8) — luật ở mục 2 cần nói rõ hơn "chỉ bấm outside/tạo bbox khi chắc chắn 100% xe đã vào/ra khung", tránh để khoảng đệm dư thừa.
- Mục 3 "Keyframe đặt dày ở đâu" cần lượng hóa cụ thể hơn (ví dụ: thêm keyframe mỗi khi IoU ước lượng giữa hai keyframe liên tiếp có khả năng tụt dưới ~0.6) thay vì chỉ mô tả định tính, vì dữ liệu chấm với gold vẫn ghi nhận một số đoạn bbox trôi (IoU 0.51–0.56) ở giữa hai keyframe.
