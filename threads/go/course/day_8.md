# Golang - Day8

## Authentication
- Authentication flow
- JWT: github.com/dgrijalva/jwt-go
- Link user data

Hủy token hoặc kiểm soát thiết bị đăng nhập:
- Nếu muốn thì chỉ cần lưu phần signature trong token thôi
- Giúp giảm dung lượng lưu trữ
- Và đảm bảo security, dù có lấy đc data bảng này cũng vô nghĩa
- Có thể revoke token đơn giản

## Tips
Dữ liệu trả về nên để (con trỏ result, error) vì khi đó có thể tái sử dụng vùng nhớ trong hàm, và có thể trả nil nếu có lỗi -> tiết kiệm bộ nhớ
