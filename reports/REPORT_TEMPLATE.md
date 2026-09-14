# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Nhữ Đình Chiến<br>
**MSSV:** 2A202602130<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp:f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh:drive_008, drive_022, drive_033, drive_038
- Số vật thể thực tế:75
- Mã SHA-256 của gói YOLO của bạn:5bc356b9001daa6df0ccfa78123680ca26a264b5fb5c3f2bf822154aba3c26e6
- Mã SHA-256 của gói CVAT gốc của bạn:46d777dfe8e8ab59fd1e225f3bc0602d5a8d482a4c947ac8a91982eda5f08d2b
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp:teacher
- Mã SHA-256 của gói đối chiếu:c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:Day_lab_02

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:
Bài làm hoàn toàn độc lập vì toàn bộ 75 nhãn vật thể trên 4 ảnh được gán và kết xuất (export) hoàn tất thành 2 gói dữ liệu độc lập (mine-yolo.zip và mine-native.zip) trước thời điểm tiếp nhận bộ nhãn đối chiếu. Tính nhất quán hình học giữa hai định dạng của bài làm đạt IoU tối thiểu là 0.9999, và mã SHA-256 của bài làm đã được chốt cố định trong file audit trước khi bước tính toán so sánh IoU (comparison_summary.json) diễn ra, đảm bảo không có sự chỉnh sửa hay tham khảo nhãn của bộ đối chiếu trong quá trình gán.



## 2. Quyết định phân lớp

 -Ảnh drive_008 (Xe hộp màu trắng ở ngã tư)Lớp: van 
 + Dấu hiệu nhìn thấy: Dáng xe hình hộp chữ nhật liền khối, khoang trần phẳng và cao, không phân tách thùng hàng riêng.
 + Quy tắc áp dụng: Gán nhãn van cho các dòng xe chở hàng hoặc chở khách dạng panel box liền khối; vẽ bounding box bao trọn toàn bộ thân xe nhìn thấy được sát mép bánh tiếp đất. 
 -Ảnh drive_038 (Xe chở đất đá/vật liệu)Lớp: truck 
 + Dấu hiệu nhìn thấy: Đầu cabin phẳng, tách biệt rõ ràng với phần thùng chở tải nặng phía sau. 
 + Quy tắc áp dụng: Gán nhãn truck cho các xe tải có kết cấu cabin và thùng hàng rời; vẽ bounding box bao trùm toàn bộ cả cabin lẫn thùng chở hàng.  

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Lớp (Class): Là danh tính phân loại ngữ nghĩa cốt lõi của đối tượng (ví dụ: car, truck, bus, van). Đổi nhãn từ car sang van là đổi hoàn toàn loại vật thể cho mô hình học.  Thuộc tính (Attribute): Là thông tin phụ trợ miêu tả tình trạng hình ảnh của vật thể đó (ví dụ: visibility="occluded", boundary="truncated", review_state="confident") mà không làm thay đổi bản chất phương tiện. 
 Ví dụ: Một chiếc ô tô con sát mép ảnh bị che khuất vẫn có lớp bản chất là car, kèm hai thuộc tính: visibility="occluded" và boundary="truncated".  

## 3. Tự kiểm tra và sửa nhãn

Trường hợp 1: drive_038 (Xe ở mép trái bị cắt ngang cạnh ảnh)
 Trước khi sửa: Bounding box của xe ở mép trái bị vẽ lỡ cỡ sát rìa và chưa khai báo tình trạng xe bị cắt. 
 Loại lỗi: Phạm vi & Thuộc tính.
 Cách phát hiện: Rà soát lại tọa độ, phát hiện cạnh hộp chạm sát mép X=0 nhưng thiếu cờ thuộc tính truncated.  Sau khi sửa và quy tắc: Kéo sát mép biên ảnh X=0, bật thuộc tính boundary="truncated" theo đúng quy tắc xử lý vật thể bị che khuất/cắt lề.
Trường hợp 2: drive_008 (Xe hộp cứu hộ/chuyên dụng)
  Trước khi sửa: Vật thể đang bị gán nhầm là xe con thông thường.
  Loại lỗi: Lớp (Class).
  Cách phát hiện: Quan sát lại kết cấu ngoại quan, nhận thấy thân xe dạng hộp liền khối, không có khoang chở khách sedan/hatchback thông thường.Sau khi sửa và quy tắc: Chuyển nhãn từ car sang van, tuân thủ quy tắc định danh các phương tiện có cấu trúc thân hộp vuông liền khối.  

- Số hộp `needs_review` trước và sau khi kiểm:
Trước khi kiểm là 3 hộp, sau khi rà soát và hiệu chỉnh toàn bộ chuyển thành 0 hộp (review_state đạt 100% confident trên 75 đối tượng).  
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:
Xe chuyên dụng cứu hộ giao thông mang biển chữ "Công An" ở góc xa bị mờ. Cách xử lý: Gắn cờ review_state="needs_review", chụp ảnh phóng to gửi kèm tọa độ trong group trao đổi lab để mentor làm rõ quy ước gán nhãn thuộc

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: 0 0.267242 0.506961 0.108547 0.071109
- Tên lớp và tọa độ điểm ảnh `xyxy`:
Lớp car (class_id = 0), trên ảnh kích thước 640x640:
    - x1 = (0.267242 - 0.108547 / 2) * 640 ≈ 136.3
    - y1 = (0.506961 - 0.071109 / 2) * 640 ≈ 301.7
    - x2 = (0.267242 + 0.108547 / 2) * 640 ≈ 205.8
    - y2 = (0.506961 + 0.071109 / 2) * 640 ≈ 347.2

- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
- **Về cú pháp:** Định dạng YOLO chỉ kiểm tra xem có đủ 5 cột số thực trong khoảng [0, 1] hay không.
- **Về ngữ nghĩa:** Máy đọc không thể tự phát hiện đối tượng thực tế trong ảnh là `truck` hay `van` nếu ta gán nhầm là `0` (`car`). Ngoài ra, hộp có thể bị bao thiếu phần đuôi xe (sai hình học), hoặc bị nhầm giữa chiều rộng/cao làm tâm hộp trôi ra ngoài lòng đường (sai phạm vi).

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`
- Mã ảnh thẩm định: `drive_008`
- Mô tả một dự đoán trong `detect_result.jpg`: Mô hình phát hiện tốt các phương tiện kích thước lớn ở cự ly gần (ô tô con, xe buýt lớn) với độ tin cậy cao, tuy nhiên các phương tiện nằm ở nút giao phía xa bị bỏ sót hoặc gán nhầm giữa `van` và `car`.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Cần rà soát lại tiêu chuẩn kích thước vật thể nhỏ ở hậu cảnh (dưới bao nhiêu pixel thì không cần gán) và làm rõ đặc trưng nhận diện giữa `van` và `car`/`truck` khi bị che khuất.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Nếu thử nghiệm với mô hình dung lượng lớn hơn hoặc sử dụng ảnh đầu vào độ phân giải gốc cao hơn mà mô hình vẫn không tách được lớp `van` và `car`, thì nguyên nhân là do dữ liệu ảnh bị mờ/mất chi tiết điểm ảnh chứ không phải do quy tắc gán nhãn.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?
  - Kích thước tập dữ liệu cực nhỏ (3 ảnh train, 1 ảnh val) hoàn toàn không đảm bảo tính đại diện thống kê và mô hình bị overfitting nghiêm trọng.
  - Bộ dữ liệu thử nghiệm thiếu toàn diện về các điều kiện thời tiết (mưa, nắng gắt), các góc chụp khác nhau và sự thay đổi ánh sáng theo thời gian thực (ban đêm, chập tối).

CHƯA ĐIỀN

## 6. Đối chiếu nhãn

- Số hộp ghép được: `48`
- IoU trung bình và trung vị: IoU trung bình `0.816476`, IoU trung vị `0.832132`
- Mức đồng thuận lớp: `70.83%` (`0.708333`)
- Số hộp phía bạn không ghép được: `27`
- Số hộp phía đối chiếu không ghép được: `2`
- Một điểm khác biệt cụ thể: Bài làm của tôi phát hiện và gán nhãn thêm nhiều phương tiện nhỏ ở xa hoặc bị che khuất nhiều (dẫn đến 27 hộp dư so với bộ đối chiếu), đồng thời có sự sai lệch phân loại giữa `car` và `van` ở cụm xe giữa ngã tư.
- Quy tắc hoặc hành động sửa phát sinh: Cần thống nhất ngưỡng kích thước tối thiểu (ví dụ: không gán nhãn vật thể dưới 10x10 pixel) và chuẩn hóa hướng dẫn phân biệt dòng xe `van` với xe tải nhỏ/xe con.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? Mức đồng thuận cao chỉ phản ánh sự trùng khớp góc nhìn giữa hai bên gán nhãn. Nếu cả hai người cùng hiểu sai tài liệu hướng dẫn hoặc cùng bỏ sót các phương tiện bị khuất thì tỷ lệ đồng thuận vẫn cao trong khi dữ liệu vẫn bị sai so với thực tế.



## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:
Tính nhất quán chuyển đổi hình học giữa hai định dạng CVAT XML và YOLO đạt IoU tối thiểu `0.999947` trên toàn bộ 75 nhãn, đồng thời độ chính xác bounding box khi so khớp với bộ tham chiếu đạt IoU trung vị `0.832132`.
Đối với các phương tiện chuyên dụng hoán cải (như xe cứu hộ cảnh sát có đầu cabin dạng xe van nhưng phía sau gắn cần cẩu hàng), dự án quy định ưu tiên gán theo kiểu dáng cabin gốc (`van`) hay theo công năng chuyên chở thực tế (`truck`)?



