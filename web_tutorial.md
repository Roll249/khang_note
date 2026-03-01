Chào bạn, dựa trên yêu cầu của bạn về việc sử dụng **Node.js/React** cho đội Web và các tài liệu kỹ thuật đã cung cấp (đặc biệt là logic hệ thống trong "Note.pdf" và quy chuẩn đạo đức trong "AI Ethics & Governance 2025"), đây là **Tutorial (Hướng dẫn thực hành)** chi tiết dành riêng cho Đội Web Developer.

Mục tiêu: Xây dựng **Seller Agent Web Interface & API Gateway**.

---

### PHẦN 1: BACKEND - XÂY DỰNG API GATEWAY (Node.js/Express)

Đội Web sẽ không xây dựng mô hình AI (việc đó của đội AI), nhưng họ phải xây dựng "hệ thống thần kinh" để kết nối User, Database và AI.

#### Bước 1: Thiết lập Database Schema (Với Prisma ORM)

Dựa trên thiết kế Database trong tài liệu kỹ thuật, Đội Web cần ánh xạ bảng PostgreSQL vào dự án Node.js bằng Prisma.

**1. Cài đặt:**

```
npm install prisma --save-dev
npm install @prisma/client
npx prisma init
```

**2. Định nghĩa Schema (`schema.prisma`):** Mô phỏng lại cấu trúc bảng `products` và `product_analytics` từ nguồn:

```
// schema.prisma

model Product {
  product_id      String   @id @default(uuid())
  platform        String   // 'tiktok', 'shopee'
  title           String
  price           Decimal
  stock           Int
  sales_count     Int      @default(0)
  trending_score  Float    @default(0.0) // Quan trọng cho logic gợi ý

  // Quan hệ với bảng Analytics
  analytics       ProductAnalytics?
  @@map("products")
}

model ProductAnalytics {
  product_id      String   @id
  product         Product  @relation(fields: [product_id], references: [product_id])
  weekly_trend_score Float // Dùng để xếp hạng sản phẩm hot
  competition_level  Int   // Thang điểm 1-5

  @@map("product_analytics")
}
```

#### Bước 2: Xây dựng API Logic (Mô phỏng Business Logic)

Tài liệu yêu cầu xử lý các logic như tìm kiếm sản phẩm trending. Đội Web cần viết API để thực hiện việc này.

**File: `services/productService.js`** (Chuyển đổi logic SQL từ nguồn sang Javascript):

```
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

async function getTrendingProducts(category, limit = 10) {
  // Logic: Ưu tiên weekly_trend_score > 0.8 hoặc tăng trưởng doanh số nhanh
  return await prisma.product.findMany({
    where: {
      AND: [
        category ? { category_name: category } : {},
        { stock: { gt: 0 } } // Chỉ lấy hàng còn trong kho
      ]
    },
    orderBy: [
      { trending_score: 'desc' }, // Sắp xếp theo điểm trending
      { sales_count: 'desc' }
    ],
    take: limit,
    include: {
      analytics: true // Lấy kèm dữ liệu phân tích thị trường
    }
  });
}
```

#### Bước 3: Xây dựng Endpoint Chat (`/api/chat`)

Đây là nơi nhận tin nhắn từ Frontend và quyết định gọi Database hay gọi AI.

**File: `controllers/chatController.js`**

```
const axios = require('axios'); // Dùng để gọi sang AI Service (Python)

exports.handleChat = async (req, res) => {
    const { message, user_id } = req.body;

    try {
        // Giai đoạn 1: Gọi sang AI Engine để phân tích ý định (Intent)
        // Giả sử Đội AI đã expose endpoint tại port 8000
        const aiResponse = await axios.post('http://localhost:8000/agent/analyze', {
            message: message
        });

        const { intent, parameters } = aiResponse.data;

        let responseData;

        // Giai đoạn 2: Xử lý dựa trên Intent
        if (intent === 'product_recommendation') {
            // WebDev tự query DB lấy sản phẩm hot để phản hồi nhanh
            responseData = await getTrendingProducts(parameters.category);
        } else {
            // Các câu hỏi phức tạp (tư vấn chiến lược) để AI trả lời hoàn toàn
            const ragResponse = await axios.post('http://localhost:8000/agent/rag', {
                message: message
            });
            responseData = ragResponse.data;
        }

        res.json({
            type: intent,
            content: responseData,
            // Yêu cầu Đạo đức AI: Luôn kèm metadata để Frontend hiển thị nhãn AI
            metadata: { generated_by: "AI_Seller_Agent_v1" }
        });

    } catch (error) {
        res.status(500).json({ error: "Lỗi kết nối Seller Agent" });
    }
};
```

---

### PHẦN 2: FRONTEND - GIAO DIỆN CHAT (React.js)

Đội Web cần xây dựng giao diện đảm bảo tính **Minh bạch (Transparency)** theo quy chuẩn đạo đức AI 2025.

#### Bước 1: Thiết kế UI Chat Component

Dựa trên gợi ý UI/UX từ tài liệu BA và kiến trúc hệ thống.

**Yêu cầu tính năng:**

1. **Message Bubble:** Phân biệt tin nhắn người dùng (bên phải) và Bot (bên trái).
2. **Product Card:** Nếu API trả về danh sách sản phẩm, hiển thị dạng thẻ (ảnh, tên, giá, nút mua) thay vì text thuần.
3. **AI Labeling (Bắt buộc):** Hiển thị nhãn nhỏ "AI Generated" dưới tin nhắn của Bot để tuân thủ quy tắc minh bạch.

#### Bước 2: Xử lý hiển thị (React Code Snippet)

```
// components/ChatMessage.js

const ChatMessage = ({ message }) => {
  const isBot = message.sender === 'bot';

  return (
    <div className={`message-row ${isBot ? 'left' : 'right'}`}>
      <div className="bubble">
        {/* Hiển thị nội dung text */}
        <p>{message.text}</p>

        {/* Nếu có dữ liệu sản phẩm, hiển thị dạng Card */}
        {message.products && (
          <div className="product-carousel">
            {message.products.map(prod => (
              <div key={prod.product_id} className="product-card">
                <h4>{prod.title}</h4>
                <p>{prod.price} VND</p>
                {/* Hiển thị điểm trending từ DB */}
                <span className="badge">Trending: {prod.trending_score}</span>
              </div>
            ))}
          </div>
        )}

        {/* Yêu cầu Đạo đức: Nhãn minh bạch */}
        {isBot && <small className="ai-disclaimer">⚡ Câu trả lời được tạo bởi AI</small>}
      </div>
    </div>
  );
};
```

---

### PHẦN 3: TRIỂN KHAI & ĐẠO ĐỨC (Compliance)

Đây là phần hướng dẫn Đội Web tích hợp các yếu tố phi chức năng quan trọng từ tài liệu "AI Ethics & Governance 2025".

#### 1. Bảo vệ dữ liệu (Privacy & Security)

- **Data Anonymization:** Trước khi gửi tin nhắn của user sang AI Service (Python), Đội Web cần viết một hàm `middleware` trong Node.js để che giấu thông tin nhạy cảm (như số điện thoại, email) nếu người dùng lỡ nhập vào.
    
    ```
    function maskSensitiveData(text) {
        // Regex che số điện thoại
        return text.replace(/(\d{4})\d{3}(\d{3})/g, '$1***$2');
    }
    ```
    

#### 2. Cơ chế phản hồi (Feedback Loop)

- **Accountability:** Thêm nút "Like/Dislike" bên cạnh câu trả lời của Bot. Nếu người dùng Dislike, API sẽ lưu lại log này vào bảng `query_patterns` để Đội AI tinh chỉnh model sau này.

#### 3. Tích hợp Đa kênh (Multi-channel)

- Dựa trên hướng dẫn tích hợp Telegram Bot, Đội Web (Node.js) có thể sử dụng thư viện `telegraf` để móc nối vào API `/api/chat` đã viết ở trên, giúp Bot chạy được song song cả trên Web và Telegram.

---

### TÓM TẮT CHECKLIST CHO ĐỘI WEBDEV

1. **Database:** Chạy Prisma generate để ánh xạ bảng `products`.
2. **API:** Viết endpoint lấy sản phẩm Trending dựa trên logic `weekly_trend_score`.
3. **Frontend:** Code giao diện chat có hiển thị thẻ sản phẩm (Product Cards) và nhãn minh bạch (AI Label).
4. **Security:** Đảm bảo filter thông tin nhạy cảm trước khi gửi request sang server AI.

Hãy đưa hướng dẫn này cho Đội Web, họ có thể bắt đầu code ngay lập tức phần khung sườn (Skeleton) của ứng dụng.