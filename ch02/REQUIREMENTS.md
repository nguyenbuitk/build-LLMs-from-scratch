# Chapter 2 — Working with Text Data — Đề bài thực hành

File này liệt kê yêu cầu (đề bài) cho từng phần code demo trong chapter 2 của sách, để tự code lại thay vì chép code mẫu. Mỗi mục ghi rõ input/output kỳ vọng và số trang tham khảo để tự đối chiếu sau khi code xong — không có sẵn code giải.

## Tiến độ chapter 2

- [ ] 2.1 Understanding word embeddings (lý thuyết, không có code demo)
- [ ] 2.2 Tokenizing text
- [x] 2.3 Converting tokens into token IDs — **xong**
- [x] 2.4 Adding special context tokens — **xong**
- [x] 2.5 Byte pair encoding — **xong (Task 1, Task 2 bỏ qua theo lựa chọn của bạn)**
- [x] 2.6 Data sampling with a sliding window — **đang làm**
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

---

## 2.4 Adding special context tokens (trang 37–42)

> Lưu ý số liệu: bản MEAP đang có trong repo (trang 39–41) tự mâu thuẫn — có đoạn ghi vocab trước khi thêm token đặc biệt là "1159" và sau khi thêm là "1161", nhưng đoạn tính vocab_size ở mục 2.3 (trang 32) lại ra 1130. Mình đã tự chạy lại toàn bộ pipeline trên đúng file `the-verdict.txt` trong repo và xác nhận con số đúng, nhất quán là **1130** (mục 2.3) → **1132** (mục 2.4). Số liệu kỳ vọng bên dưới dùng theo kết quả đã verify này, không theo con số 1159/1161 in trong PDF (nhiều khả năng là lỗi còn sót lại từ bản nháp MEAP).

### Task 1 — Mở rộng vocabulary với token đặc biệt

Yêu cầu:
- Lấy lại tập token duy nhất đã sắp xếp từ mục 2.3 (`all_tokens`), nối thêm 2 token mới vào cuối theo đúng thứ tự: `"<|endoftext|>"` rồi `"<|unk|>"`.
- Tạo lại `dict` ánh xạ token → id từ danh sách đã mở rộng này (id đánh theo thứ tự trong list, bắt đầu từ 0 — 2 token mới sẽ có id lớn nhất vì được append vào cuối, không do sắp xếp alphabet).
- In ra kích thước vocab mới.
- In ra 5 phần tử cuối cùng của vocab để kiểm tra.

Kỳ vọng để tự kiểm tra:
- Kích thước vocab mới: **1132** (1130 + 2).
- 5 phần tử cuối: token thường có id lớn nhất trước đó, rồi `"<|endoftext|>"` → **1130**, `"<|unk|>"` → **1131**.

### Task 2 — Cài đặt class `SimpleTokenizerV2`

Dựa trên `SimpleTokenizerV1` đã có, sửa lại:
- `__init__`, `decode`: giữ nguyên logic như V1.
- `encode(self, text) -> list[int]`: vẫn tách token bằng regex như cũ, nhưng **trước khi** map sang id, thay token nào không có trong vocab bằng chuỗi `"<|unk|>"`. Sau đó map toàn bộ sang id như bình thường (không còn raise `KeyError` nữa).

Test case để tự kiểm tra (trang 40–41):
```python
text1 = "Hello, do you like tea?"
text2 = "In the sunlit terraces of the palace."
text = " <|endoftext|> ".join((text1, text2))
```
- Text nối kỳ vọng: `'Hello, do you like tea? <|endoftext|> In the sunlit terraces of the palace.'`
- `encode` xong `decode` lại: các từ không có trong vocab (`"Hello"`, `"palace"`) phải được thay bằng `<|unk|>` trong kết quả decode, ví dụ: `'<|unk|>, do you like tea? <|endoftext|> In the sunlit terraces of the <|unk|>.'` — không còn raise lỗi như Task 3 của mục 2.3 nữa.

### Tham khảo

- Sách: trang 37–42, Figure 2.9–2.10, Listing 2.4.
- Phụ thuộc: `all_tokens`/vocab từ mục 2.3 (Task 1).

---

## 2.5 Byte pair encoding (trang 42–44)

### Task 1 — Dùng tokenizer BPE của `tiktoken`

Yêu cầu:
- Lấy tokenizer BPE có sẵn: `tiktoken.get_encoding("gpt2")` (thư viện `tiktoken` đã cài từ lúc setup, không cần tự code thuật toán BPE).
- `encode` câu sau (chú ý: câu có chứa token `<|endoftext|>` — cần truyền tham số cho phép token đặc biệt này, xem gợi ý bên dưới nếu chưa biết cách):
  ```
  "Hello, do you like tea? <|endoftext|> In the sunlit terraces of someunknownPlace."
  ```
- `decode` lại danh sách id vừa encode, kiểm tra khôi phục đúng nguyên văn câu gốc.

Gợi ý (nếu chưa biết): `tiktoken` mặc định coi `<|endoftext|>` là ký tự thường chứ không phải token đặc biệt, phải khai báo rõ qua tham số `allowed_special` của `encode`.

Kỳ vọng để tự kiểm tra (trang 42–43):
- Token IDs: `[15496, 11, 466, 345, 588, 8887, 30, 220, 50256, 554, 262, 4252, 18250, 8812, 2114, 286, 617, 34680, 27271, 13]`
- `<|endoftext|>` được gán id **50256** — id lớn nhất trong vocab GPT-2 (tổng vocab size **50257**).
- Decode lại phải khôi phục đúng 100% câu gốc — khác với `SimpleTokenizerV2` ở mục 2.4, BPE **không cần** `<|unk|>` vì có thể tách bất kỳ từ lạ nào (`"someunknownPlace"`) thành các subword/ký tự đã biết.

### Task 2 — Exercise 2.1 của sách: BPE trên từ lạ hoàn toàn

- `encode` chuỗi `"Akwirw ier"` (không phải từ tiếng Anh thật, chắc chắn không có sẵn trong vocab GPT-2), in ra danh sách token id.
- Với từng token id riêng lẻ trong danh sách đó, gọi `decode` để xem nó tương ứng với subword/ký tự nào (giúp thấy rõ cách BPE tách từ lạ thành các mảnh nhỏ hơn — minh hoạ cho Figure 2.11).
- Cuối cùng, `decode` toàn bộ danh sách id cùng lúc, xác nhận khôi phục đúng lại `"Akwirw ier"`.

Kỳ vọng để tự kiểm tra (sách không cho sẵn số này vì đây là bài tập tự khám phá — mình đã tự chạy `tiktoken` để tính sẵn cho bạn đối chiếu):
- Token IDs: `[33901, 86, 343, 86, 220, 959]`.
- Decode từng id riêng lẻ: `33901` → `"Ak"`, `86` → `"w"`, `343` → `"ir"`, `86` → `"w"`, `220` → `" "`, `959` → `"ier"` (ghép lại đúng là `"Akwirw ier"`, chú ý token `86` ("w") xuất hiện lặp lại 2 lần ở 2 vị trí khác nhau).
- Decode cả danh sách cùng lúc: `"Akwirw ier"` (khớp chính xác input gốc).

### Tham khảo

- Sách: trang 42–44, Figure 2.11, Exercise 2.1.
- Thư viện: `tiktoken` (đã cài, version hiện tại 0.13.0 — sách viết dựa trên 0.5.1, không ảnh hưởng tới các API dùng ở đây).

---

## 2.6 Data sampling with a sliding window (trang 45–53)

### Task 1 — Sliding window cơ bản (x/y pairs)

Yêu cầu:
- Đọc `the-verdict.txt`, `encode` toàn bộ bằng tokenizer BPE (`tiktoken`) → `enc_text`. In độ dài.
- Bỏ 50 token đầu (`enc_sample = enc_text[50:]`) — chỉ để ví dụ có văn cảnh thú vị hơn.
- Với `context_size = 4`: lấy `x = enc_sample[:context_size]`, `y = enc_sample[1:context_size+1]`, in ra cả 2.
- Dùng vòng `for i in range(1, context_size+1)`: in ra từng cặp `(context, desired)` dạng `context ----> desired`, cả 2 dạng — token id và text đã decode.

Kỳ vọng để tự kiểm tra (trang 45–47):
- `len(enc_text)` = **5145**.
- `x = [290, 4920, 2241, 287]`, `y = [4920, 2241, 287, 257]`.
- Dạng text: `and ----> established`, `and established ----> himself`, `and established himself ----> in`, `and established himself in ----> a`.

### Task 2 — Cài đặt `GPTDatasetV1` + `create_dataloader_v1`

Yêu cầu:
- Class `GPTDatasetV1(Dataset)` (kế thừa `torch.utils.data.Dataset`):
  - `__init__(self, txt, tokenizer, max_length, stride)`: encode toàn bộ `txt`, rồi trượt cửa sổ (sliding window) độ dài `max_length`, bước nhảy `stride`, để tạo các cặp `(input_chunk, target_chunk)` (target lệch input đúng 1 vị trí) — lưu mỗi cặp dưới dạng `torch.tensor`.
  - `__len__`: số lượng cặp đã tạo.
  - `__getitem__(self, idx)`: trả về `(input_ids[idx], target_ids[idx])`.
- Hàm `create_dataloader_v1(txt, batch_size=4, max_length=256, stride=128, shuffle=True, drop_last=True, num_workers=0)`: khởi tạo tokenizer GPT-2, tạo `GPTDatasetV1`, bọc bằng `torch.utils.data.DataLoader` với đúng các tham số truyền vào, trả về dataloader.

### Task 3 — Test dataloader

- Test 1: `create_dataloader_v1(raw_text, batch_size=1, max_length=4, stride=1, shuffle=False)`. Lấy 2 batch liên tiếp bằng `iter()` + `next()`.
  - Kỳ vọng: batch 1 = `[tensor([[40, 367, 2885, 1464]]), tensor([[367, 2885, 1464, 1807]])]`; batch 2 = `[tensor([[367, 2885, 1464, 1807]]), tensor([[2885, 1464, 1807, 3619]])]` (batch 2 lệch batch 1 đúng 1 vị trí vì `stride=1`).
- Test 2: `create_dataloader_v1(raw_text, batch_size=8, max_length=4, stride=4, shuffle=False)`. Lấy 1 batch, in `inputs`/`targets`.

  ⚠️ **Quan trọng — khác với code trong sách:** trang 53 của sách gọi hàm **không** truyền `shuffle`, nghĩa là dùng default `shuffle=True` của `create_dataloader_v1`. Nhưng mình đã tự chạy thử và xác nhận: nếu để `shuffle=True` (default), kết quả mỗi lần chạy sẽ khác nhau (do bị xáo trộn) và **không khớp** với tensor sách in ra. Chỉ khi gọi với `shuffle=False` thì mới ra đúng kết quả cố định như bên dưới — đây là điểm sách bỏ sót, không phải bạn code sai nếu ban đầu ra kết quả khác. Cứ truyền `shuffle=False` cho test này.

  Kỳ vọng chính xác (đã tự chạy để verify, không phải chép nguyên PDF):
  ```
  Inputs:
   tensor([[   40,   367,  2885,  1464],
           [ 1807,  3619,   402,   271],
           [10899,  2138,   257,  7026],
           [15632,   438,  2016,   257],
           [  922,  5891,  1576,   438],
           [  568,   340,   373,   645],
           [ 1049,  5975,   284,   502],
           [  284,  3285,   326,    11]])

  Targets:
   tensor([[  367,  2885,  1464,  1807],
           [ 3619,   402,   271, 10899],
           [ 2138,   257,  7026, 15632],
           [  438,  2016,   257,   922],
           [ 5891,  1576,   438,   568],
           [  340,   373,   645,  1049],
           [ 5975,   284,   502,   284],
           [ 3285,   326,    11,   287]])
  ```
- (Tuỳ chọn, Exercise 2.2 của sách): thử lại với `max_length=2, stride=2` và `max_length=8, stride=2` để cảm nhận rõ hơn tác dụng của 2 tham số này.

### Tham khảo

- Sách: trang 45–53, Figure 2.12–2.14, Listing 2.5–2.6, Exercise 2.2.
- Phụ thuộc: `tiktoken` tokenizer (mục 2.5), `torch` (đã cài).
