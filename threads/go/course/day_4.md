# Golang - Day4

## REST API
- REST = REpresentational State Transfer -> mang một cái chuẩn, convention
- Convention: https://restfulapi.net/resource-naming

- Mỗi phương thức đại diện cho một action cụ thể, mang ý nghĩa nhất định
- Note:
    - Patch: thay đổi 1 phần != Put: thay đổi cả record
    - PUT vs PATCH khác nhau ntn?

- Resource: SQL Restaurant: https://gist.github.com/viettranx/b0a22a0a869309fc9c64fd820b1d0f29

## Notes
- json dùng cho image để lưu metadata của ảnh

Gin
- shouldBind trong Gin -> để bind data từ request vào biến dựa vô struct
- Xài shouldBind thì cần truyền vào con trỏ vì nó thay đổi dữ liệu
- Xài gin.H{} để xử lí dữ liệu trả về -> giống vs map[string]interface{}
- dùng gin thì mỗi request nó sẽ xử lí ở 1 goroutine
- Xài query string thì nhớ tag form
- Query get thì get rồi parse ra -> xài strconv.Atoi (convert to int)

Gorm
- Đầu tiên cần tạo 1 struct
- Struct này cần có method TableName
- Đối vs update xài struct khác dùng con trỏ thay vì type bth

Ko có try catch nên xử lí lỗi ở từng lệnh có khả năng xảy ra lỗi

Architect
- Tổ chức lại architect theo kiến trúc đa tầng
- Transport Layer: parse request, send response, nơi setup dependencies
    - Business Layer: handle logic, chỉ xài interface
    - Storage Layer: store DB - interface
- Tách code Theo kiến trúc đa tầng
- Component là nơi quản lí đồ chơi - thư viện, xài chung …
- Phân biệt business model, vs struct trung gian để thao tác dữ liệu

Tips
- Tên thư mục là module nên theo convention là k có _ -, string thẳng đụt luôn
- Thêm context cho tất cả các hàm có IO ctx context.Context
- Interface khai báo ở đâu xài ở đó
- Lỗi trả về viết thường hết