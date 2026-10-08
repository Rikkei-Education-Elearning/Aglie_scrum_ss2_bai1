# Bài 1 – Epic "Hủy chuyến ghép" (RikkeiGo)

## Phần 1 – Đề xuất 2 chiến lược phân rã

### Chiến lược A: Phân rã theo bước trong luồng xử lý (Workflow)

Chia Epic theo từng bước xảy ra khi một khách hủy, từ đầu đến cuối.

| Feature | Nội dung |
|---|---|
| A1 | Gửi và xác nhận yêu cầu hủy chuyến |
| A2 | Xác định thời điểm hủy và tính phí hủy |
| A3 | Tính lại giá cho các khách còn lại |
| A4 | Thông báo cho khách còn lại và tài xế |

### Chiến lược B: Phân rã theo quy tắc nghiệp vụ / trường hợp hủy (Business Rule)

Chia Epic theo từng quy tắc hoặc tình huống hủy, mỗi Feature là một lát cắt chạy được từ đầu đến cuối.

| Feature | Nội dung |
|---|---|
| B1 | Hủy miễn phí trước khi tài xế nhận chuyến |
| B2 | Hủy có phí 10.000đ sau khi tài xế nhận chuyến |
| B3 | Tính lại giá và thông báo ngay cho khách còn lại |
| B4 | Xử lý hủy trùng thời điểm tài xế nhận (lấy giờ máy chủ làm căn cứ) |

---

## Phần 2 – So sánh theo INVEST

| Tiêu chí | Chiến lược A (theo luồng) | Chiến lược B (theo quy tắc) |
|---|---|---|
| **Independent** (Độc lập) | **(–)** Các bước nối tiếp nhau: A2, A3, A4 đều cần A1 xong trước, khó làm song song. | **(+)** B1 chạy được một mình, B2 chỉ thêm quy tắc phí. **(–)** nhẹ: B3 vẫn cần có hành động hủy. |
| **Small** (Nhỏ) | **(+)** Mỗi bước nhỏ, dễ ước lượng. **(–)** Nhưng A2 gồm nhiều nhánh (miễn phí/có phí/trùng giờ) nên dễ phình to. | **(+)** Mỗi Feature chỉ một quy tắc, vừa trong một Sprint, dễ ước lượng. |
| **Valuable** (Có giá trị) | **(–)** Làm xong từng bước riêng lẻ khách chưa dùng được gì; phải đủ cả chuỗi mới giải quyết phản ánh. | **(+)** Xong B1 là khách đã hủy được; xong B3 là hết tình trạng khách ở lại bị đội giá. Giao giá trị từng phần sau mỗi Sprint. |

---

## Phần 3 – Lựa chọn & đặc tả

### Chọn: Chiến lược B (theo quy tắc nghiệp vụ)

**Lý do:** B đạt INVEST tốt hơn, nhất là Independent và Valuable: mỗi Feature giao được giá trị thật sau mỗi Sprint và giải quyết đúng từng phản ánh (tài xế chờ vô ích, khách bị đội giá). Các quy tắc phí cũng tách riêng, dễ kiểm thử và dễ thay đổi sau này.

### User Story (Feature B2 – Hủy có phí sau khi tài xế nhận)

> **Là** một tài xế nhận chuyến ghép,
> **tôi muốn** khách hủy sau khi tôi đã nhận chuyến bị tính phí hủy 10.000đ,
> **để** tôi được bù đắp phần thời gian chờ vô ích.

**Acceptance Criteria:**
- Khách hủy **trước** khi tài xế nhận chuyến: phí = 0đ.
- Khách hủy **sau** khi tài xế nhận chuyến: phí = 10.000đ.
- Thời điểm tính phí lấy theo **giờ ghi nhận trên máy chủ**, không lấy giờ trên thiết bị của khách.
- Phí hủy được hiển thị cho khách trước khi xác nhận hủy.

### Kịch bản Given – When – Then (hủy đúng lúc tài xế nhận chuyến)

**Tình huống:** Khách A bấm hủy gần như cùng lúc tài xế bấm nhận.

- **Given** khách A đang trong một chuyến ghép cùng khách B, và máy chủ ghi nhận tài xế nhận chuyến lúc 10:00:05
- **When** khách A bấm hủy và máy chủ ghi nhận yêu cầu hủy lúc 10:00:07 (dù trên điện thoại khách A hiển thị 10:00:04)
- **Then** hệ thống tính phí hủy **10.000đ** cho khách A vì thời điểm hủy trên máy chủ **sau** thời điểm tài xế nhận; đồng thời giá của khách B được tính lại và thông báo ngay.

**Biến thể (ngược lại):** nếu máy chủ ghi nhận yêu cầu hủy lúc 10:00:03 (trước 10:00:05) thì phí hủy = **0đ**.
