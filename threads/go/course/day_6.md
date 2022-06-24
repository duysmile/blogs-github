# Golang - Day6

## Xử lí và lưu trữ hình ảnh
- Define một kiểu image lưu kiểu json vào DB trong gorm
- Nếu hình được sử dụng nhiều và cần đối soát, quan trọng thì nên lưu bảng riêng
- Trong trường hợp ảnh là phần phụ thôi thì chỉ cần đưa cùng vs bảng chính -> nhẹ DB hơn

- Lưu ý: Dùng Value và Scan để implement interface của driver SQL giúp DB hiểu được cách lưu json xuống DB

API upload hình ảnh
- c.FormFile() => lấy được con trỏ trỏ tới vùng file được gửi từ form data
- Một số API login, register, upload có thể break rule convention REST API
- Upload trực tiếp lên server - c.SaveUploadFile(fileHeader, destination)
- Upload length S3 - API Key - Access key - Secret Key
- CloudFront thay vì public object S3

Upload image store vào DB -> khi tạo restaurant thì verify image truyền vào body bằng cách check DB

Detect mimetype để check image chống xss
- mime type golang - https://github.com/gabriel-vasile/mimetype - signature number | magic number

## Tips
- Dùng next cursor thay cho offset để get more
- Ẩn id trên url (param hoặc query) tránh crawl data
