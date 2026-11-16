# Mỏ Vàng Số · website, thanh toán tự động, email chăm sóc

Website bán chương trình "Mỏ Vàng Số · 21 ngày" của Trúc Linh. Không có máy chủ riêng: trang web nằm trên GitHub Pages, phần xử lý nằm trong Google Apps Script, dữ liệu nằm trong Google Sheet.

## Các phần và vai trò

| Phần | Làm gì | Nằm ở đâu |
|---|---|---|
| `index.html` | Trang chủ, form danh sách chờ, chatbot Bé Vàng | GitHub Pages, thư mục gốc |
| `thanh-toan.html` | Tạo đơn, hiện mã QR VietQR, tự chuyển sang "Đã nhận thanh toán" | GitHub Pages, thư mục gốc |
| `admin/index.html` | Trang quản lý (đơn, khách, thêm đơn). Cần mật khẩu mới thấy dữ liệu | GitHub Pages, thư mục `admin` |
| Apps Script (`apps_script_FULL_v8.txt`) | Nhận form, tạo đơn, nhận tin báo tiền vào từ Sepay, gửi email qua Resend | Google Apps Script, triển khai dạng ứng dụng web |
| Google Sheet | Lưu khách, đơn hàng, nhật ký Sepay, nhật ký email | Tài khoản Google của Linh (riêng tư) |
| Sepay | Báo về khi có tiền vào tài khoản | Webhook trỏ về ứng dụng web |
| Resend | Gửi email từ `hi@truclinhmovangso.com` | Đã xác minh tên miền |

Luồng chính: khách điền form, nhận email chào mừng; bấm mua, quét QR, chuyển khoản; Sepay báo về, đơn tự chuyển `success`, khách nhận email xác nhận.

## Khoá và mật khẩu (không bao giờ nằm trong mã hay trong kho GitHub)

Tất cả nằm trong Apps Script, mục Cài đặt dự án, Thuộc tính của tập lệnh:

| Tên | Là gì |
|---|---|
| `ADMIN_KEY` | Mật khẩu vào trang admin |
| `SEPAY_KEY` | Chuỗi bí mật nằm trong địa chỉ webhook của Sepay (`...?sepay=<chuỗi>`) |
| `RESEND_KEY` | Khoá API của Resend (quyền chỉ gửi email) |
| `REPLY_TO` | Email nhận thư khách trả lời |

Chạy hàm `kiemTraKhoa` trong Apps Script để xem đã cài đủ chưa (hàm chỉ báo có hoặc chưa có, không in giá trị). Nếu thiếu `ADMIN_KEY` thì không ai vào được admin, nếu thiếu `SEPAY_KEY` thì Sepay bị từ chối.

## Cách đưa lên mạng (deploy cơ bản)

1. **Trang web:** tải đúng 3 file lên kho GitHub này, đúng vị trí: `index.html`, `thanh-toan.html` ở gốc, `admin/index.html` trong thư mục `admin`. Tải từng file một, vì các file cùng tên `index.html` sẽ đè lên nhau nếu tải chung một lượt. Chờ 1 đến 2 phút, bấm Ctrl+F5 để xem.
2. **Tên miền:** bản ghi A trỏ về 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153; `www` là CNAME trỏ về `linhmegao.github.io`. Trong GitHub, Settings, Pages, Custom domain: `truclinhmovangso.com`, bật Enforce HTTPS.
3. **Apps Script:** dán toàn bộ mã vào `Code.gs`, lưu. Cài các thuộc tính ở trên. Chạy hàm `caiTrigger` một lần (cho phép quyền và cài chạy email mỗi giờ). Triển khai, Quản lý các bản triển khai, chỉnh sửa bản đang dùng, chọn Phiên bản mới. Giữ nguyên địa chỉ ứng dụng web để website không phải sửa.
4. **Sepay:** webhook trỏ về địa chỉ ứng dụng web kèm `?sepay=<SEPAY_KEY>`.
5. **Resend:** tên miền đã xác minh bằng bản ghi DKIM, SPF, DMARC trong DNS.

## Cách thử

- Thanh toán thử 2.000đ: mở `thanh-toan.html?test=1`.
- Thử chuỗi 3 email ngay: điền form với email dạng `ten+test@gmail.com`, nhận cả 3 email có chữ `[TEST]`.
- Thêm đơn bằng tay và gửi email xác nhận: trang admin, tab Thêm đơn.

## Không được đưa lên mạng

- `brain.db`, thư mục `data/`, `admin.html` bản cục bộ (chứa tên, số điện thoại khách).
- Mọi file ghi khoá, mật khẩu, địa chỉ webhook đầy đủ.
- Đường dẫn Google Sheet và nội dung nhật ký Sepay.

## Sao lưu

`backup/brain_backup_2026-11-16.db` là bản sao `brain.db` lúc 16/11/2026. Dữ liệu thật hiện nằm trong Google Sheet, nên Sheet mới là thứ cần sao lưu thường xuyên (Tệp, Tạo bản sao).

## Khi đổi mật khẩu admin

Vào Apps Script, Cài đặt dự án, sửa giá trị `ADMIN_KEY`. Không cần triển khai lại.
