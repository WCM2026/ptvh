# PTVH — Hệ thống quản lý công việc/KPI (WinCommerce)

Frontend tĩnh (1 file `index.html`, không cần build) — host trên GitHub Pages.
Backend: Google Apps Script Web App (xem thư mục `gas/` — copy vào Apps Script
editor gắn với Google Sheet database).

## Deploy lần đầu

1. Tạo repo mới trên GitHub (Public hoặc Private đều được — Pages hoạt động với cả hai
   nếu tài khoản có GitHub Pro/Team; repo Public thì miễn phí mọi loại tài khoản).
2. Đẩy các file trong thư mục này lên nhánh `main` (xem lệnh git bên dưới).
3. Vào repo trên GitHub → **Settings → Pages**.
4. Ở mục **Build and deployment → Source**, chọn **Deploy from a branch**.
5. Chọn **Branch: main**, thư mục **/ (root)** → **Save**.
6. Đợi 1–2 phút, GitHub hiện link dạng:
   `https://<ten-tai-khoan>.github.io/<ten-repo>/`
7. Mở link đó → vào tab **Cài đặt** trong app → dán **URL Backend PTVH**
   (URL Web App Apps Script đã deploy) → **Lưu cấu hình** → thử đăng nhập.

## Lệnh git mẫu (chạy trên máy bạn, trong thư mục chứa các file này)

```bash
git init
git add index.html README.md .nojekyll
git commit -m "PTVH: deploy frontend lên GitHub Pages"
git branch -M main
git remote add origin https://github.com/<ten-tai-khoan>/<ten-repo>.git
git push -u origin main
```

## Cập nhật sau này

Mỗi khi sửa `demoluongptvh.html`, ghi đè lại thành `index.html` trong repo rồi:

```bash
git add index.html
git commit -m "Cập nhật giao diện"
git push
```

GitHub Pages tự build lại sau vài chục giây, không cần thao tác gì thêm ở phần Settings.

## Lưu ý quan trọng

- `ptvhApiUrl` (URL Backend) hiện đang lưu **trong bộ nhớ trình duyệt của phiên
  đang mở** (biến JS, không phải localStorage) — nghĩa là **mỗi lần tải lại trang,
  người dùng phải nhập lại URL này ở tab Cài đặt** cho tới khi việc này được "hard-code"
  thẳng vào file `index.html` trước khi đẩy lên GitHub (khuyến nghị cho bản chính thức
  — xem mục "Trước khi golive chính thức" bên dưới).
- File tĩnh trên GitHub Pages **không có cách nào giữ bí mật** — ai có link đều xem được
  mã nguồn JS. Điều này không sao vì toàn bộ logic nhạy cảm (xác thực, đọc/ghi dữ liệu)
  nằm ở Apps Script backend, frontend chỉ gọi API. Tuyệt đối không hard-code bất kỳ
  API key/secret nào của Apps Script vào file này ngoài URL Web App (URL đó tự nó
  không phải bí mật — bảo mật thật sự nằm ở xác thực OTP/PIN + token phiên phía backend).

## Trước khi golive chính thức

✅ Đã hard-code sẵn URL Backend vào `index.html`:
```js
var ptvhApiUrl = 'https://script.google.com/macros/s/AKfycbxRVhHxdp_R9-oWz0l4MO2MidnqoIMlZ5h6_Y2iwV9qwmhusDlF_DR0UE6fpII4_efM/exec';
```
Không cần vào tab Cài đặt nhập tay nữa — mở trang lên là gọi thẳng vào backend này.
Nếu sau này deploy phiên bản Apps Script mới (URL đổi), có 2 cách cập nhật:
- Sửa lại dòng trên trong `index.html` rồi `git push` lại, HOẶC
- Vào tab Cài đặt trên trang đang chạy, dán URL mới, bấm Lưu cấu hình (chỉ có hiệu lực
  cho phiên đang mở, mất khi tải lại trang — dùng để test nhanh trước khi sửa file gốc).
