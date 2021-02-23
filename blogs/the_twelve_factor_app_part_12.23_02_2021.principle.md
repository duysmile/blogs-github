# The Twelve-factor App - Admin processes

- Chạy các tác vụ quản trị / quản lí như một process
- Công thức cho các process là danh sách các process được dùng để thực hiện business của ứng dụng (như là xử lí các web request) khi nó được chạy. Ngoài ra, developer thường mong muốn chạy 1 lần các task quản trị hoặc bảo trì ứng dụng như là:
    - Chạy database migrations (ví dụ như `manage.py migrate`trong Python)
    - Chạy console (được gọi là REPL shell) để chạy đoạn code tùy ý hoặc kiểm tra các model của ứng dụng trực tiếp trong database. Hầu hết các ngôn ngữ cung cấp một REPL bằng cách chạy trình thông dịch mà không có tham số đi kèm (ví dụ như `node`, `python`) hoặc trong một vài trường hợp thì cung cấp câu lệnh riêng biệt (ví dụ `irb` đối với Ruby).
    - Chạy script một lần được commit cùng với repo của ứng dụng.
- Những process quản trị nên được chạy trong một môi trường giống hệt như long-running process của ứng dụng. Chúng đi kèm với một bản release, sử dụng cùng codebase và config với bất cứ process nào chạy trong bản release đó. Admin code phải đi kèm với code ứng dụng để tránh các vấn đề đồng bộ.
- Kĩ thuật cô lập dependency tương tự được sử dụng cho cùng một kiểu process. Ví dụ web process chạy Ruby sử dụng lệnh `bundle exec thin start`, sau đó một lệnh database migration dùng lệnh `bundle exec rake db:migrate`. Tương tự, một chương trình chạy Python sử dụng Virtualenv nên dùng vendor `bin/python` để chạy cả Tornado webserver cũng như bất kì process admin `[manage.py](http://manage.py)` nào.
- 12 nguyên tắc rất thích hợp với những ngôn ngữ cung cấp REPL shell tách rời, và có thể dễ dàng chạy script. Trong bản deploy local, developer có thể chạy những process admin trực tiếp bằng lệnh bên trong thư mục ứng dụng. Trong bản deploy prod, developer có thể dùng ssh hoặc những lệnh remote khác được cung cấp để chạy những process đó.
