# Những trình duyệt hoạt động như thế nào ?

Trình duyệt là một phần mềm dùng để định vị, lấy và hiển thị nội dung trên World Wide Web, bao gồm trang web, ảnh, video hoặc những file khác. Là một mô hình client/server, trình duyệt là một client, chạy trên một máy tính, có thể liên lạc đến một web server và yêu cầu thông tin. Web server gửi thông tin lại trình duyệt web để hiển thị kết quả trên máy tính hoặc những thiết biết có kết nối Internet khác có hỗ trợ trình duyệt.

Ngày nay các trình duyệt là các bộ phần mềm đầy đủ chức năng để thông dịch và hiển thị các trang web HTML, ứng dụng, Javascript, AJAX và những nội dung khác được hosted trên web servers. Nhiều trình duyệt yêu cầu những plug-ín để mở rộng khả năng của nó để hiển thị các thông tin đa phương tiện (multimedia) (bao gồm âm thanh và video), hoặc trình duyệt có thể được sử dụng để thực hiện những việc như videoconferencing (hội nghị truyền hình), để thiết kế các trang web hay thêm các bộ lọc chống lừa đảo (anti-phising filters) và các tính năng bảo mật khác trên trình duyệt.

Một trình duyệt là một nhóm các đoạn mã có cấu trúc, cùng thực hiện một loạt các tác vụ để hiển thị một trang web trên màn hình. Tùy theo tác vụ chúng thực hiện, các đoạn mã này được chia thành các thành phần khác nhau.

### Cấu trúc high-level của trình duyệt

Hình dưới là những thành phần chính của một trình duyệt web:
![Những thành phần chính của trình duyệt](https://miro.medium.com/max/499/1*RL0pnuf_hmLJ76oY6DViZw.png)

1. **Giao diện người dùng (User Interface)**: Giao diện người dùng là không gian để người dùng tương tác với trình duyệt. Nó bao gồm thanh địa chỉ (address bar), nút tới và lui (back and next buttons), nút home, refresh và stop, tùy chọn bookmark, vv. Mỗi thành phần khác ngoại trừ cửa sổ hiển thị trang web được yêu cầu đều nằm dưới nó.

2. **Browser Engine**: browser engine hoạt động như là cầu nối giữa giao diện người dùng với rendering engine. Tùy theo inputs từ các giao diện người dùng khác nhau, nó sẽ truy vấn và thao tác với rendering engine.

3. **Rendering engine**: rendering engine, như cái tên của nó thì nhiệm vụ của thành phần này là rendering trang web được yêu cầu trên màn hình trình duyệt. Rendering engine thông dịch HTML, XML documents và ảnh đã được định dạng sử dụng CSS và tạo ra layout được hiển thị ở giao diện người dùng. Tuy nhiên, với các plugin hay extension thì nó có thể hiển thị những loại dữ liệu khác nữa. Những trình duyệt khác nhau sử dụng những rendering engine khác nhau:
- Internet Explorer: Trident
- Firefox và những trình duyệt của Mozilla: Gecko
- Chrome và Opera 15+: Blink
- Chrome(iPhone) và Safari: Webkit

4. **Networking**: thành phần của trình duyệt dùng để truy xuất URLs bằng các giao thức Internet phổ biến của HTTP hoặc FTP. Networking xử lí tất cả các phần của việc giao tiếp trên Internet và bảo mật. Thành phần mạng có thể thực hiện cache các document nhận được để giảm network traffic.

5. **Trình thông dịch Javascript(Javascript Interpreter)**: Nó là một thành phần của trình duyệt dùng để thông dịch và thực thi mã javascript được nhúng vào website. Kết quả thông dịch được gửi tới rendering engine để hiển thị. Nếu script ở bên ngoài thì đầu tiên phần tài nguyên này được lấy từ mạng trước. Trình phần tích cú pháp (parser) giữ đến khi script được thực thi.

6. **UI phụ trợ(UI Backend)**: UI backend dùng để vẽ những widget cơ bản như combo boxes hoặc windows. Phần phụ trợ này hiển thị một giao diện chùng chứ ko phải là một nền tảng cụ thể. Bên dưới nó dùng các phương thức giao diện người dùng hệ điều hành.

7. **Lưu trữ dữ liệu (Data Persistence/Storage)**: đây là một lớp cố định (persistence layer). Trình duyệt hỗ trợ có chế lưu trữ như localStorage, IndexedDB, WebSQL và FileSystem. Nó là một cơ sở dữ liệu nhỏ được tạo trên bộ nhớ local của máy tính nơi trình duyệt được cài đặt. Nó quản lí dữ liệu người dùng như cache, cookies, bookmarks và preferences (yêu thích).

> Một điều quan trọng cần lưu ý ở đây là trong những trình duyệt web như Google Chrome,mỗi tab chạy trên một process riêng(nhiều thực thể của rendering engine).

### Rendering engine

Tầng mạng sẽ bắt đầu gửi nội dung của document được yêu cầu tới rendering engine trong từng phần 8KBs.

![Flow cơ bản của rendering engine](https://miro.medium.com/max/600/1*cfQpu6Xvb7e9IiH4CCuiCg.png)

Rendering engine sẽ phân tishc từng phần của HTML document và chuyển những phần thử này thành DOM nodes trên một tree gọi là "**content tree**" hay "**DOM tree**". Nó cũng phân tích cả những file CSS ngoài cũng như trong các phần tử style.

Trong khi DOM tree được khởi tạo, trình duyệt cũng khởi tạo một cái tree khác, là **render tree**. Tree này là của các thành phần trực quan theo thứ tự mà chúng được hiển thị. Nó là phần đại diện trực quan của document. Mục đích của tree này là cho phép vẽ những nội dung theo đúng thứ tự của nó. Firefox thì gọi những phần tử trong render tree là "frames". Webkit dùng thuật ngữ là rendere hoặc render object.

Sau khi khởi tạo render tree, nó sẽ đi qua "**layout process**" của render tree. Khi renderer dược tạo và thêm vào tree, nó ko có vị trí và kích thước. Quá trình tính toán những giá trị này được gọi là layout hay reflow. Điều này nghĩa là cho mỗi node một tọa độ chính xác để hiển thị trên màn hình. Vị trí của root renderer là 0, 0 và kích thước của nó là viewport - phần hiển thị của cửa sổ trình duyệt. Tất cả renderer có một phương thức "layout" hoặc "reflow", mỗi renderer gọi phương thức layout của những phần con của nó cần được bố trí.

Giai đoạn tiếp theo là **painting**. Trong giai đoạn này, render tree được đi qua và phương thức "paint()" của renderer được gọi để hiển thị nội dung trên màn hình. Painting dùng tới lớp UI backend.

Rendering engine luôn cố để hiện thị nội dung trên màn hình sớm nhất có thể để trải nghiệm người dùng là tốt nhất. Nó không đợi tới khi việc phân tích HTML được hoàn thành để bắt đầu xây dựng và bố trí render tree. Nó phân tích và hiển thị nội dung nhận được từ mạng, trong khi phần nội dung còn lại được vẫn tiếp tục đến từ mạng.

========================
