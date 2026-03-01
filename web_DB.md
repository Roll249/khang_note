# 🐘 Tutorial Chi Tiết: Xây Dựng Ứng Dụng Quản Lý Sản Phẩm với PostgreSQL trên Docker

Dưới đây là tutorial toàn diện giúp bạn học cách làm việc với database từ A-Z. Tôi sẽ hướng dẫn bạn tạo một project nhỏ quản lý sản phẩm với đầy đủ tính năng: CRUD, thống kê, dashboard, upload file...

## 📋 MỤC LỤC CHI TIẾT

1. [Chuẩn bị môi trường](#1-chuẩn-bị-môi-trường)
2. [Cài đặt PostgreSQL trên Docker](#2-cài-đặt-postgresql-trên-docker)
3. [Kết nối với DBeaver](#3-kết-nối-với-dbeaver)
4. [Chuẩn bị bộ dữ liệu mẫu](#4-chuẩn-bị-bộ-dữ-liệu-mẫu)
5. [Cấu hình Backend (Node.js/Express)](#5-cấu-hình-backend-nodejsexpress)
6. [Xây dựng API CRUD](#6-xây-dựng-api-crud)
7. [Thống kê và Dashboard](#7-thống-kê-và-dashboard)
8. [Upload file và lưu vào database](#8-upload-file-và-lưu-vào-database)
9. [Triển khai Frontend đơn giản](#9-triển-khai-frontend-đơn-giản)
10. [Kiểm thử toàn bộ hệ thống](#10-kiểm-thử-toàn-bộ-hệ-thống)

---

## 1. Chuẩn bị môi trường

### Công cụ cần cài đặt:
```bash
# 1. Docker (bắt buộc)
# macOS: brew install --cask docker
# Windows: https://www.docker.com/products/docker-desktop/
# Linux: sudo apt-get install docker docker-compose

# 2. Node.js (v18+)
# https://nodejs.org/

# 3. DBeaver (Community Edition)
# https://dbeaver.io/download/

# 4. Visual Studio Code
# https://code.visualstudio.com/
```

### Kiểm tra cài đặt:
```bash
docker --version
node --version
npm --version
```

---

## 2. Cài đặt PostgreSQL trên Docker

### Tạo file `docker-compose.yml`:
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    container_name: postgres-tutorial
    environment:
      POSTGRES_DB: product_management
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
      PGDATA: /data/postgres
    volumes:
      - postgres-data:/data/postgres
    ports:
      - "5432:5432"
    networks:
      - postgres-network
    restart: unless-stopped

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin-tutorial
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin123
    volumes:
      - pgadmin-data:/var/lib/pgadmin
    ports:
      - "5050:80"
    networks:
      - postgres-network
    restart: unless-stopped

volumes:
  postgres-data:
  pgadmin-data:

networks:
  postgres-network:
    driver: bridge
```

### Chạy Docker container:
```bash
# Khởi động dịch vụ
docker-compose up -d

# Kiểm tra trạng thái
docker-compose ps

# Xem logs nếu cần
docker-compose logs -f
```

### Kiểm tra kết nối PostgreSQL:
```bash
# Truy cập vào container PostgreSQL
docker exec -it postgres-tutorial psql -U admin -d product_management

# Thoát khỏi psql
\q
```

---

## 3. Kết nối với DBeaver

### Bước 1: Tạo kết nối mới trong DBeaver
1. Mở DBeaver
2. Click vào biểu tượng **New Database Connection** (hoặc Ctrl+N)
3. Chọn **PostgreSQL** → **Next**

### Bước 2: Cấu hình kết nối
```
Host: localhost
Port: 5432
Database: product_management
Username: admin
Password: admin123
```

### Bước 3: Test connection
- Click **Test Connection** để kiểm tra
- Nếu thành công, click **Finish**

### Bước 4: Tạo schema và tables
Trong DBeaver, chạy các lệnh SQL sau:

```sql
-- Tạo schema
CREATE SCHEMA IF NOT EXISTS product_schema;

-- Tạo bảng products
CREATE TABLE product_schema.products (
    product_id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    category VARCHAR(100),
    stock INT DEFAULT 0,
    image_url VARCHAR(500),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tạo bảng categories
CREATE TABLE product_schema.categories (
    category_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tạo bảng sales để thống kê
CREATE TABLE product_schema.sales (
    sale_id SERIAL PRIMARY KEY,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    sale_price DECIMAL(10,2) NOT NULL,
    sale_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    customer_name VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (product_id) REFERENCES product_schema.products(product_id) ON DELETE CASCADE
);

-- Tạo index để tăng tốc độ query
CREATE INDEX idx_products_category ON product_schema.products(category);
CREATE INDEX idx_sales_date ON product_schema.sales(sale_date);
CREATE INDEX idx_sales_product ON product_schema.sales(product_id);

-- Tạo function tự động cập nhật updated_at
CREATE OR REPLACE FUNCTION update_modified_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Tạo trigger cho bảng products
CREATE TRIGGER update_products_modtime
BEFORE UPDATE ON product_schema.products
FOR EACH ROW EXECUTE FUNCTION update_modified_column();
```

---

## 4. Chuẩn bị bộ dữ liệu mẫu

### Tạo file `seed_data.sql`:
```sql
-- Thêm categories mẫu
INSERT INTO product_schema.categories (name, description) VALUES
('Điện thoại', 'Các loại điện thoại thông minh'),
('Laptop', 'Máy tính xách tay các loại'),
('Phụ kiện', 'Phụ kiện điện thoại và máy tính'),
('Máy tính bảng', 'Tablet các loại');

-- Thêm products mẫu
INSERT INTO product_schema.products (name, description, price, category, stock, image_url) VALUES
('iPhone 15 Pro', 'Điện thoại cao cấp với chip A17 Pro', 29990000, 'Điện thoại', 50, 'https://example.com/iphone15.jpg'),
('Samsung Galaxy S24', 'Điện thoại flagship với AI tích hợp', 24990000, 'Điện thoại', 75, 'https://example.com/galaxys24.jpg'),
('MacBook Pro M3', 'Laptop cao cấp cho developer', 59990000, 'Laptop', 30, 'https://example.com/macbookpro.jpg'),
('Dell XPS 15', 'Laptop cao cấp cho thiết kế', 45990000, 'Laptop', 45, 'https://example.com/dellxps.jpg'),
('iPad Pro', 'Máy tính bảng cao cấp', 32990000, 'Máy tính bảng', 60, 'https://example.com/ipadpro.jpg'),
('Tai nghe AirPods Pro', 'Tai nghe không dây cao cấp', 6990000, 'Phụ kiện', 100, 'https://example.com/airpods.jpg'),
('Bàn phím cơ', 'Bàn phím gaming chất lượng cao', 2490000, 'Phụ kiện', 80, 'https://example.com/keyboard.jpg'),
('Chuột gaming', 'Chuột dành cho game thủ', 1290000, 'Phụ kiện', 120, 'https://example.com/mouse.jpg');

-- Thêm dữ liệu sales mẫu cho thống kê
INSERT INTO product_schema.sales (product_id, quantity, sale_price, customer_name, sale_date) VALUES
(1, 5, 29990000, 'Nguyễn Văn A', CURRENT_TIMESTAMP - INTERVAL '1 day'),
(2, 8, 24990000, 'Trần Thị B', CURRENT_TIMESTAMP - INTERVAL '2 days'),
(3, 3, 59990000, 'Lê Văn C', CURRENT_TIMESTAMP - INTERVAL '1 day'),
(4, 6, 45990000, 'Phạm Thị D', CURRENT_TIMESTAMP - INTERVAL '3 days'),
(5, 10, 32990000, 'Hoàng Văn E', CURRENT_TIMESTAMP - INTERVAL '2 days'),
(6, 15, 6990000, 'Vũ Thị F', CURRENT_TIMESTAMP - INTERVAL '1 day'),
(7, 20, 2490000, 'Đặng Văn G', CURRENT_TIMESTAMP - INTERVAL '4 days'),
(8, 25, 1290000, 'Bùi Thị H', CURRENT_TIMESTAMP - INTERVAL '2 days'),
(1, 3, 29990000, 'Ngô Văn I', CURRENT_TIMESTAMP - INTERVAL '5 days'),
(2, 4, 24990000, 'Đỗ Thị K', CURRENT_TIMESTAMP - INTERVAL '3 days');

-- Thêm thêm dữ liệu sales ngẫu nhiên cho thống kê
INSERT INTO product_schema.sales (product_id, quantity, sale_price, customer_name, sale_date)
SELECT 
    (random() * 7 + 1)::INT,
    (random() * 10 + 1)::INT,
    p.price * (0.9 + random() * 0.2),
    'Khách hàng ' || (random() * 1000)::INT,
    CURRENT_TIMESTAMP - (random() * 365 || ' days')::INTERVAL
FROM product_schema.products p, generate_series(1, 100) s;
```

### Chạy seed data trong DBeaver:
1. Mở file `seed_data.sql` trong DBeaver
2. Chọn toàn bộ nội dung (Ctrl+A)
3. Chạy (Ctrl+Enter)
4. Kiểm tra kết quả bằng các câu lệnh SELECT

---

## 5. Cấu hình Backend (Node.js/Express)

### Tạo project và cài đặt dependencies:
```bash
# Tạo thư mục project
mkdir product-management-backend
cd product-management-backend

# Khởi tạo Node.js project
npm init -y

# Cài đặt các packages cần thiết
npm install express cors dotenv pg prisma @prisma/client
npm install -D typescript @types/node @types/express @types/cors ts-node nodemon

# Khởi tạo TypeScript
npx tsc --init
```

### Cấu hình TypeScript (`tsconfig.json`):
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "moduleResolution": "node",
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

### Khởi tạo Prisma:
```bash
npx prisma init
```

### Cấu hình Prisma (`prisma/schema.prisma`):
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Product {
  product_id   Int      @id @default(autoincrement())
  name         String
  description  String?
  price        Decimal
  category     String?
  stock        Int      @default(0)
  image_url    String?
  created_at   DateTime @default(now())
  updated_at   DateTime @default(now()) @updatedAt
  sales        Sale[]

  @@schema("product_schema")
  @@index([category])
}

model Category {
  category_id  Int      @id @default(autoincrement())
  name         String   @unique
  description  String?
  created_at   DateTime @default(now())

  @@schema("product_schema")
}

model Sale {
  sale_id      Int      @id @default(autoincrement())
  product_id   Int
  product      Product  @relation(fields: [product_id], references: [product_id])
  quantity     Int
  sale_price   Decimal
  sale_date    DateTime @default(now())
  customer_name String?
  created_at   DateTime @default(now())

  @@schema("product_schema")
  @@index([sale_date])
  @@index([product_id])
}
```

### Tạo file cấu hình môi trường (`.env`):
```env
DATABASE_URL="postgresql://admin:admin123@localhost:5432/product_management?schema=product_schema"
PORT=3001
UPLOAD_DIR=./uploads
```

### Tạo thư mục cấu trúc project:
```bash
mkdir src
mkdir src/controllers
mkdir src/services
mkdir src/routes
mkdir src/middleware
mkdir src/utils
mkdir uploads
```

---

## 6. Xây dựng API CRUD

### File cấu hình server chính (`src/server.ts`):
```typescript
import express from 'express';
import cors from 'cors';
import dotenv from 'dotenv';
import { PrismaClient } from '@prisma/client';
import productRoutes from './routes/productRoutes';

dotenv.config();

const app = express();
const port = process.env.PORT || 3001;
const prisma = new PrismaClient();

// Middleware
app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.use('/api/products', productRoutes);

// Test route
app.get('/api/health', (req, res) => {
  res.json({ status: 'healthy', timestamp: new Date().toISOString() });
});

// Error handling middleware
app.use((err: Error, req: express.Request, res: express.Response, next: express.NextFunction) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Internal server error', message: err.message });
});

// Start server
app.listen(port, async () => {
  try {
    // Test database connection
    await prisma.$connect();
    console.log('🟢 Database connected successfully');
    
    console.log(`🚀 Server running on http://localhost:${port}`);
    console.log(`📚 API Documentation: http://localhost:${port}/api-docs`);
  } catch (error) {
    console.error('🔴 Database connection failed:', error);
    process.exit(1);
  }
});

// Graceful shutdown
process.on('SIGINT', async () => {
  await prisma.$disconnect();
  process.exit(0);
});
```

### Tạo service cho products (`src/services/productService.ts`):
```typescript
import { PrismaClient, Prisma } from '@prisma/client';

const prisma = new PrismaClient();

// Get all products with pagination
export const getAllProducts = async (page: number = 1, limit: number = 10) => {
  const skip = (page - 1) * limit;
  
  const [products, total] = await Promise.all([
    prisma.product.findMany({
      skip,
      take: limit,
      orderBy: { created_at: 'desc' }
    }),
    prisma.product.count()
  ]);

  return {
    data: products,
    meta: {
      total,
      page,
      lastPage: Math.ceil(total / limit),
      limit
    }
  };
};

// Get product by ID
export const getProductById = async (id: number) => {
  return prisma.product.findUnique({
    where: { product_id: id }
  });
};

// Create new product
export const createProduct = async (data: Prisma.ProductCreateInput) => {
  return prisma.product.create({
    data
  });
};

// Update product
export const updateProduct = async (id: number, data: Prisma.ProductUpdateInput) => {
  return prisma.product.update({
    where: { product_id: id },
    data
  });
};

// Delete product
export const deleteProduct = async (id: number) => {
  return prisma.product.delete({
    where: { product_id: id }
  });
};

// Search products
export const searchProducts = async (query: string) => {
  return prisma.product.findMany({
    where: {
      OR: [
        { name: { contains: query, mode: 'insensitive' } },
        { description: { contains: query, mode: 'insensitive' } },
        { category: { contains: query, mode: 'insensitive' } }
      ]
    },
    orderBy: { created_at: 'desc' }
  });
};

// Get products by category
export const getProductsByCategory = async (category: string) => {
  return prisma.product.findMany({
    where: { category },
    orderBy: { created_at: 'desc' }
  });
};

// Get trending products (based on sales)
export const getTrendingProducts = async (limit: number = 10) => {
  return prisma.$queryRaw`
    SELECT p.*, 
           COALESCE(SUM(s.quantity), 0) as total_sales,
           COALESCE(AVG(s.sale_price), 0) as avg_sale_price
    FROM product_schema.products p
    LEFT JOIN product_schema.sales s ON p.product_id = s.product_id
    WHERE s.sale_date >= NOW() - INTERVAL '30 days'
    GROUP BY p.product_id
    ORDER BY total_sales DESC
    LIMIT ${limit}
  `;
};
```

### Tạo controller cho products (`src/controllers/productController.ts`):
```typescript
import { Request, Response } from 'express';
import {
  getAllProducts,
  getProductById,
  createProduct,
  updateProduct,
  deleteProduct,
  searchProducts,
  getProductsByCategory,
  getTrendingProducts
} from '../services/productService';
import { Prisma } from '@prisma/client';

// Get all products with pagination
export const getProducts = async (req: Request, res: Response) => {
  try {
    const page = parseInt(req.query.page as string) || 1;
    const limit = parseInt(req.query.limit as string) || 10;
    
    const result = await getAllProducts(page, limit);
    res.json(result);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch products' });
  }
};

// Get product by ID
export const getProduct = async (req: Request, res: Response) => {
  try {
    const id = parseInt(req.params.id);
    const product = await getProductById(id);
    
    if (!product) {
      return res.status(404).json({ error: 'Product not found' });
    }
    
    res.json(product);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch product' });
  }
};

// Create new product
export const createNewProduct = async (req: Request, res: Response) => {
  try {
    const productData: Prisma.ProductCreateInput = req.body;
    const newProduct = await createProduct(productData);
    res.status(201).json(newProduct);
  } catch (error) {
    res.status(500).json({ error: 'Failed to create product' });
  }
};

// Update product
export const updateExistingProduct = async (req: Request, res: Response) => {
  try {
    const id = parseInt(req.params.id);
    const productData: Prisma.ProductUpdateInput = req.body;
    const updatedProduct = await updateProduct(id, productData);
    res.json(updatedProduct);
  } catch (error) {
    res.status(500).json({ error: 'Failed to update product' });
  }
};

// Delete product
export const deleteExistingProduct = async (req: Request, res: Response) => {
  try {
    const id = parseInt(req.params.id);
    await deleteProduct(id);
    res.json({ message: 'Product deleted successfully' });
  } catch (error) {
    res.status(500).json({ error: 'Failed to delete product' });
  }
};

// Search products
export const searchProduct = async (req: Request, res: Response) => {
  try {
    const query = req.query.q as string;
    if (!query) {
      return res.status(400).json({ error: 'Search query is required' });
    }
    
    const results = await searchProducts(query);
    res.json(results);
  } catch (error) {
    res.status(500).json({ error: 'Failed to search products' });
  }
};

// Get products by category
export const getProductsCategory = async (req: Request, res: Response) => {
  try {
    const category = req.params.category;
    const products = await getProductsByCategory(category);
    res.json(products);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch products by category' });
  }
};

// Get trending products
export const getTrending = async (req: Request, res: Response) => {
  try {
    const limit = parseInt(req.query.limit as string) || 10;
    const products = await getTrendingProducts(limit);
    res.json(products);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch trending products' });
  }
};
```

### Tạo routes cho products (`src/routes/productRoutes.ts`):
```typescript
import { Router } from 'express';
import {
  getProducts,
  getProduct,
  createNewProduct,
  updateExistingProduct,
  deleteExistingProduct,
  searchProduct,
  getProductsCategory,
  getTrending
} from '../controllers/productController';

const router = Router();

// Public routes
router.get('/', getProducts);
router.get('/search', searchProduct);
router.get('/category/:category', getProductsCategory);
router.get('/trending', getTrending);

// CRUD routes
router.get('/:id', getProduct);
router.post('/', createNewProduct);
router.put('/:id', updateExistingProduct);
router.delete('/:id', deleteExistingProduct);

export default router;
```

### Cập nhật `package.json` với scripts:
```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/server.js",
    "dev": "nodemon src/server.ts",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate deploy",
    "prisma:studio": "prisma studio"
  }
}
```

---

## 7. Thống kê và Dashboard

### Tạo service cho thống kê (`src/services/analyticsService.ts`):
```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

// Get sales statistics
export const getSalesStatistics = async () => {
  const now = new Date();
  const lastMonth = new Date();
  lastMonth.setMonth(lastMonth.getMonth() - 1);

  const [totalSales, monthlySales, categorySales, recentSales] = await Promise.all([
    // Total sales value and quantity
    prisma.$queryRaw`
      SELECT 
        COALESCE(SUM(sale_price * quantity), 0) as total_value,
        COALESCE(SUM(quantity), 0) as total_quantity,
        COUNT(*) as total_orders
      FROM product_schema.sales
    `,
    
    // Monthly sales trend
    prisma.$queryRaw`
      SELECT 
        DATE_TRUNC('day', sale_date) as sale_day,
        COALESCE(SUM(sale_price * quantity), 0) as daily_value,
        COALESCE(SUM(quantity), 0) as daily_quantity
      FROM product_schema.sales
      WHERE sale_date >= ${lastMonth} AND sale_date <= ${now}
      GROUP BY DATE_TRUNC('day', sale_date)
      ORDER BY sale_day ASC
    `,
    
    // Sales by category
    prisma.$queryRaw`
      SELECT 
        p.category,
        COALESCE(SUM(s.quantity), 0) as total_quantity,
        COALESCE(SUM(s.sale_price * s.quantity), 0) as total_value,
        COUNT(*) as total_orders
      FROM product_schema.sales s
      JOIN product_schema.products p ON s.product_id = p.product_id
      GROUP BY p.category
      ORDER BY total_value DESC
    `,
    
    // Recent sales
    prisma.sale.findMany({
      take: 10,
      orderBy: { sale_date: 'desc' },
      include: {
        product: {
          select: {
            name: true,
            price: true
          }
        }
      }
    })
  ]);

  return {
    total: totalSales[0],
    monthlyTrend: monthlySales,
    byCategory: categorySales,
    recent: recentSales
  };
};

// Get inventory statistics
export const getInventoryStatistics = async () => {
  const [totalProducts, lowStockProducts, categoryDistribution, stockValue] = await Promise.all([
    // Total products and stock
    prisma.$queryRaw`
      SELECT 
        COUNT(*) as total_products,
        COALESCE(SUM(stock), 0) as total_stock,
        COALESCE(SUM(stock * price), 0) as total_value
      FROM product_schema.products
    `,
    
    // Low stock products (less than 10)
    prisma.product.findMany({
      where: { stock: { lt: 10 } },
      orderBy: { stock: 'asc' },
      take: 10
    }),
    
    // Distribution by category
    prisma.$queryRaw`
      SELECT 
        category,
        COUNT(*) as product_count,
        COALESCE(SUM(stock), 0) as total_stock,
        COALESCE(SUM(stock * price), 0) as category_value
      FROM product_schema.products
      GROUP BY category
      ORDER BY category_value DESC
    `,
    
    // Stock value analysis
    prisma.$queryRaw`
      SELECT
        CASE
          WHEN stock = 0 THEN 'Out of Stock'
          WHEN stock < 10 THEN 'Low Stock'
          WHEN stock < 50 THEN 'Medium Stock'
          ELSE 'High Stock'
        END as stock_level,
        COUNT(*) as product_count,
        COALESCE(SUM(stock), 0) as total_stock,
        COALESCE(SUM(stock * price), 0) as total_value
      FROM product_schema.products
      GROUP BY stock_level
      ORDER BY 
        CASE stock_level
          WHEN 'Out of Stock' THEN 1
          WHEN 'Low Stock' THEN 2
          WHEN 'Medium Stock' THEN 3
          ELSE 4
        END
    `
  ]);

  return {
    summary: totalProducts[0],
    lowStock: lowStockProducts,
    byCategory: categoryDistribution,
    stockLevels: stockValue
  };
};

// Get revenue forecast
export const getRevenueForecast = async () => {
  const now = new Date();
  const lastQuarter = new Date();
  lastQuarter.setMonth(lastQuarter.getMonth() - 3);

  // Get historical sales data
  const historicalData = await prisma.$queryRaw`
    SELECT 
      DATE_TRUNC('week', sale_date) as week_start,
      COALESCE(SUM(sale_price * quantity), 0) as weekly_revenue,
      COALESCE(SUM(quantity), 0) as weekly_quantity
    FROM product_schema.sales
    WHERE sale_date >= ${lastQuarter} AND sale_date <= ${now}
    GROUP BY DATE_TRUNC('week', sale_date)
    ORDER BY week_start ASC
  `;

  // Simple moving average for forecast (last 4 weeks)
  const recentWeeks = historicalData.slice(-4);
  const avgRevenue = recentWeeks.reduce((sum, week) => sum + Number(week.weekly_revenue), 0) / 4;
  const avgQuantity = recentWeeks.reduce((sum, week) => sum + Number(week.weekly_quantity), 0) / 4;

  // Forecast next 4 weeks
  const forecast = [];
  const nextWeek = new Date();
  nextWeek.setDate(nextWeek.getDate() + 7);

  for (let i = 0; i < 4; i++) {
    const forecastDate = new Date(nextWeek);
    forecastDate.setDate(nextWeek.getDate() + (i * 7));
    
    forecast.push({
      week_start: forecastDate,
      forecasted_revenue: avgRevenue * (1 + (i * 0.05)), // 5% growth per week
      forecasted_quantity: avgQuantity * (1 + (i * 0.03)) // 3% growth per week
    });
  }

  return {
    historical: historicalData,
    forecast: forecast,
    currentWeek: {
      revenue: recentWeeks.length > 0 ? recentWeeks[recentWeeks.length - 1].weekly_revenue : 0,
      quantity: recentWeeks.length > 0 ? recentWeeks[recentWeeks.length - 1].weekly_quantity : 0
    }
  };
};
```

### Tạo controller cho analytics (`src/controllers/analyticsController.ts`):
```typescript
import { Request, Response } from 'express';
import {
  getSalesStatistics,
  getInventoryStatistics,
  getRevenueForecast
} from '../services/analyticsService';

export const getDashboard = async (req: Request, res: Response) => {
  try {
    const [salesStats, inventoryStats, revenueForecast] = await Promise.all([
      getSalesStatistics(),
      getInventoryStatistics(),
      getRevenueForecast()
    ]);

    res.json({
      sales: salesStats,
      inventory: inventoryStats,
      revenue: revenueForecast,
      timestamp: new Date().toISOString()
    });
  } catch (error) {
    console.error('Error fetching dashboard data:', error);
    res.status(500).json({ error: 'Failed to fetch dashboard data' });
  }
};

export const getSalesReport = async (req: Request, res: Response) => {
  try {
    const startDate = req.query.startDate ? new Date(req.query.startDate as string) : undefined;
    const endDate = req.query.endDate ? new Date(req.query.endDate as string) : new Date();
    
    let whereClause = {};
    if (startDate) {
      whereClause = {
        sale_date: {
          gte: startDate,
          lte: endDate
        }
      };
    }

    const salesData = await getSalesStatistics();
    res.json(salesData);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch sales report' });
  }
};

export const getInventoryReport = async (req: Request, res: Response) => {
  try {
    const inventoryData = await getInventoryStatistics();
    res.json(inventoryData);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch inventory report' });
  }
};
```

### Tạo routes cho analytics (`src/routes/analyticsRoutes.ts`):
```typescript
import { Router } from 'express';
import { getDashboard, getSalesReport, getInventoryReport } from '../controllers/analyticsController';

const router = Router();

router.get('/dashboard', getDashboard);
router.get('/sales', getSalesReport);
router.get('/inventory', getInventoryReport);

export default router;
```

### Cập nhật server để thêm routes analytics (`src/server.ts`):
```typescript
// Thêm import
import analyticsRoutes from './routes/analyticsRoutes';

// Thêm routes sau productRoutes
app.use('/api/analytics', analyticsRoutes);
```

---

## 8. Upload file và lưu vào database

### Cài đặt thêm packages:
```bash
npm install multer @types/multer
```

### Tạo middleware upload (`src/middleware/uploadMiddleware.ts`):
```typescript
import multer from 'multer';
import path from 'path';
import fs from 'fs';

// Ensure upload directory exists
const uploadDir = process.env.UPLOAD_DIR || './uploads';
if (!fs.existsSync(uploadDir)) {
  fs.mkdirSync(uploadDir, { recursive: true });
}

// Configure storage
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, uploadDir);
  },
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    const extension = path.extname(file.originalname);
    const filename = `${file.fieldname}-${uniqueSuffix}${extension}`;
    cb(null, filename);
  }
});

// File filter
const fileFilter = (req: Express.Request, file: Express.Multer.File, cb: multer.FileFilterCallback) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif', 'image/webp'];
  
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new Error('Only image files are allowed!'));
  }
};

// Create upload middleware
export const upload = multer({
  storage,
  fileFilter,
  limits: {
    fileSize: 5 * 1024 * 1024 // 5MB limit
  }
});

// Handle file upload errors
export const handleFileUploadError = (err: any, req: Express.Request, res: Express.Response, next: Express.NextFunction) => {
  if (err instanceof multer.MulterError) {
    // Multer error
    return res.status(400).json({ 
      error: 'File upload error', 
      message: err.message 
    });
  } else if (err) {
    // Other errors
    return res.status(400).json({ 
      error: 'Invalid file', 
      message: err.message 
    });
  }
  next();
};
```

### Cập nhật product service để xử lý upload (`src/services/productService.ts`):
```typescript
// Thêm function để cập nhật ảnh sản phẩm
export const updateProductImage = async (productId: number, imageUrl: string) => {
  return prisma.product.update({
    where: { product_id: productId },
    data: { image_url: imageUrl }
  });
};

// Thêm function để xóa ảnh khi xóa sản phẩm
export const deleteProductWithImage = async (id: number) => {
  const product = await getProductById(id);
  if (!product) {
    throw new Error('Product not found');
  }

  // Xóa file ảnh nếu có
  if (product.image_url && product.image_url.startsWith('/uploads/')) {
    const imagePath = path.join(process.cwd(), product.image_url);
    if (fs.existsSync(imagePath)) {
      fs.unlinkSync(imagePath);
    }
  }

  return deleteProduct(id);
};
```

### Cập nhật product controller (`src/controllers/productController.ts`):
```typescript
import { Request, Response } from 'express';
import { updateProductImage, deleteProductWithImage } from '../services/productService';
import path from 'path';

// Thêm các imports và functions mới
export const uploadProductImage = async (req: Request, res: Response) => {
  try {
    const productId = parseInt(req.params.id);
    const file = req.file;
    
    if (!file) {
      return res.status(400).json({ error: 'No file uploaded' });
    }

    // Create relative URL for the image
    const imageUrl = `/uploads/${file.filename}`;
    
    // Update product with new image URL
    const updatedProduct = await updateProductImage(productId, imageUrl);
    
    res.json({
      success: true,
      product: updatedProduct,
      imageUrl: imageUrl
    });
  } catch (error) {
    console.error('Error uploading product image:', error);
    res.status(500).json({ error: 'Failed to upload product image' });
  }
};

export const deleteProductWithImageController = async (req: Request, res: Response) => {
  try {
    const id = parseInt(req.params.id);
    await deleteProductWithImage(id);
    res.json({ message: 'Product and image deleted successfully' });
  } catch (error) {
    console.error('Error deleting product with image:', error);
    res.status(500).json({ error: 'Failed to delete product' });
  }
};
```

### Cập nhật product routes (`src/routes/productRoutes.ts`):
```typescript
import { Router } from 'express';
import { upload, handleFileUploadError } from '../middleware/uploadMiddleware';
import { uploadProductImage, deleteProductWithImageController } from '../controllers/productController';

// Thêm routes mới
router.post('/:id/upload-image', upload.single('image'), handleFileUploadError, uploadProductImage);
router.delete('/:id/with-image', deleteProductWithImageController);
```

### Tạo static file serving để xem ảnh đã upload (`src/server.ts`):
```typescript
import express from 'express';
import path from 'path';

// Thêm sau các middleware khác
app.use('/uploads', express.static(path.join(__dirname, '../uploads')));
```

---

## 9. Triển khai Frontend đơn giản

### Cài đặt các packages cho frontend:
```bash
# Tạo thư mục frontend
mkdir frontend
cd frontend

# Khởi tạo React app
npx create-react-app .
npm install axios react-router-dom @mui/material @emotion/react @emotion/styled @mui/icons-material @mui/x-data-grid react-icons
```

### Tạo cấu trúc frontend:
```bash
mkdir src/components
mkdir src/pages
mkdir src/services
mkdir src/hooks
mkdir src/utils
```

### Tạo API service (`src/services/api.js`):
```javascript
import axios from 'axios';

const API_URL = 'http://localhost:3001/api';

// Create axios instance
const api = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
  timeout: 10000 // 10 seconds
});

// Response interceptor
api.interceptors.response.use(
  response => response,
  error => {
    console.error('API Error:', error.response?.data || error.message);
    return Promise.reject(error);
  }
);

// Product API
export const productAPI = {
  getAll: (params = {}) => api.get('/products', { params }),
  getById: (id) => api.get(`/products/${id}`),
  create: (data) => api.post('/products', data),
  update: (id, data) => api.put(`/products/${id}`, data),
  delete: (id) => api.delete(`/products/${id}`),
  search: (query) => api.get('/products/search', { params: { q: query } }),
  getByCategory: (category) => api.get(`/products/category/${category}`),
  getTrending: (limit = 10) => api.get('/products/trending', { params: { limit } }),
  uploadImage: (id, file) => {
    const formData = new FormData();
    formData.append('image', file);
    return api.post(`/products/${id}/upload-image`, formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    });
  }
};

// Analytics API
export const analyticsAPI = {
  getDashboard: () => api.get('/analytics/dashboard'),
  getSalesReport: (params = {}) => api.get('/analytics/sales', { params }),
  getInventoryReport: () => api.get('/analytics/inventory')
};

// Auth API (placeholder)
export const authAPI = {
  login: (credentials) => api.post('/auth/login', credentials),
  logout: () => api.post('/auth/logout')
};

export default api;
```

### Tạo Dashboard component (`src/pages/Dashboard.js`):
```jsx
import React, { useState, useEffect } from 'react';
import { 
  Grid, 
  Card, 
  CardContent, 
  Typography, 
  Box, 
  CircularProgress,
  Alert
} from '@mui/material';
import { 
  TrendingUp, 
  Inventory, 
  AttachMoney, 
  ShoppingCart,
  Warning
} from '@mui/icons-material';
import { analyticsAPI } from '../services/api';
import ProductCard from '../components/ProductCard';
import ChartComponent from '../components/ChartComponent';

const Dashboard = () => {
  const [dashboardData, setDashboardData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await analyticsAPI.getDashboard();
        setDashboardData(response.data);
        setError(null);
      } catch (err) {
        setError('Failed to load dashboard data. Please try again later.');
        console.error('Dashboard error:', err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
    // Refresh data every 30 seconds
    const interval = setInterval(fetchData, 30000);
    return () => clearInterval(interval);
  }, []);

  if (loading) {
    return (
      <Box sx={{ display: 'flex', justifyContent: 'center', p: 4 }}>
        <CircularProgress />
      </Box>
    );
  }

  if (error || !dashboardData) {
    return (
      <Alert severity="error" sx={{ m: 2 }}>
        {error || 'No dashboard data available'}
      </Alert>
    );
  }

  const { sales, inventory, revenue } = dashboardData;

  return (
    <Box sx={{ p: 3 }}>
      <Typography variant="h4" gutterBottom>
        Dashboard Overview
      </Typography>
      
      {error && (
        <Alert severity="error" sx={{ mb: 2 }}>
          {error}
        </Alert>
      )}

      <Grid container spacing={3}>
        {/* Summary Cards */}
        <Grid item xs={12} md={3}>
          <Card sx={{ bgcolor: '#e8f5e9' }}>
            <CardContent>
              <Box sx={{ display: 'flex', alignItems: 'center', mb: 1 }}>
                <TrendingUp color="success" sx={{ fontSize: 24, mr: 1 }} />
                <Typography variant="subtitle1">Total Revenue</Typography>
              </Box>
              <Typography variant="h6">
                ₫{Number(sales.total.total_value).toLocaleString('vi-VN')}
              </Typography>
              <Typography variant="body2" color="text.secondary">
                from {sales.total.total_orders} orders
              </Typography>
            </CardContent>
          </Card>
        </Grid>

        <Grid item xs={12} md={3}>
          <Card sx={{ bgcolor: '#e3f2fd' }}>
            <CardContent>
              <Box sx={{ display: 'flex', alignItems: 'center', mb: 1 }}>
                <ShoppingCart color="primary" sx={{ fontSize: 24, mr: 1 }} />
                <Typography variant="subtitle1">Total Sales</Typography>
              </Box>
              <Typography variant="h6">
                {Number(sales.total.total_quantity).toLocaleString()}
              </Typography>
              <Typography variant="body2" color="text.secondary">
                items sold
              </Typography>
            </CardContent>
          </Card>
        </Grid>

        <Grid item xs={12} md={3}>
          <Card sx={{ bgcolor: '#fff8e1' }}>
            <CardContent>
              <Box sx={{ display: 'flex', alignItems: 'center', mb: 1 }}>
                <Inventory color="warning" sx={{ fontSize: 24, mr: 1 }} />
                <Typography variant="subtitle1">Total Stock</Typography>
              </Box>
              <Typography variant="h6">
                {Number(inventory.summary.total_stock).toLocaleString()}
              </Typography>
              <Typography variant="body2" color="text.secondary">
                items in stock
              </Typography>
            </CardContent>
          </Card>
        </Grid>

        <Grid item xs={12} md={3}>
          <Card sx={{ bgcolor: '#fce4ec' }}>
            <CardContent>
              <Box sx={{ display: 'flex', alignItems: 'center', mb: 1 }}>
                <AttachMoney color="error" sx={{ fontSize: 24, mr: 1 }} />
                <Typography variant="subtitle1">Stock Value</Typography>
              </Box>
              <Typography variant="h6">
                ₫{Number(inventory.summary.total_value).toLocaleString('vi-VN')}
              </Typography>
              <Typography variant="body2" color="text.secondary">
                total inventory value
              </Typography>
            </CardContent>
          </Card>
        </Grid>

        {/* Charts */}
        <Grid item xs={12} md={8}>
          <Card>
            <CardContent>
              <Typography variant="h6" gutterBottom>
                Revenue Trend (Last 30 Days)
              </Typography>
              <ChartComponent 
                type="line" 
                data={sales.monthlyTrend} 
                xField="sale_day" 
                yField="daily_value"
                title="Daily Revenue"
              />
            </CardContent>
          </Card>
        </Grid>

        <Grid item xs={12} md={4}>
          <Card>
            <CardContent>
              <Typography variant="h6" gutterBottom>
                Sales by Category
              </Typography>
              <ChartComponent 
                type="pie" 
                data={sales.byCategory} 
                labelField="category" 
                valueField="total_value"
                title="Category Distribution"
              />
            </CardContent>
          </Card>
        </Grid>

        {/* Low Stock Products */}
        <Grid item xs={12}>
          <Card>
            <CardContent>
              <Box sx={{ display: 'flex', justifyContent: 'space-between', mb: 2 }}>
                <Typography variant="h6">
                  Low Stock Products
                </Typography>
                {inventory.lowStock.length > 0 && (
                  <Box sx={{ display: 'flex', alignItems: 'center', color: 'error.main' }}>
                    <Warning sx={{ mr: 1 }} />
                    <Typography variant="body2">
                      {inventory.lowStock.length} products need attention
                    </Typography>
                  </Box>
                )}
              </Box>
              
              {inventory.lowStock.length === 0 ? (
                <Typography color="text.secondary">
                  No low stock products
                </Typography>
              ) : (
                <Grid container spacing={2}>
                  {inventory.lowStock.map(product => (
                    <Grid item xs={12} sm={6} md={4} key={product.product_id}>
                      <ProductCard 
                        product={product} 
                        showStockWarning={true}
                        onAction={(action) => console.log(action, product)}
                      />
                    </Grid>
                  ))}
                </Grid>
              )}
            </CardContent>
          </Card>
        </Grid>

        {/* Recent Sales */}
        <Grid item xs={12}>
          <Card>
            <CardContent>
              <Typography variant="h6" gutterBottom>
                Recent Sales
              </Typography>
              
              {sales.recent.length === 0 ? (
                <Typography color="text.secondary">
                  No recent sales
                </Typography>
              ) : (
                <Box sx={{ overflowX: 'auto' }}>
                  <table style={{ width: '100%', borderCollapse: 'collapse' }}>
                    <thead>
                      <tr>
                        <th style={{ textAlign: 'left', padding: '8px', borderBottom: '1px solid #ddd' }}>Time</th>
                        <th style={{ textAlign: 'left', padding: '8px', borderBottom: '1px solid #ddd' }}>Product</th>
                        <th style={{ textAlign: 'right', padding: '8px', borderBottom: '1px solid #ddd' }}>Quantity</th>
                        <th style={{ textAlign: 'right', padding: '8px', borderBottom: '1px solid #ddd' }}>Price</th>
                        <th style={{ textAlign: 'right', padding: '8px', borderBottom: '1px solid #ddd' }}>Total</th>
                        <th style={{ textAlign: 'left', padding: '8px', borderBottom: '1px solid #ddd' }}>Customer</th>
                      </tr>
                    </thead>
                    <tbody>
                      {sales.recent.map(sale => (
                        <tr key={sale.sale_id}>
                          <td style={{ padding: '8px', borderBottom: '1px solid #eee' }}>
                            {new Date(sale.sale_date).toLocaleTimeString('vi-VN')}
                          </td>
                          <td style={{ padding: '8px', borderBottom: '1px solid #eee' }}>
                            {sale.product.name}
                          </td>
                          <td style={{ padding: '8px', textAlign: 'right', borderBottom: '1px solid #eee' }}>
                            {sale.quantity}
                          </td>
                          <td style={{ padding: '8px', textAlign: 'right', borderBottom: '1px solid #eee' }}>
                            ₫{Number(sale.sale_price).toLocaleString('vi-VN')}
                          </td>
                          <td style={{ padding: '8px', textAlign: 'right', borderBottom: '1px solid #eee' }}>
                            ₫{Number(sale.sale_price * sale.quantity).toLocaleString('vi-VN')}
                          </td>
                          <td style={{ padding: '8px', borderBottom: '1px solid #eee' }}>
                            {sale.customer_name || 'Anonymous'}
                          </td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </Box>
              )}
            </CardContent>
          </Card>
        </Grid>
      </Grid>
    </Box>
  );
};

export default Dashboard;
```

---

## 10. Kiểm thử toàn bộ hệ thống

### Chạy backend:
```bash
# Trong thư mục backend
npm run dev
```

### Chạy frontend:
```bash
# Trong thư mục frontend
npm start
```

### Kiểm tra các chức năng:

1. **Database Connection**: 
   - Mở http://localhost:3001/api/health
   - Kiểm tra connection bằng Prisma Studio: `npx prisma studio`

2. **CRUD Operations**:
   - GET http://localhost:3001/api/products
   - POST http://localhost:3001/api/products (với body JSON)
   - PUT http://localhost:3001/api/products/1
   - DELETE http://localhost:3001/api/products/1

3. **Thống kê Dashboard**:
   - GET http://localhost:3001/api/analytics/dashboard
   - Xem kết quả trên frontend dashboard

4. **Upload File**:
   - POST http://localhost:3001/api/products/1/upload-image (với form-data chứa file)

5. **Frontend UI**:
   - Mở http://localhost:3000
   - Kiểm tra tất cả các trang và chức năng

### Docker Compose hoàn chỉnh (`docker-compose.yml`):
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    container_name: postgres-tutorial
    environment:
      POSTGRES_DB: product_management
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
      PGDATA: /data/postgres
    volumes:
      - postgres-data:/data/postgres
    ports:
      - "5432:5432"
    networks:
      - app-network
    restart: unless-stopped

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin-tutorial
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin123
    volumes:
      - pgadmin-data:/var/lib/pgadmin
    ports:
      - "5050:80"
    networks:
      - app-network
    restart: unless-stopped

  backend:
    build: ./product-management-backend
    container_name: backend-app
    ports:
      - "3001:3001"
    environment:
      DATABASE_URL: postgresql://admin:admin123@postgres:5432/product_management?schema=product_schema
      PORT: 3001
      UPLOAD_DIR: /app/uploads
    volumes:
      - ./product-management-backend:/app
      - ./product-management-backend/uploads:/app/uploads
    networks:
      - app-network
    depends_on:
      - postgres
    restart: unless-stopped

  frontend:
    build: ./frontend
    container_name: frontend-app
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    networks:
      - app-network
    restart: unless-stopped

volumes:
  postgres-data:
  pgadmin-data:

networks:
  app-network:
    driver: bridge
```

---

## 🎯 Tổng kết và hướng phát triển tiếp theo

### Những gì bạn đã học được:
✅ **Database Setup**: Cài đặt PostgreSQL trên Docker, kết nối với DBeaver  
✅ **Data Modeling**: Thiết kế schema, tạo tables, indexes, triggers  
✅ **Backend Development**: Node.js/Express với TypeScript, Prisma ORM  
✅ **CRUD Operations**: Xây dựng API đầy đủ cho quản lý sản phẩm  
✅ **Analytics**: Thống kê doanh số, hàng tồn kho, dự báo doanh thu  
✅ **File Upload**: Xử lý upload file và lưu vào database  
✅ **Frontend Integration**: React frontend kết nối với backend API  
✅ **Deployment**: Dockerize toàn bộ ứng dụng  

### Hướng phát triển tiếp theo:
🚀 **Authentication & Authorization**: Thêm JWT authentication, role-based access  
🚀 **Advanced Analytics**: Machine learning để dự báo xu hướng  
🚀 **Real-time Updates**: WebSocket cho dashboard real-time  
🚀 **Mobile App**: Phát triển app di động với React Native  
🚀 **CI/CD Pipeline**: Tự động hóa deployment với GitHub Actions  
🚀 **Monitoring**: Thêm logging, error tracking với Sentry  

### Tài nguyên tham khảo:
📚 [Prisma Documentation](https://www.prisma.io/docs)  
📚 [PostgreSQL Official Docs](https://www.postgresql.org/docs/)  
📚 [React + TypeScript Best Practices](https://react-typescript-cheatsheet.netlify.app/)  
📚 [Docker Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

**Chúc bạn thành công với project database của mình!** 🎉