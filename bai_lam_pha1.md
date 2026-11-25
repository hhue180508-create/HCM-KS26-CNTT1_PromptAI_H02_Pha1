# BÀI LÀM PHA 1 

- **Họ tên:**
- **Lớp:**
- **Link repository Pha1:**

> Pha 1 (70 phút đầu, KHÔNG dùng AI). Viết xong, kéo thả file này vào repository Pha1 trước mốc giờ giám thị công bố.

## 4.1. Bảo vệ dữ liệu và chống ảo giác

### a) Ghi chú đã ẩn danh hoá

**GHI CHU DA AN DANH**

```text
[TEN_KHACH], [SO_DIEN_THOAI], [SO_CCCD],  [EMAIL_KHACH] 
```

### b) Hai câu lệnh chống ảo giác

**CAU LENH**

```text
-Câu1: Các điều khoản đổi trả và hoàn tiền chỉ được lấy từ trong tệp file đính kèm không được tự bịa hoặc lấy những thông tin không được cung cấp . Nếu file đính kèm không có thông tin phải đặt câu hỏi lại cho nhân viên , ví dụ: "File đính kèm bạn gửi không có những điều khoản mà bạn yêu cầu " và tuyệt đối không được làm tiếp nếu chưa nhận được câu trả lời từ nhân viên

-Câu2: Khi trả lời bạn hãy nêu rõ lấy từ mục nào , điều khoản nào trong file liệt kê rõ ràng ra cho tôi, ví dụ :" Lấy từ mục...ở điều khoản... rồi nêu nội dung " ,không được lấy từ nguồn file bên ngoài mà tôi không cung cấp.

```

## 4.2. Trích xuất tin nhắn đặt vé ra JSON

**PROMPT**

```text
C-Context
-Bạn là một nhân viên hỗ trợ đặt vé , nhận tin nhắn khách hàng và xử lí tin nhắn khách hàng
R-Request
-chuyển TN1, TN2, TN3 thành một mảng JSON 3 đối tượng, đúng thứ tự, đúng 6 trường như tin nhắn mẫu
E-Explanation
-Do dữ liệu đầu vào khác nhau nên cách nhập mã vé , ngày sự kiện, thông tin khách hàng, số lượng đều khác nhau nên hãy làm thật chính xác và lấy các dữ liệu thật cẩn thận
A - Action
Bước1:
-nhận tin nhắn khách hàng Ví dụ:

<tin_nhan>
TN1: Mình muốn đặt gói Sân Vận Động trận tứ kết ngày 20/12 nha, 2 người lớn với 1 bé 8 tuổi, vé thường.

TN2: Cho 3 người đi Fan Zone trận ngày 05/01/2027 ạ. Một người cần hỗ trợ xe lăn.

TN3: Trận chung kết Cúp Sao Vàng tháng 12 còn vé không ạ? Nhà em 4 người lớn, có một người khiếm thị cần hỗ trợ tìm chỗ, lấy vé VIP.
</tin_nhan>

-xử lí phân loại yêu cầu đặt vé của khách hàng theo Bảng Mã Gói:
"SVT – Sân Vận Động: vé trận đấu
FZ – Fan Zone: khu cổ vũ
VIP – Khán đài VIP"

Bước2:
- Sau đó thật hiện theo các "QUY TẮC CHUẨN HÓA"
1. ma_goi lấy theo bảng mã gói.
2. ngay_su_kien dạng YYYY-MM-DD; tin nhắn không ghi năm thì lấy năm 2026; không có ngày cụ thể thì null.
3. Khách không nhắc trẻ em thì tre_em là 0 và mọi khách là người lớn.
4. loai_ve chỉ nhận "thuong", "vip" hoặc null.
5. ghi_chu chỉ ghi yêu cầu đặc biệt về chỗ ngồi, hỗ trợ di chuyển hoặc ăn uống; không có thì null.
Bước3:
- Tổng hợp lại toàn bộ thông tin để nhân viên check lại.
T - Tone
-Giọng nói thân thiện, dễ gần, hoài nhã, đúng chất giọng của một nhân viên hỗ trợ khách hàng.
E - Extras
-OUTPUT:
Tin nhắn mẫu 1:
Đặt giúp mình 2 vé VIP trận chung kết ngày 15/11/2026, 2 người lớn và 1 bé. Cần chỗ ngồi cạnh lối đi.
Kết quả đúng:
{"ma_goi": "VIP", "ngay_su_kien": "2026-11-15", "nguoi_lon": 2, "tre_em": 1, "loai_ve": "vip", "ghi_chu": "chỗ ngồi cạnh lối đi"}

Tin nhắn mẫu 2:
Cho mình một vé Fan Zone, ngày nào cũng được, mình đi một mình.
Kết quả đúng:
{"ma_goi": "FZ", "ngay_su_kien": null, "nguoi_lon": 1, "tre_em": 0, "loai_ve": "thuong", "ghi_chu": null}
-Bỏ qua các bước chào hỏi , không giải thích, chỉ cần  trả về JSON .


```

## 4.3. Báo giá vé bằng Chain-of-Thought

### a) Prompt

**PROMPT**

```text
C - Context
-Bạn là một nhên viên bán vé của ban tổ chức bóng đá.
R - Request
-tính tổng tiền cho yêu cầu báo giá: đưa bảng giá, quy tắc và yêu cầu báo giá vào cặp dấu phân tách; yêu cầu suy luận từng bước theo thứ tự tiền người lớn → tiền trẻ em → tiền vé → giảm giá nhóm → phụ thu → tổng tiền, mỗi bước ghi phép tính; dòng cuối có dạng [RESULT: <tổng tiền> đồng].
E - Explanation
-Do yêu cầu của mỗi khách hàng khác nhau, mỗi sân vận động có giá tiền khác nhau,và trẻ em hoặc người lớn có giá tiền khác nhau nên cần lấy thông tin thật chính xác và cẩn thận không được sai .
A - Action
Bước1: Lấy thông tin khách hàng và yêu cầu 
Bước2: Lọc và phân loại loại vé 
Bước3: Tính tiền dựa vào các "QUY TẮC TÍNH GIÁ"
1. Trẻ em từ 5 đến 11 tuổi tính 70% giá người lớn. Trẻ dưới 5 tuổi miễn phí.
2. Nhóm từ 5 người lớn trở lên được giảm 5% trên tiền vé (tiền người lớn + tiền trẻ em).
3. Phụ thu khu ghế ưu tiên: 300.000 đồng/người. Phụ thu không được giảm giá.
4. Tổng tiền = tiền vé sau giảm + phụ thu.
T - Tone
-Giọng thân thiện , dễ gần, đúng chất là một nhân viên hỗ trợ khách hàng 
E - Extras
-Bỏ qua các lời chào hỏi, không giải thích, chỉ cần  trả về JSON .

```

## 4.4. Dây chuyền xử lý khiếu nại (Prompt Chaining)

### a) Prompt Trạm 1

**PROMPT**

```text
(Viết prompt vào đây – xoá dòng này)
```

### b) Hai điều cần kiểm tra ở JSON đầu ra của Trạm 1

1. (Viết câu trả lời vào đây – xoá dòng này)

2.

### c) Câu lệnh viết tiếp

**CAU LENH**

```text
(Viết câu lệnh vào đây – xoá dòng này)
```

## 4.5. Prompt chấm bài theo Rubric

**PROMPT**

```text
(Viết prompt vào đây – xoá dòng này)
```

## 4.6. Hoàn thiện System Prompt

> Phần Vai trò và Chuẩn đầu ra đã cho sẵn. Viết tiếp phần Quy tắc ràng buộc và Xử lý ngoại lệ.

```text
[Vai trò]
Bạn là "Trợ lý Cúp Sao Vàng", nhân viên tư vấn vé và dịch vụ trên website của giải bóng đá Cúp Sao Vàng, giúp khán giả chọn và đặt một trong 3 gói: SVT (Sân Vận Động – vé trận đấu), FZ (Fan Zone – khu cổ vũ), VIP (Khán đài VIP).

[Quy tắc ràng buộc]
1. (Viết quy tắc 1 – xoá dòng này)
2.
3.

[Chuẩn đầu ra]
Trả lời bằng tiếng Việt, lịch sự, tối đa 120 từ. Giá vé trình bày dạng gạch đầu dòng. Kết thúc bằng một câu hỏi về nhu cầu tiếp theo của khách.

[Xử lý ngoại lệ]
- Khách hỏi ngoài phạm vi: (Viết cách trả lời – xoá dòng này)
- Khách cung cấp thiếu thông tin:
```
