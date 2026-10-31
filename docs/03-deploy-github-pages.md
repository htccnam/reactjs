# TỔNG HỢP TOÀN BỘ QUY TRÌNH DEPLOY DỰ ÁN FRONTEND (REACT + VITE) LÊN GITHUB PAGES TỪ A ĐẾN Z
> **Chủ đề Buổi 3:** Đóng gói sản phẩm (Production Build), cấu hình đường dẫn Base URL và tự động hóa triển khai giao diện người dùng lên GitHub Pages.  
> **Người hướng dẫn:** Senior Tech Lead & Mentor Đào tạo Lập trình Frontend.

---

<a id="mục-lục"></a>
## 📑 Mục lục (Table of Contents)

1. [Sơ đồ luồng triển khai tổng quan (CI/CD Deployment Flow)](#1-sơ-đồ-luồng-triển-khai-tổng-quan-cicd-deployment-flow)
2. [Giai đoạn 1: Thiết lập hạ tầng & Cấu hình môi trường (Setup)](#2-giai-đoạn-1-thiết-lập-hạ-tầng--cấu-hình-môi-trường-setup)
   - [2.1. Cài đặt công cụ triển khai GitHub Pages (gh-pages)](#21-cài-đặt-công-cụ-triển-khai-github-pages-gh-pages)
   - [2.2. Cấu hình định danh Base URL trong vite.config.js](#22-cấu-hình-định-danh-base-url-trong-viteconfigjs)
   - [2.3. Khai báo kịch bản tự động hóa trong package.json](#23-khai-báo-kịch-bản-tự-động-hóa-trong-packagejson)
3. [Giai đoạn 2: Quy trình Đóng gói & Triển khai 3 tầng (Build Pipeline & Deploy)](#3-giai-đoạn-2-quy-trình-đóng-gói--triển-khai-3-tầng-build-pipeline--deploy)
   - [3.1. Tầng 1: Đóng gói mã nguồn Production (Production Build)](#31-tầng-1-đóng-gói-mã-nguồn-production-production-build)
   - [3.2. Tầng 2: Kiểm thử bản Build cục bộ (Local Preview Verification)](#32-tầng-2-kiểm-thử-bản-build-cục-bộ-local-preview-verification)
   - [3.3. Tầng 3: Triển khai đám mây (Cloud Deployment tới GitHub Pages)](#33-tầng-3-triển-khai-đám-mây-cloud-deployment-tới-github-pages)
4. [Giai đoạn 3: Kiểm thử toàn diện trên Trình duyệt & DevTools (Bảng Test Cases)](#4-giai-đoạn-3-kiểm-thử-toàn-diện-trên-trình-duyệt--devtools-bảng-test-cases)
5. [Sổ tay lỗi kinh điển cần tránh ở Frontend khi Deploy (Lessons Learned)](#5-sổ-tay-lỗi-kinh-điển-cần-tránh-ở-frontend-khi-deploy-lessons-learned)

---

<a id="1-sơ-đồ-luồng-triển-khai-tổng-quan-cicd-deployment-flow"></a>
## 1. Sơ đồ luồng triển khai tổng quan (CI/CD Deployment Flow)

Dưới đây là sơ đồ dòng chảy dữ liệu kỹ thuật từ mã nguồn React JSX thô trên máy tính lập trình viên tới máy chủ đám mây GitHub Pages phục vụ hàng triệu người dùng:

```text
[ Máy tính Local / Mã nguồn React ]
             │
             │ (1) Lệnh: npm run build (Biên dịch Vite + Tailwind v4)
             ▼
┌─────────────────────────────────────────────────────────────┐
│ QUÁ TRÌNH BIÊN DỊCH & TỐI ƯU HÓA (Rollup Bundler Engine)     │
│ - Chuyển đổi JSX/ES6+ ──> Vanilla JavaScript thuần           │
│ - Quét mã tiện ích Tailwind ──> Tinh gọn CSS (Purge/Minify) │
│ - Đánh mã băm tệp tĩnh (Asset Hashing: index-B3x8s.js)       │
│ - Sinh ra thư mục đóng gói độc lập: dist/                   │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ (2) Lệnh: npm run deploy
                             ▼
┌─────────────────────────────────────────────────────────────┐
│ TỰ ĐỘNG HÓA NHÁNH TRIỂN KHAI (gh-pages Package)             │
│ - Khởi tạo nhánh mồ côi ngầm (Orphan Branch: gh-pages)      │
│ - Đẩy duy nhất nội dung bên trong dist/ lên nhánh gh-pages   │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ (3) Git Push qua giao thức HTTPS
                             ▼
┌─────────────────────────────────────────────────────────────┐
│ MÁY CHỦ GITHUB REPOSITORY (https://github.com/htccnam/reactjs)│
│ - Nhánh 'main': Chứa mã nguồn thô (src, docs, package.json) │
│ - Nhánh 'gh-pages': Chứa mã tĩnh đã build (index.html, dist) │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ (4) Kích hoạt máy chủ Web tĩnh GitHub Pages
                             ▼
┌─────────────────────────────────────────────────────────────┐
│ TRÌNH DUYỆT NGƯỜI DÙNG TOÀN CẦU (World Wide Web)            │
│ Endpoint: https://htccnam.github.io/reactjs/                │
│ - Nhận mã HTTP 200 OK                                       │
│ - Tải CSS, JavaScript, nạp giao diện React tức thì          │
└─────────────────────────────────────────────────────────────┘
```

[⬆️ Quay lại Mục lục](#mục-lục)

---

<a id="2-giai-đoạn-1-thiết-lập-hạ-tầng--cấu-hình-môi-trường-setup"></a>
## 2. Giai đoạn 1: Thiết lập hạ tầng & Cấu hình môi trường (Setup)

GitHub Pages chỉ là một dịch vụ lưu trữ web tĩnh (Static Web Hosting). Nó không có môi trường Node.js trên server để biên dịch React JSX trực tiếp. Vì vậy, ta cần công cụ hỗ trợ đóng gói và cấu hình đường dẫn tương đối.

### 2.1. Cài đặt công cụ triển khai GitHub Pages (gh-pages)

#### Lệnh Terminal thực thi:
```bash
npm install gh-pages --save-dev
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `npm`: Node Package Manager – Trình thực thi gói mã nguồn của hệ sinh thái Node.js.
- `install` (hoặc viết tắt là `i`): Lệnh tải mã nguồn thư viện từ kho `npmjs.com` về lưu vào thư mục `node_modules`.
- `gh-pages`: Tên gói thư viện mã nguồn mở chuyên dụng. Thư viện này có nhiệm vụ tạo ra một nhánh Git độc lập tên là `gh-pages` trên kho lưu trữ của bạn và chỉ đẩy riêng các tệp tĩnh đã build (thư mục `dist/`) lên nhánh đó.
- `--save-dev` (hoặc viết tắt `-D`): Cờ chỉ định lưu gói này vào khối `"devDependencies"` trong file `package.json`.
  - 💡 **Tư duy logic:** Gói `gh-pages` chỉ phục vụ duy nhất cho lập trình viên lúc thực hiện thao tác deploy ở môi trường phát triển (Development). Người dùng khi lướt web trên trình duyệt không cần chạy thư viện này. Việc dùng `--save-dev` giúp tách bạch rõ ràng giữa thư viện chạy thật (production) và công cụ hỗ trợ lập trình viên.

#### 📂 MINH HỌA SỰ TIẾN HÓA CỦA THƯ MỤC:
```text
reactbuoi1/
├── 📁 node_modules/
│   └── 📁 gh-pages/          <-- [MỚI TẢI VỀ] Bộ công cụ tự động hóa đẩy nhánh gh-pages
├── 📄 package-lock.json      <-- [CẬP NHẬT] Khóa chặt phiên bản và checksum của gh-pages
└── 📄 package.json           <-- [CẬP NHẬT dòng mã] Thêm "gh-pages" vào trường "devDependencies"
```

---

### 2.2. Cấu hình định danh Base URL trong vite.config.js

Đây là bước **quan trọng nhất** quyết định trang web có hiển thị được hay bị lỗi trắng màn hình.

#### Mở file [`vite.config.js`](file:///d:/github/reactbuoi1/vite.config.js) và bổ sung thuộc tính `base`:
```javascript
import react from '@vitejs/plugin-react'
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'

// https://vite.dev/config/
export default defineConfig({
  plugins: [
    react(),
    tailwindcss()
  ],
  base: '/reactjs/', // <-- ĐẶT TÊN TRÙNG VỚI TÊN REPOSITORY TRÊN GITHUB CỦA BẠN
})
```

#### 💡 Hướng dẫn tư duy logic cốt lõi:
- **Tại sao bắt buộc phải thêm `base: '/reactjs/'`?**
  - Mặc định, Vite giả định dự án sẽ chạy ở tên miền gốc (Root Domain): `https://example.com/` (tương ứng với `base: '/'`). Khi đó, trong file `index.html`, các thẻ nạp tài nguyên sẽ có dạng: `<script src="/assets/index-xxx.js">`.
  - Tuy nhiên, GitHub Pages lưu dự án của bạn dưới dạng một đường dẫn con (Sub-path repository):  
    👉 `https://htccnam.github.io/reactjs/`
  - Nếu bạn không đặt `base: '/reactjs/'`, trình duyệt của người dùng sẽ đi tìm file JavaScript ở địa chỉ: `https://htccnam.github.io/assets/index-xxx.js` (không tồn tại $\rightarrow$ Trả về lỗi **HTTP 404 Not Found** $\rightarrow$ Web bị trắng xóa toàn màn hình!).
  - Khi đặt `base: '/reactjs/'`, Vite sẽ tự động thêm tiền tố `/reactjs/` vào trước mọi đường dẫn:  
    👉 `<script src="/reactjs/assets/index-xxx.js">` $\rightarrow$ Tải thành công 100%!

---

### 2.3. Khai báo kịch bản tự động hóa trong package.json

Mở file [`package.json`](file:///d:/github/reactbuoi1/package.json) và bổ sung các script tự động hóa vào trường `"scripts"`:

```json
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "predeploy": "npm run build",
    "deploy": "gh-pages -d dist"
  },
```

#### 🔬 Giải phẫu chi tiết cấu hình Script:
- `"predeploy": "npm run build"`:
  - Đây là cơ chế vòng đời kịch bản (Lifecycle Hook) của NPM. Bất cứ khi nào bạn gõ `npm run deploy`, NPM sẽ tự động kích hoạt script có tiền tố `pre-` (ở đây là `predeploy`) chạy trước tiên.
  - Tác dụng: Đảm bảo mã nguồn React mới nhất luôn được biên dịch thành công ra thư mục `dist/` trước khi đem đi phát hành.
- `"deploy": "gh-pages -d dist"`:
  - `gh-pages`: Gọi chương trình nhị phân của gói vừa cài.
  - `-d` (viết tắt của `--dist` hoặc `--directory`): Chỉ định thư mục phân phối (distribution folder) chứa các file cần tải lên đám mây. Ở đây là thư mục `dist`.

[⬆️ Quay lại Mục lục](#mục-lục)

---

<a id="3-giai-doan-2-quy-trình-đóng-gói--triển-khai-3-tầng-build-pipeline--deploy"></a>
## 3. Giai đoạn 2: Quy trình Đóng gói & Triển khai 3 tầng (Build Pipeline & Deploy)

Quy trình deploy thực chiến được vận hành theo 3 tầng nối tiếp nhau:

---

<a id="31-tầng-1-đóng-gói-mã-nguồn-production-production-build"></a>
### 3.1. Tầng 1: Đóng gói mã nguồn Production (Production Build)

Trước khi gửi lên mạng, toàn bộ code React JSX và Tailwind CSS cần được nén nhỏ và băm mã.

#### Lệnh Terminal thực thi:
```bash
npm run build
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `npm run`: Lệnh thực thi kịch bản định nghĩa trong `package.json`.
- `build`: Thực thi trình biên dịch `vite build`. Vite sẽ kích hoạt bộ đóng gói siêu tốc Rollup:
  1. Quét cây phụ thuộc từ `index.html` $\rightarrow$ `src/main.jsx` $\rightarrow$ `src/App.jsx`.
  2. Gom các utility class Tailwind v4 đang sử dụng, loại bỏ toàn bộ CSS thừa (Purging).
  3. Rút gọn mã nguồn (Minification/Terser): Xóa bỏ toàn bộ khoảng trắng, comment và đổi tên biến dài thành các ký tự ngắn gọn như `a`, `b`, `c` để giảm dung lượng file xuống thấp nhất.
  4. Đánh mã băm (Hash): Tên file xuất xưởng sẽ có dạng `index-CeA_2hI1.js`. Khi bạn sửa code và build lại, mã băm này đổi sang chuỗi khác, ép trình duyệt của người dùng xóa bỏ bộ nhớ đệm (Cache Busting) và tải file mới về.

#### 📂 MINH HỌA SỰ TIẾN HÓA CỦA THƯ MỤC:
Ngay sau khi lệnh `npm run build` kết thúc thành công, một thư mục quan trọng xuất hiện ở thư mục gốc:
```text
reactbuoi1/
├── 📁 dist/                 <-- [MỚI SINH RA TỪ LỆNH BUILD] Gói xuất xưởng sẵn sàng đưa lên Web
│   ├── 📁 assets/          <-- Chứa các file tĩnh đã tối ưu hóa, nén Gzip/Brotli
│   │   ├── 📄 index-Cxxxx.js   <-- Toàn bộ code React + Component được đóng gói lại
│   │   └── 📄 index-Dyyyy.css  <-- Toàn bộ CSS Tailwind v4 được cô đọng
│   ├── 📄 favicon.ico      <-- Biểu tượng website
│   └── 📄 index.html       <-- File HTML chuẩn duy nhất để trình duyệt đọc
```

---

<a id="32-tầng-2-kiểm-thử-bản-build-cục-bộ-local-preview-verification"></a>
### 3.2. Tầng 2: Kiểm thử bản Build cục bộ (Local Preview Verification)

Senior Tech Lead nguyên tắc: **Không bao giờ deploy bản build lên server mà chưa test thử ở máy cục bộ!**

#### Lệnh Terminal thực thi:
```bash
npm run preview
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `preview`: Lệnh kích hoạt một máy chủ web nội bộ siêu nhẹ đọc trực tiếp từ thư mục xuất xưởng `dist/` (thay vì đọc từ `src/` như lệnh `dev`).
- Mở đường dẫn Terminal cung cấp (ví dụ `http://localhost:4173/reactjs/`).
- 💡 **Tư duy logic:** Lệnh `npm run dev` chạy trên bộ nhớ RAM với các module ES rời rạc để hỗ trợ code nhanh (HMR). Còn `npm run preview` mô phỏng môi trường thật 100% giống như website đang chạy trên máy chủ GitHub Pages. Nếu chạy `preview` mượt mà, website của bạn đảm bảo 100% chạy tốt trên internet.

---

<a id="33-tầng-3-triển-khai-đám-mây-cloud-deployment-tới-github-pages"></a>
### 3.3. Tầng 3: Triển khai đám mây (Cloud Deployment tới GitHub Pages)

Sau khi kiểm thử cục bộ hoàn tất, kích hoạt lệnh phát hành trực tiếp lên GitHub.

#### Lệnh Terminal thực thi:
```bash
npm run deploy
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
1. Terminal tự động kích hoạt `predeploy` $\rightarrow$ Chạy ngầm `npm run build` để tái tạo thư mục `dist/` mới nhất.
2. Terminal chạy `gh-pages -d dist`:
   - Tạo một commit mới chứa toàn bộ file trong `dist/`.
   - Kết nối tới kho lưu trữ từ xa `origin` (`https://github.com/htccnam/reactjs.git`).
   - Đẩy nhánh `gh-pages` lên GitHub.
   - Khi terminal in ra dòng chữ: `Published` nghĩa là quá trình đẩy lên mây đã hoàn tất!

#### ⚙️ Bước kích hoạt cấu hình trên giao diện GitHub (Chỉ cần làm 1 lần duy nhất):
1. Truy cập vào kho lưu trữ của bạn trên trình duyệt: `https://github.com/htccnam/reactjs`.
2. Bấm vào tab **Settings** (Bánh răng cài đặt ở thanh menu trên cùng).
3. Ở cột menu bên trái, tìm mục **Pages** (nằm trong phần *Code and automation*).
4. Tại mục **Build and deployment**:
   - **Source:** Chọn **Deploy from a branch**.
   - **Branch:** Chọn nhánh **`gh-pages`** (thay vì nhánh `main`), thư mục để mặc định là `/(root)`.
5. Bấm **Save**.
6. Đợi khoảng 1-2 phút, GitHub sẽ hiển thị đường link trang web chính thức của bạn:  
   👉 **`https://htccnam.github.io/reactjs/`**

[⬆️ Quay lại Mục lục](#mục-lục)

---

<a id="4-giai-đoạn-3-kiểm-thử-toàn-diện-trên-trình-duyệt--devtools-bảng-test-cases"></a>
## 4. Giai đoạn 3: Kiểm thử toàn diện trên Trình duyệt & DevTools (Bảng Test Cases)

Sau khi deploy, mở trình duyệt truy cập link production và mở công cụ lập trình viên (F12 / Inspect) để xác thực các tiêu chí chất lượng:

### Bảng Kịch Bản Kiểm Thử Production (Frontend Deployment Test Suite)

| STT | Mã Kiểm Thử | Hạng Mục Kiểm Thử | Thao Tác Thực Hiện | Kết Quả Mong Đợi (Expected) | Trạng Thái DevTools |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **01** | `TC_DEP_01` | **Mã phản hồi Trang chủ** | Truy cập `https://htccnam.github.io/reactjs/` | Trang web tải thành công, không bị lỗi 404. | Tab Network: `index.html` trả về HTTP `200 OK`. |
| **02** | `TC_DEP_02` | **Đường dẫn nạp File Tĩnh (Assets)** | Kiểm tra các thẻ `<script>` và `<link>` | File CSS và JS được nạp kèm tiền tố `/reactjs/assets/...`. | Tab Network: File `.js` và `.css` đạt mã `200 OK` (MIME type đúng `javascript`). |
| **03** | `TC_DEP_03` | **Hiệu lực giao diện Tailwind CSS** | Quan sát giao diện nút bấm và chữ `hellof8` | Chữ có màu đỏ chuẩn `text-red-500`, background nhận đúng màu gradient. | Tab Elements: Các class Tailwind được áp dụng đầy đủ. |
| **04** | `TC_DEP_04` | **Kiểm tra Lỗi Runtime Console** | Mở tab Console trong DevTools | Không có bất kỳ dòng chữ đỏ báo lỗi JavaScript nào (`Uncaught ReferenceError`, `MIME error`). | Tab Console: Sạch bóng (0 Errors). |
| **05** | `TC_DEP_05` | **Tối ưu hóa Tài nguyên (Minification)** | Mở tab Network $\rightarrow$ Bấm vào file `.js` chính | Mã nguồn đã được nén trên một dòng duy nhất, không còn khoảng trắng hay chú thích. | Kích thước file (Transfer Size) siêu nhẹ (< 150 KB). |
| **06** | `TC_DEP_06` | **Kiểm tra Responsive trên Di động** | Bấm tổ hợp `Ctrl + Shift + M` trong DevTools để bật Mobile View | Giao diện tự động co giãn vừa vặn màn hình điện thoại (iPhone/Android). | Không bị tràn màn hình theo chiều ngang (No Horizontal Scrollbar). |

[⬆️ Quay lại Mục lục](#mục-lục)

---

<a id="5-sổ-tay-lỗi-kinh-điển-cần-tránh-ở-frontend-khi-deploy-lessons-learned"></a>
## 5. Sổ tay lỗi kinh điển cần tránh ở Frontend khi Deploy (Lessons Learned)

Dưới đây là 5 "cái bẫy" lớn nhất khiến 95% lập trình viên mới gặp sự cố khi đưa trang web lên GitHub Pages:

### 🔴 Lỗi 1: Trang web bị trắng xóa toàn màn hình (Lỗi 404 MIME Type)
- **Triệu chứng:** Mở link GitHub Pages không thấy gì ngoài một màn hình trắng. Mở F12 Console thấy báo đỏ:  
  `Failed to load module script: Expected a JavaScript module script but the server responded with a MIME type of "text/html".`
- **Nguyên nhân gốc rễ:** Quên khai báo `base: '/reactjs/'` trong file `vite.config.js`. Trình duyệt đi tìm tài nguyên ở `htccnam.github.io/assets/` thay vì `htccnam.github.io/reactjs/assets/`.
- **Cách khắc phục:** Mở [`vite.config.js`](file:///d:/github/reactbuoi1/vite.config.js), thêm `base: '/reactjs/'`, sau đó chạy lại `npm run deploy`.

---

### 🔴 Lỗi 2: Chọn sai nhánh trong GitHub Pages Settings
- **Triệu chứng:** Vào mục Settings $\rightarrow$ Pages của GitHub nhưng trang web vẫn hiển thị file `README.md` hoặc báo lỗi 404.
- **Nguyên nhân gốc rễ:** Trong phần cài đặt nhánh (Branch), bạn vẫn để nhánh `main`. Nhánh `main` chỉ chứa file `.jsx` mà GitHub Pages không thể đọc trực tiếp.
- **Cách khắc phục:** Vào **Settings** $\rightarrow$ **Pages** $\rightarrow$ Đổi nhánh nguồn từ `main` sang **`gh-pages`** $\rightarrow$ Bấm **Save**.

---

### 🔴 Lỗi 3: Đẩy cả thư mục `dist/` vào nhánh `main` bằng lệnh `git add .`
- **Triệu chứng:** Thư mục `dist/` xuất hiện trên nhánh `main` của repository GitHub, gây phình to dung lượng git.
- **Nguyên nhân gốc rễ:** File [`.gitignore`](file:///d:/github/reactbuoi1/.gitignore) bị xóa mất dòng `dist`.
- **Cách khắc phục:** Đảm bảo file `.gitignore` luôn có dòng `dist` để nhánh `main` chỉ chứa mã nguồn phát triển sạch sẽ, còn việc đẩy `dist` đã có thư viện `gh-pages` tự động phụ trách riêng trên nhánh `gh-pages`.

---

### 🔴 Lỗi 4: Lỗi 404 khi bấm F5 (Reload) ở các trang con (Dành cho React Router SPA)
- **Triệu chứng:** Khi ứng dụng có sử dụng React Router (Single Page Application), người dùng bấm link điều hướng từ trang chủ sang `/register` thì chạy được, nhưng khi bấm F5 tải lại trang thì GitHub báo **404 File Not Found**.
- **Nguyên nhân gốc rễ:** GitHub Pages là máy chủ tĩnh. Khi người dùng gõ `/register`, máy chủ tìm kiếm file vật lý `register.html` hoặc thư mục `register/` trên ổ đĩa. Vì đây là SPA chỉ có duy nhất 1 file `index.html`, máy chủ không thấy file nên trả về 404.
- **Cách khắc phục:** 
  1. Sử dụng kỹ thuật copy file `dist/index.html` thành file `dist/404.html` trước khi deploy.
  2. Hoặc sử dụng `HashRouter` thay vì `BrowserRouter` nếu dự án có điều hướng phức tạp.

---

### 🔴 Lỗi 5: Quên chạy `git commit` trước khi chạy `npm run deploy`
- **Triệu chứng:** Đã sửa code trên giao diện, chạy deploy thành công nhưng mở link web vẫn là phiên bản giao diện cũ.
- **Nguyên nhân gốc rễ:** Bạn sửa code nhưng quên lưu lại lịch sử thay đổi trên Git hoặc chưa đóng gói lại file build mới.
- **Cách khắc phục:** Tạo thói quen chuẩn:
  ```bash
  git add .
  git commit -m "feat: cap nhat giao dien truoc khi deploy"
  npm run deploy
  ```

---
[⬆️ Quay lại Mục lục](#mục-lục)

