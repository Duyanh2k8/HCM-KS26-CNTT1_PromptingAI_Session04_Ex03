# [BTTH] Bài tập thực hành tổng hợp
## 1. Phân tích bài toán
**Bối cảnh:**

Một cửa hàng bán lẻ mỗi ngày nhận nhiều đơn hàng từ khách. Nếu nhân viên tự kiểm tra thông tin, tính tiền và kiểm tra tồn kho thì sẽ mất khá nhiều thời gian. Vì vậy, em đề xuất sử dụng AI để tự động xử lý đơn hàng thông qua nhiều bước liên kết với nhau.

**Mục tiêu:**
- Kiểm tra thông tin đơn hàng có đầy đủ và hợp lệ không.
- Tính tổng tiền đơn hàng.
- Kiểm tra số lượng hàng còn trong kho.
- Phân loại trạng thái đơn hàng.
- Có khả năng xử lý khi dữ liệu bị thiếu hoặc chuỗi prompt bị gián đoạn.
## 2. Thiết kế giải pháp
Giải pháp được chia thành 3 bước:
|Bước| Kỹ thuật| Nhiệm vụ|
|---|---|---|
|Bước 1| Chain-of-Thought| Kiểm tra và chuẩn hóa dữ liệu đầu vào|
|Bước 2| Prompt Chaining| Tính tiền và kiểm tra tồn kho|
|Bước 3| Kiểm tra đứt gãy| Kiểm tra kết quả và xuất báo cáo cuối cùng|

**Cách hoạt động:**

Dữ liệu từ khách hàng được đưa vào bước 1 để kiểm tra. Nếu hợp lệ, kết quả sẽ chuyển sang bước 2 để tính tiền và kiểm tra kho. Cuối cùng, bước 3 kiểm tra lại toàn bộ kết quả trước khi xuất báo cáo.

Nếu một bước bị gián đoạn, hệ thống sử dụng kết quả đã lưu ở bước trước để tiếp tục, không cần xử lý lại từ đầu.
## 3. Prompt thực thi
### Prompt hệ thống
Bạn là trợ lý AI hỗ trợ quản lý đơn hàng cho cửa hàng bán lẻ.

Nhiệm vụ của bạn là xử lý đơn hàng theo 3 bước liên tiếp:

**Bước 1: Kiểm tra dữ liệu**
- Kiểm tra tên khách hàng, sản phẩm, số lượng, đơn giá, tồn kho và địa chỉ.
- Chuẩn hóa dữ liệu về cùng một định dạng.
- Phát hiện dữ liệu thiếu, sai kiểu hoặc không hợp lệ.
- Không tự ý đoán những thông tin còn thiếu.

**Bước 2: Xử lý đơn hàng**
- Tính tổng tiền = số lượng × đơn giá.
- So sánh số lượng đặt hàng với số lượng tồn kho.
- Nếu đủ hàng: trạng thái là "Đủ hàng".
- Nếu không đủ hàng: trạng thái là "Không đủ hàng".
- Nếu dữ liệu chưa hợp lệ: không tiếp tục tính toán.

**Bước 3: Kiểm tra kết quả**
- Kiểm tra lại phép tính và trạng thái đơn hàng.
- Nếu phát hiện lỗi, đánh dấu và không xuất kết quả sai.
- Nếu bị gián đoạn, sử dụng dữ liệu đã lưu từ bước trước để tiếp tục.
- Xuất kết quả dưới dạng bảng Markdown và JSON.

**Yêu cầu:**
- Thực hiện tuần tự, không bỏ qua bước.
- Không tự tạo dữ liệu khi đầu vào bị thiếu.
- Không hiển thị quá trình suy luận nội bộ, chỉ đưa ra kết quả kiểm tra ngắn gọn.
- Luôn lưu trạng thái sau mỗi bước để có thể tiếp tục khi bị gián đoạn.
## 4. Dữ liệu đầu vào chạy thử
```JSON
{
  "customer": "Nguyễn Văn An",
  "product": "Chuột Logitech",
  "quantity": 3,
  "unit_price": 250000,
  "stock": 10,
  "address": "Thủ Đức, TP.HCM"
}
```
## 5. Kết quả chạy thử
### Kết quả bước 1: Kiểm tra dữ liệu
- Tên khách hàng: Hợp lệ.
- Tên sản phẩm: Hợp lệ.
- Số lượng: 3, hợp lệ.
- Đơn giá: 250.000 VNĐ, hợp lệ.
- Tồn kho: 10 sản phẩm.
- Địa chỉ: Đầy đủ.

**Kết quả:** Dữ liệu hợp lệ, chuyển sang bước 2.
### Kết quả bước 2: Xử lý đơn hàng
- Số lượng đặt: 3.
- Số lượng tồn kho: 10.
- Tổng tiền: 3 × 250.000 = 750.000 VNĐ.
- Trạng thái: Đủ hàng.

**Kết quả:** Đơn hàng có thể tiếp tục xử lý.
### Kết quả bước 3: Kiểm tra cuối cùng
- Dữ liệu đầu vào: Đã kiểm tra.
- Phép tính: Chính xác.
- Tồn kho: Đáp ứng đủ số lượng.
- Trạng thái đơn hàng: Hợp lệ.

**Báo cáo đơn hàng cuối cùng:**
|Thông tin|Kết quả|
|---|---|
|Khách hàng|Nguyễn Văn An|
|Sản phẩm|Chuột Logitech|
|Số lượng|3|
|Đơn giá|250.000 VNĐ|
|Tổng tiền|750.000 VNĐ|
|Tồn kho|10|
|Trạng thái|Đủ hàng|

**JSON đầu ra:**
```JSON
{
  "customer": "Nguyễn Văn An",
  "product": "Chuột Logitech",
  "quantity": 3,
  "unit_price": 250000,
  "total": 750000,
  "stock": 10,
  "status": "Đủ hàng",
  "valid": true,
  "errors": []
}
```
## 6. Kiểm tra khả năng xử lý lỗi
Em thử đưa thêm một trường hợp dữ liệu không hợp lệ:
```JSON
{
  "customer": "Nguyễn Văn An",
  "product": "Chuột Logitech",
  "quantity": "ba",
  "unit_price": 250000,
  "stock": 10,
  "address": ""
}
```
**Kết quả AI trả về:**
- Số lượng: Không hợp lệ vì đang ở dạng chữ.
- Địa chỉ: Bị thiếu.
- Không thực hiện phép tính tổng tiền.
- Không chuyển sang bước xác nhận đơn hàng.

Trạng thái: NEEDS_INPUT

Lỗi phát hiện:
1. quantity không đúng định dạng số.
2. address đang bị bỏ trống.

Hệ thống yêu cầu bổ sung thông tin thay vì tự đoán dữ liệu hoặc dừng toàn bộ chương trình.
