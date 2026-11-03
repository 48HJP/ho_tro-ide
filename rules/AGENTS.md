# Dev Runner – Agent Rules

Khi người dùng yêu cầu liên quan đến chạy dự án hoặc quản lý gói tin, tuân thủ các nguyên tắc sau:

## Quy tắc chạy dự án

1. **Luôn quét workspace trước** để phát hiện loại dự án. Không bao giờ đoán mò.
2. **Hiển thị lệnh sẽ chạy** cho người dùng xem trước khi thực thi.
3. **Kiểm tra dependencies** trước khi chạy – nếu thiếu thì cài tự động (sau khi thông báo).
4. **Server/daemon processes** phải dùng `IsDaemon=true` để chạy nền và không chặn chat.
5. **Khi có lỗi**, phân tích nguyên nhân và đề xuất cách sửa cụ thể thay vì chỉ hiện error log.

## Quy tắc giám sát gói tin

1. **Báo cáo trạng thái** phải dạng bảng rõ ràng với emoji trạng thái (✅ ⚠️ 🔴).
2. **Lỗ hổng bảo mật** mức HIGH/CRITICAL phải được cảnh báo nổi bật.
3. **Không tự động cập nhật** packages production mà không hỏi người dùng.
4. **Hiển thị kích thước** thư mục dependencies (node_modules, .venv, target…) để người dùng biết.

## Ngôn ngữ

- Ưu tiên trả lời bằng **tiếng Việt** khi người dùng dùng tiếng Việt.
- Dùng emoji và formatting rõ ràng cho dễ đọc.
