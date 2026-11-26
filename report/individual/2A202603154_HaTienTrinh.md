# Báo cáo Day 13 — Hà Tiến Trịnh, 2A202603154

## 1. Kết luận trước
Mã số `2A202603154`, task `D13-2A202603154`, 10 frame: `s01f20`, `s01f47`, `s02f10`, `s02f45`, `s03f05`, `s03f43`, `s04f01`, `s04f20`, `s05f24`, `s05f39`. Máy: Windows 11 Home; trình duyệt: Chrome ; CVAT v2.81.0 (chạy Docker trên máy). MSSV bạn ngồi cạnh: 2A202603286.

Đã vẽ tổng cộng **82 cuboid** (52 Car, 12 Cyclist, 10 Pedestrian, 5 Bus, 3 Truck), 5 đến 13 cuboid mỗi frame. Frame ít nhất là `s05f39` (5 cuboid): đường hai bên có dải bụi cây, trong 30 m chỉ có vài xe, và hai xe buýt ở khoảng 26 m phía trước che gần hết phần đường phía sau chúng. Phiếu `selfcheck` cuối cùng **không cờ cuboid nào (0/82)**. Tôi tin nhất phần **hướng** (xe đi thẳng cùng chiều có yaw 267,6–271,3°, xe đi thẳng ngược chiều 87,6–91,9°; ngoại lệ là 6 xe chéo hướng: `s02f10` #3 79,8°, #4 và #13 85,2°, #11 8,1°, #12 185,7°, và `s04f20` #7 258,4°) và **đáy của xe con ở gần** (14/15 xe con trong 10 m có khe hở với mặt đất từ −0,03 đến 0,14 m; riêng `s02f45` #1 là 0,37 m). Yếu nhất là đáy của người đi bộ ở sau bụi cây (đáy khoảng −1,05 m), **kích thước** (nhiều xe dùng chung một bộ kích thước), **class của xe van**, và các **vật xa 30–50 m** (người và xe con chỉ có 27–155 điểm).

Cách dùng CVAT:
- Đổi góc nhìn: trong khung 3D kéo chuột trái để xoay, kéo chuột phải để dời, lăn chuột để phóng to/thu nhỏ. Xoay đến khi nhìn thẳng từ trên xuống, nháy đúp vào chỗ trống để về góc mặc định.
- Vẽ cuboid: chọn công cụ cuboid → chọn label → **Shape**, đưa hộp tới cụm điểm rồi nháy đúp để đặt. Ở **Top** chỉnh tâm, dài, rộng và xoay yaw; ở **Side/Front** chỉ kéo đáy chạm đường và chỉnh chiều cao, không xoay.
- Bật **Appearance -> Cuboid orientation** để thấy vạch đỏ `+X` của hộp và căn nó về đầu xe. Sau khi Save, chạy `python scripts/lab.py preview` để xem cuboid chiếu lên 7 ảnh camera (`work/xem/`).

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f20-3d`, `t1-s01f20-top`, `t1-s01f47-3d`, `t1-s01f47-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

Quy ước trên mọi ảnh nhìn từ trên xuống: tôi xoay khung 3D để ảnh cam01 (nhìn về phía trước) khớp với **cạnh trên** của ảnh.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f20 | Phía trước (−y) ở **cạnh trên**; +y chỉ xuống cạnh dưới (ra sau xe); +x chỉ sang **cạnh trái** (bên trái xe) | Gần xe phẳng, thấp hơn LiDAR khoảng 1,75 m (z ≈ −1,74 đến −1,82 m trong 10 m). Dốc xuống nhẹ về phía trước: z ≈ −2,0 m ở 20–30 m và ≈ −2,15 m ở 30–40 m. Bên phải có bụi cây và vỉa hè cao hơn mặt đường | ~34 m: xe con #8 phía sau (33,7 m, 120 điểm) | Xe con, xe buýt (#3, cách 17,4 m, dài 12,4 m), người đi bộ; ở xa phía trước trên cam01/cam03 còn một dòng xe | Bên trái sát xe: xe con #1 (3,6 m, cam05/cam06). Sau-phải: xe con #2 (8,1 m, cam09). Phía sau: xe #4 (17,0 m) và #8 (33,7 m). Bên phải: 2 người đi bộ #5, #6 (12,6 m và 17,2 m, cam07/cam08). Trước-trái, bên kia dải phân cách: xe ngược chiều #7 (18,7 m, cam06) | Cụm điểm dày ở bên trái khoảng 9 m (x ≈ +9 m, y ≈ −1 m), cao từ −1,8 m đến +0,4 m, kéo dài dọc đường. Có thể là cây hoặc bụi cây ở dải phân cách, nhưng không camera nào cho thấy rõ nên tôi chưa khẳng định |
| s01f47 | Phía trước (−y) ở **cạnh trên**; +y chỉ xuống cạnh dưới; +x chỉ sang **cạnh trái** | Phẳng, thấp hơn LiDAR khoảng 1,8 m (z ≈ −1,78 đến −1,87 m tới 30 m cả trước lẫn sau). Bên phải có vỉa hè hoặc bụi cây cao hơn mặt đường (z ≈ −1,5 đến −1,3 m) | ~31 m: xe con #5 phía sau (30,6 m, 155 điểm) | Xe con (Mercedes trắng, xe trắng phía trước), van xanh đậm (gán Truck), xe đạp có người; trên cam07 có người đứng cạnh hàng xe đạp công cộng | Phía sau: xe con #4 (7,1 m) và #5 (30,6 m) trên cam09. Sau-phải: 2 xe đạp có người #6, #7 (11,6 m và 17,4 m, cam08/cam09). Phía trước: 3 xe chờ đèn (#1–#3, 9–11 m) | 5 điểm ở z ≈ −3,1 m tại (x ≈ +2,0; y ≈ −6,4 m), tức **thấp hơn mặt đường quanh đó (−1,77 m) khoảng 1,35 m**. Không thuộc vật nào; có thể là nắp cống/rãnh hoặc phản xạ sai (giả thuyết) |

Chỉ **camera** cho biết (trên s01f20, s01f47):
1. Màu sắc và loại xe cụ thể: xe buýt công cộng sơn trắng–đỏ–xanh (s01f20); van màu xanh đậm (s01f47), giúp phân biệt van chở hàng với xe con.
2. Biển báo chỉ hướng màu xanh treo trên giàn, đèn tín hiệu và mũi tên trên mặt đường, từ đó biết làn nào đi thẳng, làn nào rẽ.
3. Đầu hay đuôi xe: thấy đèn phanh đỏ và biển số sau của các xe phía trước (s01f47) nên biết chúng đi cùng chiều. Từ đó căn hướng yaw.

Chỉ **LiDAR** cho biết:
1. Khoảng cách theo mét: xe buýt cách 17,4 m, xe con phía trước cách 10,3 m (s01f47 #2).
2. Kích thước thật theo mét: xe buýt dài khoảng 12,4 m, cao 3,2 m; độ cao mặt đường (−1,75 m) và mặt đường dốc xuống về phía trước ở s01f20.
3. Vật ở phía sau và hai bên được phủ đủ 360°; vùng không được quan sát (vùng trống sau xe buýt ở s01f20) cũng hiện ra rõ, còn trên ảnh camera vùng đó chỉ là thân xe buýt.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f10 | Cạnh trên (+x sang trái, +y xuống dưới) | Ngã tư có vạch người đi bộ, nhiều xe máy, xe đạp và người đứng chờ qua đường ở bên phải. Đây là frame đông nhất (13 cuboid). Xe buýt #4 ở sau-trái tạo vùng gần như không có điểm phía sau nó |
| s02f45 | Cạnh trên | Đường có lan can dải phân cách; mặt đường thấp dần về phía trước (z ≈ −2,26 m ở 20–30 m). Hai người đi bộ ở khoảng 31–32 m phía sau chỉ có 27 điểm mỗi người |
| s03f05 | Cạnh trên | Tắc đường; bên trái là tường rào công trình sát xe (khoảng 5 m), một bức tường điểm dày che hết tầm nhìn bên trái. Bên phải không có vật nào gần |
| s03f43 | Cạnh trên | Xe SUV trắng sát bên phải (3,4 m) có 4204 điểm. Hai xe hai bánh ở sau-phải khoảng 18 m |
| s04f01 | Cạnh trên | Ngã tư cạnh trạm xăng. Nhóm 5 người đi bộ ở bên phải 14–17 m. Minivan sát trước-phải (4,8 m) có 5508 điểm, nhiều nhất bài |
| s04f20 | Cạnh trên | Dòng xe dừng đèn đỏ, 9 xe con trong vòng 20 m. Một xe máy ở phía sau 13 m |
| s05f24 | Cạnh trên | Ngã tư rộng, nhiều xe máy bên phải, hàng xe đạp công cộng dựng trên vỉa hè (không có người nên không vẽ). Có 4 điểm ở z ≈ −18,8 m ở khoảng 40 m phía trước (xem §4) |
| s05f39 | Cạnh trên | Hai xe buýt (#4, #5) ở khoảng 26 m phía trước che phần đường sau chúng; xe con #2 bên trái nằm sau bụi cây dải phân cách |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f20, 1 (3,58 m, bên trái sát xe) | Car | Có: yaw 269,6°, xe cùng chiều nên vạch đỏ chỉ về −y | Có: z đáy −1,702 m; `ground_gap` 0,14 m | Có; đối chiếu `preview` cam01, cam05, cam06 | 4,213 × 1,696 × 1,435 m: dài, cao nằm trong khoảng sedan; rộng 1,696 m thấp hơn cận dưới 1,70 m 0,004 m (trong sai số ±5%) |
| (b) xe buýt hoặc xe tải | s01f20, 3 (17,41 m, trước-trái) | Bus | Có: yaw 270,9°, cùng chiều, nằm ở làn cùng chiều trên cam01/cam03 | Có: z đáy −1,915 m; mặt đường ở đó thấp hơn (≈ −1,98 m), `ground_gap` 0,07 m | Có; `preview` cam01, cam03, cam06 | 12,427 × 2,779 × 3,208 m: dài, cao nằm trong khoảng xe buýt thường; rộng vượt 2,60 m khoảng 0,18 m (~7%). Có thể do tính cả gương chiếu hậu, hoặc hộp hơi rộng |
| (c) người đi bộ hoặc xe hai bánh | s01f20, 5 (12,60 m, bên phải) | Pedestrian | Có: yaw 271,6°, theo hướng đi về −y | Chưa chắc: `ground_gap` −0,02 m (sát mặt đất ước quanh hộp), nhưng z đáy −1,048 m cao hơn mức −1,7 m khoảng 0,65 m. Người đứng sau bụi cây; xem §5 | Có; `preview` cam07, cam08 | 0,428 × 0,488 × 1,482 m: dài nằm trong khoảng; rộng và cao thấp hơn cận dưới 0,01–0,02 m (trong sai số ±10%) |

Vòng hiệu chuẩn: lớp không chạy task chung `D13-HC485087` theo chỉ đạo của coach, nên không có phần so sánh cuboid giữa các học viên.

Vật **bỏ qua vì không đủ cơ sở**:
- `s01f47`, trước-phải, trên vỉa hè (cam07): mấy người đứng cạnh hàng xe đạp công cộng. Cụm điểm của người dính với xe đạp dựng, không tách được người với xe, cũng không rõ có ai đang lái → **UNCERTAIN_CLASS / LOW_VISIBILITY**.
- `s05f24`, phía trước, bên kia ngã tư (cam01/cam03): các xe đỗ ở xa trên 35 m, rất ít điểm, không đủ 15–20% thể tích → **LOW_VISIBILITY / UNCERTAIN_SCOPE**.
- `s01f20`, `s02f10`: dòng xe ở rất xa phía trước trên cam03 (trên 40 m), gần ngưỡng 50 m → **UNCERTAIN_SCOPE**.
- Xe đạp công cộng dựng hàng trên vỉa hè (`s05f24`, `s01f47`) không có người: theo guideline thì không vẽ.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Cả bốn hiện tượng đều lấy từ frame của tôi (task `D13-2A202603154`). Số điểm dưới đây tôi đếm trực tiếp trên point cloud của frame.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s01f20`, xe buýt #3 (17,4 m, trước-trái) / `t4-1-bong-che` / frame của tôi | Giả thuyết: thân xe buýt dài 12,4 m, cao 3,2 m chặn tia laser, nên vùng sau nó không được quét | Sau xe buýt có một vệt trống hình nêm. Trong hình quạt ±4° sau xe buýt, từ khoảng 21 m đến 42 m chỉ có **9 điểm**, trong khi hai hình quạt cùng độ rộng lệch ±25° có 524 và 337 điểm. Cùng frame, bụi cây bên phải (x ≈ −7 đến −8 m) che phần thấp phía sau nó, nên quanh người đi bộ #5, #6 không thấy điểm mặt đất | Vùng trống là **chưa quan sát được**, không phải đường trống: không vẽ vật nằm hẳn trong bóng. Vật bị che một phần mà camera vẫn thấy thì bật **occluded** và cuboid bao trọn phần suy luận được (người sau bụi cây: hộp phải kéo xuống tới mặt đất nơi họ đứng, không dừng ở điểm thấp nhất nhìn thấy trên thân người). Với `s01f20` #5, #6, mặt đất quanh hộp ở z ≈ −1,0 đến −1,1 m (giả thuyết: vỉa hè cao) nên tôi đặt đáy ở mức đó; xem §5 |
| 2. Thưa ở xa | `s02f45`, người đi bộ #5, #6 (31,1 m và 32,5 m, phía sau-phải) / `t4-2-thua-o-xa` / frame của tôi | Giả thuyết: LiDAR 40 tia nên càng xa thì các tia càng cách nhau xa; vật nhỏ ở trên 30 m chỉ bị vài tia quét qua | Mỗi người chỉ có **27 điểm**, so với 291 điểm của người đi bộ ở 10,2 m (`s02f10` #9). Xe con #2 ở 34,1 m cùng frame cũng chỉ có 85 điểm | Theo quy tắc 15–20%: cụm điểm vẫn ra hình người đứng và cam09 thấy rõ 2 người, đủ cơ sở suy luận nên tôi vẽ, kích thước dựa thêm bảng 4.3. Nếu camera không xác nhận được thì bỏ qua và ghi **LOW_VISIBILITY** |
| 3. Phản xạ thấp / ít điểm bất thường | `s02f10`, xe con #2 màu đen (11,3 m, ngay phía trước), so với xe #3 màu trắng (11,3 m, bên trái) / `t4-3-phan-xa-thap` / frame của tôi | Giả thuyết: sơn đen hấp thụ nhiều tia laser nên ít tia quay về và cường độ thấp. Cũng có thể một phần do góc nhìn: thấy đuôi xe #2 nhưng thấy hông xe #3. Tôi không khẳng định nguyên nhân nào | Cùng khoảng cách 11,3 m: xe đen có **836 điểm**, xe trắng **1339 điểm**. Tỉ lệ điểm có intensity > 2 ở xe đen là 0,4%, ở xe trắng là 12,8% | Ghi là giả thuyết, không khẳng định. Khi gán: không ôm hộp quá sát cụm điểm thưa (dễ làm hộp ngắn, thấp), mà dùng ảnh camera và bảng 4.3 để giữ kích thước hợp lý |
| 4. Điểm lạ | `s05f24`, khoảng 40 m phía trước, hơi lệch trái (x ≈ +5,6 m; y ≈ −40,0 m) / `t4-4-diem-la` / frame của tôi | Giả thuyết: phản xạ nhiều lần (tia đập vào mặt kính hoặc thân xe bóng rồi mới về cảm biến, làm quãng đường đo dài ra) hoặc nhiễu cảm biến. Chưa rõ nguyên nhân | **4 điểm ở z ≈ −18,8 m**, trong khi mặt đường quanh đó ở z ≈ −1,8 m, tức **thấp hơn mặt đường khoảng 17 m**. Không có điểm nào nối các điểm này lên mặt đường. (Ở `s01f47` cũng có 5 điểm ở z ≈ −3,1 m, thấp hơn mặt đường 1,35 m) | **Không gán thành vật**, không vẽ cuboid. Không dùng các điểm này để ước mặt đường khi căn đáy hộp |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603154-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f20, 1, Car | −0,985 m | 1,435 m | −1,702 m | 3,58 m | 3,71 m | 269,6° | **Chạm đường**: gần như đúng −1,7 m; `ground_gap` 0,14 m |
| s01f20, 5, Pedestrian | −0,307 m | 1,482 m | −1,048 m | 12,60 m | 12,61 m | 271,6° | So với −1,7 m thì **cao hơn 0,65 m, trông như lơ lửng**. Nhưng mặt đất quanh hộp chỉ ở z ≈ −1,0 đến −1,1 m và `ground_gap` là −0,02 m, nên tôi coi là **chạm mặt đất nơi người đứng**. Giả thuyết: người đứng trên vỉa hè cao sau bụi cây (x ≈ −7 đến −8 m). **Chưa chắc**: nếu bụi cây che cả mặt đất thật thì đáy thật có thể thấp hơn |
| s02f45, 2, Car | −1,454 m | 1,560 m | −2,234 m | 34,07 m | 34,10 m | 88,5° | So với −1,7 m thì **thấp hơn 0,53 m, trông như chìm**. Nhưng ở frame này đường dốc xuống về phía trước (mặt đường ≈ −2,26 m ở 20–30 m phía trước) và `ground_gap` là 0,17 m, nên thực tế **chạm đường** |

(a) Cuboid **gần nhất**: `s03f43`, 1, Car, `dist_m` = 3,43 m, r = 3,54 m (z = −0,860 m). Cuboid **xa nhất**: `s02f10`, 12, Car, `dist_m` = 41,45 m, r = 41,47 m (z = −1,329 m). Ở gần, z của tâm hộp làm r lớn hơn `dist_m` rõ (0,11 m); ở xa thì gần như bằng nhau.

(b) Kích thước so với bảng 4.3:
- Người đi bộ `s01f20` #5: 0,428 × 0,488 × 1,482 m, so với người lớn 0,4–0,9 × 0,5–0,9 × 1,50–1,95 m. **Rộng** thiếu 0,012 m và **cao** thiếu 0,018 m. Lý do: cụm chỉ có 127 điểm và tôi ôm hộp sát cụm; người đứng nghiêng so với LiDAR nên bề ngang vai chỉ thấy một phần; đáy đặt ở mặt đất quanh hộp (≈ −1,05 m); nếu chân thật ra đứng thấp hơn mà bụi cây che mất thì chiều cao thật còn lớn hơn (giả thuyết). Để so: người đi bộ `s02f10` #9 (10,2 m, 291 điểm) có 0,468 × 0,773 × 1,817 m, cả ba chiều đều nằm trong khoảng.
- Xe con `s01f20` #7: 4,852 × 1,760 × 2,029 m. So với sedan (3,8–5,0 × 1,70–1,90 × 1,40–1,60 m) thì **cao vượt 0,43 m**; so với SUV/van (4,4–5,3 × 1,80–2,10 × 1,65–2,10 m) thì **rộng thiếu 0,04 m**. Đây là xe ngược chiều ở bên kia dải phân cách (18,7 m); trên cam06 trông giống một chiếc van, nên chiều cao hợp với nhóm SUV/van. Rộng thiếu vì LiDAR chỉ thấy mặt hông hướng về phía nó, mặt kia không có điểm nên hộp hơi hẹp. Hộp này có `ground_gap` 0,30 m, nên có thể tôi đã kéo hộp cao lên thay vì hạ đáy xuống (chưa chắc).

(c) Yaw: xe con cùng chiều phía trước `s01f47` #2 (x = 0,06 m; y = −10,30 m) có `yaw_deg` = 271,3°, **gần 270°** (lệch 1,3°). Xe ngược chiều `s02f45` #1 (x = 5,26 m; y = −4,42 m, trước-trái) có `yaw_deg` = 88,5°, **gần 90°** (lệch 1,5°). Xe ngược chiều `s01f20` #7 có 90,6°.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s01f20, 2, Car | 8,14 m | 1707 |
| s04f20, 8, Car | 17,90 m | 626 |
| s02f10, 5, Car | 21,57 m | 274 |
| s01f20, 8, Car | 33,72 m | 120 |

Quy luật: **xa gấp khoảng 4 lần (8,1 m → 33,7 m) thì số điểm còn khoảng 1/14 (1707 → 120)**, gần với tỉ lệ 1/d² (1/16). Mỗi lần khoảng cách gấp đôi thì số điểm còn khoảng 1/3 đến 1/5 (8,1 → 17,9 m: 1707 → 626; 17,9 → 33,7 m: 626 → 120). Con số này khớp với đường cong trên slide (1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m). Trung vị `points_in` của 52 xe con của tôi là 2039 điểm (0–10 m, 15 xe), 831 (10–20 m, 23 xe), 284 (20–30 m, 8 xe), 114 (30–50 m, 6 xe). Ở 20–50 m số của tôi cao hơn slide một chút. Có thể do các xe xa của tôi đa số dài khoảng 4,8–5,0 m, hoặc do hộp ăn thêm vài điểm mặt đường (giả thuyết).

Tự kiểm: hai cuboid tôi **đã sửa** sau `preview` / `selfcheck`:
- `s01f20` #5 (Pedestrian): lúc đầu đáy hộp thấp hơn mặt đất quanh hộp (chìm). Tôi kéo đáy lên ở Side/Front và đối chiếu cam07/cam08. Sau khi sửa, z đáy là −1,048 m, `ground_gap` −0,02 m, không còn cờ.
- `s01f20` #6 (Pedestrian): cùng lỗi đáy chìm, sửa cùng cách. Sau khi sửa, z đáy là −1,065 m, `ground_gap` 0,14 m, không còn cờ.

Cảnh báo **giữ nguyên**: phiếu tự kiểm cuối (sau lần `sync` cuối) **không còn cờ nào (0/82)**. Có ba hộp có `ground_gap` cao nhưng chưa vượt ngưỡng cờ (0,45 m), tôi xem lại và **chưa sửa**:
- `s05f39` #2 (0,45 m, đúng bằng ngưỡng).
- `s02f45` #1 (0,37 m).
- `s01f20` #7 (0,30 m).

Lý do: cả ba là xe ngược chiều nằm sau bụi cây hoặc lan can của dải phân cách. Phần dưới thân xe không có điểm (ví dụ `s05f39` #2: điểm thấp nhất quanh xe ở z ≈ −1,56 m), còn mặt đường làn bên kia thấp hơn làn của tôi (≈ −2,1 đến −2,2 m). Tôi không có điểm nào trên chính chiếc xe để căn đáy, nên ghi vào §6(c) là điểm còn chưa chắc.

Đổi kiểm với bạn bên cạnh: đổi kiểm chéo trực tiếp trên màn hình với MSSV `2A202603286` (không gửi file bảng A cho nhau).
- Bạn `2A202603286` kiểm bài của tôi: đối chiếu `dist_m` = 3,58 m và `z_bottom` = −1,702 m của cuboid `s01f20` #1 (Car) trong báo cáo mục 5 với `submission/D13-2A202603154-bangA.csv` mở trên máy tôi (dòng `s01f20,1,Car`: `dist_m` 3.58, `z_bottom` −1.702): **khớp hoàn toàn**, không cần chỉnh sửa.
- Tôi kiểm bài của bạn ấy: đối chiếu `dist_m` = 10,20 m và `z_bottom` = −1,825 m của cuboid `s01f31` #2 (Car) trong báo cáo mục 5 của bạn với `submission/D13-2A202603286-bangA.csv` mở trên máy bạn ấy: **khớp hoàn toàn**.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Model phát hiện vật 3D học trên nhãn này phải trả về vị trí, kích thước và hướng của vật trong thế giới thật để xe lập đường đi và phanh. Nhãn đo bằng mét trong hệ tọa độ LiDAR cho model học thẳng những đại lượng như "xe cách 12 m, dài 4,5 m, đi cùng chiều", không phụ thuộc phối cảnh hay độ phân giải như pixel. Vì vậy hộp lệch 0,5 m ở đáy hay quay ngược 180° là sai số vật lý thật mà model sẽ học theo. Với vật thưa điểm, model chỉ thấy vài chục điểm. Nếu người gán đoán bừa thì model học nhiễu; nếu bỏ hết thì model học rằng vùng thưa là "không có gì" và sẽ bỏ sót vật ở xa. Quy tắc 15–20% cùng ghi chú LOW_VISIBILITY giúp nhãn nhất quán, để model học đúng giới hạn của cái mà cảm biến thật sự thấy.

(b) Dùng AI: tôi dùng **Claude** (Claude Code trong VS Code) để đọc repo và hướng dẫn chạy script.
- Câu hỏi chính: các bước trong `GUIDE.md`, `SUBMISSION.md`, `docs/COMMANDS.md`; chạy lệnh `python scripts/lab.py` (`dry`, `sync`, `preview`, `selfcheck`, `pack`) và `check_submission.py` theo thứ tự nào; cần chụp những ảnh nào, đặt tên ra sao.
- Cách dùng kết quả: tôi làm theo hướng dẫn để tự chạy lệnh và tự kiểm kết quả trên máy. Cuboid tôi tự vẽ trên CVAT; các số trong báo cáo tôi tự đọc từ bảng A và phiếu tự kiểm; các nhận xét tôi tự viết.
- Rà soát báo cáo: Claude đối chiếu số trong báo cáo với bảng A, phiếu tự kiểm, chỉ ra các chỗ mâu thuẫn, và sửa câu chữ theo số liệu của tôi (khoảng yaw ở §1, cách ghi khoảng cách xe buýt ở §2, cách gọi hai xe buýt ở `s05f39`, cách giải thích đáy người đi bộ `s01f20` #5 ở §4, §5). Tôi đọc lại và giữ các sửa đổi này.
- Không đưa cho AI: ảnh camera, ảnh trong `work/xem/`, ảnh chụp màn hình, file trong `data/data-VF/`.

(c) Điều còn chưa chắc:
- **Kích thước nhiều xe giống hệt nhau**: khoảng 14 xe con ở `s02f10`, `s02f45`, `s03f05`, `s03f43` cùng là 4,824 × 2,024 × 1,560 m, và 6 xe cùng là 4,213 × 1,696 × 1,435 m. Như vậy kích thước chưa được chỉnh riêng cho từng xe; điểm kích thước và độ khớp có thể bị ảnh hưởng.
- **Class của xe van**: `s01f47` #1 là van xanh đậm, tôi gán Truck nhưng kích thước 4,84 × 1,91 × 1,92 m lại nằm trong khoảng van chở người (Car) → **UNCERTAIN_CLASS**.
- **Chưa bật occluded cho cuboid nào**, dù có mấy vật rõ ràng bị che một phần (người sau bụi cây ở `s01f20`, xe sau bụi cây dải phân cách ở `s05f39` #2).

