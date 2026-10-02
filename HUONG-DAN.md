# MATHTIKZ-PRO — bản HTML/JavaScript + Apps Script

Bản này dùng cùng cách tổ chức với bộ `mo-hinh-3d` bạn cung cấp: các trang HTML độc lập, JavaScript/CSS dùng chung, máy chủ Google Apps Script. Không dùng React hoặc Node.js khi triển khai. Bản React trước đó vẫn ở thư mục cha để đối chiếu.

## 1. Cấu trúc

```
index.html                  Đăng nhập
thu-vien.html               Dữ liệu Hình TikZ (mỗi hình có nút Edit để copy/sửa mã, đổi tên)
hinh-thuc-te.html           (đã bỏ) tự chuyển sang thu-vien.html
them-mau.html               Thêm/sửa mã, build, AI sửa mã khi lỗi, copy và tải file
ve-theo-de.html              Tìm mẫu và AI vẽ theo đề
quet-hinh.html               Nhập file/folder, AI lọc hình thực tế và duyệt ảnh
cai-dat.html                API của 4 hãng, macro, máy chủ, mật khẩu
quan-tri.html               Quản trị tài khoản
chung/
  cau-hinh.js               Tên web và URL Apps Script
  dang-nhap.js              Tài khoản, phiên, gọi API
  tikz.js                   Xử lý mã và tìm mẫu
  kho-du-lieu.js            Drive/API và bản nháp
  khung-web.js              Thanh menu và trạng thái
  ai-cau-hinh.js            Model và cấu hình giao diện API
  quet-tikz.js              Tách nhiều hình và lấy style/macro
  giao-dien.css, web.css, theme.css  Giao diện và tông xanh đen
trang/                      JavaScript riêng cho từng trang
apps-script/
  Code.gs                   Tài khoản và phân quyền
  KhoTikz.gs                Drive, build TikZ và AI
  AI.gs                     Gemini, OpenAI, Claude, DeepSeek và nhận dạng
  SuaMa.gs                  Lệnh suaMaAI: build lấy log lỗi, AI sửa, build kiểm tra lại
  appsscript.json           Quyền dịch vụ Google
```

## 2. Xem thử

Tên hiển thị: **MATHTIKZ-PRO**. Giao diện dùng tông xanh đen; thanh chức năng nằm dọc bên trái, kể cả khi cửa sổ thu nhỏ. Trang đăng nhập riêng nằm ở index.html và có nút hiện/ẩn mật khẩu.

Nhấp đúp `index.html`; khi chưa đặt API_URL, đăng nhập bằng `admin/admin123`, `gv/gv123` hoặc `hs/hs123`. Chế độ này chỉ dùng tài khoản/bản nháp trong trình duyệt, không lưu lên Drive và không gọi AI. Build hình vẫn cần mạng và dịch vụ TikZ cho phép trình duyệt truy cập; nếu bị CORS, dùng bản đã nối Apps Script.

Bản nháp dùng IndexedDB. Khi mở trực tiếp file://, khả năng chia sẻ bản nháp giữa các trang tùy trình duyệt; dùng GitHub Pages hoặc máy chủ HTTP để có cùng vùng lưu trữ ổn định. Code vẫn có thể nhập/build/tải ngay trên trang sửa.

## 3. Cài máy chủ bằng Gmail của chủ kho

1. Mở https://script.google.com, tạo dự án riêng.
2. Dán `apps-script/Code.gs` vào Code.gs; tạo tệp KhoTikz và dán `KhoTikz.gs`; tạo tệp AI và dán `AI.gs`. Cần cả 3 tệp trong cùng dự án Apps Script.
3. Chạy hàm **caiDat**, cấp quyền; xem nhật ký để lấy mật khẩu admin ngẫu nhiên. Không đăng nhật ký hoặc mật khẩu lên GitHub.
4. Chạy **caiDatKho**. Kho Drive gồm Hình thực tế và Khối 6–9 sẽ được tạo, kèm `danh-muc.json`. Drive và bảng tài khoản thuộc Gmail này và giữ riêng tư.
5. API key sẽ được cài ở giao diện sau khi triển khai. Bản cũ có **GEMINI_API_KEY** và **GEMINI_MODEL** trong Script Properties vẫn được đọc làm cấu hình Gemini ban đầu.
6. Triển khai Web app: thực thi bằng tài khoản **Tôi**, truy cập **Bất kỳ ai**. Hệ thống kiểm tra phiên đăng nhập và vai trò trên máy chủ trước khi đọc kho hoặc thực hiện thao tác. Tổ chức Google Workspace có thể giới hạn tùy chọn này.
7. Sao chép URL kết thúc `/exec`; đặt vào **API_URL** trong `chung/cau-hinh.js`.
8. Đăng nhập admin bằng mật khẩu vừa tạo, đổi mật khẩu trong Cài đặt. Trong **Cài đặt API AI**, nhập Base URL và API key của hãng bạn dùng, chọn model rồi Lưu. Bấm **Kiểm tra kết nối** với cấu hình đã lưu; chọn **AI đang sử dụng** và bấm **Dùng AI này**.

Không cần Google OAuth Client ID ở giao diện. Apps Script thực hiện thao tác Drive bằng tài khoản chủ triển khai. Các API key nằm trong Script Properties, không trong file JavaScript công khai và không được gửi ngược về giao diện. Để trống ô key khi lưu để giữ key hiện có; chọn Xóa key để gỡ cấu hình. Đổi Base URL khi có key cũ yêu cầu nhập lại key dành cho địa chỉ mới. Các lệnh quét và vẽ dùng hãng/model đã chọn trên máy chủ.

## 4. Đưa giao diện lên GitHub Pages

Tải **các file HTML, favicon.svg, thư mục chung/ và trang/** lên kho GitHub. Không tải thư mục apps-script/ hoặc các thông tin tài khoản lên kho công khai. Bật Settings → Pages cho nhánh chứa các file đó. `index.html` phải ở gốc thư mục Pages đã chọn.

Giao diện là web tĩnh; các trang dẫn nhau bằng đường dẫn tương đối nên dùng được cả trong một repository con. Mã web sẽ công khai trên GitHub Pages; dữ liệu Drive và quyền sửa được Apps Script kiểm tra độc lập.

## 5. Kho hình và phân quyền

- **Admin/chủ kho:** thêm và sửa mẫu trên Drive; quét hình; quản trị tài khoản, API, macro; build và gọi AI.
- **Giáo viên:** xem/lấy mã, build, vẽ theo đề và lưu nháp trên máy; không thêm mẫu vào kho chung.
- **Học sinh:** xem và lấy mã mẫu; máy chủ từ chối yêu cầu build/AI/sửa kho.

Bạn là người gửi mẫu: mọi hình (thực tế và toán cơ bản) đều nằm trong Dữ liệu Hình TikZ, lọc theo khối lớp và chủ đề. Hình thực tế có thể gắn thêm khối lớp mà không cần lưu trùng file.

Mỗi mẫu lưu `.tex` chỉ chứa một khối hình và một ảnh, kèm liên kết/phân loại trong `danh-muc.json`. Ảnh và mã nằm trên Drive, không nhúng cứng vào mã web. Máy chủ đọc lại file .tex của những mẫu được chọn trước khi gọi AI. Cần kiểm tra mã/hình AI tạo trước khi lưu.

Khi sửa mẫu, Apps Script giữ các phiên bản file cũ và cập nhật danh mục sang phiên bản mới. Khóa ghi giúp các phiên không đồng thời ghi đè danh mục; mẫu đã đổi trong phiên khác sẽ yêu cầu tải lại. Chưa có chức năng dọn phiên bản cũ. Lưu Drive sẽ build lại mã ở máy chủ để đảm bảo ảnh tương ứng, không tin ảnh gửi từ trình duyệt.

Build luôn dùng `https://tikz-fly.fly.dev/compile`. Apps Script gọi dịch vụ và trả ảnh cho trình duyệt, không cần thuê một máy chủ chạy LaTeX. Macro dùng chung lưu ở Script Properties; thay macro sẽ khiến ảnh cũ cần build lại.

## 6. Cài đặt API và model

Danh sách gợi ý được tra tài liệu chính thức ngày **01/10/2026**, chỉ chọn các model sinh văn bản/mã cho TikZ. Có thể nhập model tùy chọn hoặc bấm **Lấy model từ API** sau khi lưu key. Tài khoản cần quyền truy cập model đó. Nút này lấy tối đa 200 model trong trang kết quả đầu tiên; với Gemini sẽ bỏ các model ảnh/âm thanh và model không có generateContent. Các bản preview và bản trước được ghi theo tên, không đảm bảo luôn có quyền truy cập.

| Hãng | Base URL mặc định | Model gợi ý |
| --- | --- | --- |
| Gemini | `https://generativelanguage.googleapis.com/v1beta` | gemini-3.8-flash, gemini-3.7-flash, gemini-3.6-flash, gemini-3.5-flash, gemini-3.5-flash-lite |
| OpenAI | `https://api.openai.com/v1` | gpt-6.1-sol, gpt-6-astra, gpt-6-luna, gpt-6-sol, gpt-5.6-sol |
| Claude | `https://api.anthropic.com/v1` | claude-sonnet-5-5, claude-opus-5-5, claude-fable-5-1, claude-haiku-4-5, claude-sonnet-5 (bản trước) |
| DeepSeek | `https://api.deepseek.com` | deepseek-flash, deepseek-v4-pro; deepseek-v4-flash và deepseek-v4-flash-vision-exp là alias tương thích của Flash |

DeepSeek công bố **2 model API hiện hành**, không có 5 model API độc lập để thêm. Hai alias được ghi rõ trong giao diện, không đưa deepseek-chat/reasoner đã hết lịch hỗ trợ vào gợi ý. OpenAI dùng Responses API; Claude dùng Messages; Gemini dùng generateContent; DeepSeek dùng Chat Completions. Base URL có thể thay bằng cổng HTTPS công khai tương thích đúng giao thức hãng đã chọn; không nhập đường dẫn cuối `/responses`, `/messages` hoặc `/chat/completions`.

Nguồn: [Gemini models](https://ai.google.dev/gemini-api/docs/models), [OpenAI models](https://developers.openai.com/api/docs/models), [Claude models](https://platform.claude.com/docs/en/models/overview), [Claude Sonnet 5](https://platform.claude.com/docs/en/models/sonnet-5/overview), [DeepSeek API](https://api-docs.deepseek.com/quick_start/pricing-details-cny/).

## 7. Quét và lọc hình thực tế

1. Mở **Quét hình thực tế**, chọn một/nhiều file .tex, chọn folder (kèm thư mục con), hoặc dán mã đầy đủ. Mỗi file tối đa 2 MB, mỗi khối hình tối đa 150.000 ký tự, ngữ cảnh style/macro tối đa 30.000 ký tự; một đợt tối đa 300 hình.
2. Chương trình tách các tikzpicture, bỏ qua các khối trong comment, thu các tikzset/tikzstyle/pgfkeys, thư viện TikZ, newcommand/def, definecolor/colorlet và macro PGF ngoài hình. Chưa hỗ trợ tự đọc file `input`/`include`, macro khai báo theo cú pháp tùy biến hoặc văn bản không nằm trong tikzpicture; hãy gộp các định nghĩa cần thiết vào file trước.
3. **Quét AI và render** gửi mã hình và ngữ cảnh liên quan tới hãng đang chọn. AI phân loại, gợi ý tên/chủ đề/từ khóa/mô tả/khối lớp; độ tự tin là đánh giá của AI, không phải độ chính xác đã được xác minh.
4. Với hình thực tế, AI giữ cảnh và chi tiết cấu tạo, bỏ nhãn điểm/đường phụ/kích thước dùng riêng cho đề bài, đặt style/macro còn cần **bên trong** một tikzpicture. Mã được render bằng địa chỉ build cố định. Nếu không build được, hình vẫn ở hàng chờ để sửa/render lại.
5. Duyệt từng hình: kiểm tra mã lọc cạnh ảnh, mở **Thông tin hình** để sửa tên/khối/chủ đề, hoặc xem mã gốc. Phần **Hướng dẫn** và **Dán mã LaTeX** ở thanh công cụ chỉ mở khi cần để dành không gian cho hàng chờ và ảnh. Hình cơ bản chỉ xuất hiện khi chọn bộ lọc **Tất cả hình**, không được lưu qua trang này vào kho thực tế. Muốn đưa hình toán cơ bản vào khối lớp, dùng Thêm mẫu.
6. Tích chọn những hình muốn giữ sau khi có ảnh hợp lệ, rồi **Lưu đã chọn vào Drive**. Chỉ những hình đã chọn được lưu; lỗi từng hình được báo, các hình đã lưu không bị lưu trùng khi bấm lại. Có thể lưu nháp trên máy để sửa tiếp hoặc tải .tex.

Quét chạy tuần tự, có nút dừng sau yêu cầu đang chạy và tiếp tục các hình còn chờ. Khi sửa mã, ảnh/lựa chọn lưu cũ bị vô hiệu; phải render lại. Không tự thêm mẫu AI chưa được chọn vào Drive. Nên chia đợt quét; giới hạn máy chủ: 30 nhận dạng/phút, 10 build/phút, 10 lưu/phút, 3 vẽ theo đề/phút cho mỗi người dùng. Nếu gặp hạn mức, chờ khoảng một phút rồi tiếp tục.

Chế độ chưa nối Apps Script cho phép tách mã, xem mã gốc và render thử. Không lưu API key trên máy và không giả lập kết quả nhận dạng AI; nút quét AI/lưu Drive cần máy chủ thực và key.

## 8. Sau mỗi lần cập nhật

- Sửa HTML/JS/CSS: tải lại đúng file đã sửa trên GitHub.
- Sửa Apps Script: cập nhật triển khai sang phiên bản mới để URL `/exec` dùng mã mới.
- Thêm mẫu: chỉ lưu mẫu trên Drive, không cần sửa hay tải lại mã web.

## 8b. Nút AI sửa mã (trang Thêm mẫu)

- Khi mã build lỗi, bấm **AI sửa mã**. Máy chủ build thử để lấy log lỗi, gửi mã + log + macro chung cho hãng/model đang chọn trong Cài đặt, rồi build lại mã AI trả về. Nếu còn lỗi, web tự cho AI sửa thêm 1 lần (tối đa 2 lần mỗi lần bấm).
- Mã AI sửa chỉ thay trong khung soạn, chưa lưu Drive. Nút **Khôi phục mã trước khi AI sửa** đưa mã về như trước lần bấm gần nhất.
- Admin và giáo viên dùng được; học sinh không thấy nút. Giới hạn 6 lần gọi/phút mỗi người.
- Cài máy chủ: thêm tệp `SuaMa.gs` vào dự án Apps Script, thêm vào `xuLyTikz_` (KhoTikz.gs) dòng `case 'suaMaAI': canVe_(user);hanMuc_(user.ten,'ai-fix',6);return suaMaAI_(d);` rồi triển khai phiên bản mới.

## 9. Tình trạng kiểm tra

Đã kiểm tra cú pháp JavaScript/Apps Script, đăng nhập thử và build ảnh trong trình duyệt. Kiểm tra dịch vụ mô phỏng gồm: 4 adapter AI, bảo vệ API key, phân quyền, lỗi/trả thiếu dữ liệu, tách nhiều hình, bảo toàn style/macro/màu, phân loại cơ bản/thực tế, Drive và xung đột phiên bản. Luồng giao diện quét/render/sửa/chọn/lưu đã kiểm tra trên máy chủ dữ liệu thử riêng. Máy chủ Apps Script chưa triển khai bằng Gmail của bạn nên đăng nhập thực, quyền Drive, gửi AI và build qua UrlFetchApp cần kiểm tra sau khi triển khai. Không thay đổi bộ `mo-hinh-3d.zip` gốc.

Tham khảo chính thức: [Apps Script Web Apps](https://developers.google.com/apps-script/guides/web), [UrlFetchApp](https://developers.google.com/apps-script/reference/url-fetch/url-fetch-app), [DriveApp](https://developers.google.com/apps-script/reference/drive/drive-app).

