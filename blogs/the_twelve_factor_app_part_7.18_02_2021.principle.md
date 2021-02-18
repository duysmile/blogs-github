# The Twelve-factor App - Port binding

- Export các service thông qua cổng mạng
- Những ứng dụng web thỉnh thoảng thực thi bên trong một webserver container. Ví dụ, ứng dụng PHP chạy như là một module bên trong Apache HTTPD, hoặc ứng dụng Java có thể chạy trong Tomcat.
- Ứng dụng tuân thủ 12 nguyên tắc hoàn toàn khép kín và không phụ thuộc vào runtime tích hợp của webserver trong môi trường trường thực thi để tạo ra một service web. Ứng dụng web export HTTP như một service bằng cách kết nối vào cổng mạng, và lắng nghe request gửi đến cổng đó.
- Trong môi trường phát triển, developer nhập vào url của service như `[http://localhost:5000/](http://localhost:5000/)` để truy cập vào service được export ra bởi ứng dụng. Trong quá trình phát triển, lớp điều hướng(routing layer) xử lý việc điều hướng request từ một hostname được public đến một cổng mạng của process web.
- Việc này thường được implement bằng cách định nghĩa các dependency để thêm thư viện webserver vào ứng dụng, như là `Tornado` của Python,  `Thin` của Ruby, ... Những việc này đều nằm trong *user space*, bên trong code của ứng dụng. Việc liên kết với môi trường thực thi là việc binding cổng mạng để phục vụ các request.
- HTTP không phải là service duy nhất có thể export bằng cổng mạng. Gần như bất cứ loại phần mềm server nào cũng có thể chạy thông qua một process được binding tới một cổng mạng và đợi request đến. Ví dụ như `Redis` (giao tiếp qua giao thức Redis).
- Lưu ý là kết nối cổng mạng có nghĩa là một ứng dụng có thể trở thành một service hỗ trợ cho một ứng dụng khác, bằng cách cung cấp URL đến ứng dụng hỗ trợ đó như là một tài nguyên được xử lí trong config của ứng dụng chính.
