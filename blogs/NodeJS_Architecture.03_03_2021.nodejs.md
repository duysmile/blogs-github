# Kiến trúc NodeJS

Nguồn: Grokking Viet Nam

Hầu hết chúng ta đều biết, developer sử dụng Javascript để phát triển ứng dụng trên nền tảng NodeJS, tuy nhiên NodeJS khổng chỉ được xây dựng bằng Javascript mà cả C++ nữa và sử dụng nhiều thư viện khác nhau, trong đó có 2 thư viện phụ phuộc quan trọng nhất là V8 và LIBUV.
V8 là một javascript engine được phát triển bởi Google. Nó phụ trách việc chuyển javascript code thành mã máy để máy tính có thể hiểu và thực thi.
LIBUV cung cấp cho NodeJS quyền truy cập vào hệ điều hành máy tính, mạng, hệ thống file, v.v. LIBUV là một thư viện mã nguồn mở được viết bằng C++. Ngoài việc tập trung xử lý truy xuất I/O bất đồng bộ (asynchronous I/O), LIBUV còn phát triển hai tính năng rất quan trọng khác là event loop và thread pool.
Ngoài V8 và LIBUV, NodeJS còn được tạo ra từ các thư viện khác như HTTP parser, C-ARES (DNS queries), OpenSSL và Zlib.
