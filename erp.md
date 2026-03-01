Chắc chắn rồi! Dưới đây là **kiến thức nền tảng và thực tiễn** về cách **Frappe/ERPNext lưu trữ dữ liệu**, **di chuyển giữa các máy chủ**, và **quản lý tài khoản người dùng**, được trình bày rõ ràng theo từng phần để bạn dễ hiểu và áp dụng — đặc biệt phù hợp với bối cảnh bạn đang làm việc với hệ thống tích hợp, cần tính khả chuyển, bảo mật và tuân thủ.

---

## 🔹 1. CƠ CHẾ LƯU TRỮ DỮ LIỆU TRONG FRAPPE/ERPNEXT

### 📌 Kiến trúc tổng quan
Frappe là framework nền tảng; ERPNext là ứng dụng xây dựng trên đó. Hệ thống gồm 3 thành phần chính:

| Thành phần | Vai trò | Lưu ở đâu? |
|-----------|--------|------------|
| **Database** | Lưu toàn bộ dữ liệu nghiệp vụ: khách hàng, đơn hàng, kế toán, người dùng... | MariaDB (hoặc MySQL) |
| **File system** | Lưu file đính kèm, ảnh, backup, logs, cấu hình site | Thư mục `sites/` trong Frappe Bench |
| **Mã nguồn (code)** | Định nghĩa giao diện, logic nghiệp vụ, metadata | Trong các app (`frappe`, `erpnext`, custom app...) |

> 💡 Một **site** trong Frappe tương đương một "instance" độc lập (như một tenant). Bạn có thể có nhiều site trên cùng một Bench (ví dụ: `site1.local`, `production.example.com`).

---

### 🗃️ Chi tiết lưu trữ

#### a. **Dữ liệu trong cơ sở dữ liệu (MariaDB)**
- Mỗi **site** có **một database riêng** (tên thường trùng tên site).
- Bảng quan trọng:  
  - `tabUser`: thông tin tài khoản  
  - `tabCustomer`, `tabSales Invoice`, `tabGL Entry`...: dữ liệu nghiệp vụ  
  - `tabDocType`: metadata mô tả các loại tài liệu (document types)

#### b. **Dữ liệu trên file system (`sites/`)**
Cấu trúc thư mục điển hình:
```
sites/
├── assets/                     → file tĩnh (CSS, JS)
├── common_site_config.json     → cấu hình chung (kết nối DB, Redis...)
└── your-site-name/
    ├── private/
    │   ├── backups/            → bản sao lưu tự động/thủ công
    │   └── files/              → file đính kèm (bảo mật, chỉ xem khi đăng nhập)
    ├── public/
    │   └── files/              → file công khai (ảnh sản phẩm, logo...)
    ├── site_config.json        → cấu hình riêng của site (mật khẩu DB, secret key...)
    └── logs/                   → log lỗi, truy vết
```

> 🔐 `site_config.json` chứa thông tin nhạy cảm như mật khẩu DB → **không được commit lên Git**.

---

## 🔹 2. DI CHUYỂN DỮ LIỆU GIỮA CÁC MÁY CHỦ (MIGRATION)

Việc di chuyển **hoàn toàn khả thi** và được hỗ trợ tốt nhờ thiết kế tách biệt giữa **code** và **data**.

### ✅ Quy trình chuẩn (best practice)

#### Bước 1: Tạo bản sao lưu đầy đủ trên máy nguồn
```bash
# Trong container frappe (hoặc bench environment)
bench --site your-site backup
```
→ Sinh ra 2 file trong `sites/your-site/private/backups/`:
- `your-site-date-time.sql.gz` → dữ liệu CSDL
- `your-site-date-time.tar.gz` → file đính kèm (private + public)

> 💡 Bạn cũng có thể dùng `bench --site your-site backup --with-files` để chắc chắn.

#### Bước 2: Chuyển file backup sang máy đích
Dùng `scp`, `rsync`, hoặc bất kỳ công cụ truyền file nào.

#### Bước 3: Trên máy đích — khôi phục
Giả sử bạn đã cài đặt Frappe/ERPNext (cùng phiên bản!) và có MariaDB/Redis chạy:

```bash
# Giải nén file đính kèm vào đúng vị trí
tar -xzf your-site-*.tar.gz -C ~/frappe-bench/sites/

# Tạo site mới (nếu chưa có)
bench new-site your-site --db-root-password your_mariadb_root_pass

# Khôi phục CSDL
gunzip < your-site-*.sql.gz | mysql -u root -p your-site

# Cập nhật nếu có thay đổi schema (quan trọng!)
bench --site your-site migrate

# Rebuild frontend assets (nếu cần)
bench build
```

> ⚠️ Lưu ý:
> - **Phiên bản Frappe/ERPNext phải khớp** (hoặc ít nhất tương thích ngược).
> - **Secret key** trong `site_config.json` nên giữ nguyên để tránh lỗi xác thực.
> - Nếu dùng Docker, hãy mount thư mục `sites/` từ host để dễ quản lý backup/move.

---

### 🐳 Với môi trường Docker (`frappe_docker`)
- Thay vì làm trong container, bạn nên:
  1. Dùng **bind mounts** thay vì anonymous volumes:
     ```yaml
     # Trong compose file
     volumes:
       - ./mariadb-data:/var/lib/mysql
       - ./sites:/home/frappe/frappe-bench/sites
     ```
  2. Khi cần di chuyển → chỉ cần copy thư mục `./sites` và `./mariadb-data` sang server mới.
  3. Hoặc: dùng `mysqldump` để export DB thuần SQL (linh hoạt hơn khi đổi kiến trúc).

---

## 🔹 3. TẠO TÀI KHOẢN NGƯỜI DÙNG MỚI

### 🖥️ Cách 1: Qua giao diện web (GUI)
1. Đăng nhập với tài khoản `Administrator`.
2. Vào: **Settings > Users and Permissions > User**.
3. Click **New** → điền:
   - Email (là username)
   - First Name / Last Name
   - Roles (gán quyền: Sales User, Accountant, System Manager…)
4. Lưu → hệ thống sẽ gửi email đặt mật khẩu (nếu SMTP được cấu hình).
   - Nếu chưa có SMTP: vào **User > [user] > Reset Password** để tạo link đặt lại.

### 💻 Cách 2: Qua dòng lệnh (CLI) — hữu ích cho automation
```bash
# Trong container frappe hoặc bench env
bench --site your-site add-user \
  --email user@example.com \
  --first-name "Nguyen Van" \
  --last-name "A" \
  --role "Sales User"
```

> 🔐 Tài khoản đầu tiên (`Administrator`) luôn tồn tại sau khi tạo site. Mật khẩu được đặt lúc `bench new-site`.

---

## 🔹 GỢI Ý CHO DỰ ÁN CỦA BẠN (T. M. Tuấn)

Vì bạn đang xây dựng hệ thống có yêu cầu **compliance (PCI DSS, quy định Việt Nam)** và **tích hợp đa thành phần**, hãy:

1. **Tách biệt môi trường**: dev/staging/prod → mỗi môi trường là một site riêng.
2. **Backup tự động**: viết script gọi `bench backup` + lưu vào storage an toàn (có mã hóa).
3. **Quản lý version nghiêm ngặt**: lock version Frappe/ERPNext trong `requirements.txt` hoặc Docker image tag.
4. **Không lưu dữ liệu nhạy cảm trong logs** → kiểm tra `sites/*/logs/`.
5. **Dùng REST API để tích hợp** với hệ thống homestay của bạn:
   ```http
   POST /api/method/login
   POST /api/resource/Sales Invoice
   ```

---

Nếu bạn muốn, mình có thể:
- Cung cấp **script backup/restore tự động cho Docker**
- Hướng dẫn **tích hợp ERPNext với hệ thống thanh toán/homestay** qua API
- Giải thích cách **custom role/permission** để phù hợp nghiệp vụ

Bạn cần phần nào sâu hơn?