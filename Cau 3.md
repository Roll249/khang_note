### **1. Khái niệm chung**

- **Thực hiện kiểm thử (Test Implementation)**: Là giai đoạn chuẩn bị các điều kiện cần thiết để tiến hành kiểm thử, bao gồm việc xây dựng môi trường kiểm thử, tạo dữ liệu kiểm thử, viết kịch bản kiểm thử và chuẩn bị công cụ hỗ trợ.
- **Thực thi kiểm thử (Test Execution)**: Là quá trình chạy các ca kiểm thử (test cases) đã được thiết kế trước đó trên phần mềm đang được kiểm thử, ghi nhận kết quả và so sánh với kết quả mong đợi.

Hai hoạt động này thường diễn ra liên tiếp và có thể lặp lại nhiều lần trong suốt vòng đời phát triển phần mềm.

---

### **2. Nội dung chi tiết**

#### **2.1. Thực hiện kiểm thử (Test Implementation)**

Các hoạt động chính bao gồm:

- **Xây dựng môi trường kiểm thử (Test Environment Setup)**:
  - Cài đặt hệ điều hành, cơ sở dữ liệu, máy chủ, mạng… phù hợp với yêu cầu của hệ thống.
  - Đảm bảo môi trường kiểm thử tách biệt với môi trường phát triển và sản xuất.

- **Tạo dữ liệu kiểm thử (Test Data Preparation)**:
  - Dữ liệu hợp lệ, không hợp lệ, biên, cực trị…
  - Có thể sinh tự động hoặc trích xuất từ hệ thống thật (sau khi ẩn danh).

- **Viết/Chuẩn bị kịch bản kiểm thử (Test Script/Test Case Development)**:
  - Dựa trên đặc tả yêu cầu và thiết kế kiểm thử.
  - Có thể là thủ công hoặc tự động (dùng ngôn ngữ lập trình hoặc công cụ như Selenium, JUnit…).

- **Triển khai công cụ kiểm thử (Test Tool Deployment)**:
  - Chọn và cấu hình công cụ hỗ trợ kiểm thử chức năng, hiệu năng, bảo mật…

- **Sắp xếp thứ tự thực thi các ca kiểm thử (Test Suite Organization)**:
  - Nhóm các test case theo module, mức độ ưu tiên, loại kiểm thử…

#### **2.2. Thực thi kiểm thử (Test Execution)**

Các bước chính:

- **Chạy các ca kiểm thử**:
  - Theo thứ tự đã lên kế hoạch (thường ưu tiên ca kiểm thử quan trọng trước).
  - Ghi lại kết quả: **Pass** (đạt), **Fail** (không đạt), **Blocked** (bị chặn do lỗi hệ thống khác)…

- **So sánh kết quả thực tế với kết quả mong đợi**:
  - Nếu khác nhau → ghi nhận lỗi (defect/bug).

- **Ghi nhận và theo dõi lỗi**:
  - Sử dụng hệ thống quản lý lỗi (Jira, Bugzilla…).
  - Bao gồm thông tin: mô tả lỗi, bước tái hiện, ảnh chụp màn hình, mức độ nghiêm trọng…

- **Thực hiện lại kiểm thử (Retesting & Regression Testing)**:
  - **Retesting**: Kiểm tra lại lỗi sau khi đã được sửa.
  - **Regression Testing**: Đảm bảo việc sửa lỗi không làm hỏng chức năng khác.

- **Cập nhật trạng thái kiểm thử**:
  - Cập nhật tiến độ, tỷ lệ pass/fail, độ bao phủ…

---

### **3. Một số nguyên tắc quan trọng**

- **Tính lặp lại (Repeatability)**: Cùng một test case phải cho cùng kết quả nếu điều kiện không đổi.
- **Tính độc lập**: Mỗi test case nên độc lập để dễ xác định nguyên nhân lỗi.
- **Tự động hóa khi có thể**: Giúp tiết kiệm thời gian, tăng độ chính xác, đặc biệt trong regression testing.
- **Ưu tiên theo rủi ro**: Tập trung vào các chức năng quan trọng hoặc dễ xảy ra lỗi.

---

### **4. Kết luận**

Giai đoạn **Thực hiện và Thực thi kiểm thử** đóng vai trò then chốt trong việc phát hiện lỗi sớm, đảm bảo chất lượng phần mềm trước khi bàn giao cho khách hàng. Việc tổ chức tốt hai hoạt động này giúp nâng cao hiệu quả kiểm thử, giảm chi phí bảo trì và tăng sự tin cậy của sản phẩm.
