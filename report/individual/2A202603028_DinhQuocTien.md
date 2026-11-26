# Báo cáo Day 13 — Đinh Quốc Tiến, 2A202603028

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.
>
> Xoá mọi dấu TODO và dòng gợi ý (dòng bắt đầu bằng `>`). Mỗi con số lấy từ **bảng A** hoặc **phiếu tự kiểm** trong `submission/`, kèm đơn vị (m, °). Ảnh để trong `report/individual/images/` (nộp LMS trong file zip, không commit); trong báo cáo chỉ ghi **tên file ảnh**. Báo cáo này nằm trên repo public: không chép biển số, chữ trên biển hiệu hay biển chỉ đường, tên đường, địa danh; nhắc tới bạn khác thì ghi MSSV, không ghi họ tên. Cả báo cáo ~2 trang.

## 1. Kết luận trước
MSSV `2A202603028`; task `D13-2A202603028`; 10 frame: `s01f07`, `s01f35`, `s02f40`, `s02f55`, `s03f11`, `s03f42`, `s04f38`, `s04f56`, `s05f16`, `s05f36`. Môi trường: Windows, Chrome, CVAT 2.81.0. MSSV người ngồi cạnh: chưa ghi nhận.

Bảng A hiện có 72 cuboid: Car 60, Truck 9, Bus 1, Cyclist 2. Số lượng theo frame là 10, 5, 6, 5, 9, 5, 8, 6, 11, 7 theo thứ tự trên. Phiếu tự kiểm báo 0/72 cảnh báo. Hai cuboid nằm ngoài phạm vi 50 m là `s01f35#5` ở 50.38 m và `s03f42#5` ở 74.65 m; cần xem lại trong CVAT. Tự đánh giá phần làm chắc nhất và phần yếu nhất cần Tiến xác nhận.

Trong CVAT, chọn Shape để tạo cuboid; dùng Top/Side/Front để chỉnh và kiểm tra hộp. Bật Cuboid orientation để xem hướng, đối chiếu ảnh camera bằng preview, rồi Save. Sau khi lưu, `python scripts/lab.py sync` tạo lại Bảng A và phiếu tự kiểm.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-<frame>-3d`, `t1-<frame>-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f07 | Phía trước ở mép trên; +x sang trái, +y về phía sau/mép dưới | Gần phẳng, mặt đường khoảng 1.7 m dưới LiDAR | Car #8 ở 38.21 m | Car, Truck; có xe phía sau và hai bên, thấy rõ hơn ở camera sau/bên | Cụm lẻ ven đường/cây chưa phân loại chắc chắn |
| s01f35 | Phía trước ở mép trên; +x sang trái, +y về phía sau/mép dưới | Gần phẳng, mặt đường khoảng 1.7 m dưới LiDAR | Car #5 ở 50.38 m; ngoài phạm vi mục tiêu | Car, Truck; xe ở nhiều hướng quanh xe thu dữ liệu | Cụm rất thưa ở xa; tránh suy diễn vật thể từ vài điểm |

Camera cho biết màu, hình dáng thân xe và tín hiệu giao thông; camera cũng giúp nhận ra bóng và vật che nhau. LiDAR cho biết khoảng cách theo mét, chiều sâu/hình dạng 3D và mặt đường; nó thấy được vật quanh 360° kể cả phía sau xe, nhưng không cho màu và có thể rất thưa ở xa.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f40 | Mép trên | Có Cyclist bên cạnh xe và cụm điểm phía sau |
| s02f55 | Mép trên | Bóng cây phủ một phần mặt đường |
| s03f11 | Mép trên | Nhiều xe sát nhau, hộp chiếu chồng lấn |
| s03f42 | Mép trên | Có một Car #5 ở 74.65 m, ngoài phạm vi 50 m |
| s04f38 | Mép trên | Có xe tải và nhiều xe con cùng lúc |
| s04f56 | Mép trên | Cyclist ở xa có 22 điểm trong cuboid |
| s05f16 | Mép trên | Có Bus, Truck và Car |
| s05f36 | Mép trên | Có Truck và xe ở phía bên/hậu xe thu dữ liệu |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f07, 4 | Car | Có, yaw 268.3° gần hướng cùng chiều 270° | Có, ground gap −0.06 m | Có, trong preview và các view Top/Side/Front | 4.500 × 1.800 × 1.500 m; trong khoảng sedan/hatchback |
| (b) xe buýt hoặc xe tải | s04f38, 1 | Truck | Có, yaw 93.2° gần hướng ngược chiều 90° | Có, ground gap +0.07 m | Có, trong preview và các view Top/Side/Front | 7.052 × 2.399 × 2.904 m; trong khoảng xe tải hộp/xe tải nhẹ |
| (c) người đi bộ hoặc xe hai bánh | s02f40, 4 | Cyclist | Chưa kết luận hướng từ CSV riêng lẻ; yaw 254.6° | Có, ground gap −0.02 m | Có, trong preview và các view Top/Side/Front | 1.217 × 0.670 × 1.500 m; rộng nằm trong khoảng xe đạp, dài/cao thấp hơn mốc tham chiếu |

Vòng hiệu chuẩn và MSSV bạn học đối chiếu chưa được ghi trong thông tin hiện có, nên chưa thể nêu chỗ lệch hay quy tắc cả lớp đã chốt. Các điểm lẻ ven đường và cụm quá thưa chưa được gán class nếu chưa đủ cơ sở; Tiến cần xác nhận có trường hợp nào đã chủ động bỏ qua hay không.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s02f55`, `t4-1-bong-che.png` | Bóng cây che nắng và làm giảm tương phản; đây là giả thuyết | Điểm mặt đường/vật thể thưa hơn trong vùng bóng | Chỉ gán vật có cụm đủ rõ; bật occluded nếu bị che một phần, không nới hộp theo bóng |
| 2. Thưa ở xa | `s01f35`, `t4-2-thua-o-xa.png` | Mật độ giảm theo khoảng cách; Car #5 cách 50.38 m có 39 điểm | Cụm rất thưa, sát/ngoài ngưỡng 50 m | Không đoán từ ít điểm; vật ngoài 50 m không thuộc vùng chấm |
| 3. Phản xạ thấp / ít điểm bất thường | `s04f56`, `t4-3-phan-xa-thap.png` | Cyclist ở 38.70 m chỉ có 22 điểm; phản xạ thấp là giả thuyết, khoảng cách và kích thước nhỏ cũng có thể gây ít điểm | Cụm nhỏ, thưa, nằm trong hộp Cyclist | Giữ class chỉ khi ảnh và hình dạng đủ cơ sở; không tạo thêm vật chỉ từ vài điểm |
| 4. Điểm lạ | `s03f42`, `t4-4-diem-la.png` | Car #5 ở 74.65 m có 13 điểm; có thể là vật thật rất xa hoặc cụm nhiễu, chưa thể khẳng định | Cụm rời rạc ở xa ngoài vùng 50 m | Ghi là nghi vấn, không dùng làm cuboid chấm điểm ngoài ngưỡng |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f07, 4, Car | −1.089 m | 1.500 m | −1.839 m | 3.67 m | 3.82 m | 268.3° | Đáy thấp hơn mặt đường tham chiếu khoảng 0.14 m; kiểm tra lại trên point cloud |
| s04f38, 1, Truck | −0.275 m | 2.904 m | −1.727 m | 15.69 m | 15.70 m | 93.2° | Đáy gần mặt đường tham chiếu −1.7 m |
| s03f42, 5, Car | −0.702 m | 1.500 m | −1.452 m | 74.65 m | 74.66 m | 267.2° | Vượt phạm vi 50 m; cần xem lại hoặc bỏ |

Cuboid gần nhất là `s01f07#4` (3.67 m, r = 3.82 m); xa nhất là `s03f42#5` (74.65 m, r = 74.66 m), ngoài vùng mục tiêu. Xe con `s01f07#4` có kích thước 4.500 × 1.800 × 1.500 m, trong khoảng sedan/hatchback. Cyclist `s02f40#4` có 1.217 × 0.670 × 1.500 m: chiều rộng nằm trong khoảng xe đạp, chiều dài và cao thấp hơn khoảng tham chiếu; cần kiểm tra lại phần xe/người bị che hoặc hộp quá chặt. Yaw xe cùng chiều `s01f07#4` là 268.3°; xe ngược chiều `s04f38#1` là 93.2°.
 
Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s05f16, 3, Car | 5.02 m | 4797 |
| s04f38, 3, Car | 10.82 m | 1320 |
| s05f16, 10, Car | 21.59 m | 565 |
| s05f16, 5, Car | 33.08 m | 128 |

Trong các ví dụ xe con, khoảng cách tăng từ 5.02 m lên 33.08 m (6.6 lần), số điểm giảm từ 4797 xuống 128 (còn khoảng 2.7%). Xu hướng giảm cùng chiều với đường cong bài giảng, dù các cuboid là vật thể khác nhau nên đây không phải phép đo có kiểm soát. So với slide, các ví dụ ở 0–10, 10–20, 20–30 và 30–50 m đều có số điểm cao hơn giá trị tham chiếu. Sau lần đồng bộ trước, `s03f42` tăng từ 4 lên 5 cuboid; cuboid mới `#5` ở 74.65 m cần xem lại vì ngoài ngưỡng. Phiếu tự kiểm hiện có 0 cảnh báo được giữ lại.

Đối kiểm với bạn học chưa có thông tin trong hồ sơ hiện tại; cần bổ sung MSSV người kiểm chéo và hai số đã so với Bảng A để hoàn tất mục này.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
Đo hộp 3D bằng mét giúp mô hình học vị trí và kích thước trong không gian, còn pixel thay đổi theo camera và khoảng cách. Vật ở xa có ít điểm hơn nên cần quy tắc nhất quán để phân biệt vật thật với nhiễu và tránh hộp quá rộng.

- Công cụ: ChatGPT/Codex.
- Câu hỏi chính: hỗ trợ đọc yêu cầu bài lab và hiểu quy trình chạy các lệnh trong project.
- Cách dùng: tôi dùng phần giải thích để nắm yêu cầu và tự thực hiện việc quan sát, gán nhãn, kiểm tra cuboid trong CVAT. Tôi tự chịu trách nhiệm với các nhãn đã lưu.

Điều còn cần xác nhận: các hộp ngoài phạm vi 50 m và vật thể mơ hồ trong ảnh chiếu cần được đối chiếu lại trên point cloud. Không có ground truth để khẳng định độ chính xác chỉ từ phiếu tự kiểm.
