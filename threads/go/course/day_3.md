# Golang - Day3

## Mutex Lock channel
Data racing
- Môi trường multi threading, khi concurrent vs routine thì nếu cùng đọc và ghi cùng 1 biến -> xuất hiện lỗi data racing
- Sử dụng sync.RWMutex để giải quyết: lock := new(sync.RWMutex) - lock.Lock() và lock.Unlock()
- Tác dụng: routine nào vào trước sẽ chiếm lock -> hàm Lock() để block tất cả các routine khác tới khi Unlock()
- -> tại một thời điểm chỉ có 1 routine được update giá trị cho biến được lock.
- Nhược điểm: làm giảm performance
- RWLock: block tất cả routine còn lại dù đang read hay write
- RLock: block tất cả routine write, cho phép các routine Read được phép truy xuất (shared lock)
- Có thể xài channel để lock -> channel bản chất là kênh giao tiếp giữa các routine, nhưng lock ko phải nhiệm vụ chính của nó, nên nếu đã share biến trong các routine nên xài mutex Lock.
Channel Pattern

## Các cách xài channel thường thấy
- Link:
    - https://play.golang.org/p/9C-u721el8p
    - https://play.golang.org/p/df7WGblymJB
    - https:// play.golang.org/p/RTMRgC2ddf4
    - https://play.golang.org/p/QMqTcX-doWy
- <- chan string: channel chỉ rút data ko đẩy vô đc, ngược lại vs chan <- string, còn k chỉ định thì là 2 chiều

## Project
Simple CRUD REST API
Simple Clean Architecture
- New project Golang
- Connect MySQL với GORM
- REST API
- Simple clean architecture

## Setting project
1. Setting IDE
2. Connect GORM to MySQL: https://gorm.io, nếu quá khắt khe về performance thì nên xài sqlx - viết SQL “chay”
    - Tạo file go.mod
    - Install: go get gorm.io/gorm
    - Cài theo 2 version này: gorm.io/driver/mysql v1.0.3, gorm.io/gorm v1.20.11
    - Security: xài environment, import “os” -> os.Getenv(“DBConnectStr”)
3. Demo CRUD
    - log.Fatalln() -> log và exit chương trình
    - Tag trong khai báo struct, nếu là ‘Json’ -> phục vụ chuyển đổi data

## Notes
Tại sao ko nên xài foreign key?
- Một service lớn -> chia thành nhiều service nhỏ, một bảng lớn chia thành nhiều bảng nhỏ trong DB, nên việc truy xuất data qua REST API
- Nếu data lớn thì khóa ngoại tác động vào tốc độ thay đổi record trong DB, vì khóa ngoại giúp dữ liệu nhất quán, có ràng buộc giá trị khóa ngoại đó phải tồn tại tương ứng ở bảng khác, nó sẽ check data có tồn tại ko -> giảm performance, khi delete cha thì sẽ tìm những con được tham chiếu -> nhất quán dữ liệu.
- Với hệ thống phân tán thì có thể chấp nhận sự ko nhất quán này trong thời điểm đó, và cập nhật nó sau bằng nhiều cách
- Sau này tách service và DB thì có khóa ngoại sẽ k tách đc :(
- Key word: should not use foreign key
- Chỉ bỏ khóa ngoại trong các hệ thống online transactional, những hệ thống cần sự nhất quán như ngân hàng, tiền tệ thì ko được bỏ

Index sử dụng trên nhiều cột
- Những data ko tồn tại như 1 thực thể thì ko cần id riêng, những bảng để làm quan hệ nhiều nhiều thì có thể xem xét k cần id riêng cho mỗi record.
- Ví dụ: friend request, relationship on FB
- Việc xài 2 column làm primary key thì đảm bảo k được trùng, nó tác động đến vị trí vật lí của record được lưu trong DB theo thứ tự của column đặt làm key
- Đối với index, thì column nằm trước thì khi query nếu ko có column mà chỉ có column nằm sau thì ko xài query dùng primary key được (tốc độ sẽ chậm hơn)
- Tìm theo primary key sẽ là nhanh nhất vì nó còn tìm theo cả vị trí vật lí
- Explain query -> giải thích query
- Cách đánh index multi column -> dựa vào field nào ít record hơn thì đặt trước, xài count distinct để tìm giá trị ko trùng cái nào nhiều hơn để đánh index, hoặc dựa vào nghiệp vụ.
- Khi thay đổi record thì phải thay đổi index, index cũng tiêu tốn memory.
- Nên đánh index created_at để tối ưu tìm kiếm sort theo thời gian tạo đối với những bảng quan hệ ko có id riêng (primary là multi columns)

Vd: Mạng XH có lượt like, thì nên lưu lượt like mỗi bài viết trong record bài viết luôn, ko dựa vào số record quan hệ, chạy cron job để update sau -> chấp nhận lặp dữ liệu. Đối với những food thuộc category, nếu xóa category thì nên set null ở record food chứ k xóa record food luôn @@
