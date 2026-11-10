# Kế Hoạch Triển Khai: Everyday English Pro — Giao Tiếp & Phản Xạ

## 1. Tổng Quan Sản Phẩm & Kiến Trúc
- **Tên ứng dụng**: **Everyday English Pro — Giao Tiếp & Phản Xạ** (Tác giả: Quốc Toản).
- **Kiến trúc Single-File Web App**: Giữ nguyên cấu trúc **1 file HTML độc lập duy nhất** (`index.html` sử dụng HTML5 + Tailwind CSS CDN + FontAwesome 6.4.0 + Mammoth.js + PDF.js + Vanilla JavaScript) chạy trực tiếp trên trình duyệt và GitHub Pages, xuất đầy đủ 100% mã nguồn không rút gọn.
- **Tông màu & Nhận diện thương hiệu**: Chuyển sang bảng màu chủ đạo **Indigo / Violet** hiện đại (`indigo-600`, `violet-600`, `indigo-50`, `violet-50`...) thay thế hoàn toàn tông màu Teal/Cyan cũ; biểu tượng thương hiệu đổi sang chủ đề giao tiếp & hội thoại (`fa-comments`).

## 2. Tách Biệt Tuyệt Đối Bộ Nhớ LocalStorage
Đổi toàn bộ các hằng số khóa lưu trữ sang tiền tố `comm_eng_pro_*` để không xung đột với ứng dụng *Dental English Pro*:
- `comm_eng_pro_specialties` (Danh sách chủ đề/chuyên mục học tập, mặc định: `"comm_eng"` — Tiếng Anh Giao Tiếp)
- `comm_eng_pro_current_specialty`
- `comm_eng_pro_theme`
- `comm_eng_pro_ai_key`
- `comm_eng_pro_sprint_best`
- `comm_eng_pro_app_settings`
- `comm_eng_pro_cbt_state`
- `comm_eng_pro_streak`
- `comm_eng_pro_cbt_history`
- `comm_eng_pro_synced_units_ver`
- `comm_eng_pro_seen_important_version`

## 3. Nâng Cấp Tính Năng & Giao Diện Học Tập

### A. Chuyển Đổi Nhãn Ngữ Cảnh Giao Tiếp
- Đổi toàn bộ nhãn **"Ghi chú lâm sàng"** thành **"Ngữ cảnh & Sắc thái giao tiếp"**.
- Đổi **"Điền khuyết câu lâm sàng"** thành **"Điền khuyết câu giao tiếp"**.
- Cập nhật Trợ lý AI 3 tầng (Chrome On-Device AI ➔ Gemini 2.5 Flash ➔ Từ điển Mẫu câu & Gốc từ Giao tiếp Nội tuyến) sang vai trò chuyên gia Tiếng Anh Giao Tiếp & Phản Xạ.

### B. Chế Độ 1 (Trắc Nghiệm) & Chế Độ 6 (Thi Thử CBT): Bổ Sung Chiều Câu Hỏi Thứ 4
- Thêm chiều câu hỏi thứ 4: **`dialogue` — "Phản xạ hội thoại (Tình huống A hỏi -> B đáp)"** vào cả menu thả xuống (`#quizDirectionSelect`, `#cbtExamDirection`) và thanh chọn gộp đa chiều (`#quizMultiDirectionBar`).
- **Cơ chế hiển thị**:
  - Khi rơi vào chiều `dialogue`, đề bài hiển thị **Ngữ cảnh tình huống + Câu người A nói bằng tiếng Anh** (kèm dịch nghĩa tình huống gợi ý và nút nghe câu của A).
  - Các phương án trả lời hiển thị **Câu phản xạ tiếng Anh tương ứng của người B** (`word` / mẫu câu mục tiêu), kết hợp thuật toán tạo đáp án nhiễu thông minh cùng ngữ cảnh/chủ đề.

### C. Chế Độ 3 (Chép Chính Tả - Dictation): Tích Hợp Nút Microphone Luyện Nói
- Tích hợp nút bấm **Microphone (`#btnDictMic`)** ngay cạnh ô nhập liệu `#dictInput` sử dụng **Web Speech Recognition API** (`window.SpeechRecognition || window.webkitSpeechRecognition`, `lang = 'en-US'`).
- **Luồng hoạt động**:
  - Khi nhấn nút Mic, biểu tượng chuyển sang trạng thái đang thu âm (hiệu ứng sóng âm đỏ/tím) và lắng nghe giọng nói của người học.
  - Sau khi nhận diện xong, văn bản tự động điền vào `#dictInput`.
  - Nếu câu/từ phát âm khớp với đáp án chuẩn (hoặc các biến thể hợp lệ trong `getAcceptedDictationTargets`), ứng dụng **tự động chấm điểm Đúng ngay lập tức**, phát âm thanh chiến thắng, cộng điểm SRS và chuyển sang câu tiếp theo; nếu chưa khớp, giữ văn bản trong ô nhập kèm thông báo để người học dễ dàng chỉnh sửa hoặc nói lại.

## 4. Cấu Trúc Bộ Dữ Liệu Gốc (8 Units trong `UNIT_METADATA` & `SEED_DATA`)
Xây dựng đầy đủ bộ từ vựng & mẫu câu giao tiếp thực tế (mỗi mục gồm: `id`, `unit`, `word`, `ipa`, `pos`, `meaning`, `relatedTerms`, `example`, `exampleVi`, `clinicalNote` *(Ngữ cảnh & Sắc thái giao tiếp)*, và `dialoguePrompt` / `dialoguePromptVi` *(Tình huống A nói để B phản xạ)*) chia thành 8 Unit:
1. **Unit 1**: **Siêu cơ bản (Super Basic)** — Từ vựng và cụm từ nền tảng cốt lõi hàng ngày.
2. **Unit 2**: **Giao tiếp Cơ bản (Essential Daily Expressions)** — Các cụm từ thông dụng trong sinh hoạt, mua sắm, di chuyển và công việc.
3. **Unit 3**: **Chào hỏi & Làm quen (Greetings & Icebreakers)** — Mẫu câu mở đầu tự nhiên từ thân mật đến trang trọng.
4. **Unit 4**: **Hỏi thăm & Bắt chuyện (Small Talk & Checking In)** — Cách hỏi thăm sức khỏe, công việc, cuộc sống và duy trì mạch trò chuyện.
5. **Unit 5**: **Rủ rê & Lên lịch hẹn (Invitations & Making Plans)** — Mẫu câu mời ăn uống, cà phê, đi chơi và chốt thời gian.
6. **Unit 6**: **Đồng ý & Hưởng ứng (Agreeing & Accepting)** — Các sắc thái đồng tình, nhận lời hào hứng và xác nhận ý kiến.
7. **Unit 7**: **Từ chối khéo léo (Polite Refusals & Rain Checks)** — Cách từ chối lịch sự, hẹn dịp khác mà không làm mất lòng người nghe.
8. **Unit 8**: **Phản xạ cảm xúc (Emotional Reflexes & Exclamations)** — Cụm từ biểu lộ ngạc nhiên, đồng cảm, khen ngợi, động viên và chia sẻ cảm xúc tức thì.
