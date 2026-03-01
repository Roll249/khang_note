Chào bạn, tiếp nối lộ trình của Đội Web (Node.js), đây là kế hoạch chi tiết dành cho **Đội Data**.

Trong mô hình dự án Seller Agent này, Đội Data đóng vai trò là "Kiến trúc sư thông tin". Họ không chỉ làm báo cáo mà phải xây dựng hạ tầng lưu trữ để **Đội Web** truy xuất nhanh (dưới 2 giây) và **Đội AI** có thể "hiểu" được dữ liệu (Vector Search).

Dưới đây là **Lộ trình học tập và thực hành thực chiến 8 tuần** cho Đội Data, đi từ con số 0 đến khi làm chủ PostgreSQL và Vector Database.

---

### TỔNG QUAN NHIỆM VỤ ĐỘI DATA

1. **Structured Data (Dữ liệu có cấu trúc):** Quản lý thông tin sản phẩm, đơn hàng, tồn kho (PostgreSQL).
2. **Unstructured Data (Dữ liệu phi cấu trúc):** Quản lý tài liệu kinh doanh, hướng dẫn bán hàng dưới dạng Vector (pgvector) để phục vụ AI.
3. **Data Quality & Governance:** Đảm bảo dữ liệu sạch, bảo mật và không thiên kiến.

---

### CHI TIẾT LỘ TRÌNH 8 TUẦN: TỪ SQL CƠ BẢN ĐẾN VECTOR ARCHITECT

#### GIAI ĐOẠN 1: TƯ DUY DỮ LIỆU & SQL FOUNDATION (TUẦN 1-2)

_Mục tiêu: Xây dựng được kho chứa dữ liệu (Database) theo đúng thiết kế kỹ thuật, sẵn sàng để Đội Web kết nối._

**1. Kiến thức cần học (Lý thuyết):**

- **Cài đặt & Công cụ:** Cài đặt **PostgreSQL** (phiên bản 15+) và công cụ quản lý như **pgAdmin** hoặc **DBeaver**.
- **SQL Căn bản:** Các lệnh `CREATE`, `INSERT`, `SELECT`, `UPDATE`, `DELETE`.
- **Data Modeling (Quan trọng):** Hiểu các kiểu dữ liệu (`VARCHAR`, `DECIMAL`, `TIMESTAMP`) và Khóa chính/Khóa ngoại (Primary/Foreign Key).
- **Tư duy Schema:** Học cách đọc và triển khai Schema từ tài liệu thiết kế.

**2. Bài tập thực hành (Hands-on):**

- **Bài 1 (Khởi tạo):** Cài đặt PostgreSQL trên máy cá nhân. Tạo database tên là `seller_agent_db`.
- **Bài 2 (Triển khai Table):** Mở tài liệu "Note.pdf", copy và chạy câu lệnh SQL để tạo bảng `products`.
    - _Yêu cầu hiểu:_ Tại sao `price` dùng `DECIMAL` mà không dùng `FLOAT`? (Để tránh sai số tiền tệ). Tại sao `platform` lại cần thiết?.
- **Bài 3 (Seed Data):** Viết script SQL chèn thử 50 dòng dữ liệu giả lập (Dummy data) bao gồm các trường: tên, giá, mô tả, tồn kho.
- **Kết quả đầu ra:** Một file `.sql` chứa cấu trúc bảng để gửi cho Đội Web (để họ cấu hình Prisma).

#### GIAI ĐOẠN 2: ADVANCED QUERY & OPTIMIZATION (TUẦN 3-4)

_Mục tiêu: Viết được các câu truy vấn phức tạp để tìm ra "Sản phẩm Trending" và tối ưu tốc độ._

**1. Kiến thức cần học (Lý thuyết):**

- **Advanced SQL:** `JOIN` (kết nối bảng), `GROUP BY`, và `Window Functions` (ví dụ: xếp hạng sản phẩm theo doanh số trong từng danh mục).
- **Indexing (Tối ưu tốc độ):** Học về B-tree Index. Tại sao cần đánh index cho cột `category_id` và `price`?.
- **Logic tính điểm Trending:** Hiểu công thức tính toán kinh doanh được mô tả trong tài liệu: _Trending Score = (Doanh số x 0.5) + (Rating x 0.3) + (Tương tác x 0.2)_.

**2. Bài tập thực hành (Hands-on):**

- **Bài 1 (Query Phức tạp):** Viết câu lệnh SQL `get_trending_products` để lọc ra top 10 sản phẩm bán chạy nhất trong 7 ngày qua.
- **Bài 2 (Indexing):**
    - Chạy lệnh `EXPLAIN ANALYZE` trên câu query tìm kiếm sản phẩm theo giá.
    - Tạo Index: `CREATE INDEX idx_products_price ON products(price);`.
    - Chạy lại `EXPLAIN ANALYZE` để chứng minh tốc độ truy vấn nhanh hơn.
- **Bài 3 (Materialized Views):** Tạo một bảng ảo `product_analytics_mv` để lưu sẵn các báo cáo doanh thu, giúp Đội Web không phải tính toán lại mỗi khi user load trang.

#### GIAI ĐOẠN 3: VECTOR DATABASE & AI PREPARATION (TUẦN 5-6)

_Mục tiêu: Biến Database thường thành "Bộ não" cho AI (RAG System)._

**1. Kiến thức cần học (Lý thuyết):**

- **Khái niệm Vector/Embeddings:** Hiểu đơn giản là cách biến văn bản (text) thành dãy số để máy tính hiểu được ngữ nghĩa (Ví dụ: Máy hiểu "áo ấm" gần nghĩa với "mùa đông").
- **Extension pgvector:** Học cách cài đặt và sử dụng `pgvector` trong PostgreSQL.
- **Unstructured Data:** Cách lưu trữ tài liệu PDF/Word vào database dưới dạng vector.

**2. Bài tập thực hành (Hands-on):**

- **Bài 1 (Setup AI DB):** Chạy lệnh `CREATE EXTENSION vector;` trong PostgreSQL.
- **Bài 2 (Tạo bảng chứa tri thức):** Tạo bảng `business_documents` với cột `embedding` kiểu `VECTOR(1536)` (chuẩn của OpenAI).
- **Bài 3 (Semantic Search):** Viết câu truy vấn SQL sử dụng toán tử `<=>` (Cosine Distance) để tìm các đoạn văn bản có nội dung gần giống nhất với câu hỏi: "Làm sao để tăng doanh số?".
    - _Lưu ý:_ Giai đoạn này Đội Data làm việc chặt chẽ với Đội AI. Đội AI sẽ cung cấp code Python để tạo vector, Đội Data cung cấp nơi lưu trữ.

#### GIAI ĐOẠN 4: GOVERNANCE, SECURITY & AUTOMATION (TUẦN 7-8)

_Mục tiêu: Bảo vệ dữ liệu và tự động hóa quy trình vận hành._

**1. Kiến thức cần học (Lý thuyết):**

- **Data Privacy (Bảo mật):** Theo tài liệu "AI Ethics & Governance 2025", học cách ẩn danh hóa (Anonymization) dữ liệu khách hàng trước khi lưu hoặc gửi cho AI.
- **ETL Automation:** Tự động hóa việc cập nhật dữ liệu. Ví dụ: Cứ 12h đêm tự động cập nhật lại `trending_score` cho tất cả sản phẩm.
- **Backup & Recovery:** Cách sao lưu database phòng khi sự cố.

**2. Bài tập thực hành (Hands-on):**

- **Bài 1 (Bảo mật):** Tạo một "View" riêng cho Đội AI chỉ chứa dữ liệu sản phẩm, ẩn đi số điện thoại và email khách hàng.
- **Bài 2 (Automation):** Viết một script SQL hoặc Cronjob đơn giản để tự động xóa các dữ liệu log chat cũ quá 30 ngày để tiết kiệm dung lượng.
- **Bài 3 (Documentation):** Viết tài liệu mô tả ý nghĩa các cột trong Database (Data Dictionary) để giao lại cho Đội Web và Đội AI.

---

### TÀI LIỆU HỌC TẬP TRỌNG TÂM (Dành riêng Đội Data)

Yêu cầu Đội Data tập trung đọc các nguồn sau:

1. **Sách nhập môn:** "SQL QuickStart Guide" (Walter Shields) - Đọc chương về tạo bảng và truy vấn cơ bản.
2. **Tài liệu kỹ thuật dự án (Note.pdf):**
    - **Trang-:** Copy y nguyên cấu trúc `CREATE TABLE products`. Đây là "bài thánh" của đội Data.
    - **Trang-:** Logic SQL để tính toán sản phẩm Trending.
    - **Trang-:** Hướng dẫn cài đặt và tạo bảng cho `pgvector`.
3. **Tài liệu Đạo đức AI:** Đọc phần "Data Privacy and Security" để hiểu quy tắc ẩn danh dữ liệu.
4. **Công cụ:** Cài đặt **DBeaver** (Giao diện trực quan, dễ dùng hơn dòng lệnh).

---

### TÓM TẮT SỰ PHỐI HỢP

- **Tuần 2:** Đội Data phải bàn giao **Database Connection String** và **Schema** (cấu trúc bảng) cho Đội Web (Node.js).
- **Tuần 6:** Đội Data phải bàn giao bảng **`business_documents`** (đã cài pgvector) cho Đội AI để họ bắt đầu nạp dữ liệu thông minh.

Lộ trình này đảm bảo Đội Data đi trước một bước để chuẩn bị hạ tầng cho các đội khác. Bạn hãy yêu cầu họ bắt đầu ngay với việc cài đặt PostgreSQL và tạo bảng sản phẩm nhé.