# Ví dụ minh họa

Ba ví dụ dưới đây minh họa cách áp dụng template ở `assets/task_template.md` và bảng estimate ở SKILL.md cho 3 mức độ phức tạp khác nhau (S, M, L). Dùng làm tham chiếu khi soạn task thật, không copy nguyên văn nội dung nghiệp vụ — chỉ học theo cách cấu trúc, mức độ chi tiết, và cách suy luận estimate.

---

## Ví dụ 1 — Task nhỏ (S): Bug hiển thị sai số dư

Yêu cầu gốc của người dùng (giả định): "Số dư tài khoản trong app hiển thị sai khi user có giao dịch hoàn tiền, cần fix gấp."

```
📋 PREVIEW TASK — Jira

Title: Sửa lỗi số dư khi có giao dịch hoàn tiền

Description:

### Bối cảnh
Màn hình "Số dư tài khoản" trong app mobile lấy dữ liệu từ API `GET /wallet/balance`, tính tổng dựa trên các bản ghi giao dịch trong bảng `transactions`.

### Vấn đề hiện tại
Khi user có giao dịch loại `refund`, số dư hiển thị trên app bị trừ thêm một lần nữa thay vì cộng lại, dẫn đến số dư hiển thị thấp hơn thực tế. Tái hiện: tạo 1 giao dịch `refund` cho user test → mở màn hình số dư → số dư giảm thay vì tăng.

### Mong muốn / Kết quả cần đạt
Giao dịch loại `refund` phải được cộng vào số dư thay vì bị trừ. Số dư hiển thị trên app khớp với số dư tính đúng trong hệ thống backend.

### Mục đích
Tránh gây hoang mang và mất niềm tin của user vào số dư hiển thị, giảm ticket hỗ trợ liên quan đến "số dư sai".

### Phạm vi chỉnh sửa
Hàm tính tổng số dư trong service `WalletBalanceService` (1 file), không đổi API contract, không cần migration.

### Tài liệu liên quan
Không có.

Estimate:
- Độ phức tạp: S — chỉ sửa logic tính tổng trong 1 hàm của 1 service, không ảnh hưởng API hay schema.
- Coding (AI code): 0.5h — logic sửa gọn trong 1 hàm, chỉ cần đổi dấu cộng/trừ theo loại giao dịch.
- Review code: 0.5h — diff nhỏ (1 file), dễ đọc, không cần kiểm tra nhiều luồng phụ.
- Self test: 0.5h — chỉ cần tạo 1 giao dịch refund test và kiểm tra lại số dư hiển thị.
- Tổng: 1.5h

Assignee: Chưa xác định — cần hỏi người dùng
```

---

## Ví dụ 2 — Task trung bình (M): Thêm bộ lọc theo trạng thái đơn hàng

Yêu cầu gốc của người dùng (giả định): "Team CSKH cần lọc đơn hàng theo trạng thái (đang xử lý/đã giao/đã hủy) trên trang quản trị, hiện chưa có."

```
📋 PREVIEW TASK — Notion

Title: Thêm bộ lọc trạng thái đơn hàng

Description:

### Bối cảnh
Trang quản trị đơn hàng (`/admin/orders`) hiện chỉ hiển thị danh sách đơn hàng theo thời gian tạo, chưa có cách lọc theo trạng thái. Team CSKH đang phải tự lọc bằng mắt trong danh sách dài.

### Vấn đề hiện tại
Không có UI filter theo trạng thái đơn hàng (đang xử lý / đã giao / đã hủy) trên trang quản trị, API `GET /admin/orders` cũng chưa hỗ trợ query param lọc theo status.

### Mong muốn / Kết quả cần đạt
Team CSKH chọn được 1 hoặc nhiều trạng thái trên UI để lọc danh sách đơn hàng; kết quả trả về đúng và đủ theo trạng thái đã chọn, giữ nguyên phân trang hiện có.

### Mục đích
Giảm thời gian tra cứu đơn hàng của team CSKH, hạn chế nhầm lẫn khi xử lý thủ công trên danh sách dài.

### Phạm vi chỉnh sửa
- API `GET /admin/orders`: thêm query param `status` (multi-value).
- Component filter trên trang `/admin/orders` (frontend): thêm dropdown multi-select trạng thái.
- Không cần migration DB (trường `status` đã có sẵn trong bảng `orders`).

### Tài liệu liên quan
Không có.

Estimate:
- Độ phức tạp: M — cần sửa cả API và UI (khoảng 3-4 file), nhưng không đổi schema và không ảnh hưởng service khác.
- Coding (AI code): 2h — thêm query param ở API + xây dropdown multi-select ở frontend, không phức tạp nhưng chạm 2 tầng (API + UI).
- Review code: 1h — cần review cả phần backend filter và phần UI state, nhưng logic không phức tạp.
- Self test: 1.5h — cần test nhiều tổ hợp trạng thái được chọn (1 trạng thái, nhiều trạng thái, không chọn gì) và kiểm tra phân trang vẫn đúng.
- Tổng: 4.5h

Assignee: Chưa xác định — cần hỏi người dùng
```

---

## Ví dụ 3 — Task lớn (L): Tách luồng thanh toán ra service riêng

Yêu cầu gốc của người dùng (giả định): "Luồng thanh toán hiện đang nằm chung trong service Order, cần tách ra thành service riêng để dễ scale và maintain."

```
📋 PREVIEW TASK — Jira

Title: Tách luồng thanh toán thành Payment Service riêng

Description:

### Bối cảnh
Hiện tại logic thanh toán (tạo giao dịch, gọi cổng thanh toán, xử lý callback) đang nằm chung trong `OrderService`, khiến service này ngày càng phình to và khó scale riêng phần thanh toán khi lượng giao dịch tăng.

### Vấn đề hiện tại
`OrderService` xử lý cả nghiệp vụ đơn hàng và thanh toán trong cùng 1 codebase/1 database, gây khó khăn khi cần scale riêng, khó test độc lập, và rủi ro khi deploy (một thay đổi ở phần đơn hàng có thể ảnh hưởng luồng thanh toán).

### Mong muốn / Kết quả cần đạt
Có `PaymentService` độc lập, chịu trách nhiệm toàn bộ luồng thanh toán (tạo giao dịch, gọi cổng thanh toán, xử lý callback, lưu trạng thái thanh toán). `OrderService` gọi sang `PaymentService` qua API/message queue thay vì xử lý trực tiếp. Toàn bộ luồng thanh toán hiện có (thành công, thất bại, hoàn tiền) vẫn hoạt động đúng sau khi tách.

### Mục đích
Cho phép scale riêng phần thanh toán khi traffic tăng, giảm rủi ro khi deploy, dễ maintain và test độc lập từng phần nghiệp vụ.

### Phạm vi chỉnh sửa
- Tạo mới `PaymentService` (repo/service mới).
- Migration: tách bảng liên quan thanh toán ra database riêng (hoặc schema riêng).
- Sửa `OrderService`: thay lời gọi hàm nội bộ bằng lời gọi API/message queue sang `PaymentService`.
- Cập nhật cấu hình hạ tầng (deploy, service discovery) — cần phối hợp với team DevOps.

### Tài liệu liên quan
Cần bổ sung thiết kế kỹ thuật (technical design doc) trước khi triển khai — hiện chưa có.

Estimate:
- Độ phức tạp: L, có xu hướng gần XL — liên quan nhiều module (Order + Payment + hạ tầng), có migration DB, và cần phối hợp DevOps.
- Coding (AI code): 8h — dựng service mới từ đầu, viết lại lời gọi giữa OrderService và PaymentService qua API/message queue, xử lý migration dữ liệu.
- Review code: 3h — review cần trải rộng qua nhiều phần (service mới, thay đổi trong OrderService, cấu hình hạ tầng), rủi ro cao nên cần đọc kỹ.
- Self test: 4h — phải test lại toàn bộ các luồng thanh toán hiện có (thành công, thất bại, hoàn tiền) sau khi tách service để đảm bảo không phá luồng cũ.
- Tổng: 15h

⚠️ Gợi ý: Task này ở ngưỡng L/XL — nên tách thành các task con (ví dụ: "Thiết kế kỹ thuật Payment Service", "Xây dựng Payment Service", "Migration dữ liệu thanh toán", "Tích hợp OrderService với Payment Service") để dễ estimate và theo dõi tiến độ hơn là giữ 1 task lớn.

Assignee: Chưa xác định — cần hỏi người dùng
```
