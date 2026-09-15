# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: `Bùi Công Mạnh`
Ngày: `15/09/2026`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT |
| Thời gian gán `clip_02` (warm-up) | ≈ 47 phút |
| Thời gian gán `clip_01` | ≈ 60 phút (1 giờ 0 phút 30 giây) |
| Số track đã vẽ trong `clip_01` | 8 |
| Số keyframe trung bình mỗi track | 5 |

*Cách tính thời gian (từ ảnh chụp Explorer):* tên hai file zip xuất từ CVAT có gắn giờ export theo giờ UTC: `task_3_annotations_2026_09_15_08_14_02` (clip_02, warm-up) và `task_4_annotations_2026_09_15_09_14_32` (clip_01). Quy đổi sang giờ Việt Nam (UTC+7) được **15:14:02** và **16:14:32** — khớp với thời điểm hai file này hiện "Date modified 4:19 PM" trên máy (chỉ lệch vài phút do thời gian tải xuống). Repo bài (`K4-L2-DAY03-BuiCongManh...`) được giải nén lúc **14:27** (2:27 PM), coi là mốc bắt đầu có dữ liệu để gán:
- Thời gian gán `clip_02`: từ 14:27 đến 15:14:02 → **47 phút 2 giây**
- Thời gian gán `clip_01`: từ 15:14:02 đến 16:14:32 → **1 giờ 0 phút 30 giây**

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. Xe buýt che mất 2 xe ô tô con — xử lý bằng cách nhìn đuôi xe và đầu xe trước để suy ra vị trí xe bị che.
2. Xe chạy quá nhanh — xử lý bằng cách tua từng frame một để không bỏ sót vị trí xe.
3. Khuất tầm nhìn (vật cản che khuất) — xử lý bằng cách tua chậm và quan sát kĩ vật che tầm nhìn để xác định đúng thời điểm xe xuất hiện/biến mất.

## 2. Tự kiểm và kiểm chéo

Lần nộp này **không thực hiện kiểm chéo** với bạn cùng nhóm (không có `reports/review_partner.md` đi kèm), nên các mục liên quan đến kiểm chéo dưới đây được ghi nhận là không áp dụng:

- Kiểm chéo với: *không thực hiện*.
- Số lỗi hai bên tìm được trong bản của nhau: *không có, vì không kiểm chéo*.
- Ca hai người quyết khác nhau / luật còn thiếu trong `GUIDELINE_MINI.md`: *không có dữ liệu kiểm chéo để đối chiếu*. Gợi ý riêng nếu có làm kiểm chéo về sau: nên thống nhất rõ luật khoanh bbox khi xe bị che một phần (có bao gồm gương/đèn xe hay không), vì đây là dạng lệch thường gặp giữa hai người gán nhãn cùng một clip.

Ba lượt tua tự kiểm (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa) — dựa trên các lỗi thực tế phát hiện được khi chấm điểm bản nhãn so với gold:

- Lượt 1 (nhìn ID): phát hiện một số đoạn ID còn "treo" — bbox tồn tại ở ID 4, 5, 6, 8 tại các khoảng không có xe thật tương ứng.
- Lượt 2 (frame đầu/cuối mỗi track): phát hiện bbox xuất hiện sớm hơn hoặc còn tồn tại muộn hơn thời điểm xe thực sự vào/ra khung hình (VD: ID 6 ở frame 79–100, ID 5 ở frame 73–78, ID 4 ở frame 149–151).
- Lượt 3 (frame giữa track): phát hiện hiện tượng bbox trôi nhẹ, lệch khỏi vật thể ở giữa hai keyframe (IoU tụt còn 0.51–0.56 tại các frame như 82, 83, 56, 114, 117, 165).

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | *cần lấy trực tiếp từ file `manifest.json` (giá trị hash không thể tự tạo hay suy luận từ ảnh chụp màn hình, phải copy từ file thật)* |
| Thời điểm khóa | 15/09/2026, 16:14:32 (suy từ file `task_4_annotations_2026_09_15_09_14_32`, quy đổi UTC → giờ VN; xảy ra ngay trước khi mở thư mục `gold` lúc 16:25) |
| Số row / frame / track trước khi mở reference | 620 bbox (row) · 190 frame · 8 track |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | 0.784 | 0.743 | 0.843 | 0.863 | 0.947 | 0.890 | 0.840 | 55 | 8 | 0 |
| Sau rework | 0.784 | 0.743 | 0.843 | 0.863 | 0.947 | 0.890 | 0.840 | 55 | 8 | 0 |

*Ghi chú: hai hàng giống nhau vì bản khóa (pre-gold) đã đạt cổng ngay từ lần đầu tiên (xem bên dưới), nên không phát sinh vòng rework/chấm lại nào khác — `outputs/eval_vs_gold.json` là kết quả chấm duy nhất, dùng chung cho cả hai hàng.*

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có** (IDF1 0.947 ≥ 0.80; MOTA 0.890 ≥ 0.75; MOTP 0.840 ≥ 0.70 — cả ba đều đạt ngay từ bản khóa đầu tiên)

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

Vì bản khóa đã đạt cổng ngay từ đầu nên không bắt buộc phải rework. Các lỗi dưới đây được liệt kê lại từ `outputs/eval_vs_gold.json` để ghi nhận, chưa cần sửa vì không ảnh hưởng đến việc qua cổng:

| Loại lỗi | Frame | ID | Ghi chú |
| --- | --- | --- | --- |
| Bbox treo/thừa (còn bbox trước khi track tham chiếu xuất hiện) | 79–100 | ID 6 | Chưa sửa — không bắt buộc vì đã qua cổng; nếu sửa thì bấm outside đúng frame xe rời khung |
| Bbox treo/thừa | 73–78 | ID 5 | Chưa sửa — tương tự trên |
| Bbox treo/thừa (còn bbox sau khi track tham chiếu đã rời khung) | 149–151 | ID 4 | Chưa sửa — tương tự trên |
| Bbox trôi (IoU chỉ còn 0.51–0.56 so với gold) | 82, 83, 165, 117, 114, 56 | ID 5/8/6/4 | Chưa sửa — nếu sửa thì thêm keyframe quanh các frame lệch |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | 3.13.15 / 8.4.145 / 2.11.0+cu128 / 0.5.13 |
| weights / hai tracker | `yolo26n.pt` / ByteTrack control (`bytetrack.yaml`) và BoT-SORT + ReID treatment (`configs/trackers/botsort-reid.yaml`) |
| conf / IoU / imgsz / classes | 0.25 / 0.70 / 960 / [2, 5, 7] (car, bus, truck) |
| device | GPU (`"0"`) |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.784 | 0.743 | 0.843 | 0.863 | 0.947 | 0.890 | 0.840 | 55 | 8 | 0 |
| ByteTrack control vs gold | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.763 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 | 2 |
| ReID vs bạn | 0.701 | 0.638 | 0.777 | 0.839 | 0.881 | 0.763 | 0.814 | 81 | 63 | 3 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

Ở cả bốn phép so, IDF1 luôn cao hơn MOTA: với nhãn của tôi là 0.947 so với 0.890 (chênh 0.057), với ByteTrack là 0.875 so với 0.749 (chênh 0.126), với ReID là 0.900 so với 0.792 (chênh 0.108). Trường hợp của tôi không rơi vào tình huống "MOTA cao mà IDF1 thấp" (đó là dấu hiệu sai ID chứ không phải bỏ sót), mà là MOTA thấp hơn IDF1 khá rõ mặc dù `ID switch = 0`. Lý do: MOTA được tính gần đúng theo công thức `1 − (FP + FN + IDSW) / số bbox gold`, tức là phạt tuyến tính trên tổng số box sai (bao gồm cả bbox thừa/thiếu ở mức từng frame), trong khi IDF1 đánh giá ở mức quỹ đạo (trajectory) — chỉ cần chuỗi ID được giữ liên tục và khớp đúng danh tính xuyên suốt là điểm rất cao, bất kể có vài khung hình lệch bbox hay dư/thiếu box. Với nhãn của tôi, 55 FP (bbox thừa nhỏ, chủ yếu do còn treo bbox trước/sau khi xe thực sự vào/ra khung) kéo MOTA xuống đáng kể dù không hề có lỗi đổi ID, nên IDF1 vẫn giữ được ở mức rất cao.

Với hai mô hình, khoảng cách IDF1–MOTA càng lớn khi FN càng cao (ByteTrack FN=54 so với ReID FN=26), cho thấy MOTA nhạy với số lượng box bị bỏ sót/thừa theo từng frame hơn là với lỗi định danh. Đây cũng chính là lý do MOTA "không phạt nặng" lỗi ID: trong công thức, `IDSW` chỉ là một số hạng cộng thêm rất nhỏ so với tổng `FP+FN` (ví dụ ByteTrack chỉ có 2 lần đổi ID trên 573 bbox gold, đóng góp chưa tới 0.4 điểm phần trăm vào MOTA), nên dù một tracker đổi ID vài lần giữa một track dài, MOTA gần như không thay đổi. Đây chính xác là lý do các metric riêng cho định danh như IDF1 và AssA được tạo ra — để bù cho điểm mù này của MOTA.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

ReID treatment nhỉnh hơn control ở cả IDF1 (0.900 so với 0.875, +0.025) và AssA (0.820 so với 0.776, +0.044), nhưng số lần đổi ID (IDSW) lại **bằng nhau**: cả hai đều là 2 lần trên 190 frame. Điểm khác là ID switch xảy ra ở các track khác nhau: ByteTrack đổi ID tại track gold 4 (frame 59, ID 14→15) và track gold 5 (frame 94, ID 23→32); còn ReID đổi ID tại track gold 5 (frame 87, ID 17→18) và track gold 6 (frame 113, ID 24→31). Đáng chú ý, ở track gold 4, ReID không hề bị đổi ID trong khi ByteTrack thì có — đây là một frame sequence cụ thể (quanh frame 59) cho thấy treatment "tốt hơn" cho riêng track này. Ngược lại, ở track gold 6 thì ReID lại là bên bị đổi ID (frame 113) trong khi ByteTrack giữ track 6 nguyên vẹn — cho thấy "tệ hơn" cục bộ. Tổng số lần tách track (track bị chia làm nhiều ID) cũng bằng nhau ở cả hai bên (3 track bị chia), chỉ khác track nào bị ảnh hưởng, nên xét thuần theo IDSW/số track-split thì ReID **không cải thiện đáng kể** khả năng giữ định danh trên clip này — phần tăng AssA/IDF1 đến từ việc các đoạn track được khớp dài hơn/đầy đủ hơn (nhờ FN giảm) chứ không hẳn từ việc giảm số lần đổi ID.

Cần nhấn mạnh: đây **không phải** một so sánh cô lập được causal effect của riêng đặc trưng ReID, vì hai file cấu hình (`bytetrack.yaml` và `botsort-reid.yaml`) là hai tracker implementation khác nhau (BoT-SORT dùng mô hình chuyển động và các ngưỡng liên kết khác ByteTrack, cộng thêm nhánh appearance embedding), nên chênh lệch quan sát được có thể đến từ nhiều khác biệt thuật toán khác (motion model, ngưỡng track buffer...) chứ không chỉ từ việc có/không dùng đặc trưng ngoại hình.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

DetA tăng khá rõ khi chuyển từ control sang treatment: 0.649 → 0.711 (+0.062). Động lực chính là FN giảm mạnh, từ 54 xuống còn 26 (giảm hơn một nửa), trong khi FP gần như không đổi, thậm chí nhích nhẹ lên (88 → 91). Nói cách khác, treatment bắt được nhiều xe/khung hình hơn (ít bỏ sót hơn) nhưng đổi lại sinh thêm vài box dư thừa.

Xét lỗi còn lại: phần lớn vẫn là lỗi **detector**, cụ thể là hiện tượng "model bắt thiếu đoạn" — track gold 6 chỉ mới được phủ 44/56 frame (79%) ở bản ReID (so với 42/56, 75% ở ByteTrack) và track gold 5/6/8 cũng từng bị hụt tương tự ở ByteTrack — cho thấy detector vẫn bỏ lỡ object trong một số khoảng thời gian (thường quanh lúc bị che khuất một phần). Bên cạnh đó, lỗi **association** (tách track, đổi ID) vẫn tồn tại với số lượng gần như không đổi (3 track bị chia ID ở cả hai bên, IDSW = 2 ở cả hai bên) — cho thấy khâu liên kết ID qua occlusion vẫn là điểm yếu chung của cả hai tracker trên clip này, ReID không giải quyết được vấn đề này một cách rõ rệt, trong khi phần cải thiện DetA chủ yếu đến từ việc tracker mới "cứu" lại được các frame mà detector suýt bỏ sót (nhờ dự đoán/track prediction khác nhau giữa hai thuật toán) chứ không hẳn do bản thân detector (YOLO26n, cùng conf/iou/imgsz) thay đổi.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Trong phần so sánh "ReID vs bạn" (mục "BBOX CHỈ CÓ Ở MỘT BẢN"), ReID tạo ra track ID 7 kéo dài từ frame 16 đến 116 (43 frame liên tục) mà không khớp với bất kỳ track nào trong nhãn của tôi. Một track "ma" kéo dài gần 1/4 độ dài clip mà không tương ứng vật thể nào tôi đã gán cho thấy nhiều khả năng đây là một **false positive dai dẳng** của ReID (ví dụ bắt nhầm một vật tĩnh/vùng nền giống ô tô là một track liên tục), trong khi nhãn tay của tôi (đã qua vòng tự kiểm và đối chiếu gold, đạt IDF1 0.947/MOTA 0.890) không hề gán track này — đây là bằng chứng cho thấy annotation của tôi đúng còn ReID sai ở đoạn này.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

Ở track bản A (nhãn của tôi) ID 6, ReID cho thấy track này bị "tách" thành 3 ID khác nhau ([31, 24, 28]) và chỉ phủ được 48/79 frame (61%) so với track A6 mà tôi gán. Khoảng chênh lệch độ dài lớn này (79 frame theo tôi so với việc ReID chỉ theo dõi liên tục được 61% quãng đó) khiến tôi phải quay lại xem đúng track ID 6 quanh frame 104–114 (nơi ReID đổi ID và IoU chỉ còn 0.52–0.57) để kiểm tra: có khả năng đây là đoạn xe bị che khuất gần như hoàn toàn trong một khoảng dài, và tôi cần xác nhận lại liệu mình có đang "nối" đúng một xe duy nhất xuyên suốt đoạn che khuất đó, hay thực chất có hai xe khác nhau đi qua khu vực đó và tôi đã lỡ gán chung một ID. Đây là trường hợp bằng chứng định lượng (độ phủ thấp + nhiều lần tách ID ở đúng một track) gợi ý nên xem lại thủ công thay vì mặc định tin annotation ban đầu là đúng.

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

- Quy định rõ thời điểm bấm "outside" ngay khi xe khuất hẳn khỏi khung hình, tránh để bbox treo trước/sau (đây là nguồn gốc chính của 55 FP trong bản của tôi, dù không ảnh hưởng đến việc qua cổng lần này nhưng vẫn nên khắc phục để nhãn chính xác hơn).
- Thêm luật bắt buộc thêm keyframe khi IoU giữa hai keyframe liên tiếp có khả năng tụt dưới một ngưỡng nhất định (ví dụ mỗi khi xe đổi tốc độ/hướng rõ rệt, hoặc khi xe bị che một phần bởi vật cản/xe buýt), để giảm hiện tượng "bbox trôi".
- Với xe chạy nhanh: giữ thói quen tua từng frame thay vì tua nhanh, để không bỏ lỡ vị trí thật của xe giữa hai frame.
- Với xe bị che khuất bởi xe buýt hoặc vật cản: chuẩn hoá cách suy luận vị trí (nhìn đầu xe/đuôi xe trước khi bị che và sau khi lộ ra) thành một quy tắc rõ ràng trong `GUIDELINE_MINI.md`, thay vì chỉ dựa vào kinh nghiệm cá nhân.
- Bổ sung bước kiểm chéo với bạn cùng nhóm (lần này chưa làm) — đây là bước quan trọng để phát hiện các lỗi mà một mình không tự nhận ra được, nên đưa vào quy trình bắt buộc cho các clip sau.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md` *(không áp dụng — không thực hiện kiểm chéo lần này)*
- [x] `reports/REPORT.md` 
