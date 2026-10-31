# CẨM NANG LÝ THUYẾT: CÁC MÔ HÌNH KIẾN TRÚC TỔ CHỨC THƯ MỤC TRONG DỰ ÁN REACT
> **Chủ đề:** Phân tích chuyên sâu về Grouping by File Type, Feature-based Architecture và Atomic Design trong phát triển ứng dụng Frontend hiện đại.  
> **Người biên soạn:** Senior Tech Lead & Mentor Đào tạo Lập trình.

---

<a id="muc-luc"></a>
## 📑 Mục lục (Table of Contents)

1. [Tổng quan: Bản chất của việc Quy hoạch Thư mục Dự án](#1-tong-quan-ban-chat-cua-viec-quy-hoach-thu-muc-du-an)
2. [Mô hình 1: Grouping by File Type (Technical Role-based / Horizontal Layering)](#2-mo-hinh-1-grouping-by-file-type-technical-role-based--horizontal-layering)
   - [2.1. Định nghĩa & Các thuật ngữ đồng nghĩa](#21-dinh-nghia--cac-thuat-ngu-dong-nghia)
   - [2.2. Ý nghĩa & Trách nhiệm của từng thư mục lõi (components, hooks, utils, services)](#22-y-nghia--trach-nhiem-cua-tung-thu-muc-loi)
   - [2.3. Đánh giá ưu điểm & hạn chế](#23-danh-gia-uu-diem--han-che)
3. [Mô hình 2: Feature-based Architecture (Vertical Slicing / Domain-Driven)](#3-mo-hinh-2-feature-based-architecture-vertical-slicing--domain-driven)
   - [3.1. Định nghĩa & Triết lý cắt lát theo chiều dọc](#31-dinh-nghia--triet-ly-cat-lat-theo-chieu-doc)
   - [3.2. Cấu trúc thư mục mẫu cấp Doanh nghiệp (Enterprise)](#32-cau-truc-thu-muc-mau-cap-doanh-nghiep-enterprise)
   - [3.3. Đánh giá ưu điểm & hạn chế](#33-danh-gia-uu-diem--han-che)
4. [Mô hình 3: Atomic Design Pattern trong thiết kế UI Component](#4-mo-hinh-3-atomic-design-pattern-trong-thiet-ke-ui-component)
   - [4.1. 5 cấp độ tiến hóa: Atoms -> Molecules -> Organisms -> Templates -> Pages](#41-5-cap-do-tien-hoa)
5. [Bảng Ma trận So sánh Đối chiếu Toàn diện (Comparative Matrix)](#5-bang-ma-tran-so-sanh-doi-chieu-toan-dien)
6. [Sổ tay Quy ước Đặt tên & Lỗi chính tả kinh điển (Lessons Learned)](#6-so-tay-quy-uoc-dat-ten--loi-chinh-ta-kinh-dien)

---

<a id="1-tong-quan-ban-chat-cua-viec-quy-hoach-thu-muc-du-an"></a>
## 1. Tổng quan: Bản chất của việc Quy hoạch Thư mục Dự án

Trong thế giới kỹ thuật phần mềm, cấu trúc thư mục không chỉ đơn thuần là "chỗ để cất file". Nó phản ánh **Tư duy Kiến trúc (Architectural Thinking)** của người lập trình:
- Giúp các thành viên trong nhóm biết chính xác **file mới cần tạo nằm ở đâu** mà không cần hỏi người khác.
- Giảm thiểu hiện tượng **Spaghetti Code** (mã nguồn rối như mỳ Ý) khi dự án tăng từ 5 file lên 500 file.
- Tối ưu hóa nguyên lý **Separation of Concerns (Phân tách Trách nhiệm)**: Mỗi file chỉ làm tốt đúng một nhiệm vụ duy nhất.

```text
[ CÂY THƯ MỤC TỐT ] ──> Dễ tìm kiếm ──> Dễ bảo trì ──> Dễ mở rộng (Scalability)
[ CÂY THƯ MỤC XẤU ] ──> Sửa 1 chỗ hỏng 3 chỗ ──> Khó bàn giao ──> Tốn chi phí bảo trì
```

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="2-mo-hinh-1-grouping-by-file-type-technical-role-based--horizontal-layering"></a>
## 2. Mô hình 1: Grouping by File Type (Technical Role-based / Horizontal Layering)

Đây chính là mô hình bạn đang xây dựng cho dự án `reactbuoi1`.

<a id="21-dinh-nghia--cac-thuat-ngu-dong-nghia"></a>
### 2.1. Định nghĩa & Các thuật ngữ đồng nghĩa

* **Grouping by File Type / Grouping by Nature:** Gom nhóm theo tính chất kỹ thuật của file. Tất cả các file cùng loại kỹ thuật sẽ nằm chung một mái nhà.
* **Technical Role-based Architecture:** Kiến trúc phân loại theo vai trò kỹ thuật trong hệ sinh thái React.
* **Horizontal Layering (Phân tầng theo chiều ngang):** Dự án được chia thành các tầng kỹ thuật nằm ngang xếp chồng lên nhau:
  ```text
  ┌─────────────────────────────────────────────────────────┐
  │ TẦNG HIỂN THỊ (Presentation Layer): src/components/     │
  ├─────────────────────────────────────────────────────────┤
  │ TẦNG LOGIC TÁI SỬ DỤNG (Business Hooks): src/hooks/     │
  ├─────────────────────────────────────────────────────────┤
  │ TẦNG GIAO THỨC MẠNG (Data Access): src/services/        │
  ├─────────────────────────────────────────────────────────┤
  │ TẦNG HÀM BỔ TRỢ (Utility Layer): src/utils/             │
  └─────────────────────────────────────────────────────────┘
  ```

---

<a id="22-y-nghia--trach-nhiem-cua-tung-thu-muc-loi"></a>
### 2.2. Ý nghĩa & Trách nhiệm của từng thư mục lõi

```text
src/
├── 📁 components/   <-- Tầng Giao diện (Presentation)
├── 📁 hooks/        <-- Tầng Logic Trạng thái (Custom Hooks)
├── 📁 utils/        <-- Tầng Tiện ích thuần túy (Helper Functions)
├── 📁 services/     <-- Tầng Mạng & API (Data Layer)
├── 📁 assets/       <-- Tầng Tài nguyên tĩnh (Images, SVGs, Fonts)
├── 📁 contexts/     <-- Tầng Quản lý State toàn cục (React Context)
├── 📄 App.jsx       <-- Component Vỏ ngoài / Điều hướng
├── 📄 index.css     <-- Cấu hình Style toàn cục
└── 📄 main.jsx      <-- Cổng nạp gốc
```

#### Chi tiết vai trò từng thư mục:
1. **`components/` (Component UI):**
   - **Chỉ làm:** Dựng khung JSX, trang trí CSS/Tailwind, nhận props và render giao diện.
   - **Ví dụ:** `Button.jsx`, `Input.jsx`, `RegisterForm.jsx`, `Navbar.jsx`.
2. **`hooks/` (Custom Hooks):**
   - **Chỉ làm:** Đóng gói các logic tái sử dụng có sử dụng hook React (`useState`, `useEffect`, `useCallback`...). Tên file **bắt buộc** bắt đầu bằng tiền tố `use`.
   - **Ví dụ:** `useDebounce.js` (hoãn tìm kiếm), `useAuth.js` (kiểm tra đăng nhập), `useToggle.js`.
3. **`utils/` (Utilities / Helpers):**
   - **Chỉ làm:** Chứa các hàm JavaScript thuần túy (Pure Functions), **không chứa JSX**, **không gọi React Hook**. Đầu vào là dữ liệu $\rightarrow$ Đầu ra là kết quả tính toán.
   - **Ví dụ:** `formatCurrency(200000)` $\rightarrow$ `"200.000 ₫"`, `validateEmail(str)` $\rightarrow$ `true/false`, `formatDate(timestamp)`.
4. **`services/` (API Services):**
   - **Chỉ làm:** Độc lập hoàn toàn với React UI. Chịu trách nhiệm cấu hình đường dẫn API base URL, gắn Headers, gửi `fetch` hoặc `axios` và trả về JSON.
   - **Ví dụ:** `authService.js`, `userService.js`, `paymentService.js`.

---

<a id="23-danh-gia-uu-diem--han-che"></a>
### 2.3. Đánh giá ưu điểm & hạn chế

* ✅ **Ưu điểm:**
  - Rất trực quan, tư duy ngăn nắp, người mới học nhìn vào hiểu ngay.
  - Cực kỳ thích hợp cho các dự án quy mô vừa và nhỏ (dưới 10 màn hình).
* ⚠️ **Hạn chế khi mở rộng (Scaling Issue):**
  - Khi dự án có 50 màn hình, thư mục `components/` sẽ phình to chứa hơn 100 file. Mỗi lần muốn sửa tính năng "Giỏ hàng", bạn phải mở `components/Cart.jsx`, rồi lại nhảy sang `hooks/useCart.js`, rồi lại mò sang `services/cartService.js` $\rightarrow$ Thao tác nhảy file rất mỏi mắt.

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="3-mo-hinh-2-feature-based-architecture-vertical-slicing--domain-driven"></a>
## 3. Mô hình 2: Feature-based Architecture (Vertical Slicing / Domain-Driven)

Khi dự án bước vào quy mô Doanh nghiệp lớn với nhiều đội ngũ (Team) cùng phát triển, mô hình này là tiêu chuẩn số 1.

<a id="31-dinh-nghia--triet-ly-cat-lat-theo-chieu-doc"></a>
### 3.1. Định nghĩa & Triết lý cắt lát theo chiều dọc

* **Feature-based Architecture:** Gom toàn bộ mã nguồn thuộc về **một tính năng nghiệp vụ cụ thể** vào chung một thư mục.
* **Vertical Slicing (Cắt lát dọc):** Thay vì chia bánh theo từng lớp kem ngang, ta cắt một miếng bánh dọc từ trên xuống dưới chứa đủ: Giao diện + Logic + Dịch vụ + Test của riêng tính năng đó.

---

<a id="32-cau-truc-thu-muc-mau-cap-doanh-nghiep-enterprise"></a>
### 3.2. Cấu trúc thư mục mẫu cấp Doanh nghiệp (Enterprise)

```text
src/
├── 📁 features/                  <-- Thư mục chứa các tính năng độc lập
│   ├── 📁 auth/                  <-- TÍNH NĂNG XÁC THỰC (Đăng nhập / Đăng ký)
│   │   ├── 📁 components/        <-- LoginForm.jsx, RegisterForm.jsx
│   │   ├── 📁 hooks/             <-- useAuth.js, useLogin.js
│   │   ├── 📁 services/          <-- authApi.js
│   │   └── 📄 authSlice.js       <-- Redux slice (nếu có)
│   │
│   ├── 📁 products/              <-- TÍNH NĂNG SẢN PHẨM
│   │   ├── 📁 components/        <-- ProductCard.jsx, ProductList.jsx
│   │   ├── 📁 hooks/             <-- useProductFilter.js
│   │   └── 📁 services/          <-- productApi.js
│   │
│   └── 📁 checkout/              <-- TÍNH NĂNG THANH TOÁN
│       ├── 📁 components/        <-- PaymentModal.jsx
│       └── 📁 services/          <-- paymentApi.js
│
├── 📁 components/                <-- Các component dùng chung TOÀN APP (Button, Modal, Navbar, Footer)
├── 📁 utils/                     <-- Các tiện ích dùng chung TOÀN APP
└── 📁 hooks/                     <-- Các custom hook dùng chung TOÀN APP
```

---

<a id="33-danh-gia-uu-diem--han-che"></a>
### 3.3. Đánh giá ưu điểm & hạn chế

* ✅ **Ưu điểm:**
  - **Tính tự đóng gói cao (Self-contained):** Khi cần xóa tính năng `checkout`, lập trình viên chỉ cần xóa đúng 1 thư mục `features/checkout` là sạch sẽ 100%, không lo rác vương vãi khắp nơi.
  - Phù hợp tuyệt đối cho làm việc nhóm (Team A làm `auth`, Team B làm `products` mà không bao giờ bị conflict mã nguồn với nhau).
* ⚠️ **Hạn chế:** Cấu trúc cồng kềnh đối với dự án nhỏ.

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="4-mo-hinh-3-atomic-design-pattern-trong-thiet-ke-ui-component"></a>
## 4. Mô hình 3: Atomic Design Pattern trong thiết kế UI Component

Được khởi xướng bởi chuyên gia thiết kế Brad Frost, mô hình này lấy cảm hứng từ môn Hóa học để tổ chức nội bộ thư mục `src/components/`.

<a id="41-5-cap-do-tien-hoa"></a>
### 4.1. 5 cấp độ tiến hóa

```text
Atoms (Nguyên tử) ──> Molecules (Phân tử) ──> Organisms (Sinh vật) ──> Templates ──> Pages
```

```text
src/components/
├── 📁 atoms/        <-- [NGUYÊN TỬ] Những thứ nhỏ nhất, không thể phân rã thêm
│   ├── 📄 Button.jsx
│   ├── 📄 Input.jsx
│   └── 📄 Label.jsx
│
├── 📁 molecules/    <-- [PHÂN TỬ] Gom 2-3 nguyên tử lại thành 1 cụm có nghĩa
│   └── 📄 FormField.jsx  <-- Kết hợp giữa <Label> + <Input> + thông báo lỗi <ErrorMessage>
│
├── 📁 organisms/    <-- [SINH VẬT] Khối giao diện hoàn chỉnh có nghiệp vụ
│   ├── 📄 RegisterForm.jsx <-- Ghép từ nhiều FormField + Button Submit
│   └── 📄 Navbar.jsx
│
├── 📁 templates/    <-- [BẢN MẪU KHUNG] Bố cục layout chưa có dữ liệu thật (Header + Sidebar + Content)
└── 📁 pages/        <-- [TRANG HOÀN CHỈNH] Gắn dữ liệu thật từ API vào Template
```

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="5-bang-ma-tran-so-sanh-doi-chieu-toan-dien"></a>
## 5. Bảng Ma trận So sánh Đối chiếu Toàn diện (Comparative Matrix)

| Tiêu chí so sánh | Grouping by File Type (Mô hình bạn đang dùng) | Feature-based Architecture | Atomic Design Pattern |
| :--- | :--- | :--- | :--- |
| **Quy mô dự án lý tưởng** | Nhỏ & Vừa (Small / Medium App, Landing Page, Lab) | Lớn & Rất lớn (Enterprise, E-commerce, SaaS) | Phù hợp cho đội ngũ Design System / UI Library |
| **Độ dốc đường cong học tập** | ⭐ Rất thấp (Dễ học nhất, trực quan) | ⭐⭐⭐ Trung bình (Cần tư duy phân tích nghiệp vụ) | ⭐⭐⭐⭐ Khá cao (Cần thống nhất chặt chẽ giữa Designer & Dev) |
| **Tốc độ tìm kiếm file** | Nhanh khi ít file, chậm khi nhiều file | Cực nhanh ở dự án lớn vì file nằm cụm theo tính năng | Cực nhanh khi tìm kiếm Component giao diện |
| **Khả năng xóa/bỏ tính năng** | Khó (Phải đi nhặt từng file ở 4 thư mục) | Cực dễ (Xóa 1 thư mục feature là xong) | Trung bình |

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="6-so-tay-quy-uoc-dat-ten--loi-chinh-ta-kinh-dien"></a>
## 6. Sổ tay Quy ước Đặt tên & Lỗi chính tả kinh điển (Lessons Learned)

Khi làm việc trong môi trường chuyên nghiệp hoặc phỏng vấn việc làm, các lỗi đặt tên sau đây sẽ ảnh hưởng rất lớn đến đánh giá chuyên môn:

### 🔴 Lỗi 1: Sai chính tả từ khóa tiếng Anh kinh điển
* ❌ `componets` $\rightarrow$ ✅ Sửa đúng: **`components`** *(Thiếu chữ `n` giữa `o` và `e`). Bắt nguồn từ tiếng Anh "Component" nghĩa là Thành phần cấu thành.*
* ❌ `ultis` $\rightarrow$ ✅ Sửa đúng: **`utils`** *(Nhầm chữ `l` và `t`). Viết tắt của "Utilities" nghĩa là Tiện ích bổ trợ.*

---

### 🔴 Lỗi 2: Không đồng nhất chuẩn viết hoa / viết thường
Quy tắc quốc tế bất di bất dịch:
1. **Thư mục (Folders):** Luôn dùng chữ thường số nhiều: `components`, `hooks`, `services`, `utils`.
2. **File React Component:** Bắt buộc viết hoa chữ cái đầu (**PascalCase**): `Button.jsx`, `RegisterForm.jsx`, `Header.jsx`.
3. **File Script, Hook, Utils thông thường:** Dùng chữ thường lạc đà (**camelCase**): `authService.js`, `useDebounce.js`, `formatCurrency.js`, `main.jsx`.

---
[⬆️ Quay lại Mục lục](#muc-luc)

