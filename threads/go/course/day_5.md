# Golang - Day5

## Xử lí lỗi
Embed struct, tag inline -> để flatten embed struct

Trước khi update cần find:
- Find này đáng giá, vì mình cần data cũ
- Find giúp mình phân biệt lỗi
- Có trạng thái mới nhất khi sắp update

Error handling
- Chuẩn hóa error response: code, error message (client), error_key (client đa ngữ), log (hệ thống)
- Panic và recover
- Dùng middle handle panic và recover

Xử lí crashes như lỗi bth trong khi Golang ko có try/catch

Panic:
- Builtin func, bắt đầu panicking
- Panic xảy ra thì nó dừng execution của hàm, những được defer trước đó được thực thi
Recover
- Lấy lại control từ panicking
- Chỉ hữu ích khi đặt trong defer func
Middlewares

Khi xài routine mà gọi panic thì ct dễ bị crashes nếu k handle

Panic và recover chỉ chạy trên current stack frame của nó thôi
