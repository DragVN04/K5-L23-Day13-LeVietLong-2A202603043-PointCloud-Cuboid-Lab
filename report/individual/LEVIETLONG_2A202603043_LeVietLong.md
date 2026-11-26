# Báo cáo Day 13 — Lê Việt Long, 2A202603043

## 1. Kết luận trước

- Mã dùng trong lab: `LEVIETLONG_2A202603043`.
- Task cá nhân: `D13-LEVIETLONG_2A202603043`.
- Môi trường thực hiện: Windows, CVAT Server 2.74.1.
- Các frame thực hiện: `s01f19`, `s01f52`, `s02f03`, `s02f28`, `s03f09`, `s03f48`, `s04f34`, `s04f56`, `s05f08` và `s05f41`.

Kết quả lần sync cuối ghi nhận tổng cộng 85 cuboid trên 10 frame, trung bình 8,5 cuboid/frame. Frame đầu `s01f19` có 8 cuboid và frame có ít cuboid nhất là `s03f48` với 6 cuboid.

Phiếu tự kiểm ghi nhận 8/85 cuboid có cảnh báo, chiếm khoảng 9,41%. Trong đó:

- 1 cuboid có cảnh báo đáy cao.
- 7 cuboid có cảnh báo về kích thước.
- 77/85 cuboid không bị đánh dấu cảnh báo, chiếm khoảng 90,59%.

Frame `s03f48` có ít cuboid nhất. Nguyên nhân có thể đến từ số lượng phương tiện trong cảnh ít hơn hoặc một số cụm điểm không đủ rõ để xác định đối tượng và thực hiện gán nhãn.

Phần tôi tự tin nhất là việc xác định vị trí, kích thước ngang và hướng của các phương tiện thông qua góc nhìn Top. Phần tôi muốn kiểm tra kỹ hơn là chiều cao, vị trí đáy cuboid và kích thước của các vật thể ở xa hoặc có mật độ điểm thấp.

Các thao tác chính trong CVAT:

- Kéo chuột trái để xoay khung nhìn 3D, kéo chuột phải để di chuyển khung nhìn và sử dụng con lăn để thu phóng.
- Chọn Shape và nháy đúp để đặt cuboid.
- Sử dụng Top để điều chỉnh tâm, chiều dài, chiều rộng và yaw.
- Sử dụng Side và Front để kiểm tra chiều cao và vị trí đáy hộp.
- Bật Cuboid orientation để kiểm tra hướng của vật thể.
- Sử dụng preview để đối chiếu cuboid với ảnh camera.

## 2. Đọc frame (Bước 2)

Ảnh minh họa:

- `s01f19`: `t1-s01f19-3d.png`, `t1-s01f19-top.png`.
- `s01f52`: `t1-s01f52-3d.png`, `t1-s01f52-top.png`.

Trong hệ tọa độ VF:

- Trục −y: hướng phía trước xe thu dữ liệu.
- Trục +y: hướng phía sau xe thu dữ liệu.
- Trục +x: hướng bên trái xe.
- Trục −x: hướng bên phải xe.
- Trục +z: hướng lên trên.

Camera `cam01` được sử dụng để đối chiếu hướng phía trước của xe thu dữ liệu. Khi xoay khung nhìn 3D, vị trí các hướng trên màn hình có thể thay đổi nhưng hệ tọa độ thực tế không thay đổi.

Khi đọc point cloud, cần xác định mặt đường dựa trên các cụm điểm nằm thấp, có tính liên tục và trải rộng theo phương ngang. Các cụm điểm nằm cao hơn mặt đường có thể là phương tiện, người đi bộ hoặc những vật thể khác trong môi trường.

Đối với vật thể ở xa, mật độ điểm thường giảm, làm cho việc nhận diện hình dạng và xác định kích thước trở nên khó khăn hơn.

### So sánh thông tin từ camera và LiDAR

Ba thông tin camera cung cấp mà point cloud hình học không thể hiện rõ:

1. Màu sắc của phương tiện và vật thể.
2. Các chi tiết ngoại hình như đèn xe, kính xe và biển số.
3. Đặc điểm trực quan giúp phân biệt những vật thể có hình dạng tương tự nhau.

Ba thông tin LiDAR cung cấp mà ảnh camera đơn lẻ không trực tiếp đo được:

1. Khoảng cách thực tế từ cảm biến đến vật thể.
2. Vị trí và kích thước vật thể trong không gian ba chiều theo đơn vị mét.
3. Sự phân bố độ sâu, độ cao và hình dạng không gian của các vật thể xung quanh.

Đối với tám frame còn lại, nguyên tắc xác định hướng vẫn dựa trên hệ tọa độ VF và đối chiếu với `cam01`. Các vấn đề cần chú ý gồm mật độ điểm, vật thể bị che khuất, cụm điểm rời rạc và sự thay đổi của mặt đường.

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh minh họa:

- `t2-a-car.png`.
- `t2-b-bus-truck.png`.
- `t2-c-ped-cyclist.png`.

| Vật thể                      | Class              | Nội dung kiểm tra                                         |
| ---------------------------- | ------------------ | --------------------------------------------------------- |
| Xe con                       | Car                | Tâm hộp, chiều dài, chiều rộng, chiều cao và hướng đầu xe |
| Xe buýt hoặc xe tải          | Bus/Truck          | Kích thước thân xe, hướng di chuyển và vị trí đáy hộp     |
| Người đi bộ hoặc xe hai bánh | Pedestrian/Cyclist | Chiều cao, độ rộng và mức độ bao phủ cụm điểm             |

Đối với xe con, cần ưu tiên xác định chiều dài và chiều rộng theo góc nhìn Top, sau đó kiểm tra hướng đầu xe bằng ảnh camera.

Đối với xe tải hoặc xe buýt, cần chú ý chiều dài thân xe và chiều cao tổng thể. Không nên chỉ dựa vào phần điểm nhìn thấy nếu vật thể bị che khuất một phần.

Đối với người đi bộ hoặc người đi xe hai bánh, kích thước vật thể nhỏ hơn nên việc điều chỉnh cuboid cần chính xác hơn, đặc biệt là chiều rộng và vị trí tiếp xúc với mặt đường.

### Nhận xét về hiệu chuẩn

Các sai lệch dễ gặp khi thực hiện gán nhãn cuboid gồm:

- Cuboid quá rộng hoặc quá dài so với vật thể.
- Tâm hộp bị lệch khỏi vị trí thực tế.
- Đáy hộp nằm cao hơn mặt đường.
- Hướng yaw không trùng với hướng đầu xe.
- Hộp chỉ bao phủ phần điểm nhìn thấy mà không phản ánh đầy đủ kích thước vật thể theo guideline.

Qua nội dung hiệu chuẩn, tôi nhận thấy không nên chỉ sử dụng một góc nhìn để đánh giá cuboid. Cần kết hợp Top, Side, Front và ảnh camera để hạn chế sai lệch.

Đối với các cụm điểm quá thưa hoặc không đủ thông tin để xác định class, cần tránh suy đoán và gán nhãn khi chưa có đủ cơ sở. Những trường hợp này có thể thuộc nhóm `UNCERTAIN_CLASS` hoặc `LOW_VISIBILITY`.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Các nguyên nhân dưới đây được trình bày dưới dạng giả thuyết dựa trên đặc điểm của dữ liệu LiDAR.

| Hiện tượng   | Tên ảnh                 | Nguyên nhân giả thuyết                                                 | Biểu hiện trên point cloud                                  | Hệ quả khi gán nhãn                                                              |
| ------------ | ----------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Bóng che     | `t4-1-bong-che.png`     | Vật thể phía trước chắn tia LiDAR                                      | Xuất hiện vùng thiếu điểm phía sau vật cản                  | Khó xác định đầy đủ hình dạng và kích thước vật thể bị che                       |
| Thưa ở xa    | `t4-2-thua-o-xa.png`    | Khoảng cách giữa các tia quét tăng theo khoảng cách                    | Các điểm trên vật thể phân bố thưa và rời rạc               | Khó xác định tâm, hướng và kích thước cuboid                                     |
| Phản xạ thấp | `t4-3-phan-xa-thap.png` | Bề mặt vật thể phản xạ tín hiệu LiDAR yếu hoặc góc tới không thuận lợi | Một số vùng trên vật thể có ít điểm bất thường              | Có thể làm cuboid bị nhỏ hơn kích thước thực nếu chỉ dựa trên điểm quan sát được |
| Điểm lạ      | `t4-4-diem-la.png`      | Nhiễu cảm biến, phản xạ bất thường hoặc ảnh hưởng từ môi trường        | Xuất hiện các điểm hoặc cụm điểm không liên tục với vật thể | Có nguy cơ gán nhãn nhầm hoặc làm cuboid bị mở rộng quá mức                      |

Qua bốn hiện tượng trên, tôi nhận thấy chất lượng dữ liệu LiDAR không đồng đều giữa các vật thể. Việc gán nhãn cần kết hợp thông tin hình học, ảnh camera và quy định trong guideline thay vì chỉ dựa trên số lượng điểm.

## 5. Số từ cuboid của tôi (Bước 5)

Nguồn dữ liệu:

- [Bảng A](../../submission/D13-LEVIETLONG_2A202603043-bangA.csv).
- [Phiếu tự kiểm](../../submission/D13-LEVIETLONG_2A202603043-tukiem.csv).

### 5.1. Công thức xác định vị trí và khoảng cách

Độ cao đáy cuboid:

`z_bottom = z - h/2`

Khoảng cách trên mặt phẳng ngang:

`dist_m = sqrt(x² + y²)`

Khoảng cách 3D từ cảm biến đến tâm cuboid:

`r = sqrt(dist_m² + z²)`

Việc đánh giá đáy cuboid cần dựa trên độ cao mặt đường tại vị trí của vật thể, không mặc định mặt đường có tọa độ z = 0.

Nếu đáy cuboid cao hơn mặt đường quá nhiều thì hộp có thể bị lơ lửng. Ngược lại, nếu đáy cuboid thấp hơn mặt đường thì hộp có thể bị chìm xuống dưới mặt đất.

### 5.2. Kích thước và hướng của cuboid

Đối với Car, chiều dài thường lớn hơn chiều rộng và chiều cao. Đối với Pedestrian, chiều cao thường lớn hơn hai kích thước còn lại. Cyclist cần chú ý kích thước tổng thể của người và phương tiện theo quy định gán nhãn.

Các cảnh báo kích thước cần được kiểm tra lại bằng point cloud và ảnh camera. Không phải mọi kích thước nằm ngoài khoảng tham chiếu đều là lỗi, vì kích thước thực tế của vật thể có thể khác nhau.

Trong hệ tọa độ VF, nếu yaw được đo từ trục +x theo chiều dương:

- Xe hướng về phía trước, theo −y: yaw gần 270°.
- Xe hướng về phía sau, theo +y: yaw gần 90°.
- Xe hướng sang trái, theo +x: yaw gần 0°.
- Xe hướng sang phải, theo −x: yaw gần 180°.

Đối với phương tiện đang rẽ hoặc nằm chéo so với hướng xe thu dữ liệu, yaw có thể khác các giá trị trên.

### 5.3. Mật độ điểm theo khoảng cách

Khi khoảng cách từ LiDAR đến vật thể tăng, mật độ điểm trên bề mặt vật thể thường giảm. Nguyên nhân là khoảng cách giữa các tia quét tăng lên khi đi xa cảm biến.

Đối với các vật thể gần, số lượng điểm thường nhiều hơn, giúp xác định hình dạng, kích thước và hướng dễ dàng hơn.

Đối với các vật thể ở xa, số lượng điểm thường ít hơn nên việc xác định các cạnh của cuboid khó khăn hơn. Tuy nhiên, số điểm còn phụ thuộc vào kích thước vật thể, mức độ che khuất, góc quan sát và khả năng phản xạ của bề mặt.

Xu hướng lý thuyết này phù hợp với đặc điểm suy giảm mật độ điểm theo khoảng cách trong bài giảng. Việc đối chiếu định lượng cần sử dụng các giá trị `points_in` và `dist_m` từ Bảng A.

### 5.4. Kết quả tự kiểm và chỉnh sửa

| Cuboid              | Cảnh báo ban đầu                      | Kết quả lần kiểm cuối      |
| ------------------- | ------------------------------------- | -------------------------- |
| `s02f28 #5 Car`     | Đáy cao hơn mặt đường ước tính 0,90 m | Không còn cảnh báo đáy cao |
| `s03f48 #6 Cyclist` | Đáy cao hơn mặt đường ước tính 0,45 m | Không còn cảnh báo đáy cao |
| `s01f19 #7 Car`     | Đáy cao hơn mặt đường ước tính 0,54 m | Còn cảnh báo ở mức 0,48 m  |

Kết quả cho thấy hai cuboid đã hết cảnh báo đáy cao. Cuboid `s01f19 #7 Car` giảm mức chênh lệch từ 0,54 m xuống 0,48 m, tương ứng giảm 0,06 m.

Khi xử lý lỗi đáy cao trong CVAT, cần sử dụng góc nhìn Side và Front để xác định mặt đường, sau đó điều chỉnh tọa độ z hoặc chiều cao của cuboid sao cho phù hợp với vật thể.

Đối với các cảnh báo kích thước còn lại, không nên tự động thay đổi kích thước chỉ để vượt qua công cụ kiểm tra. Cần đối chiếu lại hình dạng thực tế của vật thể và guideline trước khi quyết định sửa hoặc giữ nguyên.

### 5.5. Tổng hợp chất lượng nhãn

| Chỉ số                         | Kết quả |
| ------------------------------ | ------: |
| Tổng số frame                  |      10 |
| Tổng số cuboid                 |      85 |
| Số cuboid trung bình/frame     |     8,5 |
| Cuboid có cảnh báo             |       8 |
| Cuboid không có cảnh báo       |      77 |
| Cảnh báo đáy cao               |       1 |
| Cảnh báo kích thước            |       7 |
| Tỷ lệ cuboid có cảnh báo       |   9,41% |
| Tỷ lệ cuboid không có cảnh báo |  90,59% |

Công cụ tự kiểm giúp phát hiện và ưu tiên những cuboid có khả năng xảy ra sai sót. Tuy nhiên, cảnh báo không đồng nghĩa với lỗi đã được xác nhận. Việc đánh giá cuối cùng vẫn cần người gán nhãn kiểm tra trực tiếp.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

### Ý nghĩa của nhãn

Nhãn cuboid 3D giúp mô hình xác định vị trí, kích thước và hướng của vật thể trong không gian ba chiều.

Khác với bounding box 2D sử dụng tọa độ pixel trên ảnh, cuboid 3D sử dụng hệ tọa độ không gian và kích thước theo đơn vị mét. Điều này có ý nghĩa quan trọng trong các hệ thống ADAS và xe tự hành, đặc biệt đối với việc nhận diện phương tiện, xác định khoảng cách và đánh giá không gian xung quanh xe.

Qua bài lab, tôi hiểu thêm rằng việc gán nhãn LiDAR không chỉ là tạo một hộp bao quanh vật thể mà còn phải đảm bảo tính chính xác về vị trí, kích thước, hướng và sự phù hợp với mặt đường.

### Cách dùng AI

- Công cụ sử dụng: Codex.
- Nội dung hỏi: cách sử dụng CVAT 3D, cách điều chỉnh cuboid, các lệnh tự kiểm, cách chụp ảnh và trình bày báo cáo.
- AI được sử dụng để hỗ trợ giải thích kiến thức, hướng dẫn thao tác và tổng hợp kết quả từ log sync.

### Điều còn chưa chắc

Trường hợp tôi còn phân vân là các phương tiện ở xa hoặc bị che khuất, khi point cloud chỉ thể hiện một phần hình dạng của vật thể.

Trong những trường hợp này, việc xác định chiều dài, chiều rộng và tâm thực tế của cuboid tương đối khó. Nếu chỉ bao quanh các điểm nhìn thấy thì cuboid có thể nhỏ hơn vật thể thực tế. Ngược lại, nếu mở rộng hộp quá nhiều dựa trên suy đoán thì có thể tạo ra sai lệch kích thước.

Ngoài ra, tôi vẫn cần kiểm tra kỹ trường hợp `s01f19 #7 Car`, do cuboid này còn cảnh báo đáy cao 0,48 m sau lần tự kiểm cuối.

Trong các bài thực hành tiếp theo, tôi muốn cải thiện khả năng đọc point cloud, xác định mặt đường và sử dụng kết hợp các góc nhìn Top, Side, Front để nâng cao chất lượng gán nhãn 3D.
