# Week 3

## Transactions
    - Transaction là khi thực hiện nhiều command, nếu một command bị fail thì tất cả những command khác trong transaction cần được rollback lại.
    - Transaction trong redis
        - MULTI -> dùng để bắt đầu một transaction, command được queue vào transaction
        - EXEC -> dùng để thực thi những lệnh trong queue -> chỉ bị lỗi khi lệnh trong queue sai syntax hoặc watched key bị modified, còn đối vs lệnh thực thi trên wrong datatype thì đc redis skip.
        - DISCARD -> bỏ những lệnh trong queue -> lúc này những lệnh được đưa vào queue chưa đc thực hiện nên ko cần undo commands.
    - Các lệnh trong transaction được đưa vào queue được thực hiện tuần tự
    - Đối với những database khác thì:
        - Command được thực thi ngay lập tức
        - Tiêu tốn tài nguyên hệ thống
    - Với Redis:
        - Command được queue
        - Thực hiện trong 1 operation
        - Ko cho phép nesting transactions

    - Vì khi tạo một transaction thì sẽ queue command vào trong nó, nên nếu số lượng command lớn thì làm sao để đảm bảo là một key đã bị thay đổi bởi một process hay một application khác ko? -> Optimistic Locking or Optimistic concurrency control
    - Optimistic concurrency control:
        - Theo dpi thay đổi trên 1 hoặc nhiều keys
        - Bỏ transaction nếu key thay đổi
        - Cách implement:
            - Dùng WATCH key [key …] trước khi một transaction bắt đầu (MULTI), có thể chạy nhiều lệnh watch
            - Những lệnh WATCH tuần tự sẽ k override lệnh WATCH trước đó
            - WATCH là local với current client và connection.
            - Tất cả key được UNWATCH sau khi thực thi transaction (EXEC)
            - Transaction sẽ fail nếu key được watch thay đổi bởi process khác
            - Những data type có thể watch: tất cả

## Object storage with Hashes
    - Có thể serialize để lưu data JSON vào redis
    - Và deserialize để read data từ redis
    - Có thể lưu dưới dạng string: binary, text
    - Nhưng nếu muốn truy cập vào một thuộc tính, phần tử, thì cần làm gì thay vì lưu JSON, vì tốn băng thông network khi truyền tải cả object -> xài HASH
    - HGETALL block operations, nếu số field của key nhỏ hơn 100 thì ok nhưng lớn hơn thì ko nên dùng
    - Có thể dùng HSCAN thay thế nếu field nhiều
    - Dùng hash để lưu object với key để định dang vào field - value được gán tương ứng
    - Xài HLEN để get số lượng field trong key
    - Nếu muốn lưu trữ các object phức tạp trong Redis thì có thể tham khảo module như ReJSON và Graph
    - Đối với các object phức tạp, lồng nhau có 3 cách để tiếp cận
        - Dùng nhiều hash, mỗi hash tương ứng 1 object (có thể là object cha, có thể là object con)
            - Ví dụ: {a: {b: {c: 1}}, b: 1} -> lưu như sau: key - {b: 1}, “key:a:b” - {c: 1}
            - Relationship ends:
                - Set for each end -> thêm vào set để lưu trữ quan hệ giữa các key
                - Key name added to Set at each end -> thêm vào key object cha các key của sub-object
                - Ưu:
                    - Extensible structures
                    - Independently store
                    - Expiration
                - Nhược điểm
                    - Many keys requires to represent the object
                    - Relationship maintenance
                    - Không dùng cho redis cluster được
        - Dùng nhiều hash, và dùng thêm sets
        - Cách cuối cùng là Flatten relationship và lưu vào single hash:
            - Cách này khá đơn giản là sẽ thay vì lưu: {a: {b : 1}} -> đổi thành {“a:b”: 1}
            - Ưu điểm:
                - Atomic updates
                - Atomic deletes
                - No transaction -> có thể thay đổi nhiều field mà ko cần transaction
                - Encapsulation -> khi xóa key thì những dữ liệu liên quan cũng bị xóa
            - Nhược:
                - Relationship maintenance -> xóa sub object thì phải xóa nhiều
                - Which end to store? -> khi muốn gom 2 key nhỏ sang một object lớn thì phải lưu lại hết field
                - Large objects
## Use case
    - Inventory control
