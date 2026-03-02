# Cổng Hadamard (H) — Giải thích chi tiết từ bản chất đến ứng dụng

> Tài liệu này được biên soạn lại dựa trên các ý tưởng cốt lõi thường gặp trong tài liệu về Hadamard gate, diễn giải mới hoàn toàn để học tập.

## 1) Vì sao cổng H quan trọng?

Trong mạch lượng tử, cổng `H` thường là “cửa vào” của thế giới lượng tử vì nó:

1. đưa trạng thái xác định (`|0⟩` hoặc `|1⟩`) thành chồng chập,
2. mở đường cho **giao thoa** (interference),
3. giúp thuật toán lượng tử chuyển đổi giữa hai cơ sở đo quan trọng: cơ sở `Z` và cơ sở `X`.

Không có H, nhiều mạch sẽ chỉ giống mạch xác suất cổ điển hơn là mạch lượng tử mạnh đúng nghĩa.

---

## 2) Lịch sử ngắn gọn

Tên “Hadamard” đến từ nhà toán học Jacques Hadamard và họ ma trận $
\pm 1$ nổi tiếng trong đại số tuyến tính. Ý tưởng đó sau này đi vào tính toán cổ điển qua Walsh-Hadamard transform, rồi trở thành một cổng cốt lõi của tính toán lượng tử.

---

## 3) Định nghĩa toán học của cổng H

Trong cơ sở tính toán $\{|0\rangle, |1\rangle\}$:

$$
H = \frac{1}{\sqrt{2}}
\begin{bmatrix}
1 & 1 \\
1 & -1
\end{bmatrix}
$$

Tác dụng lên hai trạng thái cơ sở:

$$
H|0\rangle = \frac{|0\rangle + |1\rangle}{\sqrt{2}} = |+\rangle
$$

$$
H|1\rangle = \frac{|0\rangle - |1\rangle}{\sqrt{2}} = |-\rangle
$$

Điểm cực kỳ quan trọng: dấu `+` và `-` là **pha tương đối**, không phải trang trí. Chính pha tạo ra giao thoa tăng cường/triệt tiêu.

---

## 4) H là cổng tự nghịch đảo: vì sao `H·H = I`?

Vì:

$$
H^2 = I
$$

Nên áp dụng H hai lần sẽ quay về trạng thái ban đầu (bỏ qua global phase). Ví dụ:

$$
H(H|0\rangle)=|0\rangle,
\quad
H(H|1\rangle)=|1\rangle
$$

Đây là lý do trong bài `foundations05b`, nếu mạch là `H -> H` (và không đo ở giữa), phân bố cuối quay về trạng thái gốc.

---

## 5) Đổi cơ sở: H nối giữa Z-basis và X-basis

- `Z-basis`: $|0\rangle, |1\rangle$
- `X-basis`: $|+\rangle, |-\rangle$

H làm nhiệm vụ “đổi ngôn ngữ” giữa hai cơ sở:

$$
H Z H = X, \quad H X H = Z
$$

Ý nghĩa thực tế: có thể biến một phép đo/điều khiển theo trục này sang trục kia bằng “kẹp Hadamard” hai bên.

---

## 6) Trực giác Bloch sphere

### 6.1 Trục và điểm
- $|0\rangle$: cực bắc (+Z)
- $|1\rangle$: cực nam (-Z)
- $|+\rangle$: +X (xích đạo)
- $|-\rangle$: -X

### 6.2 H là phép quay gì?
H tương đương (đến global phase) với quay góc $\pi$ quanh trục nằm chéo giữa X và Z:

$$
\hat n = \frac{\hat x + \hat z}{\sqrt{2}}
$$

Có thể phân rã (đến phase toàn cục) thành các chuỗi quay tiêu chuẩn, ví dụ dạng $R_y$ và $R_z$ hoặc $R_x$ tùy compiler/hardware.

---

## 7) Vì sao “H là 50%-50%” vừa đúng vừa chưa đủ?

Đúng ở mức đo trong cơ sở Z:
- từ `|0⟩` qua H rồi đo ngay sẽ cho 50% `0`, 50% `1`.

Nhưng chưa đủ vì:
- H không chỉ tạo xác suất, mà tạo **biên độ có pha**.
- Trước khi đo, ta phải cộng **biên độ**; chỉ khi đo mới bình phương biên độ để ra xác suất.

Đó là lý do câu hỏi phổ biến “đã 50/50 rồi qua H nữa sao không vẫn 50/50?” thường sai khi **không có đo ở giữa**.

---

## 8) Giao thoa: cốt lõi của lợi thế lượng tử

Mạch một qubit:

1. H đầu tiên tách thành hai “nhánh biên độ”.
2. Giữa mạch có thể gắn pha (ví dụ Z thêm dấu `-` cho một thành phần).
3. H thứ hai trộn lại để nhánh này tăng cường, nhánh kia triệt tiêu.

Ví dụ:

$$
H Z H = X
$$

Nên nếu đầu vào `|0⟩`, mạch `H-Z-H` cho `|1⟩` chắc chắn.

---

## 9) Vai trò trong thuật toán lượng tử

Cổng H xuất hiện liên tục trong các thuật toán nổi tiếng vì nó:

- tạo chồng chập đều trên không gian trạng thái,
- làm khối trộn để đọc thông tin pha thành xác suất đo,
- hỗ trợ các bước “query in superposition + interfere to extract global property”.

Ví dụ nơi H rất thường gặp:
- Deutsch / Deutsch-Jozsa,
- Grover (khởi tạo và diffusion),
- Simon,
- Shor (liên quan đến QFT và các bước trộn/đổi cơ sở),
- Hadamard test.

---

## 10) Ví dụ mạch kinh điển: tạo Bell pair

Bắt đầu từ `|00⟩`:

1. áp dụng H lên qubit 1:
$$
|00\rangle \to \frac{|00\rangle + |10\rangle}{\sqrt{2}}
$$
2. áp dụng CNOT (qubit 1 điều khiển qubit 2):
$$
\frac{|00\rangle + |10\rangle}{\sqrt{2}} \to \frac{|00\rangle + |11\rangle}{\sqrt{2}}
$$

Ta thu được trạng thái Bell vướng víu.

---

## 11) Triển khai trên phần cứng (khái quát)

Tuỳ công nghệ, H có thể được biên dịch thành chuỗi cổng gốc khác nhau:

- **Siêu dẫn (transmon):** chuỗi xung vi ba tương đương các quay trục.
- **Ion trap:** xung laser theo pha/thời lượng phù hợp.
- **Quang học:** phần tử quang như half-wave plate ở góc thích hợp hoặc cấu hình beam splitter tương đương.

Về mặt phần mềm, framework thường cho dùng H trực tiếp, rồi compiler tự hạ xuống native gates.

---

## 12) Góc nhìn nhiều qubit: Walsh–Hadamard transform

Áp dụng H lên từng qubit tạo biến đổi tensor:

$$
H^{\otimes n}
$$

Nó trộn biên độ toàn bộ không gian $2^n$ trạng thái theo cấu trúc dấu `±`, và cũng tự nghịch đảo:

$$
(H^{\otimes n})^2 = I
$$

Đây là một lý do toán học khiến H cực kỳ hữu dụng cho khởi tạo và “uncompute”.

---

## 13) Hiểu đúng về đo giữa mạch

Hai thí nghiệm khác nhau hoàn toàn:

### A) Không đo ở giữa
`H -> H` trên `|0⟩` cho ra `|0⟩` chắc chắn.

### B) Có đo ở giữa
`H -> Measure -> H -> Measure` thì lần đo cuối lại 50/50.

Nguyên nhân: phép đo làm sụp đổ trạng thái, phá thông tin pha cần cho giao thoa ở bước sau.

---

## 14) Tóm tắt “mang đi dùng ngay”

- H là cổng đổi cơ sở Z ↔ X.
- H tạo chồng chập **kèm pha** (không chỉ xác suất).
- `H^2 = I`, nên H có thể dùng để “đi ra” rồi “đi vào” lại cùng cơ sở.
- Giao thoa nhờ H là trung tâm của nhiều lợi thế thuật toán lượng tử.

---

## 15) Bài tập tự kiểm tra nhanh

1. Chứng minh trực tiếp bằng nhân ma trận rằng $H^2 = I$.
2. Tính $H X H$ và $H Z H$.
3. So sánh hai mạch:
   - `|0⟩ -> H -> H -> Measure`
   - `|0⟩ -> H -> Measure -> H -> Measure`
4. Với đầu vào `|1⟩`, dự đoán đầu ra của `H -> Z -> H`.

---

## 16) Gợi ý liên hệ với file mô phỏng của bạn

Trong file [doubleH.html](doubleH.html), khi để mặc định `H` ở moment 1 và 2:
- Nếu input là `0`, report sẽ nghiêng về `|0⟩` gần 100%.
- Nếu thêm `X` trước hoặc giữa các cổng, bạn sẽ thấy phân bố đổi theo đúng các đồng nhất thức ở trên.

Bạn có thể dùng chính mô phỏng đó để kiểm tra lại các công thức mục 3, 4, 8, 13.
