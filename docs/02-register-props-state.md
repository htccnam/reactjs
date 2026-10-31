# TỔNG HỢP TOÀN BỘ QUY TRÌNH XÂY DỰNG TÍNH NĂNG ĐĂNG KÝ (REGISTER)
> **Chủ đề Buổi 2:** Hoàn thiện Module Đăng ký (Register) với Kỹ thuật Reusable Button (Props Forwarding) & Làm chủ Bản chất Cốt lõi của React State.  
> **Người hướng dẫn:** Senior Tech Lead & Mentor Đào tạo Lập trình.

---

<a id="mục-lục"></a>
## 📑 Mục lục (Table of Contents)

1. [Sơ đồ luồng dữ liệu tổng quan](#1-sơ-đồ-luồng-dữ-liệu-tổng-quan)
2. [Giai đoạn 1: Thiết lập hạ tầng & Dự án (Setup)](#2-giai-đoạn-1-thiết-lập-hạ-tầng--dự-án-setup)
   - [2.1. Cài đặt các thư viện tiện ích bổ trợ (clsx & lucide-react)](#21-cài-đặt-các-thư-viện-tiện-ích-bổ-trợ-clsx--lucide-react)
   - [2.2. Kiểm tra cấu trúc phân tầng thư mục chuẩn công nghiệp](#22-kiểm-tra-cấu-trúc-phân-tầng-thư-mục-chuẩn-công-nghiệp)
3. [Giai đoạn 2: Xây dựng tính năng theo 3 tầng kiến trúc (Service, Controller, Routes)](#3-giai-đoạn-2-xây-dựng-tính-năng-theo-3-tầng-kiến-trúc-service-controller-routes)
   - [3.1. Tầng 1: Routes / Layout Container (App.jsx)](#31-tầng-1-routes--layout-container-appjsx)
   - [3.2. Tầng 2: Controller & UI Components](#32-tầng-2-controller--ui-components)
     - [3.2.1. Giải quyết vấn đề nút không hiện màu bằng Reusable Button (Kỹ thuật Gom & Truyền toàn bộ Props)](#321-giải-quyết-vấn-đề-nút-không-hiện-màu-bằng-reusable-button-kỹ-thuật-gom--truyền-toàn-bộ-props)
     - [3.2.2. Xây dựng Form Đăng Ký: Khai phá Bản chất của React State (State Re-render & State Persistence)](#322-xây-dựng-form-đăng-ký-khai-phá-bản-chất-của-react-state-state-re-render--state-persistence)
   - [3.3. Tầng 3: Service Layer (Giao thức mạng & Chuẩn hóa DTO)](#33-tầng-3-service-layer-giao-thức-mạng--chuẩn-hóa-dto)
4. [Giai đoạn 3: Kiểm thử toàn diện với Postman (Bảng Test Cases)](#4-giai-đoạn-3-kiểm-thử-toàn-diện-với-postman-bảng-test-cases)
5. [Sổ tay lỗi kinh điển cần tránh (Lessons Learned)](#5-sổ-tay-lỗi-kinh-điển-cần-tránh-lessons-learned)

---

<a id="1-sơ-đồ-luồng-dữ-liệu-tổng-quan"></a>
## 1. Sơ đồ luồng dữ liệu tổng quan

Dưới đây là sơ đồ luồng dữ liệu chuẩn từ người dùng, qua tầng Controller (nơi React State làm việc), xuống tầng Reusable Component và chuyển tiếp tới Service Layer kết nối Backend API:

```text
[ Người dùng / Trình duyệt ]
             │
             │ (1) Nhập ký tự vào Form (Username, Email, Password)
             ▼
┌─────────────────────────────────────────────────────────────┐
│ TẦNG 1: ROUTES / VIEW SHELL (src/App.jsx)                   │
│ - Bố cục căn giữa màn hình (Tailwind CSS: min-h-screen)     │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ Nạp Component Form Đăng Ký
                             ▼
┌─────────────────────────────────────────────────────────────┐
│ TẦNG 2: CONTROLLER & COMPONENTS (src/components/)           │
│                                                             │
│ [RegisterForm.jsx] (Quản lý State & Sự kiện)                │
│  ├─ BẢN CHẤT REACT STATE:                                   │
│  │   • setFormData() được gọi ──> Component Re-render       │
│  │   • Re-render lại hàm nhưng State KHÔNG bị reset!        │
│  │   • Giữ nguyên dữ liệu input trong Fiber Node            │
│  │                                                          │
│  └─ [Button.jsx] (Kỹ thuật Gom & Truyền toàn bộ Props)      │
│      • Gom props: { children, className, variant, ...rest } │
│      • Forwarding: <button className={...} {...rest}>       │
│      • Giải quyết dứt điểm: Nút ăn màu Tailwind & giữ onClick│
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ (2) Bấm nút Submit (e.preventDefault)
                             │     Validate dữ liệu Client-side
                             ▼
┌─────────────────────────────────────────────────────────────┐
│ TẦNG 3: SERVICE LAYER (src/services/authService.js)         │
│ - Hàm registerUser(payload)                                 │
│ - Đóng gói Headers: Content-Type: application/json          │
│ - Điều hướng HTTP POST tới Backend API                      │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ (3) Gửi Request qua mạng Internet
                             ▼
┌─────────────────────────────────────────────────────────────┐
│ HỆ THỐNG MÁY CHỦ BACKEND API (Kiểm thử độc lập qua Postman) │
│ - Endpoint: POST /api/v1/auth/register                      │
│ - Kiểm tra email trùng lặp, băm mật khẩu (bcrypt hashing)   │
│ - Lưu vào Database & Phản hồi HTTP 201 Created              │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ (4) Trả về HTTP Status & JSON Payload
                             ▼
┌─────────────────────────────────────────────────────────────┐
│ CẬP NHẬT GIAO DIỆN (Reactive UI Update qua State)           │
│ - setSuccessMessage() ──> Kích hoạt Re-render ──> Hiện Toast│
│ - setIsLoading(false) ──> Kích hoạt Re-render ──> Tắt Spin  │
└─────────────────────────────────────────────────────────────┘
```

[⬆️ Quay lại Mục lục](#mục-lục)

---

<a id="2-giai-đoạn-1-thiết-lập-hạ-tầng--dự-án-setup"></a>
## 2. Giai đoạn 1: Thiết lập hạ tầng & Dự án (Setup)

Để giao diện Form Đăng ký có thể hiển thị các icon trực quan (như icon mắt ẩn/hiện mật khẩu, icon spinner khi đang tải) và hỗ trợ ghép nối class linh hoạt không bị xung đột, chúng ta cần cài đặt hai thư viện tiện ích chuyên dụng: `clsx` và `lucide-react`.

### 2.1. Cài đặt các thư viện tiện ích bổ trợ (clsx & lucide-react)

#### Lệnh Terminal thực thi:
```bash
npm install clsx lucide-react
```

#### 🔬 Giải phẫu chi tiết lệnh Terminal:
- `npm`: Node Package Manager – CLI tương tác trực tiếp với kho lưu trữ mã nguồn mở toàn cầu `npmjs.com`.
- `install` (hoặc viết tắt là `i`): Lệnh quét cấu hình, tải code nhị phân và mã nguồn thư viện về lưu trong thư mục `node_modules`, đồng thời ghi nhận vào `package.json`.
- `clsx`: Tiện ích siêu nhẹ (dung lượng < 1KB) hỗ trợ ghép nối các class CSS/Tailwind có điều kiện (conditional classes) thay cho việc cộng chuỗi thủ công dễ gây lỗi khoảng trắng.
- `lucide-react`: Bộ thư viện Icon mã nguồn mở hiện đại, cung cấp các React Component biểu tượng SVG sắc nét (ví dụ: `Eye`, `EyeOff`, `Lock`, `Mail`, `User`, `Loader2`).

#### 📂 MINH HỌA SỰ TIẾN HÓA CỦA THƯ MỤC:
```text
reactbuoi1/
├── 📁 node_modules/
│   ├── 📁 clsx/              <-- [MỚI TẢI VỀ] Thư viện ghép nối class CSS
│   └── 📁 lucide-react/      <-- [MỚI TẢI VỀ] Bộ Icon Component React
├── 📄 package-lock.json      <-- [CẬP NHẬT] Ghi nhận hash SHA-512 và phiên bản của clsx và lucide-react
└── 📄 package.json           <-- [CẬP NHẬT dòng mã] Thêm clsx và lucide-react vào khối "dependencies"
```
*Chi tiết cập nhật bên trong `package.json`:*
```json
  "dependencies": {
    "@tailwindcss/vite": "^4.3.3",
    "clsx": "^2.1.1",
    "lucide-react": "^1.16.0",
    "react": "^19.3.1",
    "react-dom": "^19.3.1",
    "tailwindcss": "^4.3.3"
  }
```

---

### 2.2. Kiểm tra cấu trúc phân tầng thư mục chuẩn công nghiệp

Trước khi viết code, chúng ta quy hoạch lại không gian dự án theo đúng 3 tầng kiến trúc:
```text
reactbuoi1/
└── 📁 src/
    ├── 📁 components/        <-- TẦNG 2: Chứa View & Controller UI (Button.jsx, RegisterForm.jsx)
    ├── 📁 services/          <-- TẦNG 3: Chứa giao thức mạng API (authService.js)
    ├── 📄 App.jsx            <-- TẦNG 1: Routes / Layout Shell
    ├── 📄 index.css          <-- Nơi kích hoạt @import "tailwindcss";
    └── 📄 main.jsx           <-- Cổng nạp ReactDOM
```

[⬆️ Quay lại Mục lục](#mục-lục)

---

<a id="3-giai-doan-2-xây-dựng-tính-năng-theo-3-tầng-kiến-trúc-service-controller-routes"></a>
## 3. Giai đoạn 2: Xây dựng tính năng theo 3 tầng kiến trúc (Service, Controller, Routes)

---

<a id="31-tầng-1-routes--layout-container-appjsx"></a>
### 3.1. Tầng 1: Routes / Layout Container (App.jsx)

💡 **Tư duy logic:**  
File `App.jsx` đóng vai trò là khung nhìn trung tâm (Viewport Container). Nó không tham gia vào việc quản lý dữ liệu Form hay gọi API, mà chịu trách nhiệm thiết lập bố cục toàn màn hình (`min-h-screen`), màu nền hài hòa (`bg-gradient-to-br from-slate-900 via-slate-800 to-slate-950`) nhằm làm tôn vinh khối Form Đăng ký ở trung tâm.

#### Mã nguồn [`src/App.jsx`](file:///d:/github/reactbuoi1/src/App.jsx):
```jsx
import RegisterForm from './components/RegisterForm'

function App() {
  return (
    <main className="min-h-screen w-full bg-linear-to-br from-slate-900 via-slate-800 to-slate-950 flex items-center justify-center p-4 selection:bg-blue-500 selection:text-white">
      {/* Nạp Component Form Đăng Ký từ Tầng 2 */}
      <RegisterForm />
    </main>
  )
}

export default App
```

---

<a id="32-tầng-2-controller--ui-components"></a>
### 3.2. Tầng 2: Controller & UI Components

Tầng này gồm hai khối then chốt của bài học:
1. **Component `Button.jsx` tái sử dụng:** Giải quyết dứt điểm vấn đề nút không ăn màu và mất sự kiện.
2. **Component `RegisterForm.jsx`:** Quản lý Controller State và minh chứng bản chất của React State.

---

<a id="321-giải-quyết-vấn-đề-nút-không-hiện-màu-bằng-reusable-button-kỹ-thuật-gom--truyền-toàn-bộ-props"></a>
#### 3.2.1. Giải quyết vấn đề nút không hiện màu bằng Reusable Button (Kỹ thuật Gom & Truyền toàn bộ Props)

Tạo file: `src/components/Button.jsx`

##### 🚨 Vấn đề kinh điển thường gặp:
Người mới học tạo Component `<Button>` thường viết như sau:
```jsx
// ❌ CÁCH VIẾT SAI KHIẾN NÚT MẤT MÀU VÀ MẤT SỰ KIỆN:
function Button({ text }) {
  return <button className="bg-blue-500">{text}</button>
}
// Khi sử dụng:
<Button text="Đăng ký" className="bg-red-500" onClick={handleSubmit} disabled={loading} type="submit" />
// KẾT QUẢ TAI HẠI:
// 1. Nút KHÔNG BAO GIỜ đổi sang màu đỏ (vì component bỏ qua prop className truyền vào).
// 2. Nút KHÔNG BẤM ĐƯỢC (vì component không nhận prop onClick).
// 3. Nút KHÔNG SUBMIT ĐƯỢC FORM (vì mất prop type="submit").
// 4. Nút KHÔNG BỊ VÔ HIỆU HÓA KHI ĐANG TẢI (vì mất prop disabled).
```

##### 💡 Tư duy logic & Giải pháp kỹ thuật: "Kỹ thuật Gom và Truyền toàn bộ Props (Props Forwarding via Rest/Spread)":
- **Gom Props thừa bằng Rest Parameter (`...rest`):** Ngoài các props định danh riêng (`children`, `variant`, `className`, `isLoading`), toàn bộ các thuộc tính HTML nguyên bản (`onClick`, `type`, `disabled`, `id`, `name`, `aria-*`) được gom vào một object duy nhất tên là `rest`.
- **Trải Props vào thẻ nguyên bản (`{...rest}`):** Bằng toán tử Spread, mọi thuộc tính và sự kiện được ủy quyền thẳng vào thẻ `<button>` gốc của HTML.
- **Ghép nối Class thông minh:** Dùng `clsx` để kết hợp `baseStyles` (định dạng cơ bản), `variants[variant]` (bộ màu sắc theo chủ đề) và `className` (class bổ sung do lập trình viên truyền từ ngoài vào). Nhờ vậy, nút **luôn hiện màu chuẩn** và dễ dàng trang trí tùy ý.

##### Mã nguồn chuẩn `src/components/Button.jsx`:
```jsx
import clsx from 'clsx'
import { Loader2 } from 'lucide-react'

function Button({
  children,
  variant = 'primary',
  className = '',
  isLoading = false,
  disabled = false,
  ...rest // <-- GOM TOÀN BỘ PROPS CÒN LẠI (type, onClick, onMouseEnter,...)
}) {
  // 1. Bộ khung style gốc áp dụng cho mọi loại nút
  const baseStyles = 'inline-flex items-center justify-center font-medium rounded-xl px-5 py-3 text-sm transition-all duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2 active:scale-[0.98] disabled:opacity-60 disabled:cursor-not-allowed disabled:active:scale-100 shadow-sm'

  // 2. Bảng biến thể màu sắc (Color Variants) được định nghĩa chặt chẽ bằng Tailwind CSS
  const variants = {
    primary: 'bg-blue-600 hover:bg-blue-700 text-white focus:ring-blue-500 shadow-blue-500/25 hover:shadow-blue-500/40',
    secondary: 'bg-slate-700 hover:bg-slate-600 text-slate-100 focus:ring-slate-500',
    danger: 'bg-red-600 hover:bg-red-700 text-white focus:ring-red-500 shadow-red-500/25',
    outline: 'border border-slate-600 hover:bg-slate-800 text-slate-200 focus:ring-slate-400'
  }

  return (
    <button
      className={clsx(baseStyles, variants[variant], className)} // <-- Nối class chuẩn, nhận diện màu 100%
      disabled={disabled || isLoading}
      {...rest} // <-- TRUYỀN TOÀN BỘ PROPS VÀO THẺ HTML NGUYÊN BẢN
    >
      {isLoading ? (
        <>
          <Loader2 className="w-4 h-4 mr-2 animate-spin" />
          <span>Đang xử lý...</span>
        </>
      ) : (
        children
      )}
    </button>
  )
}

export default Button
```

---

<a id="322-xây-dựng-form-đăng-ký-khai-phá-bản-chất-của-react-state-state-re-render--state-persistence"></a>
#### 3.2.2. Xây dựng Form Đăng Ký: Khai phá Bản chất của React State (State Re-render & State Persistence)

Tạo file: `src/components/RegisterForm.jsx`

##### 🧠 BẢN CHẤT CỐT LÕI CỦA REACT STATE (KIẾN THỨC BẮT BUỘC):
Để hiểu vì sao React thống trị thế giới Frontend, bạn phải thấu hiểu 2 tính chất vàng của State:

1. **Tính chất 1: State thay đổi $\rightarrow$ Component Re-render:**
   - Trong JavaScript thuần, khi bạn đổi biến `let username = 'abc'`, giao diện trên màn hình **không tự đổi**. Bạn phải tự tay tìm DOM `document.getElementById('...').innerText = username`.
   - Trong React: Khi bạn gọi `setFormData(newValue)`, React lập tức phát hiện Component này đã có dữ liệu mới. React sẽ **chạy lại hàm Component `RegisterForm()` từ đầu đến cuối (Re-render)** để tạo ra Virtual DOM mới và cập nhật tức thì phần thay đổi lên màn hình.

2. **Tính chất 2: Khi Component Re-render, State KHÔNG BỊ KHỞI TẠO LẠI (State Persistence):**
   - *Câu hỏi hóc búa:* Nếu mỗi lần gõ phím, hàm `RegisterForm()` đều bị gọi lại từ dòng 1, vậy tại sao dòng mã `const [formData, setFormData] = useState({ username: '' })` lại không gán `formData` trở về `{ username: '' }` rỗng?
   - *Bản chất ngầm:* Hàm `useState` không lưu giá trị bên trong component! React lưu trữ state bên trong một đối tượng nội bộ của React (gọi là **Fiber Node / Hook Linked List** gắn liền với vị trí của component trên cây DOM).
   - Khi Component chạy lần đầu (Mount): React lưu giá trị ban đầu vào bộ nhớ riêng.
   - Khi Component chạy lại (Re-render): React tìm thấy Hook này trong Fiber, **bỏ qua giá trị khởi tạo truyền trong ngoặc**, và trả về giá trị mới nhất đang được bảo tồn. Nhờ đó, người dùng gõ đến ký tự nào thì ký tự đó được lưu giữ trọn vẹn, không bao giờ bị xóa trắng!

##### Mã nguồn chuẩn `src/components/RegisterForm.jsx`:
```jsx
import { useState } from 'react'
import { User, Mail, Lock, Eye, EyeOff, CheckCircle2, AlertCircle } from 'lucide-react'
import Button from './Button'
import { registerUser } from '../services/authService'

function RegisterForm() {
  // 1. Quản lý trạng thái Form (Bảo toàn dữ liệu qua các lần re-render)
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
    confirmPassword: ''
  })

  // 2. Trạng thái giao diện hỗ trợ (UI State)
  const [showPassword, setShowPassword] = useState(false)
  const [errorMessage, setErrorMessage] = useState('')
  const [successMessage, setSuccessMessage] = useState('')
  const [isLoading, setIsLoading] = useState(false)

  // 3. Controller Handler: Cập nhật state khi người dùng gõ ký tự
  const handleChange = (e) => {
    const { name, value } = e.target
    // Gọi hàm setter kích hoạt re-render để giao diện luôn đồng bộ
    setFormData((prev) => ({
      ...prev,
      [name]: value
    }))
    if (errorMessage) setErrorMessage('')
  }

  // 4. Controller Handler: Xử lý Submit Form & Điều phối xuống Tầng Service
  const handleSubmit = async (e) => {
    e.preventDefault() // Ngăn chặn hành vi reload trang web mặc định của HTML Form

    // Validate nghiệp vụ ở Client (Tối ưu tài nguyên mạng)
    if (!formData.username.trim() || !formData.email.trim() || !formData.password) {
      setErrorMessage('Vui lòng điền đầy đủ tất cả các trường thông tin!')
      return
    }

    if (formData.password.length < 6) {
      setErrorMessage('Mật khẩu bảo mật phải có độ dài tối thiểu 6 ký tự!')
      return
    }

    if (formData.password !== formData.confirmPassword) {
      setErrorMessage('Mật khẩu xác nhận không khớp với mật khẩu đã nhập!')
      return
    }

    setIsLoading(true)
    setErrorMessage('')
    setSuccessMessage('')

    try {
      // Đóng gói DTO (Data Transfer Object)
      const payload = {
        username: formData.username.trim(),
        email: formData.email.trim().toLowerCase(),
        password: formData.password
      }

      // Gọi xuống Tầng Service (Tầng 3)
      const result = await registerUser(payload)
      setSuccessMessage(`Chào mừng ${result.data?.username || payload.username}! Bạn đã đăng ký thành công.`)
      // Reset form sau khi đăng ký thành công
      setFormData({ username: '', email: '', password: '', confirmPassword: '' })
    } catch (err) {
      setErrorMessage(err.message || 'Đăng ký tài khoản thất bại. Vui lòng thử lại!')
    } finally {
      setIsLoading(false)
    }
  }

  return (
    <div className="w-full max-w-md bg-slate-900/90 backdrop-blur-xl border border-slate-800 rounded-3xl p-8 shadow-2xl shadow-black/50">
      <div className="text-center mb-8">
        <h2 className="text-3xl font-extrabold text-white tracking-tight">Đăng Ký Tài Khoản</h2>
        <p className="text-slate-400 text-sm mt-2">Tham gia hệ thống học lập trình thực chiến cùng F8</p>
      </div>

      {/* Thông báo lỗi động bằng State */}
      {errorMessage && (
        <div className="mb-6 p-4 bg-red-500/10 border border-red-500/30 rounded-2xl flex items-center gap-3 text-red-400 text-sm">
          <AlertCircle className="w-5 h-5 shrink-0" />
          <span>{errorMessage}</span>
        </div>
      )}

      {/* Thông báo thành công động bằng State */}
      {successMessage && (
        <div className="mb-6 p-4 bg-emerald-500/10 border border-emerald-500/30 rounded-2xl flex items-center gap-3 text-emerald-400 text-sm">
          <CheckCircle2 className="w-5 h-5 shrink-0" />
          <span>{successMessage}</span>
        </div>
      )}

      <form onSubmit={handleSubmit} className="space-y-5">
        {/* Trường Username */}
        <div>
          <label className="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-2">Tên người dùng</label>
          <div className="relative">
            <User className="w-5 h-5 absolute left-4 top-1/2 -translate-y-1/2 text-slate-500" />
            <input
              type="text"
              name="username"
              value={formData.username}
              onChange={handleChange}
              placeholder="vd: f8_student"
              className="w-full bg-slate-800/60 border border-slate-700 rounded-xl pl-12 pr-4 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition"
            />
          </div>
        </div>

        {/* Trường Email */}
        <div>
          <label className="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-2">Địa chỉ Email</label>
          <div className="relative">
            <Mail className="w-5 h-5 absolute left-4 top-1/2 -translate-y-1/2 text-slate-500" />
            <input
              type="email"
              name="email"
              value={formData.email}
              onChange={handleChange}
              placeholder="hocvien@f8.edu.vn"
              className="w-full bg-slate-800/60 border border-slate-700 rounded-xl pl-12 pr-4 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition"
            />
          </div>
        </div>

        {/* Trường Mật khẩu */}
        <div>
          <label className="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-2">Mật khẩu</label>
          <div className="relative">
            <Lock className="w-5 h-5 absolute left-4 top-1/2 -translate-y-1/2 text-slate-500" />
            <input
              type={showPassword ? 'text' : 'password'}
              name="password"
              value={formData.password}
              onChange={handleChange}
              placeholder="Tối thiểu 6 ký tự"
              className="w-full bg-slate-800/60 border border-slate-700 rounded-xl pl-12 pr-12 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition"
            />
            <button
              type="button"
              onClick={() => setShowPassword((prev) => !prev)}
              className="absolute right-4 top-1/2 -translate-y-1/2 text-slate-400 hover:text-white transition"
            >
              {showPassword ? <EyeOff className="w-5 h-5" /> : <Eye className="w-5 h-5" />}
            </button>
          </div>
        </div>

        {/* Trường Xác nhận Mật khẩu */}
        <div>
          <label className="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-2">Xác nhận mật khẩu</label>
          <div className="relative">
            <Lock className="w-5 h-5 absolute left-4 top-1/2 -translate-y-1/2 text-slate-500" />
            <input
              type={showPassword ? 'text' : 'password'}
              name="confirmPassword"
              value={formData.confirmPassword}
              onChange={handleChange}
              placeholder="Nhập lại mật khẩu phía trên"
              className="w-full bg-slate-800/60 border border-slate-700 rounded-xl pl-12 pr-4 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition"
            />
          </div>
        </div>

        {/* Sử dụng Component Button Reusable có nhận đầy đủ Props & Màu sắc */}
        <Button
          type="submit"
          variant="primary"
          isLoading={isLoading}
          className="w-full mt-4 text-base py-3.5 shadow-lg shadow-blue-600/30 hover:shadow-blue-600/50"
        >
          Đăng Ký Tài Khoản
        </Button>
      </form>
    </div>
  )
}

export default RegisterForm
```

---

<a id="33-tầng-3-service-layer-giao-thức-mạng--chuẩn-hóa-dto"></a>
### 3.3. Tầng 3: Service Layer (Giao thức mạng & Chuẩn hóa DTO)

Tạo file: `src/services/authService.js`  
💡 **Tư duy logic:**
- Tách tầng Service giúp kiểm thử độc lập (Mock testing) mà không cần phụ thuộc vào React DOM.
- Tích hợp cơ chế thông minh: Khi chưa có Backend thật, tự động fallback sang chế độ **Mock Server Response** sau 800ms để học viên có thể quan sát trạng thái Loading, Thành công và Lỗi một cách sinh động.

#### Mã nguồn `src/services/authService.js`:
```javascript
/**
 * Module Dịch vụ Xác thực (Authentication Service)
 */
const API_BASE_URL = 'https://api.f8education.vn/api/v1'

export async function registerUser(userData) {
  try {
    const response = await fetch(`${API_BASE_URL}/auth/register`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(userData),
    })

    const data = await response.json()

    if (!response.ok) {
      throw new Error(data.message || `Lỗi từ máy chủ: Mã trạng thái ${response.status}`)
    }

    return data
  } catch (error) {
    // Kịch bản Mockup dành cho môi trường Local Lab khi chưa khởi chạy Backend
    console.warn('[Mock Mode Active] Chưa kết nối máy chủ thật, tự động kích hoạt phản hồi mô phỏng:', error.message)
    
    // Giả lập độ trễ mạng 800ms
    await new Promise((resolve) => setTimeout(resolve, 800))
    
    return {
      status: 'success',
      code: 201,
      message: 'Mô phỏng đăng ký thành công (Local Mock Sandbox)!',
      data: {
        id: Math.floor(Math.random() * 10000),
        username: userData.username,
        email: userData.email,
        createdAt: new Date().toISOString()
      }
    }
  }
}
```

#### 📂 MINH HỌA SỰ TIẾN HÓA CỦA THƯ MỤC SAU GIAI ĐOẠN 2:
```text
reactbuoi1/
├── 📁 src/
│   ├── 📁 components/
│   │   ├── 📄 Button.jsx         <-- [MỚI TẠO] Component Nút dùng chung (Props Forwarding)
│   │   └── 📄 RegisterForm.jsx   <-- [MỚI TẠO] Form Đăng ký ứng dụng React State
│   ├── 📁 services/
│   │   └── 📄 authService.js     <-- [MỚI TẠO] Tầng giao tiếp mạng chuyên dụng
│   ├── 📄 App.jsx                <-- [CẬP NHẬT dòng mã] Nhúng RegisterForm vào Layout đẹp mắt
│   ├── 📄 index.css              <-- [GIỮ NGUYÊN] Đang nạp Tailwind CSS v4
│   └── 📄 main.jsx               <-- [GIỮ NGUYÊN] Entry point
```

[⬆️ Quay lại Mục lục](#mục-lục)

---

<a id="4-giai-đoạn-3-kiểm-thử-toàn-diện-với-postman-bảng-test-cases"></a>
## 4. Giai đoạn 3: Kiểm thử toàn diện với Postman (Bảng Test Cases)

Để đảm bảo tính năng Đăng ký hoạt động bền bỉ, chuẩn RESTful và không bị crash hệ thống khi gặp dữ liệu xấu, các kỹ sư cần đối chiếu kiểm thử giữa Postman (tầng Backend) và Form React (tầng Frontend):

### Bảng Kịch Bản Kiểm Thử (Register API Test Cases Suite)

| STT | Mã Test Case | Kịch Bản Kiểm Thử | Endpoint & Method | Payload Body (JSON) | Expected Status | Phản Hồi Mong Đợi (Response Body) | Ý Nghĩa Nghiệp Vụ Cốt Lõi |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **01** | `TC_AUTH_01` | **Đăng ký hợp lệ (Happy Path)** | `POST /auth/register` | `{"username":"f8_lead","email":"lead@f8.edu.vn","password":"Password123"}` | **201 Created** | `{"status":"success","data":{"userId":101}}` | Mật khẩu phải được băm (hash bcrypt) trước khi lưu vào DB. |
| **02** | `TC_AUTH_02` | **Thiếu trường bắt buộc (`email`)** | `POST /auth/register` | `{"username":"f8_lead","password":"Password123"}` | **400 Bad Request** | `{"status":"error","message":"Email is required"}` | Ngăn chặn dữ liệu rác lọt xuống tầng Database. |
| **03** | `TC_AUTH_03` | **Email sai định dạng Regex** | `POST /auth/register` | `{"username":"f8_lead","email":"email_khong_co_a_cong","password":"Password123"}` | **422 Unprocessable** | `{"status":"error","message":"Invalid email format"}` | Đảm bảo email người dùng hợp lệ để gửi link xác thực. |
| **04** | `TC_AUTH_04` | **Trùng lặp Email đã đăng ký** | `POST /auth/register` | `{"username":"user2","email":"lead@f8.edu.vn","password":"Password123"}` | **409 Conflict** | `{"status":"error","message":"Email already in use"}` | Kiểm tra ràng buộc duy nhất (Unique Constraint) trong cơ sở dữ liệu. |
| **05** | `TC_AUTH_05` | **Mật khẩu dưới 6 ký tự** | `POST /auth/register` | `{"username":"user3","email":"u3@f8.edu.vn","password":"123"}` | **400 Bad Request** | `{"status":"error","message":"Password must be >= 6 chars"}` | Áp dụng chính sách bảo mật tối thiểu chống brute-force. |
| **06** | `TC_AUTH_06` | **Sai Header `Content-Type`** | `POST /auth/register` | Dữ liệu dạng Plain Text: `username=abc` | **415 Unsupported Media** | `{"status":"error","message":"Expected application/json"}` | Đảm bảo API chỉ tiếp nhận giao thức JSON RESTful. |

[⬆️ Quay lại Mục lục](#mục-lục)

---

<a id="5-sổ-tay-lỗi-kinh-điển-cần-tránh-lessons-learned"></a>
## 5. Sổ tay lỗi kinh điển cần tránh (Lessons Learned)

### 🔴 Lỗi 1: Nút Button không hiện màu hoặc không nhận sự kiện click
- **Triệu chứng:** Truyền `<Button className="bg-red-500" onClick={handleClick}>` nhưng nút vẫn màu xanh và bấm không có phản hồi gì.
- **Nguyên nhân gốc rễ:** Component con không khai báo nhận `className` và không sử dụng kỹ thuật gom/trải props `...rest` vào thẻ `<button>` gốc.
- **Khắc phục:** Luôn khai báo `{ children, className, ...rest }` và gắn `{...rest}` vào thẻ HTML đích bên trong component.

---

### 🔴 Lỗi 2: Nhầm lẫn giữa biến thông thường (`let count = 0`) và React State
- **Triệu chứng:** Khai báo `let count = 0;` trong component, mỗi lần bấm nút tăng `count++`, nhưng màn hình không cập nhật số mới; hoặc khi component render lại thì `count` bị reset về `0`.
- **Nguyên nhân gốc rễ:** Biến `let/const` là biến cục bộ của hàm (local variable). Khi hàm kết thúc hoặc re-render, biến bị hủy và tạo mới lại từ đầu. React hoàn toàn không biết biến thông thường bị thay đổi để vẽ lại màn hình.
- **Khắc phục:** Mọi dữ liệu mà sự thay đổi của nó làm ảnh hưởng tới giao diện người dùng **bắt buộc phải lưu trong `useState`**.

---

### 🔴 Lỗi 3: Đột biến trực tiếp giá trị của State (Direct State Mutation)
- **Triệu chứng:** Viết `formData.username = 'abc'` thay vì `setFormData(...)`, giao diện không cập nhật.
- **Nguyên nhân gốc rễ:** React so sánh nông (Shallow Compare - Object.is) giữa con trỏ state cũ và state mới. Khi sửa trực tiếp vào thuộc tính của object, địa chỉ vùng nhớ không đổi, React cho rằng không có gì thay đổi nên không kích hoạt re-render.
- **Khắc phục:** Luôn tạo ra một bản sao object mới bằng cú pháp Spread: `setFormData(prev => ({ ...prev, username: 'abc' }))`.

---

### 🔴 Lỗi 4: Quên `e.preventDefault()` trong sự kiện Submit Form
- **Triệu chứng:** Bấm nút "Đăng Ký", trang web bỗng nhiên giật một cái và load lại từ đầu, toàn bộ thông tin vừa nhập biến mất sạch.
- **Nguyên nhân gốc rễ:** Hành vi mặc định của thẻ `<form>` trong trình duyệt là gửi một HTTP GET/POST và tải lại toàn bộ trang (Full Page Reload).
- **Khắc phục:** Luôn đặt dòng `e.preventDefault();` ngay ở dòng đầu tiên của hàm xử lý sự kiện submit.

---

### 🔴 Lỗi 5: Đọc giá trị State ngay lập tức sau khi gọi hàm `setState`
- **Triệu chứng:**
  ```javascript
  setCount(count + 1);
  console.log(count); // Vẫn in ra giá trị cũ!
  ```
- **Nguyên nhân gốc rễ:** Hàm `setState` trong React là **bất đồng bộ (Asynchronous / Batching)**. React gom các lần cập nhật lại để tối ưu hóa hiệu năng render 1 lần duy nhất, nên giá trị biến `count` ở dòng tiếp theo chưa được cập nhật ngay trong cùng một lượt thực thi hàm.
- **Khắc phục:** Sử dụng hàm callback `setCount(prev => prev + 1)` hoặc dùng `useEffect` nếu muốn thực hiện hành động dựa trên giá trị state mới nhất.

---
[⬆️ Quay lại Mục lục](#mục-lục)

