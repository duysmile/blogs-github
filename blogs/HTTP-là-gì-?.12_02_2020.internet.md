# HTTP là gì?

HTTP là viết tắt của Hypertext Transfer Protocol, và HTTP là một giao thức giao tiếp được sử dụng cho việt duyệt web. Giao thức này sử dụng một mô hình dựa trên message trong đó client tạo một yêu cầu HTTP tới một web server và server sẽ trả lời bằng một tài nguyên được hiển thị trên trình duyệt.

`Mỗi tương tác HTTP sẽ bao gồm một request và một response. Về bản chất thì HTTP là không trạng thái (stateless).`

Không trạng thái nghĩa là tất cả request tách biệt với nhau do đó mỗi request phải chứa đầy đủ thông tin cần thiết để hoàn thành yêu cầu. Điều này nghĩa là mỗi transaction của mô hình dựa trên message của HTTP được xử lí riêng biệt với nhau.

### URL

URL(Uniform Resource Locator) có lẽ là một khái niệm rất phổ biến trên Web. Nó cũng là một khái niệm rất quan trọng và hữu ích. URL là một địa chỉ web dùng để định danh tài nguyên trên Web.

> Ý tưởng của web là được cấu trúc xung quanh tài nguyên, từ khi bắt đầu, Web là một nền tảng để chia sẻ text/HTML files, documents, ảnh, vv. và nó được coi như là một tập các tài nguyên.
[Ví dụ của URL](https://miro.medium.com/max/1000/0*DTR8JpFZo31ht-Kd)

**Giao thức(protocol)** - Thông thường chúng là HTTP(hoặc là HTTPS, một phiên bản bảo mật của HTTP)
Những giao thức đáng chú ý khác:
- File Transfer Protocol (FTP) - một giao thức chuẩn dùng để chuyển file giữa client và server thông qua mạng.
- Simple Mail Transfer Protocol (SMTP) là một chuẩn cho việc gửi email.

**Miền(domain)** - Tên dùng để định danh một hoặc nhiều địa chỉ IP, nơi mà tài nguyên được lưu trữ.

**Đường dẫn(path)** - Chỉ định vị trí tài nguyên trên server. Nó sử dụng logic giống như tài nguyên trên máy tính của bạn nè (ví dụ /search/cars/BMW hay C:/cars/Audi).

**Tham số(parameters)** - Dữ liệu bổ sung dùng để dịnh danh hay lọc tài nguyên trên server.

> **NOTE**
> Khi tìm kiếm những bài viết hoặc nhiều thông tin hơn về HTTP, bạn có thể gặp những khái niệm về URI(hay Uniform resource identifier). URI cũng đôi khi được sử dụng thay cho URL nhưng hầu hết là trong các tài liệu và một số thích thể hiện :))

### HTTP Requests

Trong HTTP, mỗi request phải có một địa chỉ URL. Hơn nữa, mỗi request cần một phương thức(method). Bốn phương thức HTTP chính là:
- GET
- POST
- PUT
- DELETE

Giờ mình sẽ giải thích những phương thức này nhiều hơn, trong phần Những phương thức HTTP trong bài viết nhé!

Và những phương thức này tương ứng với những action:
- Đọc
- Cập nhật
- Tạo mới
- Xóa

Tất cả các HTTP message có một hoặc nhiều headers, theo sau là một phần message body tùy chọn. Phần body chứa dữ liệu sẽ được gửi cùng với request hoặc dữ liệu nhận từ response.

Phần đầu tiền của mỗi HTTP request gồm 3 phần:
*Ví dụ:*
- *GET /adds/search-result?item=vw+beetle HTTP/1.1*

Khi một URL chưa một kí tự "?" có nghĩa là nó có bao gồm query. Điều này nghĩa là nó gửi đi cacs tham số của tài nguyên được yêu cầu.
1. Phần đầu tiên là một `phương thức` sẽ cho chúng ta biết phương thức HTTP nào được dùng. Phương thức được sử dụng phổ biến nhất là GET. Phương thức GET dùng để lấy tài nguyên từ web server và vì GET không có phần body, chỉ có phần header là cần thiết.
2. Phần thứ hai là URL được yêu cầu
3. Phần thứ 3 là một phiên bản HTTP được sử dụng. Phiên bản 1.1 là phiên bản phổ biến trên hầu hết các trình duyệt, tuy nhiên đã đến lúc phiên bản 2.0 tiếp quản :))

Cũng có những thứ thú vị khác trong một HTTP request:
- **Referer header** - cho biết URL mà request bắt nguồn.
- **User-Agent header** - thông tin bổ sung về trình duyệt được sử dụng để gửi request.
- **Host header** - định danh duy nhất một host name, điều này là cần thiết khi mà nhiều trang web được host trên cùng một server.
- **Cookie header** - gửi thông tin bổ sung cho client

### HTTP Responses

Giống như HTTP request, HTTP Response cũng có 3 phần:
Ví dụ:
*HTTP/1.1 200 OK*

1. Phần đầu là phiên bản HTTP được sử dụng.
2. Phần thứ 2 là code kết quả của request.
3. Phần 3 là phần mô tả cho phần 2.

Có một vài điều thú vị trong một HTTP Response:
- **Server header** - thông tin về phần mềm web server được sử dụng
- **Set-Cookie header** - cấp cookie cho trình duyệt
- **Message body** - đầy là phần phổ biến cho một HTTP Response để giữ message body.
- **Content-Length header** - cho biết kích thước message body theo byte.

### HTTP Methods

Những method phổ biến nhất là GET và POST, tuy nhiên còn nhiều method khác nữa.

**GET** - dùng để yêu cầu dữ liệu từ một tài nguyên cụ thể nơi mà dữ liệu ko được thay đổi vì GET requests ko dùng để thay đổi trạng thái của tài nguyên.
**POST** - dùng để gửi dữ liệu tới server để tạo tài nguyên.
**PUT** - method dùng để cập nhật tài nguyên có sẵn trên server bằng cách dùng nôi dung của request body.
**HEAD** - method này có cùng chức năng với method GET nhưng có một sự khác biệt là kêt quả trả về của HEAD method ko nên chứa body. Tuy nhiên, kết quả trả về sẽ cùng header nếu dùng GET. HEAD method được dùng để kiểm tra nếu tài nguyên có tồn tại không trước khi thực hiện GET request.
**TRACE** - method được thiết kế cho mục đích chẩn đoán (diagnostic). Response body sẽ chứa cùng nội dung của request message.
**OPTIONS** - method này dùng để mô tả tùy chọn giao tiếp (communication options) (HTTP methods) có sẵn ở tài nguyên đích không.
**PATCH** - method này được dùng để áp dụng sửa đổi một phần cho tài nguyên.
**DELETE** - dùng để xóa một tài nguyên cụ thể.

====================

### REST

Representational state transfer (REST) là một kiểu kiến trúc trong đó request và response chứa trạng thái hiện tại của tài nguyên hệ thống.

Kiểu "bình thường":
- http://carapp.com/search?make=wv&model=beetle
Kiểu REST:
- http://carapp.com/search/vw/beetle

### HTTP Headers
Có 3 phần chính để tạo nên cấu trúc của request/response. Bao gồm:
- First line
- Headers
- Body/Content

Chúng ta đã nói về first line trong HTTP requests và responses, body function cũng đã được đề cập, bây giờ là tới phần HTTP Headers.

HTTP Headers được thêm vào sau First line và được định nghĩa kiểu theo từng cặp name:value phần cách nhau bởi dấu phẩy. HTTP headers được dùng để gửi các tham số bổ sung đi kèm với request hay response.

Như chúng ra đã đề cập, phần body của message bao gồm dữ liệu được gửi với request hay dữ liệu nhận về với response.

Có những kiểu headers khác nhau mà chúng ta sẽ phân loại chúng dựa trên việc sử dụng chúng thành 4 nhóm lớn:
- **General header** - Những headers dùng trong cả request và response message và chúng độc lập với dữ liệu được trao đổi.
- **Request header** - những headers định nghĩa các tham số cho dữ liệu yêu cầu hoặc là những tham số chứa những thông tin quan trọng về client tạo request.
- **Response header** - những headers chứa thông tin về incoming response.
- **Entity header** - những headers này mô tả nội dung tạo nên phần body của message.

[Những loại headers](https://miro.medium.com/max/837/0*0BI1BEJpajUiJ_4R)
































































































