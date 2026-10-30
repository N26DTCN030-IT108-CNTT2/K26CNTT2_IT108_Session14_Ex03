# K26CNTT2_IT108_Session14_Ex03

# 🚀 Báo cáo Bài tập: Ngăn chặn quá tải lưu trữ khi đồng bộ mã nguồn
**Môn học:** Nhập Môn Công Nghệ Thông Tin (Buổi 15: Không gian cộng tác đám mây)  
**Học viên:** Lê Hồ Thanh Thảo (N26DTCN030)  
**Repository:** `K26CNTT2_IT108_Session14_Ex03`

---

## 1. Mục tiêu
- Thiết kế kiến trúc đồng bộ tự động giữa Local và Cloud sao cho tối ưu, an toàn.
- Xử lý triệt để bẫy dữ liệu phát sinh trong quá trình đồng bộ (đặc biệt là thư mục nặng `node_modules` trong dự án JavaScript).

---

## 2. Bối cảnh & Vấn đề
> **Tình huống:** Khi phát triển dự án Startup bằng JavaScript, thư mục dự án thường chứa hàng vạn tệp tin nhỏ nằm sâu bên trong thư mục `node_modules`. Nếu bật chế độ Auto Sync toàn bộ, phần mềm đồng bộ đám mây sẽ bị treo (`freeze`) và làm tràn dung lượng Cloud một cách lãng phí.

---

## 3. Yêu cầu đầu ra

### Phần 1: Báo cáo phân tích luồng dữ liệu (I/O)
*Quy trình diễn ra từ lúc lập trình viên nhấn `Ctrl+S` cho đến khi tệp tin được đồng bộ thành công lên mây:*

1. **Ghi nhận tại tầng Local (`Input`):** Khi bạn chỉnh sửa mã nguồn và nhấn tổ hợp phím `Ctrl+S`, hệ điều hành sẽ ghi trực tiếp nội dung mới vào tệp tin mã nguồn nằm trên ổ cứng SSD cá nhân.
2. **Kích hoạt trình theo dõi tệp tin (`File System Watcher`):** Phần mềm đồng bộ đám mây đang chạy ngầm phát hiện sự thay đổi (`modified timestamp`) của tệp tin vừa được ghi nhận.
3. **Kiểm tra quy tắc loại trừ (`Exclusion Filter Check`):** Phần mềm quét qua danh sách quy tắc (như tệp `.gitignore`). Nếu tệp thuộc danh mục bị chặn (ví dụ: `node_modules`), tiến trình sẽ lập tức bỏ qua để tránh quá tải.
4. **Tải lên Cloud (`Output`):** Nếu là tệp mã nguồn hợp lệ (như `index.js`, `package.json`), gói dữ liệu thay đổi được mã hóa và truyền tải qua Internet lên máy chủ đám mây.
5. **Hoàn tất đồng bộ (`Sync Complete`):** Khi máy chủ Cloud xác nhận nhận dữ liệu thành công, biểu tượng **tích xanh (Green Checkmark)** xuất hiện bên cạnh tệp tin, thông báo dữ liệu Local và Cloud đã đồng nhất hoàn toàn.

---

### Phần 2: Thiết kế sơ đồ tổ chức thư mục Cloud chuẩn cho Lập trình viên
*Áp dụng mô hình **Hybrid Workflow** kết hợp tính năng **Files On-Demand** để giữ máy tính chạy nhanh mượt trên SSD cục bộ nhưng vẫn an toàn tuyệt đối trên Cloud:*

```text
📁 My_Cloud_Workspace/                 <-- Thư mục gốc đồng bộ trên Cloud
│
├── 📁 01_Code_Projects/               <-- Chứa mã nguồn dự án (Đã cấu hình loại trừ node_modules)
│   └── 📁 startup-smart-food/
│       ├── 📄 package.json            <-- Quản lý danh sách thư viện (Thay vì đồng bộ node_modules)
│       ├── 📄 package-lock.json
│       ├── 📁 src/                    <-- Source code chính (Đồng bộ thời gian thực)
│       └── 📄 .gitignore              <-- Khai báo bỏ qua node_modules và các tệp tạm thời
│
├── 📁 02_Project_Docs/                <-- Tài liệu đặc tả, sơ đồ, báo cáo dự án
│   ├── 📄 SRS_SmartFood.docx
│   └── 📊 WBS_Project_Plan.xlsx
│
└── 📁 03_Assets_Media/                <-- Tài nguyên hình ảnh, video (Bật chế độ Files On-Demand)
    ├── 🖼️ logo-brand.png
    └── 🎬 demo-intro.mp4
