## Tóm tắt video

**Tên video:** *Build an LLM from Scratch 1: Set up your code environment*
**Tác giả:** Sebastian Raschka
**Thời lượng:** khoảng 21 phút.

Video hướng dẫn thiết lập môi trường Python để chạy code từ **chương 2 đến chương 7** của sách *Build a Large Language Model (From Scratch)*. Đây chưa phải video xây dựng LLM, mà là bước chuẩn bị môi trường trước khi học tokenization, attention, GPT, pretraining và finetuning. ([Podwise][1])

### 1. Tại sao cần tạo môi trường riêng?

Mỗi project Python có thể yêu cầu phiên bản thư viện khác nhau. Vì vậy, nên dùng **virtual environment** để:

* Tách biệt thư viện của project LLM với hệ thống.
* Tránh xung đột phiên bản PyTorch, TensorFlow, NumPy…
* Giúp người khác cài lại đúng môi trường.
* Có thể xóa môi trường và tạo lại khi xảy ra lỗi.

Môi trường ảo thường nằm trong thư mục:

```text
.venv/
```

### 2. Sử dụng `uv` thay cho `pip` hoặc `conda`

Tác giả khuyến nghị sử dụng **uv** — công cụ quản lý Python, virtual environment và package.

So với `pip` hoặc `conda`, `uv` có ưu điểm:

* Cài thư viện nhanh hơn.
* Có thể tự tải phiên bản Python cần thiết.
* Tự tạo `.venv`.
* Quản lý dependency tương đối đơn giản.
* Có thể chạy lệnh trực tiếp trong môi trường bằng `uv run`.

Video chủ yếu sử dụng giao diện `uv pip`, tức là cách dùng gần giống `pip`. Tài liệu mới của repository cũng hỗ trợ cách dùng native như `uv sync` và `uv add`. ([Podwise][1])

### 3. Quy trình thiết lập chính

Quy trình tổng quát được trình bày trong video:

```text
Cài Python
    ↓
Cài uv
    ↓
Clone repository
    ↓
Tạo virtual environment
    ↓
Cài các package trong requirements.txt
    ↓
Chạy JupyterLab
    ↓
Thực hành code từng chương
```

Ví dụ theo tài liệu hiện tại của repository:

```bash
git clone --depth 1 https://github.com/rasbt/LLMs-from-scratch.git
cd LLMs-from-scratch
```

Cài `uv` trên Linux/macOS:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Sau đó tạo môi trường, cài Python 3.11 và dependency:

```bash
uv sync --dev --python 3.11
```

Kiểm tra môi trường:

```bash
uv run python setup/02_installing-python-libraries/python_environment_check.py
```

Khởi động JupyterLab:

```bash
uv run jupyter lab
```

`uv sync` sẽ tự tạo thư mục `.venv` và cài các package được khai báo trong project. ([GitHub][2])

### 4. Cách sử dụng `uv pip`

Cách gần với nội dung video hơn là tự tạo virtual environment:

```bash
uv venv --python=python3.11
```

Kích hoạt trên Linux/macOS:

```bash
source .venv/bin/activate
```

Cài thư viện:

```bash
uv pip install -r requirements.txt
```

Sau khi kích hoạt `.venv`, có thể chạy bình thường:

```bash
python script.py
jupyter lab
```

Điểm quan trọng là các package chỉ được cài trong `.venv`, không ảnh hưởng đến Python toàn hệ thống. ([GitHub][3])

### 5. Không nhất thiết phải có GPU mạnh

Code của các chương chính được thiết kế để có thể chạy trên laptop thông thường. Nếu máy có NVIDIA GPU, PyTorch có thể sử dụng GPU để tăng tốc.

GPU hữu ích hơn khi học:

* Chương 5: Pretraining.
* Chương 6: Finetuning cho classification.
* Chương 7: Instruction finetuning.

Các phần đầu như tokenization, data loader và attention cơ bản vẫn có thể học bằng CPU. ([GitHub][3])

### 6. Phương án khi máy local gặp lỗi

Tác giả đề xuất hai lựa chọn cloud:

* **Google Colab:** dễ sử dụng, nhưng mỗi phiên có thể phải cài lại package.
* **Lightning AI Studio:** môi trường được lưu lâu dài, hỗ trợ terminal, VS Code, JupyterLab và có thể gắn GPU.

Trên Google Colab có thể cài dependency bằng:

```python
!pip install uv
!uv pip install --system -r https://raw.githubusercontent.com/rasbt/LLMs-from-scratch/refs/heads/main/requirements.txt
```

Lightning Studio phù hợp hơn khi muốn giữ nguyên môi trường giữa nhiều lần học. ([Podwise][1])

### 7. Lưu ý với Windows và TensorFlow

Một số phiên bản Windows có thể gặp lỗi khi cài TensorFlow. TensorFlow chủ yếu được dùng ở phần tải trọng số GPT-2 gốc trong chương 5, nên người học có thể tạm thời bỏ TensorFlow khỏi `requirements.txt` và vẫn học các chương trước.

Tác giả cũng cung cấp phương án sử dụng trọng số GPT-2 đã chuyển sang định dạng PyTorch, tránh phụ thuộc vào TensorFlow. ([Sebastian Raschka's Magazine][4])

## Mốc thời gian của video

* **00:00:** Giới thiệu sách, chuỗi video và mục tiêu thiết lập môi trường.
* **04:52:** Cài Python, tạo môi trường và sử dụng `uv`.
* **15:24:** Xử lý lỗi cài đặt; giới thiệu Google Colab và giải pháp cloud. ([Podwise][1])

## Ý chính cần nhớ

Video này muốn bạn chuẩn bị một môi trường học LLM **tách biệt, dễ tái tạo và ít xung đột dependency**:

```text
Repository code
      │
      ▼
Python 3.11
      │
      ▼
uv
      │
      ▼
.venv
      │
      ├── PyTorch
      ├── JupyterLab
      ├── tiktoken
      └── Các dependency khác
```

Sau khi chạy được JupyterLab và script kiểm tra môi trường không báo lỗi, bạn đã sẵn sàng học **chương 2 — Working with Text Data**.

[1]: https://podwise.ai/dashboard/episodes/5987256 "Build an LLM from Scratch 1: Set up your code environment | Sebastian Raschka | Podwise"
[2]: https://github.com/rasbt/LLMs-from-scratch "GitHub - rasbt/LLMs-from-scratch: Implement a ChatGPT-like LLM in PyTorch from scratch, step by step · GitHub"
[3]: https://github.com/rasbt/LLMs-from-scratch/blob/main/setup/README.md "LLMs-from-scratch/setup/README.md at main · rasbt/LLMs-from-scratch · GitHub"
[4]: https://magazine.sebastianraschka.com/p/coding-llms-from-the-ground-up?utm_source=chatgpt.com "Coding LLMs from the Ground Up: A Complete Course"
