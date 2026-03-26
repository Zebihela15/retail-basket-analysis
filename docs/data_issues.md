# Data Quality Report — Online Retail II
## 1. Cancelled Orders (InvoiceNo LIKE 'C%')

**Vấn đề:** Một số InvoiceNo bắt đầu bằng chữ 'C' — đây là đơn hàng bị hủy.

**Ví dụ thực tế:**
InvoiceNo: C489449  →  đơn hàng bị hủy
Quantity: -5        →  số âm xác nhận đây là return

**Tại sao cần loại?**
Đơn hủy không đại diện cho hành vi mua hàng thực sự.
Nếu giữ lại, revenue sẽ bị tính sai (bị trừ đi).

**Cách xử lý:** `WHERE InvoiceNo NOT LIKE 'C%'`

**Ước tính impact:** ~2% tổng số dòng

## 2. Missing CustomerID (NULL)

**Vấn đề:** Khoảng 25% dòng không có CustomerID.

**Nguyên nhân:** Khách mua hàng không đăng nhập (guest checkout).

**Tại sao cần loại (với basket analysis)?**
Basket analysis cần biết một NGƯỜI mua những sản phẩm nào.
Nếu không có CustomerID, ta không thể biết đó là ai.
Ví dụ: Invoice A001 và A002 đều NULL CustomerID — 
có thể là 2 người khác nhau, có thể là 1 người.
Ta không biết → không thể dùng cho basket analysis.

**Cách xử lý:** `WHERE CustomerID IS NOT NULL`

**Ước tính impact:** ~25% dòng bị loại


## 3. Negative Quantity

**Vấn đề:** Một số dòng có Quantity < 0.

**Nguyên nhân:** Đây là giao dịch trả hàng (return/refund),
không phải giao dịch mua mới.

**Ví dụ thực tế:**

StockCode: 84406B
Quantity: -1
-> Khách trả lại 1 sản phẩm


**Tại sao cần loại?**
Revenue = Quantity × UnitPrice → nếu Quantity âm, revenue âm
-> tính tổng revenue sẽ bị sai.

**Cách xử lý:** `WHERE Quantity > 0`

## 4. Zero hoặc Negative UnitPrice

**Vấn đề:** Một số dòng UnitPrice = 0 hoặc < 0.

**Nguyên nhân:** Test entry, điều chỉnh kế toán nội bộ.

**Cách xử lý:** `WHERE UnitPrice > 0`


## 5. Đặc biệt StockCode không phải sản phẩm thực

**Vấn đề:** Một số StockCode là dịch vụ, không phải hàng hóa:
- POST -> phí vận chuyển
- DOT  -> phí dotcom
- M    ->  manual adjustment
- BANK CHARGES -> phí ngân hàng

**Tại sao cần loại?**
Những "sản phẩm" này sẽ làm méo kết quả basket analysis.
Phí ship sẽ xuất hiện trong mọi đơn hàng -> lift cao giả tạo.

**Cách xử lý:** `WHERE StockCode NOT IN ('POST','DOT','M','BANK CHARGES','C2')`
