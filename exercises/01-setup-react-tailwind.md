# BÀI TẬP 01: KHỞI TẠO DỰ ÁN REACT (VITE) & TÍCH HỢP TAILWIND CSS V4

> **Mục tiêu:** Tự tay thiết lập môi trường phát triển React hiện đại từ con số 0, dọn dẹp mã nguồn mặc định và cấu hình thành công Tailwind CSS v4.  
> **Tài liệu tham khảo lời giải chi tiết:** [docs/01-setup-react-tailwind.md](../docs/01-setup-react-tailwind.md)

---

## 🎯 Đề bài & Yêu cầu kỹ thuật

### Nhiệm vụ 1: Khởi tạo dự án
- [ ] Sử dụng công cụ **Vite** để khởi tạo dự án React (Javascript thuần).
- [ ] Đặt tên dự án là `reactbuoi1`.
- [ ] Chạy lệnh tải các thư viện dependencies cơ bản.

### Nhiệm vụ 2: Dọn dẹp mã nguồn thừa (Clean Boilerplate)
- [ ] Xóa bỏ file `src/App.css`.
- [ ] Dọn dẹp `src/App.jsx`: Xóa các logo, CSS import cũ và khối code đếm số, chỉ giữ lại khung component cơ bản.
- [ ] Xóa sạch nội dung CSS mặc định trong `src/index.css`.
- [ ] Tạm thời comment/tắt `<StrictMode>` trong `src/main.jsx`.

### Nhiệm vụ 3: Tích hợp Tailwind CSS v4
- [ ] Cài đặt bộ đôi thư viện `tailwindcss` và `@tailwindcss/vite`.
- [ ] Đăng ký plugin `tailwindcss()` vào mảng `plugins` trong file `vite.config.js`.
- [ ] Kích hoạt chỉ thị `@import "tailwindcss";` trong `src/index.css`.
- [ ] Đảm bảo `src/main.jsx` đã import file `index.css`.

### Nhiệm vụ 4: Kiểm thử hiển thị giao diện
- [ ] Trong `src/App.jsx`, viết một thẻ `<p>` có nội dung `hellof8`.
- [ ] Sử dụng class của Tailwind CSS để làm cho chữ **màu đỏ** (ví dụ: `text-red-500` hoặc `text-[red]`).
- [ ] Khởi động dev server (`npm run dev`) và mở trình duyệt để xác thực kết quả.

---

## ✅ Tiêu chí nghiệm thu (Definition of Done)
1. Dev server khởi động không có bất kỳ thông báo lỗi nào trên terminal.
2. Mở trình duyệt tại `http://localhost:5173/`, dòng chữ `hellof8` hiển thị đúng màu đỏ.
3. Không còn file thừa `src/App.css` trong cây thư mục mã nguồn.

