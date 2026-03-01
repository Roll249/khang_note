Chào bạn, tiếp nối kế hoạch cho Đội Web (Node.js), đây là lộ trình chi tiết dành cho **Đội Data**.

Trong dự án Seller Agent này, Đội Data không chỉ làm báo cáo (Analytics) mà đóng vai trò là **Kỹ sư Dữ liệu (Data Engineers)**. Nhiệm vụ cốt lõi của họ là xây dựng và tối ưu hóa "bộ não" lưu trữ thông tin cho AI truy xuất.

Dựa trên tài liệu kỹ thuật "Note.pdf" và các nguồn sách tham khảo, đây là lộ trình 8 tuần từ cơ bản đến nâng cao:

---

### KẾ HOẠCH ĐÀO TẠO CHI TIẾT: ĐỘI DATA (DATABASE & VECTOR STORE)

**Mục tiêu:** Xây dựng hệ thống lưu trữ kép: (1) Database sản phẩm (PostgreSQL) cho tra cứu thông thường và (2) Vector Database (pgvector) cho AI tìm kiếm ngữ nghĩa.

#### Giai đoạn 1: Thiết kế & Xây dựng Database Sản phẩm (Tuần 1-2)

_Mục tiêu: Xây dựng kho chứa dữ liệu có cấu trúc (Structured Data) vững chắc._

**1. Chi tiết bài học:**

- **SQL Căn bản & Nâng cao:** Đội Data cần nắm vững `CREATE`, `INSERT`, `SELECT`, và đặc biệt là `JOIN` để kết nối dữ liệu sản phẩm với dữ liệu bán hàng.
- **Data Modeling (Thiết kế Schema):** Học cách chuyển đổi yêu cầu kinh doanh thành bảng dữ liệu. Trong dự án này, cần thiết kế 3 bảng chính theo tài liệu:
    - `products`: Chứa thông tin tĩnh (tên, giá, mô tả).
    - `product_analytics`: Chứa thông tin động (điểm trending, tăng trưởng doanh số).
    - `query_patterns`: Lưu lịch sử câu hỏi của người dùng để phân tích sau này.

**2. Bài tập thực hành (Hands-on):**

- **Bài tập 1 (Cài đặt):** Cài đặt PostgreSQL (phiên bản 15+) và pgAdmin hoặc DBeaver để quản lý.
- **Bài tập 2 (Tạo Schema):** Copy và chạy đoạn code SQL tạo bảng `products` trong tài liệu "Note.pdf" tại mục ****. Yêu cầu hiểu rõ ý nghĩa của từng trường dữ liệu như `trending_score` hay `commission_rate`.
- **Bài tập 3 (Seed Data):** Viết script SQL hoặc dùng Python để nạp thử 50 dòng dữ liệu giả lập (dummy data) vào bảng vừa tạo để kiểm tra.

#### Giai đoạn 2: Tối ưu hóa Truy vấn & Hiệu suất (Tuần 3-4)

_Mục tiêu: Đảm bảo khi người dùng hỏi, hệ thống trả về kết quả dưới 2 giây, ngay cả khi có hàng triệu sản phẩm._

**1. Chi tiết bài học:**

- **Indexing (Đánh chỉ mục):** Học cách tạo Index để tăng tốc độ tìm kiếm. Tài liệu nhấn mạnh việc tạo Index cho các trường thường xuyên được lọc như `category_id`, `price`, và `platform`.
- **Chiến lược Indexing:** Hiểu khi nào dùng B-tree (cho tìm kiếm khoảng giá) và khi nào dùng GIN index (cho tìm kiếm full-text hoặc array).
- **Materialized Views:** Học cách tạo các bảng "ảo" lưu trữ kết quả tính toán sẵn (ví dụ: bảng xếp hạng top 10 sản phẩm bán chạy mỗi ngày) để không phải tính lại mỗi khi có người hỏi.

**2. Bài tập thực hành (Hands-on):**

- **Bài tập 1 (Indexing):** Chạy lệnh `EXPLAIN ANALYZE` trên một câu query tìm kiếm sản phẩm. Sau đó, thêm Index vào cột `price` và chạy lại lệnh trên để so sánh tốc độ cải thiện.
- **Bài tập 2 (Trending Query):** Viết hàm SQL phức tạp `get_trending_products` để lọc ra sản phẩm đang "hot" dựa trên công thức: _Điểm Trending = (Doanh số x 0.5) + (Rating x 0.3) + (Tương tác x 0.2)_. Tham khảo logic tại.

#### Giai đoạn 3: Vector Database cho RAG (Tuần 5-6)

_Mục tiêu: Biến Database thường thành "Vector Database" để AI có thể hiểu được ngữ nghĩa (Ví dụ: Tìm "áo cho mùa hè" sẽ ra kết quả dù trong tên sản phẩm không có chữ "mùa hè")._

**1. Chi tiết bài học:**

- **Khái niệm Vector & Embeddings:** Hiểu cách biến văn bản (text) thành dãy số (vector) để máy tính so sánh độ tương đồng.
- **Cài đặt pgvector:** Cài đặt extension `pgvector` cho PostgreSQL. Đây là công nghệ cốt lõi để biến PostgreSQL thành Vector DB.
- **Hybrid Search:** Học cách kết hợp tìm kiếm từ khóa (Keyword Search - chính xác) và tìm kiếm ngữ nghĩa (Semantic Search - thấu hiểu) để AI trả lời chính xác nhất.

**2. Bài tập thực hành (Hands-on):**

- **Bài tập 1 (Setup pgvector):** Chạy lệnh `CREATE EXTENSION vector;` trong PostgreSQL.
- **Bài tập 2 (Vector Table):** Tạo bảng `business_documents` với cột `embedding` kiểu `VECTOR(1536)` (tương thích với OpenAI) theo hướng dẫn tại.
- **Bài tập 3 (Semantic Search Query):** Viết câu query SQL sử dụng toán tử `<=>` (cosine distance) để tìm các đoạn văn bản gần giống nhất với câu hỏi của người dùng.

#### Giai đoạn 4: Quản trị Dữ liệu & Đạo đức AI (Tuần 7-8)

_Mục tiêu: Đảm bảo dữ liệu an toàn, tuân thủ pháp luật và tự động hóa quy trình cập nhật._

**1. Chi tiết bài học:**

- **ETL Pipelines:** Xây dựng quy trình tự động: Lấy dữ liệu -> Làm sạch -> Nạp vào DB. Ví dụ: Tự động cập nhật giá và tồn kho mỗi đêm.
- **Data Privacy (Bảo mật):** Theo tài liệu "AI Ethics & Governance 2025", Đội Data phải học cách ẩn danh hóa dữ liệu cá nhân (Anonymization) của khách hàng trước khi đưa vào AI để tránh lộ thông tin nhạy cảm.
- **Chống thiên kiến (Bias Audit):** Kiểm tra xem dữ liệu sản phẩm có bị thiên lệch không (ví dụ: chỉ gợi ý sản phẩm giá cao) để điều chỉnh thuật toán.

**2. Bài tập thực hành (Hands-on):**

- **Bài tập 1 (Data Security):** Viết script để mã hóa hoặc che đi số điện thoại/email trong bảng `user_query_logs` trước khi lưu trữ.
- **Bài tập 2 (Automation):** Viết một script Python đơn giản chạy định kỳ (cronjob) để quét folder tài liệu, nếu có file PDF mới thì tự động phân tích và nạp vào Vector DB.

---

### TÀI LIỆU THAM KHẢO CHO ĐỘI DATA (Từ nguồn cung cấp)

1. **Sách "SQL QuickStart Guide" (Walter Shields):** Dành cho tuần 1-2. Đọc để nắm vững cách quản lý và thao tác dữ liệu cơ bản.
2. **Sách "Practical Statistics for Data Scientists":** Dành cho tuần 3-4. Giúp hiểu về phân tích xu hướng và thống kê để viết các query `trending_score` chính xác.
3. **File "Note.pdf":** Đây là "kinh thánh" cho đội Data trong dự án này.
    - Trang ****: Schema chi tiết cần copy y nguyên.
    - Trang ****: Các chiến lược tối ưu Index và Full-text search.
    - Trang ****: Hướng dẫn chi tiết từng bước code cho `pgvector`.
4. **Tài liệu "AI Ethics & Governance 2025":** Đọc phần _Data Privacy and Security_ để đảm bảo hệ thống không vi phạm luật bảo vệ dữ liệu.

**Lời khuyên:** Đội Data nên làm việc chặt chẽ với Đội Web (Node.js). Đội Web dùng **Prisma**, nên Đội Data sau khi thiết kế xong Database (Tuần 2) cần cung cấp cấu trúc bảng (Schema) để Đội Web tạo file `schema.prisma` tương ứng. Điều này giúp hai đội đồng bộ ngay từ đầu.

Chào bạn, đây là **Lộ trình học tập và thực hành cực kỳ chi tiết (8 tuần)** dành riêng cho **Đội Web (Stack: Node.js/React)**.

Dựa trên yêu cầu của bạn về việc sử dụng **Node.js** làm nền tảng backend để dễ mở rộng (Next.js, NestJS, Docker) và kiến trúc hệ thống được mô tả trong tài liệu kỹ thuật,, tôi đã điều chỉnh lại lộ trình trong "Note.pdf" để thay thế phần Python/FastAPI bằng hệ sinh thái JavaScript.

---

### TỔNG QUAN NHIỆM VỤ ĐỘI WEB

Trong hệ thống Seller Agent, Đội Web chịu trách nhiệm 3 phần việc cốt lõi:

1. **API Server (Backend):** Xử lý logic, kết nối Database, và gọi sang AI Engine.
2. **User Interface (Frontend):** Xây dựng giao diện Chat cho người dùng cuối.
3. **Integration Layer:** Tích hợp các kênh chat (Telegram/Web) và đảm bảo tính minh bạch theo tiêu chuẩn đạo đức AI.

---

### CHI TIẾT LỘ TRÌNH 8 TUẦN: TỪ SỐ 0 ĐẾN DEPLOYMENT

#### GIAI ĐOẠN 1: JAVASCRIPT CORE & BACKEND BASICS (TUẦN 1-2)

_Mục tiêu: Xây dựng được một API Server đơn giản xử lý dữ liệu JSON, chưa cần database._

**1. Kiến thức cần học (Lý thuyết):**

- **JavaScript ES6+ (Nền tảng):**
    - Biến (`let`, `const`) và Arrow Functions (`=>`).
    - Xử lý chuỗi (String manipulation) và JSON object (quan trọng để xử lý tin nhắn chat).
    - **Bất đồng bộ (Asynchronous) [Cực kỳ quan trọng]:** Hiểu sâu về `Promise` và `async/await`. Node.js là đơn luồng (single-threaded), nếu không hiểu cái này server sẽ bị treo khi gọi Database hoặc AI.
- **Node.js Runtime & NPM:** Cách cài đặt gói thư viện (`npm install`), file `package.json`.
- **Express.js Framework:**
    - Cách dựng server lắng nghe port (ví dụ: 3000).
    - Cách viết các phương thức HTTP: `GET` (lấy dữ liệu), `POST` (gửi tin nhắn chat).

**2. Bài tập thực hành (Hands-on):**

- **Bài 1 (Setup):** Cài Node.js (v18+), VS Code. Tạo project `seller-agent-backend`.
- **Bài 2 (Logic API):** Viết một API `POST /api/chat-mock`.
    - _Input:_ Nhận JSON `{ "message": "giá iphone 15" }`.
    - _Logic:_ Dùng `if/else` đơn giản để check từ khóa.
    - _Output:_ Trả về JSON `{ "reply": "iPhone 15 giá 20 triệu" }`.
- **Mục đích:** Làm quen với quy trình Request - Response mà không bị rối bởi Database.

---

#### GIAI ĐOẠN 2: DATABASE INTERACTION VỚI PRISMA ORM (TUẦN 3-4)

_Mục tiêu: Thay thế dữ liệu giả bằng dữ liệu thật từ Database do Đội Data thiết kế._

**1. Kiến thức cần học (Lý thuyết):**

- **Prisma ORM:** Tài liệu kỹ thuật đề xuất dùng **Prisma** cho Node.js. Đây là công cụ giúp viết code thao tác database mà không cần rành rẽ SQL thuần.
    - Học cách định nghĩa `schema.prisma` khớp với bảng `products` trong tài liệu.
    - Các lệnh: `npx prisma db pull` (lấy cấu trúc từ DB về), `prisma.product.findMany()`.
- **API Design RESTful:**
    - Thiết kế endpoint lấy danh sách sản phẩm trending: `GET /api/products/trending`.
    - Phân trang (Pagination) và Lọc (Filtering) theo category hoặc giá.

**2. Bài tập thực hành (Hands-on):**

- **Bài 1 (Kết nối DB):**
    - Yêu cầu Đội Data cung cấp chuỗi kết nối PostgreSQL (Connection String).
    - Cấu hình Prisma để kết nối.
    - Ánh xạ bảng `products` từ tài liệu vào code Node.js. Chú ý các trường quan trọng: `product_id`, `price`, `trending_score`, `stock`.
- **Bài 2 (Query API):**
    - Viết API tìm kiếm sản phẩm.
    - _Logic:_ Sử dụng Prisma để query những sản phẩm có `trending_score > 0.8` (theo logic tại).
    - _Kết quả:_ API trả về danh sách sản phẩm thực từ Database.

---

#### GIAI ĐOẠN 3: FRONTEND INTERFACE & AI INTEGRATION (TUẦN 5-6)

_Mục tiêu: Xây dựng giao diện Chat và kết nối với bộ não AI._

**1. Kiến thức cần học (Lý thuyết):**

- **Frontend cơ bản (React.js):**
    - Tạo giao diện chat giống Messenger/Zalo.
    - `State Management`: Quản lý danh sách tin nhắn (mảng `messages`).
    - `Fetching Data`: Dùng `axios` hoặc `fetch` để gọi API Backend mình đã viết ở Tuần 1-4.
- **Kết nối AI Service:**
    - Backend Node.js sẽ đóng vai trò là "Trung gian" (Proxy). Nó nhận tin nhắn từ React -> Gọi sang AI Service (thường là Python/RAG do Đội AI làm) -> Nhận câu trả lời -> Trả về React.
    - Hiểu về `Timeout` và xử lý lỗi khi AI trả lời chậm.
- **Đạo đức AI & UI:** Theo tài liệu "AI Ethics & Governance 2025", giao diện cần:
    - Có nhãn thông báo "Bạn đang chat với AI" (Transparency).
    - Cơ chế người dùng phản hồi (Feedback loop) nếu AI trả lời sai.

**2. Bài tập thực hành (Hands-on):**

- **Bài 1 (UI Chat):** Dựng trang web có 1 ô input và nút Gửi. Khi bấm Gửi, tin nhắn hiện lên khung chat.
- **Bài 2 (Đấu nối):**
    - Frontend gọi `POST /api/chat` lên Backend Node.js.
    - Backend Node.js nhận tin, query dữ liệu sản phẩm từ Database (bước đệm cho RAG), sau đó giả lập hoặc gọi thật sang AI Service.
    - Hiển thị câu trả lời lên màn hình.

---

#### GIAI ĐOẠN 4: DEPLOYMENT, DOCKER & MỞ RỘNG (TUẦN 7-8)

_Mục tiêu: Đóng gói ứng dụng để chạy ổn định trên server thật (Production)._

**1. Kiến thức cần học (Lý thuyết):**

- **Docker cơ bản:**
    - Viết `Dockerfile` cho ứng dụng Node.js.
    - Hiểu khái niệm Container: "Chạy được trên máy tôi thì cũng chạy được trên Server".
- **Multi-channel Integration (Telegram/Zalo):**
    - Thay vì chỉ chat trên web, học cách dùng thư viện `telegraf` (Node.js) để biến API hiện tại thành Telegram Bot.
- **Logging & Monitoring:** Ghi log xem ngày hôm nay có bao nhiêu người chat, lỗi ở đâu (quan trọng cho việc bảo trì).

**2. Bài tập thực hành (Hands-on):**

- **Bài 1 (Dockerize):** Tạo file `Dockerfile`, chạy lệnh `docker build` để đóng gói Backend Node.js thành một image.
- **Bài 2 (Telegram Bot):**
    - Đăng ký Bot Father trên Telegram lấy Token.
    - Viết code Node.js lắng nghe tin nhắn từ Telegram, tái sử dụng logic tìm sản phẩm ở Tuần 3 để trả lời người dùng ngay trên Telegram.

---

### TÓM TẮT CHECKLIST CHO ĐỘI WEB

1. **Tech Stack chốt:** Node.js (Runtime), Express/NestJS (Framework), Prisma (Database ORM), React (Frontend), Docker (Deployment).
2. **Tài liệu cần đọc kỹ:**
    - Phần **Database Schema** trong "Note.pdf" (Trang-): Để biết tên bảng và kiểu dữ liệu mà mapping vào Node.js.
    - Phần **API Integration** (Trang): Hiểu cách thiết kế API chuẩn.
    - Phần **Transparency** trong "AI Ethics" (Trang): Để thiết kế UI minh bạch.

Bạn hãy đưa lộ trình này cho Đội Web. Tuần đầu tiên, yêu cầu duy nhất là họ cài đặt môi trường và viết được một API "Hello World" bằng Node.js. Khi họ làm được, chúng ta sẽ sang bước kết nối Database.

