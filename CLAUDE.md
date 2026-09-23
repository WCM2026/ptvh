# CLAUDE.md — PTVH

Cổng quản lý công việc/KPI của phòng PTVH (WinCommerce). Trả lời và viết commit bằng tiếng Việt.

## Kiến trúc

- **Frontend**: một file duy nhất `index.html` (HTML + CSS + JS thuần, không build, không framework, không npm). Deploy qua GitHub Pages.
- **Backend**: Google Apps Script Web App + Google Sheet. Mã `.gs` **không nằm trong repo** (repo công khai — xem `.gitignore`); người dùng dán trực tiếp vào Apps Script. Khi đổi/thêm action phía frontend, phải nói rõ phần `.gs` tương ứng cần sửa thế nào.
- Không có test tự động. Kiểm tra bằng cách mở `index.html` trong trình duyệt (Playwright/Chromium) và xem console không có lỗi JS.

## Bố cục `index.html`

| Dòng (xấp xỉ) | Nội dung |
|---|---|
| `<style>` đầu file | Toàn bộ CSS |
| `<section class="panel" id="panel-<id>">` | Mỗi tab là một panel: `login`, `dashboard`, `work`, `programs`, `competitions`, `budget`, `gifts`, `vouchers`, `webapps`, `kpi`, `docs`, `settings` |
| `<script>` cuối file | Toàn bộ JS, chia khối bằng comment `/* ---------------- TÊN KHỐI ---------------- */` |

Điều hướng: `go(id)` bật panel `panel-<id>` và gọi hàm `render...()` của tab đó. Thêm tab mới = thêm `<section>`, mục sidebar (`renderSidebar()`), và nhánh trong `go()`.

## Gọi API

- Mọi lời gọi backend đi qua `callPtvhApi(action, payload)`: `POST` body JSON với `Content-Type: text/plain` (tránh CORS preflight), tự gắn `token: sessionToken`.
- Kết quả dạng `{ ok, error, message, ... }`. Mẫu xử lý lỗi dùng thống nhất:
  ```js
  if (r.error !== 'NO_API_URL' && r.error !== 'NETWORK_ERROR') showToast(r.message || '...');
  ```
- `UNAUTHORIZED` → tự đăng xuất (phiên 8 tiếng). URL backend ở biến `ptvhApiUrl`, chỉ chấp nhận `script.google.com`.
- Quy ước tên action: `get<X>List`, `add<X>`, `update<X>`, `delete<X>`. Dữ liệu từ API được chuẩn hoá qua hàm `map<X>FromApi_()`.
- Đăng nhập: email → OTP (lần đầu) → đặt PIN → đăng nhập bằng PIN. Quyền quản trị: `isAdminRole()` = "Trưởng phòng" / "Trưởng nhóm".

## Trạng thái các module

- **Đã nối API thật**: Công việc (Module 2), Sự kiện + Đợt voucher (Module 3), Thi đua (Module 4), Ngân sách 05/06 (Module 5), nhân sự, cài đặt thông báo.
- **Còn demo / dữ liệu trong bộ nhớ**: Quà tặng (`gifts`), KPI (`saveKpi()` chỉ hiện toast), Tài liệu (`docs`), Web app.

## Quy tắc bắt buộc

- **Chống XSS**: mọi dữ liệu từ Sheet/API chèn vào HTML phải qua helper:
  - `esc(v)` — nội dung HTML và giá trị thuộc tính
  - `jsArg(v)` — tham số trong `onclick="fn(...)"` (tự thêm nháy)
  - `safeUrl(u)` — `href`/`src`
- **Không commit** mã `.gs`, file `.xlsx/.csv`, dữ liệu nhân sự, token hay khoá API (repo công khai).
- Giữ phong cách hiện có: JS thuần, `async/await`, tên hàm/biến tiếng Việt không dấu (`congViec`, `suKien`, `nganSach`), hàm nội bộ có hậu tố `_`, comment tiếng Việt.
- Sửa có mục tiêu bằng Edit, không viết lại cả file.
