# Báo cáo Day 13 — Lê Việt Long, 2A202603043

## 1. Kết luận trước

- Mã dùng trong lab là `LEVIETLONG_2A202603043`. Task cá nhân là `D13-LEVIETLONG_2A202603043`.
- Các frame là `s01f19`, `s01f52`, `s02f03`, `s02f28`, `s03f09`, `s03f48`, `s04f34`, `s04f56`, `s05f08` và `s05f41`.
- Môi trường dùng Windows và CVAT Server 2.74.1. Trình duyệt và MSSV bạn ngồi cạnh — TODO(học viên).

TODO(học viên) — Ghi tổng số cuboid, frame có ít nhất và lý do, số cuboid bị cờ sau lần tự kiểm cuối. Nêu phần bạn tự tin nhất và phần cần kiểm kỹ nhất.

- Kéo chuột trái để xoay khung 3D đến góc nhìn từ trên xuống. Kéo chuột phải để dời khung và dùng con lăn để thu phóng.
- Chọn **Shape** và nháy đúp để đặt cuboid. Dùng **Top** để chỉnh tâm, kích thước ngang và yaw. Dùng **Side** và **Front** để kiểm chiều cao và đáy.
- Bật **Cuboid orientation** để thấy hướng hộp. Dùng `preview` để xem cuboid chiếu lên ảnh camera.

## 2. Đọc frame (Bước 2)

- `s01f19` — `t1-s01f19-3d.png`, `t1-s01f19-top.png`
- `s01f52` — `t1-s01f52-3d.png`, `t1-s01f52-top.png`

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f19` | TODO(học viên) | | | | | |
| `s01f52` | TODO(học viên) | | | | | |

TODO(học viên): 3 điều **chỉ camera** cho biết và 3 điều **chỉ LiDAR** cho biết, lấy từ chính hai frame trên.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f03` | TODO(học viên) | |
| `s02f28` | TODO(học viên) | |
| `s03f09` | TODO(học viên) | |
| `s03f48` | TODO(học viên) | |
| `s04f34` | TODO(học viên) | |
| `s04f56` | TODO(học viên) | |
| `s05f08` | TODO(học viên) | |
| `s05f41` | TODO(học viên) | |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh mẫu gồm `t2-a-car.png`, `t2-b-bus-truck.png` và `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | TODO(học viên) | | | | | |
| (b) xe buýt hoặc xe tải | TODO(học viên) | | | | | |
| (c) người đi bộ hoặc xe hai bánh | TODO(học viên) | | | | | |

TODO(học viên): vòng hiệu chuẩn: cuboid của bạn lệch so với các bạn ở điểm nào, cả lớp chốt cách hiểu guideline nào? Và các vật bạn **bỏ qua vì không đủ cơ sở** (frame, vị trí, mô tả, loại nghi vấn như UNCERTAIN_CLASS / LOW_VISIBILITY), hoặc ghi "không có".

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `t4-1-bong-che.png`, TODO(học viên) | | | |
| 2. Thưa ở xa | `t4-2-thua-o-xa.png`, TODO(học viên) | | | |
| 3. Phản xạ thấp / ít điểm bất thường | `t4-3-phan-xa-thap.png`, TODO(học viên) | | | |
| 4. Điểm lạ | `t4-4-diem-la.png`, TODO(học viên) | | | |

## 5. Số từ cuboid của tôi (Bước 5)

Dùng [Bảng A](../../submission/D13-LEVIETLONG_2A202603043-bangA.csv) và [phiếu tự kiểm](../../submission/D13-LEVIETLONG_2A202603043-tukiem.csv) sau lần `sync` cuối. Tính khoảng cách 3D đến tâm hộp theo công thức `r = √(dist_m² + z²)`. So sánh đáy hộp với mặt đường ở vị trí của vật.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| TODO(học viên) | | | | | | | |
| TODO(học viên) | | | | | | | |
| TODO(học viên) | | | | | | | |

TODO(học viên): (a) cuboid **gần nhất** và **xa nhất**: `dist_m` và r của từng cái. (b) Kích thước l, w, h của một người đi bộ (hoặc xe hai bánh) và một xe con so với bảng 4.3: chiều nào ngoài khoảng, vì sao. (c) `yaw_deg` của một xe cùng chiều phía trước và một xe ngược chiều (nếu có): có gần 270° và 90° không.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| TODO(học viên) | | |
| TODO(học viên) | | |
| TODO(học viên) | | |
| TODO(học viên) | | |

TODO(học viên): một câu quy luật ("xa gấp … lần thì số điểm còn khoảng …") và so với đường cong ở slide bài giảng (xe con: khoảng 1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m, 19 ở 60–80 m). Rồi phần tự kiểm: hai cuboid bạn **đã sửa** sau `preview` / `selfcheck` (frame, idx, sửa gì), và những cảnh báo bạn **giữ nguyên** kèm lý do.

TODO(học viên): đổi kiểm: MSSV người đã kiểm chéo với bạn, hai con số nào được đối chiếu với bảng A, khớp hay lệch (lệch thì đã sửa gì).

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

**Ý nghĩa của nhãn.** TODO(học viên) — Viết hai câu giải thích vì sao mô hình cần nhãn 3D đo bằng mét và vì sao vật thưa điểm cần quy tắc riêng.

**Cách dùng AI**

- Công cụ dùng là Codex.
- Nội dung hỏi gồm cách dùng CVAT, các lệnh tự kiểm và cách chụp ảnh nộp bài.
- Tôi dùng hướng dẫn để thực hiện các thao tác và chuẩn bị bố cục báo cáo.

**Điều còn chưa chắc.** TODO(học viên) — Ghi trường hợp cụ thể bạn vẫn còn phân vân sau khi xem lại point cloud và ảnh camera.
