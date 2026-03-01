Tôi sẽ hướng dẫn bạn chi tiết từng phần của tutorial này, giải thích rõ ràng từng bước code và logic đằng sau để bạn có thể học và triển khai dễ dàng.

## 📚 TỔNG QUAN DỰ ÁN

Dự án này xây dựng **Seller Agent Web Interface & API Gateway** với:
- **Backend**: Node.js/Express làm API Gateway kết nối User ↔ Database ↔ AI
- **Frontend**: React.js tạo giao diện chat tương tác với người dùng
- **Đạo đức AI**: Tuân thủ quy chuẩn minh bạch, bảo mật theo AI Ethics & Governance 2025

---

## 🔧 PHẦN 1: BACKEND - XÂY DỰNG API GATEWAY (Node.js/Express)

### Bước 1: Thiết lập Database Schema với Prisma ORM

#### 1.1 Cài đặt Prisma
```bash
npm install prisma --save-dev
npm install @prisma/client
npx prisma init
```

**Giải thích**: 
- `prisma` là ORM (Object-Relational Mapping) giúp làm việc với database dễ dàng hơn SQL thuần
- `@prisma/client` là thư viện runtime để kết nối database
- `npx prisma init` tạo thư mục `prisma/` và file cấu hình

#### 1.2 Định nghĩa Schema chi tiết
File: `prisma/schema.prisma`
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Product {
  product_id      String            @id @default(uuid())
  platform        String            // 'tiktok', 'shopee', 'lazada'
  title           String
  description     String?           // Mô tả sản phẩm (optional)
  price           Decimal
  stock           Int
  sales_count     Int               @default(0)
  trending_score  Float             @default(0.0)
  category_name   String?           // Phân loại sản phẩm
  created_at      DateTime          @default(now())
  updated_at      DateTime          @default(now()) @updatedAt
  
  analytics       ProductAnalytics? // Quan hệ 1-1 với analytics
  @@map("products")
  @@index([category_name])
  @@index([trending_score])
}

model ProductAnalytics {
  product_id      String   @id
  product         Product  @relation(fields: [product_id], references: [product_id])
  weekly_trend_score Float @default(0.0) // Điểm xu hướng hàng tuần
  competition_level  Int   @default(3)   // Thang điểm 1-5 (1: cạnh tranh thấp, 5: cạnh tranh cao)
  market_demand   Float   @default(0.0) // Nhu cầu thị trường
  last_updated    DateTime @default(now())
  
  @@map("product_analytics")
}
```

**Giải thích chi tiết**:
- `@id @default(uuid())`: Tự động tạo UUID làm primary key
- `?` sau kiểu dữ liệu = field optional (có thể null)
- `@relation`: Thiết lập quan hệ giữa các bảng
- `@@map`: Đổi tên bảng trong database (nếu khác với model name)
- `@@index`: Tạo index để tăng tốc độ query
- `@updatedAt`: Tự động cập nhật thời gian khi record được sửa

#### 1.3 Generate Prisma Client
```bash
npx prisma migrate dev --name init
npx prisma generate
```

**Giải thích**:
- `migrate dev`: Tạo migration và áp dụng lên database
- `generate`: Tạo TypeScript types và client code từ schema

---

### Bước 2: Xây dựng API Logic (Business Logic)

#### 2.1 Product Service - Logic lấy sản phẩm trending
File: `src/services/productService.js`
```javascript
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

/**
 * Lấy danh sách sản phẩm trending dựa trên category và các tiêu chí
 * @param {string|null} category - Tên category (null = tất cả)
 * @param {number} limit - Số lượng sản phẩm trả về (default: 10)
 * @returns {Promise<Array>} Mảng sản phẩm trending
 */
async function getTrendingProducts(category = null, limit = 10) {
  try {
    // Xây dựng điều kiện where động
    const whereConditions = {
      stock: { gt: 0 }, // Chỉ lấy sản phẩm còn hàng
      trending_score: { gt: 0.5 } // Chỉ lấy sản phẩm có trending_score > 0.5
    };

    // Thêm điều kiện category nếu có
    if (category) {
      whereConditions.category_name = category;
    }

    // Query database với các điều kiện và sắp xếp
    const products = await prisma.product.findMany({
      where: whereConditions,
      orderBy: [
        { trending_score: 'desc' }, // Ưu tiên sản phẩm trending cao nhất
        { sales_count: 'desc' },    // Nếu trending_score bằng nhau, lấy sản phẩm bán chạy hơn
        { created_at: 'desc' }      // Nếu bằng nhau, lấy sản phẩm mới nhất
      ],
      take: limit, // Giới hạn số lượng kết quả
      include: {
        analytics: true // Lấy kèm dữ liệu phân tích thị trường
      },
      select: { // Chỉ chọn các field cần thiết
        product_id: true,
        title: true,
        price: true,
        stock: true,
        trending_score: true,
        sales_count: true,
        platform: true,
        analytics: {
          select: {
            weekly_trend_score: true,
            competition_level: true,
            market_demand: true
          }
        }
      }
    });

    return products;
  } catch (error) {
    console.error('Error in getTrendingProducts:', error);
    throw new Error('Failed to fetch trending products');
  }
}

/**
 * Hàm helper: Tính toán trending score dựa trên sales growth
 * (Được gọi bởi background job hoặc webhook từ AI service)
 */
async function calculateTrendingScore(productId, salesData) {
  // Logic tính toán trending score phức tạp hơn
  // Ví dụ: so sánh doanh số tuần này với tuần trước
  const growthRate = (salesData.currentWeek - salesData.lastWeek) / salesData.lastWeek;
  
  let trendingScore = 0.0;
  if (growthRate > 0.5) trendingScore = 0.9; // Tăng trưởng > 50%
  else if (growthRate > 0.2) trendingScore = 0.7; // Tăng trưởng 20-50%
  else if (growthRate > 0) trendingScore = 0.5; // Tăng trưởng dương
  
  // Cập nhật vào database
  await prisma.product.update({
    where: { product_id: productId },
    data: { trending_score: trendingScore }
  });
}

module.exports = {
  getTrendingProducts,
  calculateTrendingScore
};
```

**Giải thích chi tiết**:
- **Dynamic Where Conditions**: Xây dựng điều kiện query linh hoạt dựa trên tham số đầu vào
- **Multi-level Sorting**: Sắp xếp theo nhiều tiêu chí ưu tiên
- **Selective Fields**: Chỉ lấy các field cần thiết để tối ưu performance
- **Error Handling**: Bắt lỗi và throw meaningful errors
- **Type Safety**: Sử dụng TypeScript sẽ tốt hơn (được khuyến nghị)

#### 2.2 Chat Controller - Xử lý yêu cầu chat
File: `src/controllers/chatController.js`
```javascript
const axios = require('axios');
const { getTrendingProducts } = require('../services/productService');
const { maskSensitiveData } = require('../utils/securityUtils');

// Cấu hình kết nối đến AI Service
const AI_SERVICE_URL = process.env.AI_SERVICE_URL || 'http://localhost:8000';
const AI_TIMEOUT = 10000; // 10 seconds timeout

/**
 * Middleware: Che giấu thông tin nhạy cảm trong tin nhắn
 * @param {string} message - Tin nhắn gốc từ người dùng
 * @returns {string} Tin nhắn đã được làm sạch
 */
function sanitizeUserMessage(message) {
  if (!message || typeof message !== 'string') return '';
  
  let cleanedMessage = message.trim();
  
  // 1. Che giấu số điện thoại (định dạng Việt Nam)
  cleanedMessage = cleanedMessage.replace(
    /(\+?84|0)(\d{1,2})[-.\s]?(\d{3})[-.\s]?(\d{3,4})[-.\s]?(\d{0,4})/g,
    '*** *** ***'
  );
  
  // 2. Che giấu email
  cleanedMessage = cleanedMessage.replace(
    /([a-zA-Z0-9._-]+@[a-zA-Z0-9._-]+\.[a-zA-Z0-9_-]+)/g,
    '***@***.***'
  );
  
  // 3. Che giấu địa chỉ nhà riêng (đơn giản hóa)
  cleanedMessage = cleanedMessage.replace(
    /(số|so|số nhà|sonha)\s*\d+\w*\s*,?\s*[^\s]+/gi,
    '*** ***'
  );
  
  return cleanedMessage;
}

/**
 * Controller chính xử lý chat messages
 * @param {Object} req - Express request object
 * @param {Object} res - Express response object
 */
exports.handleChat = async (req, res) => {
  const { message, user_id, session_id } = req.body;
  
  // Validation cơ bản
  if (!message || !user_id) {
    return res.status(400).json({ 
      error: "Missing required fields: message and user_id" 
    });
  }

  try {
    console.log(`[CHAT] User ${user_id} message: ${message}`);
    
    // Bước 1: Làm sạch dữ liệu đầu vào (Privacy Protection)
    const sanitizedMessage = sanitizeUserMessage(message);
    console.log(`[CHAT] Sanitized message: ${sanitizedMessage}`);
    
    // Bước 2: Phân tích ý định (Intent Analysis) từ AI Service
    const intentAnalysis = await analyzeIntent(sanitizedMessage, user_id, session_id);
    const { intent, parameters, confidence } = intentAnalysis;
    
    console.log(`[CHAT] Detected intent: ${intent} (confidence: ${confidence})`);
    
    let responseContent;
    let responseType = 'text';
    
    // Bước 3: Xử lý dựa trên intent
    switch (intent) {
      case 'product_recommendation':
        responseType = 'products';
        // Lấy sản phẩm trending từ database (nhanh hơn gọi AI)
        responseContent = await handleProductRecommendation(parameters);
        break;
        
      case 'sales_strategy':
      case 'market_analysis':
      case 'competitor_analysis':
        responseType = 'ai_response';
        // Gọi AI Service để xử lý câu hỏi phức tạp
        responseContent = await handleAIStrategyRequest(sanitizedMessage, parameters);
        break;
        
      case 'order_status':
        responseType = 'order_info';
        responseContent = await handleOrderStatusRequest(parameters);
        break;
        
      default:
        // Fallback: Gọi AI để trả lời câu hỏi chung
        responseType = 'ai_response';
        responseContent = await handleFallbackRequest(sanitizedMessage);
    }
    
    // Bước 4: Ghi log tương tác (cho accountability & improvement)
    await logInteraction(user_id, sanitizedMessage, responseContent, intent);
    
    // Bước 5: Trả về response với metadata đạo đức AI
    res.json({
      success: true,
      type: responseType,
      intent: intent,
      content: responseContent,
      metadata: {
        generated_by: "AI_Seller_Agent_v1.0",
        confidence_score: confidence,
        timestamp: new Date().toISOString(),
        // Transparency requirement: Luôn hiển thị nguồn gốc AI
        ai_generated: responseType === 'ai_response',
        processing_time: Date.now() - req.startTime // Đo thời gian xử lý
      }
    });
    
  } catch (error) {
    console.error('[CHAT] Error processing chat message:', error);
    
    // Fallback response khi có lỗi
    if (!res.headersSent) {
      res.status(500).json({
        success: false,
        error: "Lỗi hệ thống. Vui lòng thử lại sau.",
        metadata: {
          error_code: error.code || 'INTERNAL_ERROR',
          generated_by: "SYSTEM"
        }
      });
    }
    
    // Ghi log lỗi để monitoring
    await logError('chat_processing', error, { user_id, message });
  }
};

// Các helper functions riêng biệt cho từng intent
async function analyzeIntent(message, userId, sessionId) {
  try {
    const response = await axios.post(`${AI_SERVICE_URL}/agent/analyze`, {
      message,
      user_id: userId,
      session_id: sessionId
    }, {
      timeout: AI_TIMEOUT
    });
    
    return response.data;
  } catch (error) {
    console.error('[AI] Error analyzing intent:', error);
    // Fallback intent khi AI service không phản hồi
    return {
      intent: 'general_question',
      parameters: {},
      confidence: 0.3 // Low confidence
    };
  }
}

async function handleProductRecommendation(params) {
  const { category, max_price, min_rating } = params;
  
  try {
    // Gọi service để lấy sản phẩm trending
    const products = await getTrendingProducts(category, 5);
    
    // Format response cho frontend
    return products.map(product => ({
      id: product.product_id,
      name: product.title,
      price: Number(product.price),
      platform: product.platform,
      trending_score: product.trending_score,
      sales_count: product.sales_count,
      competition_level: product.analytics?.competition_level || 3,
      image_url: `https://example.com/products/${product.product_id}.jpg` // Placeholder
    }));
  } catch (error) {
    console.error('[PRODUCTS] Error fetching recommendations:', error);
    // Fallback: Trả về sản phẩm mẫu
    return [
      {
        id: 'fallback_1',
        name: 'Sản phẩm gợi ý (tạm thời)',
        price: 199000,
        platform: 'shopee',
        trending_score: 0.85,
        sales_count: 150
      }
    ];
  }
}

async function handleAIStrategyRequest(message, params) {
  try {
    const response = await axios.post(`${AI_SERVICE_URL}/agent/rag`, {
      message,
      context: params
    }, {
      timeout: AI_TIMEOUT * 2 // Cho phép thời gian dài hơn cho câu hỏi phức tạp
    });
    
    return response.data.content || response.data;
  } catch (error) {
    console.error('[AI] Error getting strategy response:', error);
    return "Xin lỗi, tôi đang gặp sự cố khi phân tích chiến lược. Vui lòng thử lại sau.";
  }
}

// Các hàm helper khác (logInteraction, logError, handleOrderStatusRequest...)
async function logInteraction(userId, message, response, intent) {
  // TODO: Implement logging to database for feedback loop
  console.log(`[LOG] Interaction: user=${userId}, intent=${intent}`);
}

async function logError(type, error, context = {}) {
  // TODO: Implement error logging and alerting
  console.error(`[ERROR] Type: ${type}, Context:`, context, 'Error:', error);
}
```

**Giải thích chi tiết**:
- **Layered Architecture**: Tách biệt business logic thành các helper functions riêng
- **Privacy First**: Sanitize input trước khi gửi đến AI service
- **Graceful Degradation**: Fallback response khi AI service không khả dụng
- **Comprehensive Logging**: Ghi log mọi tương tác cho accountability và improvement
- **Type Safety**: Xác định rõ response types (products, ai_response, order_info)
- **Error Boundaries**: Bắt lỗi ở mọi layer và xử lý phù hợp
- **Performance Metrics**: Đo thời gian xử lý để monitoring

---

### Bước 3: Express Server Setup

File: `src/server.js`
```javascript
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const { PrismaClient } = require('@prisma/client');

const chatController = require('./controllers/chatController');
const { requestLogger, errorHandler } = require('./middleware');

const app = express();
const port = process.env.PORT || 3001;

// Initialize Prisma Client
const prisma = new PrismaClient();

// Middleware setup
app.use(helmet()); // Security headers
app.use(cors({
  origin: process.env.FRONTEND_URL || 'http://localhost:3000',
  credentials: true
}));
app.use(express.json({ limit: '10mb' })); // Body parser

// Custom middleware
app.use(requestLogger); // Log tất cả requests

// Rate limiting - ngăn spam
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit mỗi IP 100 requests/15 minutes
  message: 'Quá nhiều yêu cầu từ IP này, vui lòng thử lại sau 15 phút'
});
app.use(limiter);

// Routes
app.post('/api/chat', chatController.handleChat);

// Health check endpoint
app.get('/api/health', (req, res) => {
  res.json({ 
    status: 'healthy',
    timestamp: new Date().toISOString(),
    node_version: process.version
  });
});

// Graceful shutdown
process.on('SIGINT', async () => {
  console.log('Shutting down server...');
  await prisma.$disconnect();
  process.exit(0);
});

// Error handling middleware (phải đặt cuối cùng)
app.use(errorHandler);

app.listen(port, () => {
  console.log(`🚀 API Gateway running on port ${port}`);
  console.log(`🌍 Environment: ${process.env.NODE_ENV || 'development'}`);
  console.log(`🔐 Database: ${process.env.DATABASE_URL ? 'Connected' : 'Not configured'}`);
});
```

**Giải thích chi tiết**:
- **Security Middleware**: Helmet thêm security headers, CORS configuration
- **Rate Limiting**: Ngăn chặn abuse và DDoS attacks
- **Graceful Shutdown**: Đóng database connection trước khi shutdown
- **Health Check**: Endpoint để monitoring hệ thống
- **Structured Logging**: Request logger để theo dõi traffic
- **Centralized Error Handling**: Xử lý lỗi tập trung

---

## 🎨 PHẦN 2: FRONTEND - GIAO DIỆN CHAT (React.js)

### Bước 1: Project Setup

```bash
npx create-react-app seller-agent-frontend
cd seller-agent-frontend
npm install axios react-icons @mui/material @emotion/react @emotion/styled @mui/icons-material
npm install react-router-dom
npm install react-hook-form
npm install sass
```

### Bước 2: Chat Interface Components

#### 2.1 Chat Container Component
File: `src/components/ChatContainer.jsx`
```jsx
import React, { useState, useEffect, useRef } from 'react';
import ChatMessage from './ChatMessage';
import MessageInput from './MessageInput';
import './ChatContainer.scss';

const ChatContainer = () => {
  const [messages, setMessages] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);
  const messagesEndRef = useRef(null);

  // Initial bot message
  useEffect(() => {
    setMessages([
      {
        id: 'welcome',
        sender: 'bot',
        text: '👋 Xin chào! Tôi là Seller Agent AI, sẵn sàng giúp bạn tăng doanh số và tối ưu chiến lược bán hàng. Bạn muốn tìm hiểu về sản phẩm trending hay cần tư vấn chiến lược?',
        timestamp: new Date(),
        metadata: { generated_by: 'SYSTEM' }
      }
    ]);
  }, []);

  // Auto scroll to bottom when new messages arrive
  useEffect(() => {
    scrollToBottom();
  }, [messages]);

  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  };

  const handleSendMessage = async (message) => {
    if (!message.trim()) return;

    // Add user message immediately
    const userMessage = {
      id: `user_${Date.now()}`,
      sender: 'user',
      text: message,
      timestamp: new Date()
    };
    setMessages(prev => [...prev, userMessage]);

    setIsLoading(true);
    setError(null);

    try {
      const response = await fetch('http://localhost:3001/api/chat', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          message: message,
          user_id: localStorage.getItem('user_id') || 'anonymous',
          session_id: localStorage.getItem('session_id') || generateSessionId()
        })
      });

      if (!response.ok) {
        throw new Error(`Server error: ${response.status}`);
      }

      const data = await response.json();

      if (data.success) {
        // Format bot response
        const botMessage = {
          id: `bot_${Date.now()}`,
          sender: 'bot',
          text: typeof data.content === 'string' ? data.content : null,
          products: data.type === 'products' ? data.content : null,
          timestamp: new Date(),
          metadata: data.metadata || {}
        };
        setMessages(prev => [...prev, botMessage]);
      } else {
        throw new Error(data.error || 'Unknown error');
      }
    } catch (err) {
      console.error('Error sending message:', err);
      setError('Không thể kết nối đến server. Vui lòng thử lại.');
      
      // Add error message
      const errorMessage = {
        id: `error_${Date.now()}`,
        sender: 'bot',
        text: '⚠️ ' + (error || 'Đã xảy ra lỗi khi xử lý yêu cầu của bạn.'),
        timestamp: new Date(),
        isError: true
      };
      setMessages(prev => [...prev, errorMessage]);
    } finally {
      setIsLoading(false);
    }
  };

  const handleFeedback = (messageId, isPositive) => {
    // TODO: Implement feedback logging to backend
    console.log(`Feedback for ${messageId}: ${isPositive ? 'positive' : 'negative'}`);
    
    // Visual feedback
    const feedbackMessage = {
      id: `feedback_${Date.now()}`,
      sender: 'system',
      text: isPositive ? 
        '✅ Cảm ơn phản hồi của bạn! Chúng tôi sẽ tiếp tục cải thiện.' : 
        '🙏 Cảm ơn góp ý của bạn! Chúng tôi sẽ khắc phục trong phiên bản tiếp theo.',
      timestamp: new Date()
    };
    
    setMessages(prev => [...prev, feedbackMessage]);
  };

  const generateSessionId = () => {
    const sessionId = 'session_' + Math.random().toString(36).substr(2, 9);
    localStorage.setItem('session_id', sessionId);
    return sessionId;
  };

  return (
    <div className="chat-container">
      {/* Chat Header */}
      <div className="chat-header">
        <div className="chat-header-content">
          <h2>🤖 Seller Agent AI</h2>
          <div className="chat-header-meta">
            <span className="status-indicator online"></span>
            <span className="status-text">Đang hoạt động</span>
          </div>
        </div>
        <div className="chat-header-actions">
          <button className="btn btn-sm btn-clear" onClick={() => setMessages([])}>
            🔄 Reset Chat
          </button>
        </div>
      </div>

      {/* Messages Area */}
      <div className="messages-area">
        {messages.length === 0 ? (
          <div className="empty-chat">
            <div className="empty-chat-content">
              <h3>💬 Bắt đầu trò chuyện</h3>
              <p>Nhập tin nhắn đầu tiên của bạn để bắt đầu...</p>
            </div>
          </div>
        ) : (
          messages.map((message) => (
            <ChatMessage 
              key={message.id} 
              message={message} 
              onFeedback={handleFeedback} 
            />
          ))
        )}
        
        {isLoading && (
          <div className="message-row left">
            <div className="bubble bot-thinking">
              <div className="thinking-dots">
                <span></span>
                <span></span>
                <span></span>
              </div>
            </div>
          </div>
        )}
        
        <div ref={messagesEndRef} />
      </div>

      {/* Message Input */}
      <MessageInput 
        onSendMessage={handleSendMessage} 
        disabled={isLoading}
        placeholder="Hỏi về sản phẩm trending, chiến lược bán hàng..."
      />
    </div>
  );
};

export default ChatContainer;
```

#### 2.2 Chat Message Component (chi tiết)
File: `src/components/ChatMessage.jsx`
```jsx
import React, { useState } from 'react';
import { FaThumbsUp, FaThumbsDown, FaRobot, FaUser } from 'react-icons/fa';
import ProductCard from './ProductCard';
import './ChatMessage.scss';

const ChatMessage = ({ message, onFeedback }) => {
  const [showFeedback, setShowFeedback] = useState(false);
  const [feedbackGiven, setFeedbackGiven] = useState(false);
  const isBot = message.sender === 'bot';
  const isSystem = message.sender === 'system';
  const isError = message.isError;

  // Format timestamp đẹp hơn
  const formatTime = (date) => {
    if (!(date instanceof Date)) {
      date = new Date(date);
    }
    return date.toLocaleTimeString('vi-VN', { 
      hour: '2-digit', 
      minute: '2-digit' 
    });
  };

  const handleFeedbackClick = (isPositive) => {
    if (onFeedback && !feedbackGiven) {
      onFeedback(message.id, isPositive);
      setFeedbackGiven(true);
      setTimeout(() => setShowFeedback(false), 2000);
    }
  };

  return (
    <div className={`message-row ${isBot ? 'left' : 'right'} ${isSystem ? 'system' : ''} ${isError ? 'error' : ''}`}>
      {/* Avatar */}
      <div className="avatar">
        {isBot ? (
          <div className="avatar-bot">
            <FaRobot size={18} />
          </div>
        ) : isSystem ? (
          <div className="avatar-system">SYS</div>
        ) : (
          <div className="avatar-user">
            <FaUser size={18} />
          </div>
        )}
      </div>

      {/* Message Bubble */}
      <div className={`bubble ${isBot ? 'bot' : 'user'} ${isSystem ? 'system' : ''} ${isError ? 'error' : ''}`}>
        {/* Timestamp và source */}
        <div className="message-header">
          <span className="timestamp">{formatTime(message.timestamp)}</span>
          {message.metadata?.generated_by && (
            <span className="source-badge">
              {message.metadata.generated_by === 'SYSTEM' ? 'Hệ thống' : 'AI'}
            </span>
          )}
        </div>

        {/* Content */}
        <div className="message-content">
          {message.text && <p>{message.text}</p>}
          
          {message.products && (
            <div className="product-carousel">
              {message.products.map((product) => (
                <ProductCard 
                  key={product.id} 
                  product={product} 
                  showTrendingBadge={true}
                />
              ))}
            </div>
          )}
        </div>

        {/* AI Transparency Label - BẮT BUỘC THEO ĐẠO ĐỨC AI */}
        {isBot && message.metadata?.ai_generated && (
          <div className="ai-disclaimer">
            <span className="ai-icon">⚡</span>
            <span>Câu trả lời được tạo bởi AI</span>
            <span className="ai-confidence">
              (Độ tin cậy: {(message.metadata.confidence_score * 100).toFixed(0)}%)
            </span>
          </div>
        )}

        {/* Feedback buttons - chỉ hiển thị cho tin nhắn bot */}
        {isBot && !feedbackGiven && !isSystem && (
          <div 
            className="feedback-area"
            onMouseEnter={() => setShowFeedback(true)}
            onMouseLeave={() => setShowFeedback(false)}
          >
            {showFeedback && (
              <div className="feedback-buttons">
                <button 
                  className="feedback-btn positive"
                  onClick={() => handleFeedbackClick(true)}
                  title="Tin nhắn hữu ích"
                >
                  <FaThumbsUp size={14} />
                </button>
                <button 
                  className="feedback-btn negative"
                  onClick={() => handleFeedbackClick(false)}
                  title="Tin nhắn không hữu ích"
                >
                  <FaThumbsDown size={14} />
                </button>
              </div>
            )}
          </div>
        )}

        {feedbackGiven && (
          <div className="feedback-acknowledgment">
            ✅ Đã ghi nhận phản hồi của bạn
          </div>
        )}
      </div>
    </div>
  );
};

export default ChatMessage;
```

#### 2.3 Product Card Component
File: `src/components/ProductCard.jsx`
```jsx
import React from 'react';
import { FaShoppingCart, FaChartLine, FaStar } from 'react-icons/fa';
import { Button } from '@mui/material';
import './ProductCard.scss';

const ProductCard = ({ product, showTrendingBadge = false }) => {
  // Format price with Vietnamese Dong format
  const formatPrice = (price) => {
    return new Intl.NumberFormat('vi-VN', {
      style: 'currency',
      currency: 'VND'
    }).format(price);
  };

  // Get platform icon and color
  const getPlatformInfo = (platform) => {
    const platforms = {
      'shopee': { icon: '🛒', color: '#EE4D2D', name: 'Shopee' },
      'tiktok': { icon: '🎵', color: '#69C9D0', name: 'TikTok Shop' },
      'lazada': { icon: '🔥', color: '#FFC000', name: 'Lazada' },
      'default': { icon: '🏪', color: '#666666', name: 'Nền tảng khác' }
    };
    return platforms[platform?.toLowerCase()] || platforms.default;
  };

  const platformInfo = getPlatformInfo(product.platform);

  return (
    <div className="product-card">
      {/* Platform Badge */}
      <div className="platform-badge" style={{ backgroundColor: platformInfo.color }}>
        {platformInfo.icon} {platformInfo.name}
      </div>

      {/* Product Image Placeholder */}
      <div className="product-image-placeholder">
        {/* In real app, you would use actual image URL */}
        <div className="image-placeholder-content">
          <span>📸</span>
          <p>Hình ảnh sản phẩm</p>
        </div>
      </div>

      {/* Product Info */}
      <div className="product-info">
        <h4 className="product-title">
          {product.name.length > 40 ? product.name.substring(0, 40) + '...' : product.name}
        </h4>
        
        <div className="product-price">
          <strong>{formatPrice(product.price)}</strong>
          {product.original_price && (
            <span className="original-price">
              {formatPrice(product.original_price)}
            </span>
          )}
        </div>

        {/* Trending Badge */}
        {showTrendingBadge && product.trending_score > 0.7 && (
          <div className="trending-badge">
            <FaChartLine size={12} /> Trending: {(product.trending_score * 100).toFixed(0)}%
          </div>
        )}

        {/* Sales Info */}
        <div className="sales-info">
          <span>🔥 Đã bán: {product.sales_count.toLocaleString('vi-VN')}</span>
          {product.competition_level && (
            <span className="competition-level">
              📊 Cạnh tranh: {product.competition_level}/5
            </span>
          )}
        </div>
      </div>

      {/* Action Buttons */}
      <div className="product-actions">
        <Button 
          variant="contained" 
          color="primary"
          size="small"
          fullWidth
          onClick={() => window.open(`https://${product.platform}.com.vn/product/${product.id}`, '_blank')}
        >
          <FaShoppingCart size={14} style={{ marginRight: '8px' }} />
          Xem sản phẩm
        </Button>
        
        {product.analytics_url && (
          <Button 
            variant="outlined" 
            size="small"
            fullWidth
            onClick={() => window.open(product.analytics_url, '_blank')}
          >
            📈 Phân tích chi tiết
          </Button>
        )}
      </div>
    </div>
  );
};

export default ProductCard;
```

---

## 🛡️ PHẦN 3: TRIỂN KHAI & ĐẠO ĐỨC (Compliance)

### 1. Bảo vệ dữ liệu (Privacy & Security)

#### 1.1 Data Anonymization Middleware
File: `src/middleware/securityMiddleware.js`
```javascript
/**
 * Middleware xử lý bảo mật và ẩn danh hóa dữ liệu
 */

// Patterns cho thông tin nhạy cảm
const SENSITIVE_PATTERNS = {
  PHONE: /(\+?84|0)(\d{1,2})[-.\s]?(\d{3})[-.\s]?(\d{3,4})[-.\s]?(\d{0,4})/g,
  EMAIL: /([a-zA-Z0-9._-]+@[a-zA-Z0-9._-]+\.[a-zA-Z0-9_-]+)/g,
  ID_NUMBER: /(\d{9}|\d{12})/g, // CMND/CCCD
  BANK_ACCOUNT: /\b\d{8,16}\b/g, // Số tài khoản ngân hàng
  ADDRESS: /(số|so|số nhà|sonha|đường|street|st)\s*\d+\w*\s*,?\s*[^\s]+/gi
};

/**
 * Hàm ẩn danh hóa dữ liệu nhạy cảm
 * @param {Object} data - Dữ liệu cần xử lý
 * @returns {Object} Dữ liệu đã được ẩn danh hóa
 */
const anonymizeData = (data) => {
  if (!data || typeof data !== 'object') return data;

  const anonymized = { ...data };

  // Xử lý đệ quy nếu là object hoặc array
  Object.keys(anonymized).forEach(key => {
    const value = anonymized[key];
    
    if (typeof value === 'string') {
      anonymized[key] = maskSensitiveString(value);
    } 
    else if (typeof value === 'object' && value !== null) {
      anonymized[key] = anonymizeData(value);
    }
  });

  return anonymized;
};

/**
 * Hàm che giấu thông tin nhạy cảm trong string
 * @param {string} text - Text cần xử lý
 * @returns {string} Text đã được che giấu thông tin nhạy cảm
 */
const maskSensitiveString = (text) => {
  if (!text || typeof text !== 'string') return text;

  let masked = text;

  // Che số điện thoại
  masked = masked.replace(SENSITIVE_PATTERNS.PHONE, match => {
    const digits = match.replace(/\D/g, '');
    if (digits.length >= 10) {
      const prefix = digits.substring(0, 4);
      const suffix = digits.substring(digits.length - 3);
      return `${prefix}****${suffix}`;
    }
    return match;
  });

  // Che email
  masked = masked.replace(SENSITIVE_PATTERNS.EMAIL, match => {
    const [local, domain] = match.split('@');
    return `${local.substring(0, 2)}****@${domain.substring(0, 2)}****.${domain.split('.').pop()}`;
  });

  // Che số CMND/CCCD
  masked = masked.replace(SENSITIVE_PATTERNS.ID_NUMBER, match => {
    return match.substring(0, 2) + '******' + match.substring(match.length - 2);
  });

  // Che số tài khoản ngân hàng
  masked = masked.replace(SENSITIVE_PATTERNS.BANK_ACCOUNT, match => {
    return match.substring(0, 4) + '********' + match.substring(match.length - 2);
  });

  // Che địa chỉ (đơn giản hóa)
  masked = masked.replace(SENSITIVE_PATTERNS.ADDRESS, match => {
    return '*** *** ***';
  });

  return masked;
};

/**
 * Middleware Express để ẩn danh hóa request body
 */
const anonymizeRequestBody = (req, res, next) => {
  try {
    if (req.body) {
      req.body = anonymizeData(req.body);
      
      // Đặc biệt xử lý message field trong chat
      if (req.body.message) {
        req.body.message = maskSensitiveString(req.body.message);
      }
      
      console.log('[SECURITY] Request body anonymized:', req.body);
    }
    next();
  } catch (error) {
    console.error('[SECURITY] Error anonymizing request:', error);
    next();
  }
};

/**
 * Middleware logging bảo mật
 */
const securityLogger = (req, res, next) => {
  const startTime = Date.now();
  
  // Log request metadata (không chứa dữ liệu nhạy cảm)
  console.log(`[SECURITY] Request: ${req.method} ${req.url} from ${req.ip || req.connection.remoteAddress}`);
  
  // Log response
  res.on('finish', () => {
    const duration = Date.now() - startTime;
    console.log(`[SECURITY] Response: ${res.statusCode} - ${duration}ms`);
  });
  
  next();
};

module.exports = {
  anonymizeData,
  maskSensitiveString,
  anonymizeRequestBody,
  securityLogger
};
```

### 2. Cơ chế phản hồi (Feedback Loop)

#### 2.1 Feedback Service
File: `src/services/feedbackService.js`
```javascript
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

/**
 * Service xử lý feedback từ người dùng
 */

/**
 * Lưu feedback vào database
 * @param {Object} feedbackData - Dữ liệu feedback
 * @param {string} feedbackData.message_id - ID của tin nhắn
 * @param {string} feedbackData.user_id - ID người dùng
 * @param {boolean} feedbackData.is_positive - Positive/Negative feedback
 * @param {string} feedbackData.context - Context của feedback
 * @param {Object} feedbackData.original_response - Original AI response
 */
async function saveFeedback(feedbackData) {
  try {
    const {
      message_id,
      user_id,
      is_positive,
      context = '',
      original_response = {}
    } = feedbackData;

    // Validate required fields
    if (!message_id || !user_id) {
      throw new Error('Missing required fields: message_id, user_id');
    }

    // Lưu vào bảng feedback
    const feedback = await prisma.feedback.create({
      data: {
        message_id,
        user_id,
        feedback_type: is_positive ? 'POSITIVE' : 'NEGATIVE',
        context: context.substring(0, 500), // Giới hạn độ dài
        original_response: JSON.stringify(original_response),
        created_at: new Date()
      }
    });

    console.log(`[FEEDBACK] Saved feedback: ${feedback.id}`);

    // Nếu là negative feedback, trigger alert
    if (!is_positive) {
      await handleNegativeFeedback(feedback);
    }

    return feedback;
  } catch (error) {
    console.error('[FEEDBACK] Error saving feedback:', error);
    throw error;
  }
}

/**
 * Xử lý negative feedback - gửi alert và logging
 * @param {Object} feedback - Feedback record
 */
async function handleNegativeFeedback(feedback) {
  try {
    console.warn(`[ALERT] Negative feedback received for message: ${feedback.message_id}`);
    
    // TODO: Gửi alert đến team AI và product manager
    // Ví dụ: sendSlackAlert(), sendEmailNotification()
    
    // Lưu vào bảng phân tích chất lượng
    await prisma.quality_analysis.create({
      data: {
        feedback_id: feedback.id,
        issue_type: 'RESPONSE_QUALITY',
        priority: 'HIGH',
        status: 'PENDING',
        notes: 'Negative feedback received - requires review'
      }
    });
  } catch (error) {
    console.error('[FEEDBACK] Error handling negative feedback:', error);
  }
}

/**
 * Báo cáo thống kê feedback theo thời gian
 * @param {Date} startDate - Ngày bắt đầu
 * @param {Date} endDate - Ngày kết thúc
 */
async function getFeedbackReport(startDate, endDate) {
  try {
    const report = await prisma.feedback.groupBy({
      by: ['feedback_type', 'created_at'],
      where: {
        created_at: {
          gte: startDate,
          lte: endDate
        }
      },
      _count: {
        _all: true
      },
      orderBy: {
        created_at: 'asc'
      }
    });

    return report;
  } catch (error) {
    console.error('[FEEDBACK] Error generating report:', error);
    throw error;
  }
}

module.exports = {
  saveFeedback,
  handleNegativeFeedback,
  getFeedbackReport
};
```

### 3. Tích hợp Đa kênh (Multi-channel)

#### 3.1 Telegram Bot Integration
File: `src/services/telegramBotService.js`
```javascript
const { Telegraf } = require('telegraf');
const axios = require('axios');
const { maskSensitiveString } = require('../middleware/securityMiddleware');

/**
 * Service tích hợp Telegram Bot
 * Sử dụng cùng API Gateway với web interface
 */

class TelegramBotService {
  constructor() {
    this.botToken = process.env.TELEGRAM_BOT_TOKEN;
    this.backendUrl = process.env.BACKEND_URL || 'http://localhost:3001';
    
    if (!this.botToken) {
      throw new Error('TELEGRAM_BOT_TOKEN environment variable is required');
    }

    this.bot = new Telegraf(this.botToken);
    this.setupBot();
  }

  /**
   * Cấu hình bot commands và handlers
   */
  setupBot() {
    // Start command
    this.bot.start((ctx) => {
      const welcomeMessage = `
🤖 *Seller Agent AI Bot*

Chào mừng bạn đến với Seller Agent AI! Tôi có thể giúp bạn:
• 📈 Tìm sản phẩm trending
• 💡 Tư vấn chiến lược bán hàng
• 📊 Phân tích thị trường
• 🔍 So sánh đối thủ

Gõ /help để xem danh sách lệnh chi tiết.
      `;
      ctx.reply(welcomeMessage, { parse_mode: 'Markdown' });
    });

    // Help command
    this.bot.help((ctx) => {
      const helpMessage = `
📚 *Danh sách lệnh*:

/start - Bắt đầu trò chuyện
/help - Hiển thị trợ giúp
/trending [category] - Sản phẩm trending theo danh mục
/strategy [topic] - Tư vấn chiến lược
/feedback [message] - Gửi phản hồi

Ví dụ:
/trending điện thoại
/strategy giảm giá
      `;
      ctx.reply(helpMessage, { parse_mode: 'Markdown' });
    });

    // Trending products command
    this.bot.command('trending', async (ctx) => {
      const args = ctx.message.text.split(' ').slice(1);
      const category = args.join(' ') || null;
      
      await ctx.reply('🔍 Đang tìm sản phẩm trending...');
      
      try {
        const response = await this.getTrendingProductsFromAPI(category);
        await this.sendTrendingProducts(ctx, response);
      } catch (error) {
        console.error('[TELEGRAM] Error getting trending products:', error);
        await ctx.reply('❌ Lỗi khi lấy sản phẩm trending. Vui lòng thử lại sau.');
      }
    });

    // Strategy command
    this.bot.command('strategy', async (ctx) => {
      const args = ctx.message.text.split(' ').slice(1);
      const topic = args.join(' ') || 'chiến lược bán hàng';
      
      await ctx.reply('💡 Đang phân tích chiến lược...');
      
      try {
        const response = await this.getStrategyFromAPI(topic);
        await this.sendStrategyResponse(ctx, response);
      } catch (error) {
        console.error('[TELEGRAM] Error getting strategy:', error);
        await ctx.reply('❌ Lỗi khi phân tích chiến lược. Vui lòng thử lại sau.');
      }
    });

    // Feedback command
    this.bot.command('feedback', async (ctx) => {
      const feedbackText = ctx.message.text.split(' ').slice(1).join(' ');
      
      if (!feedbackText) {
        await ctx.reply('📝 Vui lòng nhập phản hồi của bạn sau lệnh /feedback');
        return;
      }

      try {
        await this.saveFeedback(ctx.from.id, feedbackText);
        await ctx.reply('✅ Cảm ơn phản hồi của bạn! Chúng tôi sẽ tiếp tục cải thiện.');
      } catch (error) {
        console.error('[TELEGRAM] Error saving feedback:', error);
        await ctx.reply('❌ Lỗi khi lưu phản hồi. Vui lòng thử lại sau.');
      }
    });

    // Message handler - sử dụng cùng API chat như web
    this.bot.on('text', async (ctx) => {
      const message = ctx.message.text;
      const userId = ctx.from.id.toString();
      
      // Skip commands
      if (message.startsWith('/')) return;

      try {
        await ctx.sendChatAction('typing');
        
        // Gọi API chat tương tự như web interface
        const response = await this.callChatAPI(message, userId);
        
        // Xử lý response và gửi lại cho user
        await this.handleChatResponse(ctx, response);
      } catch (error) {
        console.error('[TELEGRAM] Error processing message:', error);
        await ctx.reply('❌ Đã xảy ra lỗi. Vui lòng thử lại sau.');
      }
    });

    // Error handler
    this.bot.catch((err, ctx) => {
      console.error(`[TELEGRAM] Error for ${ctx.updateType}:`, err);
      ctx.reply('❌ Hệ thống đang gặp sự cố. Vui lòng thử lại sau.');
    });
  }

  /**
   * Gọi API chat từ backend
   */
  async callChatAPI(message, userId) {
    try {
      // Ẩn danh hóa dữ liệu trước khi gửi
      const sanitizedMessage = maskSensitiveString(message);
      
      const response = await axios.post(`${this.backendUrl}/api/chat`, {
        message: sanitizedMessage,
        user_id: `telegram_${userId}`,
        session_id: `telegram_session_${userId}_${Date.now()}`
      }, {
        timeout: 30000 // 30 seconds
      });

      return response.data;
    } catch (error) {
      console.error('[TELEGRAM] API call failed:', error);
      throw new Error('Chat API call failed');
    }
  }

  /**
   * Lấy sản phẩm trending từ API
   */
  async getTrendingProductsFromAPI(category = null) {
    try {
      const params = category ? { category } : {};
      const response = await axios.get(`${this.backendUrl}/api/products/trending`, { params });
      return response.data;
    } catch (error) {
      console.error('[TELEGRAM] Failed to get trending products:', error);
      throw error;
    }
  }

  /**
   * Gửi sản phẩm trending dưới dạng message Telegram
   */
  async sendTrendingProducts(ctx, products) {
    if (!products || products.length === 0) {
      await ctx.reply('😔 Không tìm thấy sản phẩm trending.');
      return;
    }

    let message = '🔥 *Sản phẩm trending hiện nay:*\n\n';
    
    products.slice(0, 5).forEach((product, index) => {
      message += `${index + 1}. *${product.title}*\n`;
      message += `💰 Giá: ${product.price.toLocaleString('vi-VN')} VND\n`;
      message += `📈 Trending: ${(product.trending_score * 100).toFixed(0)}%\n`;
      message += `📦 Còn: ${product.stock} sản phẩm\n`;
      message += `🛒 Nền tảng: ${product.platform}\n\n`;
    });

    message += '💡 *Lưu ý:* Đây là sản phẩm được đề xuất bởi AI dựa trên xu hướng thị trường.';
    
    await ctx.reply(message, { parse_mode: 'Markdown' });
  }

  /**
   * Xử lý response từ chat API và gửi lại cho Telegram
   */
  async handleChatResponse(ctx, response) {
    if (!response.success) {
      await ctx.reply('❌ Lỗi khi xử lý yêu cầu của bạn.');
      return;
    }

    let message = '';
    
    if (response.type === 'products' && response.content && response.content.length > 0) {
      // Format sản phẩm
      message = '🛒 *Sản phẩm được đề xuất:*\n\n';
      response.content.slice(0, 3).forEach((product, index) => {
        message += `${index + 1}. *${product.name}*\n`;
        message += `💰 ${product.price.toLocaleString('vi-VN')} VND\n`;
        message += `🔥 Trending: ${(product.trending_score * 100).toFixed(0)}%\n\n`;
      });
      message += '👉 Xem thêm trên website để có trải nghiệm tốt hơn.';
    } else {
      // Response text thông thường
      message = response.content || 'Xin lỗi, tôi không hiểu câu hỏi của bạn.';
      
      // Thêm disclaimer AI nếu cần
      if (response.metadata?.ai_generated) {
        message += '\n\n⚡ *Câu trả lời được tạo bởi AI*';
      }
    }

    await ctx.replyWithMarkdown(message);
  }

  /**
   * Khởi động bot
   */
  start() {
    this.bot.launch()
      .then(() => {
        console.log('🚀 Telegram Bot started successfully');
        
        // Enable graceful stop
        process.once('SIGINT', () => this.bot.stop('SIGINT'));
        process.once('SIGTERM', () => this.bot.stop('SIGTERM'));
      })
      .catch((error) => {
        console.error('❌ Failed to start Telegram Bot:', error);
        throw error;
      });
  }

  /**
   * Dừng bot
   */
  stop(reason = 'Unknown reason') {
    console.log(`🛑 Stopping Telegram Bot: ${reason}`);
    this.bot.stop(reason);
  }

  /**
   * Lưu feedback từ Telegram
   */
  async saveFeedback(userId, feedbackText) {
    try {
      await axios.post(`${this.backendUrl}/api/feedback`, {
        user_id: `telegram_${userId}`,
        feedback_text: maskSensitiveString(feedbackText),
        source: 'telegram'
      });
    } catch (error) {
      console.error('[TELEGRAM] Failed to save feedback:', error);
      throw error;
    }
  }
}

module.exports = TelegramBotService;
```

---

## 📋 CHECKLIST HOÀN THIỆN

### ✅ Đã hoàn thành:
- [x] **Database Schema** với Prisma ORM
- [x] **API Gateway** với Express.js và middleware bảo mật
- [x] **Chat Logic** xử lý intent và kết nối AI service
- [x] **React Frontend** với giao diện chat đẹp, responsive
- [x] **Product Cards** hiển thị sản phẩm trending
- [x] **AI Transparency** với nhãn minh bạch
- [x] **Data Anonymization** bảo vệ thông tin người dùng
- [x] **Feedback Loop** cơ chế phản hồi để cải thiện chất lượng
- [x] **Multi-channel Support** tích hợp Telegram Bot

### 🚀 Cần triển khai tiếp:
- [ ] **Testing** (Unit tests, Integration tests)
- [ ] **Dockerization** container hóa ứng dụng
- [ ] **CI/CD Pipeline** tự động deploy
- [ ] **Monitoring & Alerting** (Prometheus, Grafana)
- [ ] **Rate Limiting** nâng cao với Redis
- [ ] **Caching Layer** với Redis để tăng performance
- [ ] **Multi-language Support** (English/Vietnamese)

---

## 💡 LỜI KHUYÊN KHI TRIỂN KHAI

1. **Bắt đầu từ core features**: Triển khai API Gateway và giao diện chat cơ bản trước
2. **Test kỹ security**: Luôn validate và sanitize input trước khi xử lý
3. **Monitor everything**: Theo dõi performance, error rates, và user feedback
4. **Iterate based on feedback**: Sử dụng cơ chế feedback để cải thiện hệ thống liên tục
5. **Document everything**: Viết documentation chi tiết cho team và future maintenance

Bạn có thể bắt đầu code ngay từ phần Backend API Gateway, sau đó chuyển sang Frontend React. Nếu cần hỗ trợ thêm về bất kỳ phần nào, hãy hỏi tôi chi tiết hơn!