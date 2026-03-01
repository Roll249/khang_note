# Hướng dẫn triển khai deployweb lên server

  

**Mục tiêu:** Sau khi làm xong các bước dưới đây, bạn truy cập từ **máy tính khác** (điện thoại, laptop khác, v.v.) vào:

  

- **Backend (API):** http://101.96.66.222:8038/

- **Frontend (Giao diện web):** http://101.96.66.222:8039/

  

---

  

## Tổng quan các bước

  

1. **Sửa file** trong project cho đúng cấu hình (port 8038, 8039, lắng nghe mọi giao diện).

2. **Đẩy code lên GitHub** từ máy bạn.

3. **Trên server:** clone/pull code từ GitHub, cài đặt, chạy backend và frontend.

4. **Mở cổng firewall** (8038, 8039) trên server để máy khác truy cập được.

  

---

  

## Bước 1. Sửa file trong project cho đúng

  

Bạn cần chỉnh **2 file** để backend chạy cổng 8038, frontend cổng 8039 và lắng nghe trên mọi giao diện mạng (`0.0.0.0`).

  

### 1.1. Backend — `backend/main.py`

  

**Đoạn cần sửa:** Cuối file, phần `if __name__ == '__main__':`.

  

**Sửa từ:**

  

```python

# Run the app

if __name__ == '__main__':

app.run(debug=True)

```

  

**Thành:**

  

```python

# Run the app

if __name__ == '__main__':

import os

port = int(os.environ.get('PORT', 8038))

app.run(host='0.0.0.0', port=port, debug=os.environ.get('FLASK_DEBUG', 'false').lower() == 'true')

```

  

**Giải thích ngắn:** Backend chạy cổng **8038**, lắng nghe trên **0.0.0.0** (máy khác kết nối được). Có thể đổi cổng bằng biến môi trường `PORT`.

  

---

  

### 1.2. Frontend — `frontend/main.py`

  

**a) Thêm `import os`** (đầu file, cùng với các import khác):

  

```python

import os

import requests

import json

```

  

**b) Đổi URL backend** — tìm dòng:

  

```python

base_url = 'http://127.0.0.1:5000/users'

```

  

Sửa thành:

  

```python

_api_base = os.environ.get('API_BASE_URL', 'http://127.0.0.1:8038')

base_url = f'{_api_base.rstrip("/")}/users'

```

  

**c) Đổi cách chạy app** — tìm đoạn:

  

```python

# Run the app

if __name__ == '__main__':

app.run(debug=True, port=5001)

```

  

Sửa thành:

  

```python

# Run the app

if __name__ == '__main__':

port = int(os.environ.get('PORT', 8039))

app.run(host='0.0.0.0', port=port, debug=os.environ.get('FLASK_DEBUG', 'false').lower() == 'true')

```

  

**Giải thích ngắn:** Frontend chạy cổng **8039**, gọi backend tại `http://127.0.0.1:8038` (khi backend và frontend cùng chạy trên một server). Có thể đổi bằng `PORT` và `API_BASE_URL`.

  

---

  

## Bước 2. Đẩy code lên GitHub

  

Thực hiện trên **máy tính của bạn** (nơi đang có project).

  

### 2.1. Tạo repository trên GitHub (nếu chưa có)

  

1. Vào https://github.com → đăng nhập.

2. **New repository** → đặt tên (ví dụ: `deployweb`).

3. **Không** chọn “Add a README” nếu project đã có sẵn code.

4. Tạo xong, copy URL repo (ví dụ: `https://github.com/username/deployweb.git`).

  

### 2.2. Trong thư mục project — kiểm tra và push

  

Mở terminal trong thư mục gốc project (có thư mục `backend`, `frontend`):

  

```bash

# Xem trạng thái

git status

  

# Thêm mọi thay đổi (file đã sửa)

git add .

  

# Commit (ghi chú rõ đã sửa để deploy)

git commit -m "Cấu hình deploy: backend 8038, frontend 8039, bind 0.0.0.0"

  

# Nếu chưa có remote origin, thêm (thay URL bằng repo của bạn):

git remote add origin https://github.com/USERNAME/deployweb.git

  

# Đẩy lên nhánh main (hoặc master tùy repo)

git push -u origin main

```

  

Nếu repo dùng nhánh `master`:

  

```bash

git push -u origin master

```

  

Sau bước này, code đã sửa đã nằm trên GitHub.

  

---

  

## Bước 3. Trên server — pull code và chạy

  

Thực hiện trên **máy server** (IP 101.96.66.222). Cần có: SSH vào server, Python 3, `pip`.

  

### 3.1. Clone project (lần đầu) hoặc pull (đã clone rồi)

  

**Lần đầu trên server:**

  

```bash

cd ~

git clone https://github.com/USERNAME/deployweb.git

cd deployweb

```

  

*(Thay `USERNAME/deployweb` bằng đúng URL repo của bạn.)*

  

**Các lần sau (đã có thư mục `deployweb`):**

  

```bash

cd ~/deployweb

git pull origin main

```

  

*(Nếu dùng nhánh `master` thì `git pull origin master`.)*

  

### 3.2. Chạy Backend (cổng 8038)

  

Mở **terminal 1** trên server:

  

```bash

cd ~/deployweb/backend

pip install -r requirements.txt

python main.py

```

  

Giữ terminal này mở. Nếu thấy dòng kiểu `Running on http://0.0.0.0:8038/` là backend đã chạy.

  

### 3.3. Chạy Frontend (cổng 8039)

  

Mở **terminal 2** trên server (SSH session khác hoặc dùng tmux/screen):

  

```bash

cd ~/deployweb/frontend

pip install -r requirements.txt

export API_BASE_URL=http://127.0.0.1:8038

python main.py

```

  

Giữ terminal này mở. Thấy `Running on http://0.0.0.0:8039/` là frontend đã chạy.

  

**Lưu ý:** Luôn chạy **backend trước**, rồi mới chạy frontend.

  

### 3.4. Chạy nền (để tắt SSH vẫn chạy) — tùy chọn

  

Dùng `nohup`:

  

```bash

# Terminal 1 — backend

cd ~/deployweb/backend

nohup python main.py > backend.log 2>&1 &

  

# Terminal 2 — frontend

cd ~/deployweb/frontend

export API_BASE_URL=http://127.0.0.1:8038

nohup python main.py > frontend.log 2>&1 &

```

  

Xem log nếu cần: `tail -f ~/deployweb/backend/backend.log` và `tail -f ~/deployweb/frontend/frontend.log`.

  

---

  

## Bước 4. Truy cập từ máy tính khác

  

### 4.1. Mở cổng firewall trên server

  

Trên server (101.96.66.222) cần cho phép TCP **8038** và **8039**.

  

**Nếu dùng `ufw`:**

  

```bash

sudo ufw allow 8038/tcp

sudo ufw allow 8039/tcp

sudo ufw reload

sudo ufw status

```

  

**Nếu dùng `firewalld`:**

  

```bash

sudo firewall-cmd --permanent --add-port=8038/tcp

sudo firewall-cmd --permanent --add-port=8039/tcp

sudo firewall-cmd --reload

```

  

**Nếu server nằm sau router/NAT:** Cần forward port 8038 và 8039 vào IP nội bộ của server (tùy thiết bị, làm trong trang cấu hình router).

  

### 4.2. Kiểm tra từ máy khác

  

Trên **máy tính khác** (cùng mạng hoặc internet, tùy cách bạn expose server):

  

- Mở trình duyệt.

- **Backend:** http://101.96.66.222:8038/users — nếu trả về JSON (danh sách user) là backend đã ra ngoài.

- **Frontend:** http://101.96.66.222:8039/ — thấy trang User Management là thành công.

  

---

  

## Checklist nhanh

  

- [ ] Đã sửa `backend/main.py` (port 8038, `host='0.0.0.0'`).

- [ ] Đã sửa `frontend/main.py` (port 8039, `API_BASE_URL`/8038, `host='0.0.0.0'`).

- [ ] Đã `git add`, `commit`, `push` lên GitHub.

- [ ] Trên server: đã `git clone` hoặc `git pull`.

- [ ] Trên server: đã chạy backend (8038) rồi frontend (8039).

- [ ] Firewall đã mở 8038, 8039 (và router/NAT nếu cần).

- [ ] Từ máy khác truy cập được http://101.96.66.222:8038/ và http://101.96.66.222:8039/.

  

---

  

## Một số lỗi thường gặp

  

| Triệu chứng | Gợi ý xử lý |

|-------------|-------------|

| Máy khác không mở được http://101.96.66.222:8039 | Kiểm tra firewall (8038, 8039); kiểm tra backend/frontend đang chạy trên server (`curl http://127.0.0.1:8038/users`, `curl http://127.0.0.1:8039/`). |

| Frontend báo lỗi khi load danh sách user | Backend phải chạy trước; kiểm tra `API_BASE_URL=http://127.0.0.1:8038` khi chạy frontend. |

| `ModuleNotFoundError` trên server | Chạy `pip install -r requirements.txt` trong đúng thư mục `backend` hoặc `frontend`. |

| `website.db` không tìm thấy | Chạy backend từ thư mục `backend`: `cd ~/deployweb/backend` rồi mới `python main.py`. |

  

Khi đã làm đủ các bước trên, bạn sẽ truy cập được **http://101.96.66.222:8038/** (backend) và **http://101.96.66.222:8039/** (frontend) từ máy tính khác.