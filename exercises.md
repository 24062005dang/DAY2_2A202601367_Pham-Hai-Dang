# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng  bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> *Ở 0.0 thì gần như lần nào cũng ra câu trả lời giống hệt nhau. Tăng dần lên 1.0 thì câu chữ bắt đầu đổi khác, đôi khi đổi luôn cả sự thật được kể. Ở 1.5 thì hơi lộn xộn, có lúc trả lời không còn logic lắm.*

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> *Với chatbot hỗ trợ khách hàng, nên đặt temperature thấp (khoảng 0.0–0.3). Vì loại ứng dụng này ưu tiên tính nhất quán, chính xác, tránh bịa thông tin (hallucination) — khách hàng cần câu trả lời đáng tin cậy hơn là sáng tạo.*

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> *GPT-4o đắt hơn mini khoảng 16-17 lần (0.010 vs 0.0006 USD/1K token output). Với 10k user x 3 lần x 350 token thì GPT-4o tốn ~100 USD/ngày, mini chỉ ~6 USD/ngày — chênh khá nhiều. Dùng GPT-4o khi cần suy luận sâu (vd debug code, tư vấn phức tạp), còn mấy việc đơn giản như trả lời FAQ thì dùng mini cho rẻ.*

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> *Với persona "giáo viên tiểu học", phản hồi dùng từ ngữ đơn giản, câu ngắn, ví dụ gần gũi (so sánh blockchain với "cuốn sổ ghi chép mà ai cũng có thể xem"). Với persona "chuyên gia tài chính", phản hồi dài hơn, dùng thuật ngữ kỹ thuật (sổ cái phân tán, cơ chế đồng thuận, mã hóa), giả định người đọc đã có nền tảng. System prompt định hình rõ rệt độ phức tạp từ vựng, độ dài, và cách tiếp cận nội dung — dù câu hỏi gốc giống hệt nhau.*

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> *(Cần bạn tự chạy count_tokens trên đoạn văn cụ thể để có số chính xác) — Thông thường, số token thật từ tiktoken cao hơn ước lượng số từ/0.75 khoảng 30–70% với tiếng Việt. Nguyên nhân: tiktoken được huấn luyện chủ yếu trên dữ liệu tiếng Anh, nên các ký tự có dấu và từ ghép tiếng Việt thường bị tách thành nhiều token nhỏ (subword) thay vì gọn trong 1 token như từ tiếng Anh thông dụng.*

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> *Streaming quan trọng nhất trong các ứng dụng chat tương tác trực tiếp, nơi người dùng cảm nhận độ trễ (latency) rõ rệt — thấy chữ xuất hiện dần giúp giảm cảm giác chờ đợi dù tổng thời gian xử lý không đổi. Non-streaming phù hợp hơn khi hệ thống cần xử lý toàn bộ response trước khi dùng (ví dụ parse JSON có cấu trúc, gọi tiếp một hàm khác dựa trên kết quả), hoặc khi gọi API từ backend không có giao diện hiển thị trực tiếp cho người dùng.*

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> *Nếu hàng nghìn client cùng dùng delay cố định (ví dụ luôn chờ 1 giây), tất cả sẽ đồng loạt gửi lại request cùng một thời điểm sau khi lỗi — tạo ra "cơn sóng" retry đồng bộ khiến server tiếp tục quá tải, thậm chí tệ hơn ban đầu. Exponential backoff giãn dần thời gian chờ giữa các client (kết hợp với jitter — độ trễ ngẫu nhiên nhỏ — thường dùng thực tế), giúp các request retry dàn trải theo thời gian thay vì dồn cùng lúc, cho server cơ hội hồi phục.*

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> *Persona: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt.","trả lời ngắn gọn": giảm số token output → giảm chi phí và độ trễ, đồng thời giữ hội thoại dễ theo dõi trên giao diện CLI.,"bằng tiếng Việt": đảm bảo phù hợp với đối tượng người dùng mục tiêu (sinh viên Việt Nam), tránh model tự chuyển sang tiếng Anh khi gặp thuật ngữ kỹ thuật*

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> *Hạn chế lớn nhất: history chỉ giữ 3 lượt gần nhất, không có bộ nhớ dài hạn — nếu người dùng quay lại hỏi tiếp về điều đã nói ở đầu phiên (quá 3 lượt), trợ lý sẽ "quên" hoàn toàn. Cải thiện đề xuất: khi history vượt quá giới hạn, thay vì xóa bỏ hoàn toàn các lượt cũ, tóm tắt chúng thành 1 message ngắn (dùng chính API để tóm tắt) rồi chèn vào đầu messages dưới dạng system/context message — giữ được thông tin quan trọng mà không tốn quá nhiều token.*

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
