# TỔNG HỢP TOÀN BỘ QUY TRÌNH KHỞI TẠO DỰ ÁN REACT (VITE) VÀ TÍCH HỢP TAILWIND CSS V4
> **Chủ đề Buổi 1:** Khởi tạo dự án React từ con số 0 với Vite, dọn dẹp mã nguồn mặc định (Clean Boilerplate) và cấu hình Tailwind CSS v4 chuẩn kỹ thuật.  
> **Người hướng dẫn:** Senior Tech Lead & Mentor Đào tạo Lập trình.

---

<a id="muc-luc"></a>
## 📑 Mục lục (Table of Contents)

1. [Sơ đồ luồng triển khai tổng quan](#1-so-do-luong-trien-khai-tong-quan)
2. [Giai đoạn 1: Khởi tạo hạ tầng dự án React bằng Vite](#2-giai-doan-1-khoi-tao-ha-tang-du-an-react-bang-vite)
   - [2.1. Kiểm tra môi trường thực thi (Node.js & NPM)](#21-kiem-tra-moi-truong-thuc-thi-nodejs--npm)
   - [2.2. Khởi tạo khung dự án React bằng Vite CLI](#22-khoi-tao-khung-du-an-react-bang-vite-cli)
   - [2.3. Cài đặt các gói phụ thuộc cơ sở (Dependencies)](#23-cai-dat-cac-goi-phu-thuoc-co-so-dependencies)
3. [Giai đoạn 2: Tái cấu trúc & Dọn dẹp mã nguồn thừa (Clean Boilerplate)](#3-giai-doan-2-tai-cau-truc--don-dep-ma-nguon-thua-clean-boilerplate)
   - [3.1. Xóa file CSS cục bộ thừa (App.css)](#31-xoa-file-css-cuc-bo-thua-appcss)
   - [3.2. Tối giản mã nguồn trong Component App.jsx](#32-toi-gian-ma-nguon-trong-component-appjsx)
   - [3.3. Dọn sạch file CSS toàn cục (index.css)](#33-don-sach-file-css-toan-cuc-indexcss)
   - [3.4. Tùy chỉnh điểm khởi chạy main.jsx (Tắt StrictMode)](#34-tuy-chinh-diem-khoi-chay-mainjsx-tat-strictmode)
4. [Giai đoạn 3: Cài đặt và Tích hợp Tailwind CSS v4](#4-giai-doan-3-cai-dat-va-tich-hop-tailwind-css-v4)
   - [4.1. Cài đặt thư viện Tailwind CSS và Plugin Vite](#41-cai-dat-thu-vien-tailwind-css-va-plugin-vite)
   - [4.2. Đăng ký Plugin vào cấu hình Vite (vite.config.js)](#42-dang-ky-plugin-vao-cau-hinh-vite-viteconfigjs)
   - [4.3. Kích hoạt Tailwind Engine trong index.css](#43-kich-hoat-tailwind-engine-trong-indexcss)
5. [Giai đoạn 4: Khởi động Dev Server & Xác thực giao diện](#5-giai-doan-4-khoi-dong-dev-server--xac-thuc-giao-dien)
   - [5.1. Khởi chạy máy chủ phát triển (Development Server)](#51-khoi-chay-may-chu-phat-trien-development-server)
   - [5.2. Viết mã thử nghiệm class màu trên App.jsx](#52-viet-ma-thu-nghiem-class-mau-tren-appjsx)
6. [Sổ tay lỗi kinh điển cần tránh (Lessons Learned)](#6-so-tay-loi-kinh-dien-can-tranh-lessons-learned)
   - [6.1. Lỗi 1: Nhầm lẫn tên class (red thay vì text-red-500)](#61-loi-1-nham-lan-ten-class-red-thay-vi-text-red-500)
   - [6.2. Lỗi 2: Nhầm lẫn giữa cấu hình Tailwind v3 và Tailwind v4](#62-loi-2-nham-lan-giua-cau-hinh-tailwind-v3-va-tailwind-v4)
   - [6.3. Lỗi 3: Quên import file CSS vào main.jsx](#63-loi-3-quen-import-file-css-vao-mainjsx)
   - [6.4. Lỗi 4: Đặt tên file Component bằng chữ thường](#64-loi-4-dat-ten-file-component-bang-chu-thuong)
   - [6.5. Lỗi 5: Terminal bị chiếm dụng cổng Port (EADDRINUSE: 5173)](#65-loi-5-terminal-bi-chiem-dung-cong-port-eaddrinuse-5173)

---

<a id="1-so-do-luong-trien-khai-tong-quan"></a>
## 1. Sơ đồ luồng triển khai tổng quan

Dưới đây là sơ đồ chuỗi hành động chuẩn kỹ thuật trong buổi 1 từ khi máy tính chưa có gì đến khi giao diện React nhận diện thành công Tailwind CSS:

```text
[ Máy tính / OS ] 
       │ (Kiểm tra runtime)
       ▼
[ Node.js + NPM ] ──> Lệnh: npm create vite@latest
       │
       ▼
[ Khung dự án React mặc định ] (Chứa nhiều file demo, logo, CSS rác)
       │
       ▼
[ Bước dọn dẹp Boilerplate ] ──> Xóa App.css
       │                     ──> Reset App.jsx về khung tối giản
       │                     ──> Dọn sạch index.css
       │                     ──> Tắt StrictMode trong main.jsx
       ▼
[ Dự án React sạch (Clean State) ]
       │
       ▼
[ Cài đặt Tailwind CSS v4 ] ──> Lệnh: npm install tailwindcss @tailwindcss/vite
       │
       ▼
[ Cấu hình tích hợp ] ──> Thêm tailwindcss() vào vite.config.js
       │              ──> Thêm @import "tailwindcss"; vào index.css
       ▼
[ Kiểm thử giao diện ] ──> Viết thẻ <p className="text-red-500">hellof8</p>
       │
       ▼
[ Trình duyệt / Browser ] ──> Hiển thị chữ hellof8 màu đỏ chuẩn xác!
```

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="2-giai-doan-1-khoi-tao-ha-tang-du-an-react-bang-vite"></a>
## 2. Giai đoạn 1: Khởi tạo hạ tầng dự án React bằng Vite

<a id="21-kiem-tra-moi-truong-thuc-thi-nodejs--npm"></a>
### 2.1. Kiểm tra môi trường thực thi (Node.js & NPM)

Trước khi bắt đầu, môi trường bắt buộc cần có Node.js để chạy công cụ biên dịch và NPM để kéo mã nguồn thư viện về máy.

#### Lệnh Terminal thực thi:
```bash
node -v
npm -v
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `node`: Tên chương trình nhị phân (Executable) thực thi JavaScript Engine (V8).
- `npm`: Node Package Manager – CLI tương tác với kho lưu trữ gói trực tuyến `npmjs.com`.
- `-v` (flag viết tắt của `--version`): Tham số cờ yêu cầu in ra mã số phiên bản hiện tại rồi thoát.

---

#### 🟢 Trường hợp 1: Máy đã cài đặt thành công (Happy Path)
Terminal sẽ in ra mã phiên bản cụ thể:
```text
v20.18.0   <-- Phiên bản Node.js hiện hành
10.8.2     <-- Phiên bản NPM đi kèm
```
* **Tiêu chuẩn phiên bản:**
  - Khuyến nghị: `Node >= 18.x` hoặc `20.x LTS` để tương thích hoàn toàn với Vite 6+ và Tailwind CSS v4.
  - Nếu phiên bản của bạn quá cũ (ví dụ `v14.x` hoặc `v16.x`), khi khởi tạo Vite hoặc Tailwind v4 sẽ bị văng lỗi không tương thích cú pháp (Engine unsupported). Bạn cần tải lại bản mới nhất để ghi đè cập nhật.

---

#### 🔴 Trường hợp 2: KHÔNG CÓ VERSION (Chưa cài đặt hoặc chưa nhận lệnh)

Nếu trên Terminal xuất hiện một trong các thông báo lỗi sau:
* **Trên Windows PowerShell:**
  ```text
  node : The term 'node' is not recognized as the name of a cmdlet, function, script file, or operable program.
  ```
* **Trên Windows Command Prompt (CMD):**
  ```text
  'node' is not recognized as an internal or external command, operable program or batch file.
  ```
* **Trên macOS / Linux:**
  ```text
  command not found: node
  ```

##### 💡 Tư duy logic & Hướng dẫn khắc phục từ A đến Z:
Khi gặp thông báo trên, nghĩa là máy tính của bạn chưa có Node.js hoặc hệ điều hành chưa biết đường dẫn đến file chạy `node.exe` trong biến môi trường (PATH). Bạn xử lý theo 3 bước:

1. **Bước 1: Tải bộ cài đặt chính thức:**
   - Truy cập trang chủ chính thức: [https://nodejs.org/](https://nodejs.org/)
   - Chọn tải phiên bản **LTS (Long Term Support)** (Ví dụ: `20.x` hoặc `22.x LTS`).
   - 💡 *Tại sao phải chọn bản LTS mà không chọn bản Current?* Bản LTS là bản ổn định dài hạn đã được kiểm thử nghiêm ngặt trong doanh nghiệp, giúp bạn không bị lỗi vặt khi cài các gói thư viện mới.
2. **Bước 2: Tiến hành cài đặt:**
   - Mở file vừa tải về (`.msi` trên Windows hoặc `.pkg` trên macOS).
   - Bấm **Next** liên tục theo mặc định. Đảm bảo ô **"Add to PATH"** được tích chọn (đây là tùy chọn tự động khai báo đường dẫn với hệ điều hành).
   - Khi cài Node.js, NPM sẽ được tự động cài đặt song song đi kèm, bạn không cần phải cài NPM riêng lẻ.
3. **Bước 3: Khởi động lại Terminal (BƯỚC QUAN TRỌNG NHẤT):**
   - Sau khi cài xong, bạn **bắt buộc phải tắt hoàn toàn VS Code hoặc cửa sổ Terminal đang mở** và bật lại.
   - *Lý do:* Hệ điều hành chỉ nạp lại danh sách biến môi trường `PATH` mới khi một phiên làm việc (Session) Terminal mới được sinh ra.
   - Chạy lại lệnh `node -v` và `npm -v` để kiểm tra, nếu hiện ra số phiên bản là bạn đã sẵn sàng cho bước tiếp theo!

---

<a id="22-khoi-tao-khung-du-an-react-bang-vite-cli"></a>
### 2.2. Khởi tạo khung dự án React bằng Vite CLI

Thay vì sử dụng Create React App (chậm chạp và đã bị khai tử), chúng ta sử dụng **Vite** để khởi tạo dự án React siêu tốc.

#### Lệnh Terminal thực thi:
```bash
npm create vite@latest reactbuoi1 -- --template react
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `npm`: Bộ quản lý package.
- `create`: Kịch bản viết tắt của `npm init <initializer>`. Khi gõ `npm create vite`, NPM tự động hiểu và tải package khởi tạo `create-vite`.
- `vite@latest`: Chỉ định sử dụng phiên bản mới nhất của bộ công cụ tạo khung Vite.
- `reactbuoi1`: Tên thư mục dự án được tạo ra trên ổ đĩa.
- `--`: Cú pháp phân tách chuẩn (POSIX delimiter) thông báo cho NPM biết rằng các tham số sau nó sẽ được chuyển thẳng cho CLI của `create-vite`, không phải tham số của npm.
- `--template react`: Tùy chọn chỉ định mẫu dự án là React với JavaScript thuần.

#### 📂 MINH HỌA SỰ TIẾN HÓA CỦA THƯ MỤC:
Ngay sau khi lệnh tạo dự án hoàn tất, cấu trúc thư mục xuất hiện:
```text
reactbuoi1/
├── 📁 public/              <-- [MỚI TẠO] Thư mục chứa tài nguyên tĩnh không qua đóng gói (vite.svg)
├── 📁 src/                 <-- [MỚI TẠO] Thư mục chứa mã nguồn ứng dụng
│   ├── 📁 assets/          <-- [MỚI TẠO] Chứa ảnh logo React (react.svg)
│   ├── 📄 App.css          <-- [MỚI TẠO] CSS mặc định demo animation xoay logo
│   ├── 📄 App.jsx          <-- [MỚI TẠO] Component demo có nút bấm tăng số (Counter)
│   ├── 📄 index.css        <-- [MỚI TẠO] CSS mặc định căn giữa toàn trang
│   └── 📄 main.jsx         <-- [MỚI TẠO] Entry point kích hoạt ReactDOM
├── 📄 .gitignore           <-- [MỚI TẠO] Khai báo bỏ qua node_modules khi commit git
├── 📄 eslint.config.js     <-- [MỚI TẠO] Cấu hình kiểm tra chuẩn code JS/React
├── 📄 index.html           <-- [MỚI TẠO] Trang web duy nhất chứa thẻ <div id="root">
├── 📄 package.json         <-- [MỚI TẠO] Khai báo metadata và danh sách thư viện (chưa tải về)
└── 📄 vite.config.js       <-- [MỚI TẠO] Cấu hình nạp plugin React cho Vite
```

---

<a id="23-cai-dat-cac-goi-phu-thuoc-co-so-dependencies"></a>
### 2.3. Cài đặt các gói phụ thuộc cơ sở (Dependencies)

Lệnh khởi tạo chỉ mới tạo ra file `package.json` (danh sách thực đơn), máy trạm chưa tải code thật sự của React và Vite về.

#### Lệnh Terminal thực thi:
```bash
cd reactbuoi1
npm install
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `cd reactbuoi1`:
  - `cd` (Change Directory): Lệnh di chuyển con trỏ dòng lệnh của Terminal vào bên trong thư mục dự án vừa tạo.
- `npm install` (viết tắt là `npm i`):
  - Đọc file `package.json`, tìm hai trường `"dependencies"` (chứa `react`, `react-dom`) và `"devDependencies"` (chứa `vite`, `@vitejs/plugin-react`...).
  - Kết nối internet tới registry `npmjs.com`, tải toàn bộ các gói phần mềm và các dependency lồng nhau về lưu tại máy cục bộ.

#### 📂 MINH HỌA SỰ TIẾN HÓA CỦA THƯ MỤC:
```text
reactbuoi1/
├── 📁 node_modules/         <-- [MỚI TẠO từ lệnh npm install] Thư mục nặng hàng trăm MB chứa toàn bộ code thư viện
├── 📄 package-lock.json     <-- [MỚI TẠO từ lệnh npm install] File khóa cứng phiên bản và mã băm checksum từng package
└── 📄 package.json          <-- [GIỮ NGUYÊN] Đọc thông tin cài đặt
```

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="3-giai-doan-2-tai-cau-truc--don-dep-ma-nguon-thua-clean-boilerplate"></a>
## 3. Giai đoạn 2: Tái cấu trúc & Dọn dẹp mã nguồn thừa (Clean Boilerplate)

Khung mặc định của Vite chứa nhiều file ví dụ và CSS định dạng sẵn. Nếu giữ nguyên, các CSS này sẽ xung đột với CSS của chúng ta và gây rối mắt cho người mới học. Chúng ta tiến hành "dọn rác" để đưa dự án về trạng thái tinh gọn nhất.

<a id="31-xoa-file-css-cuc-bo-thua-appcss"></a>
### 3.1. Xóa file CSS cục bộ thừa (App.css)

File `src/App.css` mặc định chứa hiệu ứng xoay logo Vite/React và các khoảng đệm không cần thiết.

#### Thao tác:
Xóa bỏ hoàn toàn file: `src/App.css`.

💡 **Tư duy logic:** Khi đã sử dụng Tailwind CSS, chúng ta sẽ viết trực tiếp utility class vào JSX, không cần duy trì các file CSS cục bộ rời rạc gây khó khăn cho việc quản lý mã nguồn.

---

<a id="32-toi-gian-ma-nguon-trong-component-appjsx"></a>
### 3.2. Tối giản mã nguồn trong Component App.jsx

File `src/App.jsx` ban đầu có các đoạn import logo `react.svg`, import file `App.css` và khối code nút bấm tăng giảm `count`.

#### Thao tác chỉnh sửa file [`src/App.jsx`](file:///d:/github/reactbuoi1/src/App.jsx):
Xóa bỏ import `reactLogo`, `viteLogo`, xóa `import './App.css'`, chỉ giữ lại khung hàm cơ bản:

```jsx
import { useState } from 'react'

function App() {
  const [count, setCount] = useState(0)

  return (
    <>
      <p className='red'>hellof8</p>
    </>
  )
}

export default App
```

💡 **Tư duy logic:** Giữ lại một thẻ `<p>` đơn giản có nội dung `hellof8` để làm mốc kiểm tra (test milestone). Khi thêm class style, chúng ta sẽ nhận biết được ngay style có hiệu lực hay chưa.

---

<a id="33-don-sach-file-css-toan-cuc-indexcss"></a>
### 3.3. Dọn sạch file CSS toàn cục (index.css)

File `src/index.css` mặc định có hàng chục dòng CSS thiết lập font chữ, chế độ dark/light mode và margin cho toàn bộ thẻ body.

#### Thao tác chỉnh sửa file [`src/index.css`](file:///d:/github/reactbuoi1/src/index.css):
Xóa sạch toàn bộ nội dung cũ bên trong `index.css`. Để trống hoặc sẵn sàng cho bước nạp Tailwind.

💡 **Tư duy logic:** Các dòng reset CSS mặc định của Vite sẽ đè lên cơ chế Preflight (CSS Reset) của Tailwind. Xóa sạch file này giúp Tailwind kiểm soát 100% diện mạo trang web mà không bị ảnh hưởng bởi CSS lạ.

---

<a id="34-tuy-chinh-diem-khoi-chay-mainjsx-tat-strictmode"></a>
### 3.4. Tùy chỉnh điểm khởi chạy main.jsx (Tắt StrictMode)

Kiểm tra file [`src/main.jsx`](file:///d:/github/reactbuoi1/src/main.jsx):

```jsx
// import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.jsx'

createRoot(document.getElementById('root')).render(
  // <StrictMode>
    <App />
  // </StrictMode>,
)
```

💡 **Tư duy logic:**
- **Tại sao lại comment `<StrictMode>`?**
  - Trong môi trường phát triển (Development Mode), React bọc `<StrictMode>` sẽ tự động **chạy Render và gọi Hook (như `useEffect`) 2 lần liên tiếp** nhằm mục đích giúp lập trình viên phát hiện các lỗi rò rỉ bộ nhớ (Side-effects).
  - Đối với người mới bắt đầu học React Buổi 1, việc component chạy 2 lần thường gây bối rối khi kiểm tra `console.log()` hoặc quan sát vòng đời component. Do đó, việc tạm thời tắt/comment `StrictMode` giúp luồng chạy trực quan và dễ tiếp cận hơn.

#### 📂 MINH HỌA SỰ TIẾN HÓA CỦA THƯ MỤC SAU GIAI ĐOẠN 2:
```text
reactbuoi1/
└── 📁 src/
    ├── ❌ App.css            <-- [ĐÃ BỊ XÓA] Loại bỏ file CSS demo thừa
    ├── 📄 App.jsx            <-- [CẬP NHẬT] Xóa sạch logic demo, chỉ giữ lại thẻ <p>
    ├── 📄 index.css          <-- [CẬP NHẬT] Xóa sạch hàng chục dòng CSS mặc định
    └── 📄 main.jsx           <-- [CẬP NHẬT] Đã comment lại <StrictMode>
```

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="4-giai-doan-3-cai-dat-va-tich-hop-tailwind-css-v4"></a>
## 4. Giai đoạn 3: Cài đặt và Tích hợp Tailwind CSS v4

Hiện nay, phiên bản **Tailwind CSS v4** mang tới bước nhảy vọt: Không cần tạo file cấu hình `tailwind.config.js` hay `postcss.config.js`. Thay vào đó, Tailwind v4 tích hợp trực tiếp dưới dạng một plugin dành riêng cho bộ biên dịch Vite.

<a id="41-cai-dat-thu-vien-tailwind-css-va-plugin-vite"></a>
### 4.1. Cài đặt thư viện Tailwind CSS và Plugin Vite

#### Lệnh Terminal thực thi:
```bash
npm install tailwindcss @tailwindcss/vite
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `npm install`: Lệnh cài đặt gói mới vào thư mục `node_modules` và lưu tên gói vào `package.json`.
- `tailwindcss`: Gói lõi chứa toàn bộ engine biên dịch CSS của Tailwind thế hệ 4.
- `@tailwindcss/vite`: Plugin chính thức do Tailwind Labs phát triển, đóng vai trò cầu nối tích hợp trực tiếp Tailwind Engine vào chu trình đóng gói (bundle pipeline) của Vite.

#### 📂 MINH HỌA SỰ TIẾN HÓA CỦA THƯ MỤC:
```text
reactbuoi1/
├── 📁 node_modules/
│   ├── 📁 @tailwindcss/vite/ <-- [MỚI TẢI VỀ THÊM] Plugin Vite của Tailwind
│   └── 📁 tailwindcss/       <-- [MỚI TẢI VỀ THÊM] Engine Tailwind CSS v4
├── 📄 package-lock.json      <-- [CẬP NHẬT] Bổ sung chữ ký và mã băm của 2 thư viện mới
└── 📄 package.json           <-- [CẬP NHẬT dòng mã] Thêm @tailwindcss/vite và tailwindcss vào "dependencies"
```
*Trích đoạn thay đổi trong `package.json`:*
```json
  "dependencies": {
    "@tailwindcss/vite": "^4.3.3",
    "react": "^19.3.1",
    "react-dom": "^19.3.1",
    "tailwindcss": "^4.3.3"
  }
```

---

<a id="42-dang-ky-plugin-vao-cau-hinh-vite-viteconfigjs"></a>
### 4.2. Đăng ký Plugin vào cấu hình Vite (vite.config.js)

Vite cần được khai báo để biết rằng nó phải sử dụng plugin `@tailwindcss/vite` mỗi khi biên dịch file mã nguồn.

#### Thao tác chỉnh sửa file [`vite.config.js`](file:///d:/github/reactbuoi1/vite.config.js):
```javascript
import react from '@vitejs/plugin-react'
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'

// https://vite.dev/config/
export default defineConfig({
  plugins: [
    react(),
    tailwindcss() // <-- Thêm hàm kích hoạt plugin Tailwind vào mảng plugins
  ],
})
```

💡 **Tư duy logic:** 
Mảng `plugins` trong `vite.config.js` nhận vào danh sách các công cụ chuyển đổi. Khi thêm `tailwindcss()`, mỗi khi lập trình viên lưu file `.jsx`, plugin này sẽ quét toàn bộ các class tiện ích đang có mặt trong code và tự động sản sinh CSS siêu tốc ngay tại bộ nhớ RAM (Just-in-Time compilation).

---

<a id="43-kich-hoat-tailwind-engine-trong-indexcss"></a>
### 4.3. Kích hoạt Tailwind Engine trong index.css

Mở file [`src/index.css`](file:///d:/github/reactbuoi1/src/index.css) và thêm dòng chỉ thị duy nhất:

```css
@import "tailwindcss";
```

💡 **Tư duy logic:**
- Trong Tailwind v3 trước đây, bạn phải gõ 3 dòng:
  ```css
  /* Cách cũ của v3 */
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  ```
- Trong Tailwind v4 hiện tại, chuẩn CSS đã được hiện đại hóa. Chỉ cần một dòng `@import "tailwindcss";`, plugin của Vite sẽ tự động nạp toàn bộ hệ thống biến màu sắc CSS Variable, bộ quy tắc Preflight và các utility class.

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="5-giai-doan-4-khoi-dong-dev-server--xac-thuc-giao-dien"></a>
## 5. Giai đoạn 4: Khởi động Dev Server & Xác thực giao diện

<a id="51-khoi-chay-may-chu-phat-trien-development-server"></a>
### 5.1. Khởi chạy máy chủ phát triển (Development Server)

Sau khi hoàn tất cấu hình, khởi động Vite để xem kết quả trực tiếp trên trình duyệt.

#### Lệnh Terminal thực thi:
```bash
npm run dev
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `npm run`: Lệnh yêu cầu NPM chạy một script được định nghĩa sẵn.
- `dev`: Tên script ánh xạ tới lệnh `vite` (được cấu hình trong mục `"scripts"` của `package.json`).
- Kết quả Terminal sẽ trả về đường dẫn Local URL: `http://localhost:5173/`.

---

<a id="52-viet-ma-thu-nghiem-class-mau-tren-appjsx"></a>
### 5.2. Viết mã thử nghiệm class màu trên App.jsx

Mở file [`src/App.jsx`](file:///d:/github/reactbuoi1/src/App.jsx) để kiểm tra:

```jsx
import { useState } from 'react'

function App() {
  const [count, setCount] = useState(0)

  return (
    <>
      {/* ❌ Viết sai: class "red" không tồn tại trong Tailwind */}
      {/* <p className='red'>hellof8</p> */}

      {/* ✅ Viết đúng theo chuẩn Tailwind CSS: */}
      <p className='text-red-500 font-bold text-2xl'>hellof8</p>
    </>
  )
}

export default App
```

💡 **Tư duy logic:**
- Nếu bạn để `className='red'`, trình duyệt sẽ **không đổi màu đỏ** vì Tailwind không tạo ra class nào tên là `.red { ... }`.
- Thay vào đó, Tailwind quản lý màu chữ bằng tiền tố `text-{tên_màu}-{độ_đậm}`. Khi viết `text-red-500`, Tailwind sẽ sinh ra: `color: rgb(239 68 68);`.
- Khi lưu file, công nghệ Vite HMR (Hot Module Replacement) sẽ cập nhật màn hình ngay lập tức mà không cần F5 trình duyệt.

[⬆️ Quay lại Mục lục](#muc-luc)

---

<a id="6-so-tay-loi-kinh-dien-can-tranh-lessons-learned"></a>
## 6. Sổ tay lỗi kinh điển cần tránh (Lessons Learned)

Dưới đây là các lỗi mà 90% người mới bắt đầu học React + Tailwind CSS thường gặp:

<a id="61-loi-1-nham-lan-ten-class-red-thay-vi-text-red-500"></a>
### 6.1. Lỗi 1: Nhầm lẫn tên class (dùng "red" thay vì "text-red-500")
- **Triệu chứng:** Thêm class vào nhưng chữ không đổi màu đỏ, giao diện trơ trơ.
- **Nguyên nhân:** Trong CSS thuần bạn có thể tự tạo `.red { color: red; }`, nhưng Tailwind là **Utility-first framework** với quy tắc đặt tên nghiêm ngặt:
  - Màu chữ: `text-red-500` (đỏ vừa), `text-red-600` (đỏ đậm).
  - Màu nền: `bg-red-500`.
  - Màu viền: `border-red-500`.
- **Cách khắc phục:** Luôn tuân thủ cú pháp `text-{màu}-{cấp_độ}` hoặc nếu muốn dùng trực tiếp chữ `red` chuẩn CSS thuần thì dùng cú pháp ngoặc vuông: `text-[red]`.

---

<a id="62-loi-2-nham-lan-giua-cau-hinh-tailwind-v3-va-tailwind-v4"></a>
### 6.2. Lỗi 2: Nhầm lẫn giữa cấu hình Tailwind v3 và Tailwind v4
- **Triệu chứng:** Đi tìm file `tailwind.config.js` hoặc cố chạy lệnh `npx tailwindcss init -p` nhưng bị lỗi hoặc không có tác dụng.
- **Nguyên nhân:** Hiện tại dự án đã sử dụng thế hệ **Tailwind v4** với `@tailwindcss/vite`. Thế hệ này đã **bỏ hoàn toàn file `tailwind.config.js`**.
- **Cách khắc phục:** Chỉ cần cài đúng `@tailwindcss/vite`, thêm vào `plugins` của `vite.config.js` và nạp `@import "tailwindcss";` vào file CSS. Tuyệt đối không tạo lại file config của v3.

---

<a id="63-loi-3-quen-import-file-css-vao-mainjsx"></a>
### 6.3. Lỗi 3: Quên import file CSS vào main.jsx
- **Triệu chứng:** Cấu hình đúng hết trong `vite.config.js` và `index.css`, nhưng lên web thì tất cả class Tailwind đều không nhận.
- **Nguyên nhân:** File `src/main.jsx` là cổng nạp gốc của dự án. Nếu trong `main.jsx` thiếu dòng:
  ```jsx
  import './index.css'
  ```
  thì trình duyệt sẽ không bao giờ tải file `index.css` đó vào DOM.

---

<a id="64-loi-4-dat-ten-file-component-bang-chu-thuong"></a>
### 6.4. Lỗi 4: Đặt tên file Component bằng chữ thường
- **Triệu chứng:** Tạo file `app.jsx` hoặc đặt `function app() { ... }`, khi chạy trình duyệt báo màn hình trắng hoặc không render.
- **Nguyên nhân:** 
  - JSX quy ước: Thẻ viết thường như `<div>`, `<p>`, `<span>`, `<app>` là **thẻ HTML có sẵn**.
  - Thẻ viết hoa chữ cái đầu (PascalCase) như `<App />`, `<Header />` mới là **React Component**.
- **Cách khắc phục:** Luôn đặt tên file Component viết hoa: `App.jsx`, `Header.jsx`. Tên file thông thường (không phải component) như `main.jsx`, `vite.config.js`, `index.css` thì viết thường.

---

<a id="65-loi-5-terminal-bi-chiem-dung-cong-port-eaddrinuse-5173"></a>
### 6.5. Lỗi 5: Terminal bị chiếm dụng cổng Port (EADDRINUSE: 5173)
- **Triệu chứng:** Terminal báo `Port 5173 is in use, trying another one...` và app nhảy sang chạy ở cổng `5174` hoặc `5175`.
- **Nguyên nhân:** Bạn đang mở nhiều cửa sổ terminal cùng chạy lệnh `npm run dev`, hoặc phiên làm việc trước đó chưa được bấm `Ctrl + C` để tắt.
- **Cách khắc phục:** Bấm tổ hợp phím `Ctrl + C` tại cửa sổ Terminal đang chạy để dừng server trước khi tắt hẳn cửa sổ.

---
[⬆️ Quay lại Mục lục](#muc-luc)

