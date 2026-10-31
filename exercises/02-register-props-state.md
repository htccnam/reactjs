# BÀI TẬP 02: HOÀN THIỆN TÍNH NĂNG ĐĂNG KÝ (REGISTER)
> **Chủ đề:** Làm chủ Kỹ thuật Reusable Button (Props Forwarding) & Khai phá Bản chất Cốt lõi của React State.  
> **Tài liệu tham khảo lời giải chi tiết:** [docs/02-register-props-state.md](../docs/02-register-props-state.md)

---

## 🎯 Bối cảnh & Mục tiêu bài tập

Trong quá trình xây dựng giao diện Form Đăng ký (Register Form), các lập trình viên thường đối mặt với hai vấn đề kỹ thuật lớn:
1. **Vấn đề Component hóa nút bấm:** Tách thẻ `<button>` ra thành Component `<Button>` riêng để tái sử dụng, nhưng nút **không nhận màu sắc** khi truyền class từ ngoài vào hoặc **không thể click/submit** được do bỏ sót thuộc tính.
2. **Vấn đề quản trị dữ liệu người dùng:** Chưa hiểu rõ vì sao mỗi khi gõ phím, hàm component lại chạy lại (Re-render) mà các chữ cái đã gõ trước đó không bị biến mất.

Bài tập này yêu cầu bạn hoàn thiện tính năng Đăng ký tài khoản hoàn chỉnh theo chuẩn 3 tầng kiến trúc, làm chủ kỹ thuật **Gom & Truyền toàn bộ Props** và **Bản chất lưu trữ State trong Fiber Node**.

---

## 📋 Danh sách nhiệm vụ chi tiết (Checklist)

### Nhiệm vụ 1: Thiết lập hạ tầng & Tiện ích
- [ ] Cài đặt hai thư viện hỗ trợ: `clsx` (xử lý ghép class điều kiện) và `lucide-react` (icon giao diện).
- [ ] Bóc tách và nắm rõ vai trò của từng thư viện trong dự án.

### Nhiệm vụ 2: Xây dựng Reusable Button Component (`src/components/Button.jsx`)
- [ ] Giải quyết triệt để vấn đề nút không hiện màu và mất sự kiện bằng kỹ thuật **Props Forwarding**:
  - Khai báo nhận `{ children, variant, className, isLoading, disabled, ...rest }`.
  - Trải toàn bộ props thừa `{...rest}` vào thẻ `<button>` gốc của HTML.
- [ ] Định nghĩa các biến thể màu sắc (Variants) bằng Tailwind CSS: `primary` (xanh), `secondary` (xám), `danger` (đỏ), `outline`.
- [ ] Tích hợp trạng thái `isLoading` hiển thị spinner quay khi đang gửi dữ liệu.

### Nhiệm vụ 3: Xây dựng Form Đăng Ký (`src/components/RegisterForm.jsx`)
- [ ] Ứng dụng bản chất của **React State**:
  - Khai báo `useState` để lưu thông tin Form: `username`, `email`, `password`, `confirmPassword`.
  - Giải thích được cơ chế: *Vì sao khi gọi `setFormData`, component re-render nhưng dữ liệu gõ trong input không bị reset?*
- [ ] Thiết kế giao diện Form chuyên nghiệp với Tailwind CSS:
  - Input tên tài khoản (Username) kèm Icon User.
  - Input Email kèm Icon Mail.
  - Input Mật khẩu có nút bấm con mắt chuyển đổi Ẩn/Hiện mật khẩu (`Eye` / `EyeOff`).
  - Input Xác nhận mật khẩu.
- [ ] Xử lý sự kiện `handleSubmit` có `e.preventDefault()`, validate độ dài mật khẩu $\ge 6$ ký tự và trùng khớp xác nhận.
- [ ] Nhúng Component `<Button type="submit" variant="primary">` đã tạo ở Nhiệm vụ 2.

### Nhiệm vụ 4: Xây dựng Tầng Dịch Vụ (`src/services/authService.js`)
- [ ] Tạo hàm bất đồng bộ `registerUser(payload)` gửi HTTP POST Request tới API backend.
- [ ] Tích hợp chế độ **Mock Server Response** (giả lập phản hồi thành công sau 800ms) để kiểm thử trạng thái Loading và Alert trên giao diện.

---

## ✅ Tiêu chí nghiệm thu (Definition of Done)

1. **Hiển thị & Style:** Form Đăng ký căn giữa màn hình với nền gradient sang trọng, các input có icon, nút bấm hiển thị chuẩn màu xanh Tailwind (`bg-blue-600`) và có hiệu ứng hover/active mượt mà.
2. **Kỹ thuật Button:** Nút bấm `<Button>` nhận được đầy đủ prop `className` tùy biến từ ngoài vào mà không bị mất màu nền, nhận prop `type="submit"` và tự động hiển thị icon quay khi `isLoading = true`.
3. **Bản chất State:** Gõ vào các trường input dữ liệu được cập nhật tức thì (mỗi ký tự gõ kích hoạt re-render mà không mất các ký tự trước đó). Nút con mắt ẩn/hiện mật khẩu hoạt động chuẩn xác.
4. **Validation:** Khi bỏ trống hoặc mật khẩu $< 6$ ký tự, form hiển thị Alert lỗi màu đỏ. Khi điền đúng, hiển thị Alert chúc mừng màu xanh lá.

