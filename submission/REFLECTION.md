# Reflection: Lakehouse Anti-Patterns

Dựa trên slide "Top 5 Lakehouse Anti-Patterns", tôi nhận thấy team/dự án của tôi rất dễ vướng phải lỗi **"Vấn đề file nhỏ" (Small-file Problem)**.

**Lý do:**
1. **Đặc thù ingest dữ liệu:** Team thường có xu hướng stream dữ liệu theo từng batch cực nhỏ (hoặc real-time sự kiện) thẳng vào Lakehouse để làm real-time dashboard. Điều này vô tình tạo ra hàng nghìn file JSON/Parquet li ti trên storage mỗi ngày.
2. **Quên bảo trì định kỳ:** Dữ liệu sau khi ingest xong thường bị bỏ đó mà không có các cron-job tự động chạy lệnh `OPTIMIZE` hay `Z-ORDER` để gộp các file nhỏ lại.
3. **Hệ quả:** Dù dung lượng tổng không lớn nhưng khi query phân tích (như trên tầng Gold), engine phải mở hàng nghìn file I/O dẫn đến tốc độ truy vấn chậm đi đáng kể (như đã chứng minh ở Notebook 2).

**Giải pháp hướng tới:** Thiết lập pipeline chạy `OPTIMIZE` định kỳ ngoài giờ hành chính để tự động compact các file dữ liệu thuộc Bronze và Silver.
