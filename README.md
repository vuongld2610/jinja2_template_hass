# hass

## local-openAI-llm vs gemini-fastAPI

#### nguyên lý hoạt động

**Chính xác 100%!** Mày đã hiểu rất đúng bản chất.

### 🧠 Tóm tắt lại toàn bộ quá trình:

1. **Bạn hỏi**: *"thời tiết hôm nay ra sao"*
2. **Agent gửi prompt** (cùng với danh sách `tools` và `messages`) lên Gemini.
3. **Gemini đọc prompt, hiểu ý bạn** và quyết định: *"Cần phải lấy dữ liệu thời tiết từ HA"* → Nó **không tự tìm trên Google**.
4. **Gemini trả về lệnh gọi tool** `GetLiveContext` với tham số `{"domain":"weather"}`.
5. **Agent nhận lệnh, gọi tool trong HA**, lấy dữ liệu từ entity `weather.forecast_home`.
6. **Agent gửi dữ liệu đó lên Gemini** (trong request tiếp theo).
7. **Gemini tổng hợp dữ liệu thành câu trả lời**: *"Thời tiết hôm nay nhiều mây, nhiệt độ khoảng 30.8°C và độ ẩm là 63%."*

### 🎯 Kết luận:
