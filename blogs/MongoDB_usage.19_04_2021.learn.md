Reference: anh Việt Trần

Mongo thì mình sẽ ưu tiên dùng khi:
- Chưa biết kiểu data, lưu tạm trước rồi tính sau.
- Cần spam table (collection) nhanh: ví dụ messages trong 1 room chat nên là 1 collection riêng cho mỗi room.
- Cần lưu các kết quả tính toán phức tạm: Feed của user, scale theo user nên mỗi user có 1 collection để tăng tải đọc.
- Notification theo user: giống Feed.
- Dữ liệu có dạng râu ria rễ má nhưng chỉ dùng để show không dùng để tìm kiếm. VD mấy cái metadata bổ trợ thông tin.

Mỗi user 1 collection luôn cũng được ạ anh?

Nếu 1 triệu user lưu thành 1 triệu collection thì có sao không nhỉ?

-> để ko bị locking table/collection

thì có làm sao? khi ấy càng dễ sharding mà, cũng tương đương sharding key là user_id thôi


Việc tạo nhiều collection: https://developer.mongodb.com/article/schema-design-anti-pattern-massive-number-collections/
