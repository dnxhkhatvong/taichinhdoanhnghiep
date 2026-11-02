# Quản trị tài chính DNXH Khát Vọng (phiên bản 2)

Web quản trị tài chính một trang (HTML thuần, không cần build) cho Công ty cổ phần – Doanh nghiệp xã hội Khát Vọng. Dữ liệu nằm trong Google Sheet trên Google Drive, mỗi người đăng nhập bằng tài khoản riêng.

## Có gì trong bản này
- **Nhập một lần**: phiếu thu chi liên kết hóa đơn, chi phí, cổ đông, quỹ tài trợ, phân phối lợi nhuận. Công nợ, số dư tiền, vốn đã góp tự cập nhật.
- **Thuế suất từng dòng** (KCT, 0, 5, 8, 10%), VAT tự tính.
- **51% lợi nhuận sau thuế** theo Điều 10 Luật Doanh nghiệp 2020, tính từ sổ Phân phối lợi nhuận, tách khỏi chi phí hoạt động.
- **Công ty cổ phần**: sổ cổ đông theo số cổ phần, vốn điều lệ, cảnh báo quá 90 ngày chưa góp đủ.
- **Khấu hao tự động** từ sổ Tài sản vào Kết quả kinh doanh.
- **Quỹ tài trợ**: tách tiền tài trợ chưa dùng khỏi tiền tự do, báo cáo cho nhà tài trợ.
- **Đối chiếu ngân hàng**: nạp sao kê CSV, tự khớp, tạo phiếu từ dòng sao kê.
- **Kiểm soát**: tài khoản riêng từng người, khóa sổ theo tháng, nhật ký thay đổi, chống ghi đè khi hai người sửa cùng lúc, sao lưu tự động hằng ngày.
- **Báo cáo**: Tổng hợp (tiền tự do, số tháng đủ chi, DSO, chỉ số tác động), Kết quả kinh doanh, Dòng tiền, Công nợ và tuổi nợ, Tái đầu tư 51%, Cổ đông, Tài trợ, Ngân sách. Xuất Excel.
- **Dễ dùng cho người khuyết tật vận động**: form cỡ lớn, nút to, dùng được hoàn toàn bằng bàn phím (Tab, Enter để lưu, Esc để đóng).

## Các tệp
| Tệp | Vai trò |
|---|---|
| `index.html` | Toàn bộ ứng dụng |
| `config.json` | `apiUrl`: địa chỉ Web App Apps Script. `driveUrl`: liên kết file Google Sheet (chỉ Admin và Quản trị thấy) |
| `apps-script/Code.gs` | Máy chủ dữ liệu: đăng nhập, phân quyền, kiểm tra dữ liệu, khóa sổ, nhật ký, sao lưu |

## Vai trò
| Vai trò | Xem | Ghi |
|---|---|---|
| Admin | Tất cả | Tất cả, quản lý người dùng, khóa và mở khóa sổ, sửa được kỳ đã khóa (có ghi nhật ký) |
| Quản trị | Tất cả trừ Người dùng | Mọi sổ trừ Danh mục và Tài khoản tiền; ngân sách, thuế, tỷ lệ 51%, đối chiếu ngân hàng |
| Nhân viên | Hướng dẫn, Hóa đơn bán hàng, Chi phí, Thu chi tiền, Công nợ khác | Thêm mới; chỉ sửa dòng do mình tạo; không xóa; không ghi vào kỳ đã khóa |

Máy chủ kiểm tra lại quyền ở mọi thao tác, nên sửa mã trong trình duyệt không vượt được quyền.

## Cài đặt (khoảng 15 phút)
1. **Google Sheet**: mở file "Khát Vọng - Cơ sở dữ liệu tài chính" trên Drive (hoặc tạo file mới). Các tab tự tạo ở lần chạy đầu.
2. **Apps Script**: trong file Sheet chọn Extensions > Apps Script, xóa mã mẫu, dán toàn bộ `apps-script/Code.gs`, lưu.
3. **Mật khẩu Admin lần đầu**: Project Settings > Script properties > thêm `ADMIN_INIT_PASS` = mật khẩu tối thiểu 8 ký tự.
4. **Triển khai**: Deploy > New deployment > Web app. Execute as: **Me**. Who has access: **Anyone**. Cấp quyền, sao chép địa chỉ kết thúc bằng `/exec`.
5. **Sao lưu tự động**: trong trình soạn Apps Script chọn hàm `installTriggers` > Run (một lần). Mỗi đêm một bản sao vào thư mục Drive "Khát Vọng - Sao lưu", giữ 30 bản gần nhất.
6. **Config**: dán địa chỉ `/exec` vào `apiUrl` trong `config.json`, commit lên GitHub.
7. **GitHub Pages**: Settings > Pages, nhánh `main`, thư mục `/ (root)`. Mở `https://<tài-khoản>.github.io/<tên-repo>/`.
8. **Lần đăng nhập đầu**: tên đăng nhập `admin`, mật khẩu là `ADMIN_INIT_PASS`. Hệ thống tạo danh mục và tài khoản tiền mặc định. Vào Kiểm soát > Người dùng để tạo tài khoản cho từng người, rồi đổi mật khẩu admin (nút Tài khoản) và **xóa** `ADMIN_INIT_PASS` khỏi Script properties.

Khi sửa `Code.gs` sau này: Deploy > Manage deployments > Edit > Version: New version (giữ nguyên địa chỉ `/exec`).

Khi `apiUrl` để trống, trang chạy chế độ cục bộ với dữ liệu mẫu, lưu trong trình duyệt, không cần đăng nhập. Dùng để xem thử và đào tạo.

## Quy trình hằng tháng
1. Hằng ngày: nhập Hóa đơn bán hàng, Chi phí; khi thu hoặc trả tiền, lập phiếu ở Thu chi tiền và chọn chứng từ liên kết.
2. Cuối tháng: Kiểm soát > Đối chiếu ngân hàng, nạp sao kê CSV, Tự khớp, xử lý dòng còn lại. Kiểm kê quỹ tiền mặt rồi đánh dấu "Đã đối chiếu".
3. Nhập thuế TNDN của tháng ở Kết quả kinh doanh (nhập 0 nếu không có).
4. Xem Tổng hợp và các cảnh báo, rồi Khóa sổ tháng.
5. Cuối năm: sau quyết toán, ghi quyết định ở Phân phối lợi nhuận (tối thiểu 51% lợi nhuận sau thuế cho "Tái đầu tư mục tiêu xã hội").

## Định dạng sao kê CSV
Cần dòng tiêu đề có cột ngày (dd/mm/yyyy hoặc yyyy-mm-dd), mô tả, và **một** trong hai cách ghi số tiền: một cột "Số tiền" (vào dương, ra âm) hoặc hai cột "Ghi nợ" (tiền ra) và "Ghi có" (tiền vào). Dấu phân cách `;`, `,` hoặc tab đều được.

## Lưu ý
- Đây là công cụ quản trị. Sổ kế toán và báo cáo tài chính pháp định lập trên phần mềm kế toán theo Thông tư 99/2025/TT-BTC. Kế toán cần xác nhận cách hạch toán tài trợ, khấu hao và phân phối lợi nhuận.
- Mỗi lần lưu, máy chủ kiểm tra phiên bản từng dòng; nếu người khác vừa sửa cùng dòng, thay đổi bị từ chối và trang tải lại bản mới.
- Dữ liệu tài chính không nằm trong repo. Địa chỉ Web App công khai nhưng chỉ trả dữ liệu khi đăng nhập đúng; sai 5 lần bị khóa 15 phút.
