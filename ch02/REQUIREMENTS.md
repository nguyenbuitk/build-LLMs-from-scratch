# Chapter 2 — Working with Text Data — Đề bài thực hành

File này liệt kê yêu cầu (đề bài) cho từng phần code demo trong chapter 2 của sách, để tự code lại thay vì chép code mẫu. Mỗi mục ghi rõ input/output kỳ vọng và số trang tham khảo để tự đối chiếu sau khi code xong — không có sẵn code giải.

## Tiến độ chapter 2

- [ ] 2.1 Understanding word embeddings (lý thuyết, không có code demo)
- [ ] 2.2 Tokenizing text
- [x] 2.3 Converting tokens into token IDs — **đang làm**
- [ ] 2.4 Adding special context tokens
- [ ] 2.5 Byte pair encoding
- [ ] 2.6 Data sampling with a sliding window
- [ ] 2.7 Creating token embeddings
- [ ] 2.8 Encoding word positions

---

## 2.3 Converting tokens into token IDs (trang 31–37)

### Chuẩn bị (phụ thuộc mục 2.2)

Task dưới đây cần một biến `preprocessed`: list các token dạng string, tách ra từ toàn bộ nội dung `ch02/the-verdict.txt` theo quy tắc của mục 2.2 (tách theo khoảng trắng và các dấu câu `,.:;?_!"()'` cùng dấu `--`, loại bỏ các phần tử rỗng/chỉ chứa khoảng trắng). Nếu chưa code phần 2.2, cứ tự viết nhanh đoạn này trước (dùng module `re` chuẩn của Python) — phần 2.2 đầy đủ sẽ bổ sung sau nếu cần.

### Task 1 — Xây dựng vocabulary

Yêu cầu:
- Lấy tập hợp các token duy nhất từ `preprocessed`, sắp xếp theo thứ tự alphabet.
- In ra kích thước vocabulary.
- Tạo một `dict` ánh xạ `token (str) -> id (int)`, id đánh theo đúng thứ tự alphabet đã sắp xếp ở trên, bắt đầu từ 0.

Kỳ vọng để tự kiểm tra (trang 32–33):
- Kích thước vocab: **1130**.
- In 51 phần tử đầu tiên của vocab, một vài mốc để đối chiếu: `"!"` → `0`, `'"'` → `1`, `"'"` → `2`, `"Her"` → `49`, `"Hermia"` → `50`.

### Task 2 — Cài đặt class `SimpleTokenizerV1`

Yêu cầu class với 2 method:
- `__init__(self, vocab)`: nhận vào dict `vocab` (token→id) tạo ở Task 1; tự tạo thêm chiều ngược lại (id→token) để dùng cho decode.
- `encode(self, text) -> list[int]`: tách `text` thành token bằng đúng quy tắc regex của mục 2.2, loại bỏ phần tử rỗng, rồi ánh xạ từng token sang id qua vocab.
- `decode(self, ids) -> str`: nối các token (tra ngược từ id) lại bằng dấu cách, sau đó loại bỏ khoảng trắng thừa đứng trước các dấu câu (`,.?!"()'`) để câu đọc tự nhiên hơn.

Test case để tự kiểm tra (trang 35–36) — encode rồi decode lại câu:
```
"It's the last he painted, you know," Mrs. Gisburn said with pardonable pride.
```
Kết quả decode kỳ vọng (sách note đây là hạn chế đã biết của tokenizer đơn giản này — dấu nháy đơn trong "It's" bị tách rời khi decode, không phải lỗi code của bạn):
```
" It' s the last he painted, you know," Mrs. Gisburn said with pardonable pride.
```

### Task 3 — Kiểm tra giới hạn của tokenizer

- Gọi `encode` trên một câu chứa từ không có trong vocab, ví dụ: `"Hello, do you like tea?"`
- Kỳ vọng: chương trình raise `KeyError` (vì `"Hello"` không nằm trong vocab xây từ *The Verdict*).
- Đây chính là lý do mục 2.4 sẽ giới thiệu token `<|unk|>` — tạm thời **không cần** xử lý lỗi này ở Task 3, chỉ cần xác nhận nó xảy ra.

### Tham khảo

- Sách: trang 31–37, Figure 2.6–2.8, Listing 2.2–2.3.
- Dữ liệu: `ch02/the-verdict.txt` (đã có sẵn trong repo).
