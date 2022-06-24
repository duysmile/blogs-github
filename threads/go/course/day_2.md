# Golang - Day2

## Notes
Muốn export function, method để sử dụng ở các package khác, thì cần 2 điều kiện:
- Đầu tiên là chữ cái đầu tiên trong tên của nó phải VIẾT HOA
- Phải được định nghĩa trong package block, hoặc nó là một field name hoặc method name.

Channel:
- dấu `<-` để thể hiện việc gửi, nhận của channel, nếu không thì mặc định nó là bidirectional - hai chiều.
- chan T          // can be used to send and receive values of type T
- chan<- float64  // can only be used to send float64s
- <-chan int      // can only be used to receive ints
- Nếu channel được tạo mà ko set capacity thì nó sẽ k được buffer và việc communication chỉ thành công khi cả sender và receiver sẵn sàng
- Ngược lại thì channel sẽ được buffer và communication sẽ thành công mà k cần block nếu buffer chưa đầy (nếu send) hoặc chưa rỗng (nếu nhận)
- Có thể dùng hàm close để đóng một channel

Concurrency
- Goroutines: Chạy một thread khác ko phải main thread, đồng thời, không phải song song, lúc này thread thật của cpu thì nó chỉ switch context, nên nó là concurrent chứ k phải parralel
- Goroutines là một lightweight thread được quản lí bởi Go runtime
- Dùng từ khóa go để chạy goroutines.
- Routine của Gỗ nhỏ hơn thread thật của máy nhiều
- Trong Golang thì mặc định là code chạy từ trên xuống dưới, nếu muốn chạy ở một routine khác thì phải xài từ khóa go.
- Channel: khi có 2 hay nhiều routine trao đổi dữ liệu với nhau
- Dùng lệnh make(chan <type>) để tạo channel với dữ liệu trong nó có type là <type>
- Như một đường ống để giao tiếp giữa 2 routine với nhau.
- Bth nếu muốn share dữ liệu 2 thread thì dùng chung biến (share memory).
- c <- data  : đẩy dữ liệu vào channel c
- <- c: lấy dữ liệu từ channel c ra
- Nếu trao đổi dữ liệu trên channel thì thread hiện tại bị block
- Khi 2 routine giao tiếp với nhau qua thì chắc chắn phải block routine tại thời điểm chúng giao tiếp vs nhau.
- Khi có routine đẩy vô thì phải có routine nhận về, nếu k thì sẽ bị block
- Buffer channel: make(chan <type>, size), size là size của buffer mà buffer channel có thể chứa.
- Buffer channel làm cho việc sử dụng channel k bị block, nếu buffer channel bị đầy mà vẫn đẩy vào thì vẫn bị deadlock
- Buffer channel là queue: first in first out
- Nếu buffer channel được đọc thì nó giải phóng và có thể đẩy tiếp vào được
- i := range c -> tương đương với lấy từng data trong channel c đưa vào i
- close() -> đóng channel (khi nào đóng thì thoát vòng lặp range để lấy data từ channel)
- Xài range khi xài vòng lặp qua mảng, hoặc channel, tương đương mỗi vòng lặp sẽ lấy từng phần tử đẩy vào channel, dùng khi ko biết channel có còn gì giao tiếp ko, còn nếu biết trước thì gán len cố định. Nếu xài range mà k close thì nó đợi mãi mãi, ko thoát được vòng lặp range channel.
- Ko nên close channel, vì nếu ko xci range thì sẽ lỗi ở bên nhận, nếu cả 2 đầu k xài channel đó nữa thì Go sẽ tự động thu hồi, chỉ bị leak khi channel được đẩy vào mà k có thằng nào nhận thì mới leak thôi.

Select:
- Dùng khi có nhiều hơn 2 channel trở lên
- Trong trường hợp k biết routine nào done trước thì xài select
- Select sẽ chọn thẳng routine nào xong trước thì xài thôi, bỏ qua các cái còn lại, nếu muốn xử lí được hết theo thứ tự phải chạy for
- Nếu ko xài select thì code sẽ chạy theo thứ tự đã define dù channel nào xong trước.
- Select dùng cho phép một routine đợi nhiều operation giao tiếp.
- Thường đi với for để xử lí được hết cả trường hợp routine xong trước và xong sau, để tránh leak.

## Phân tích từ giao diện tới DB
- Xác định loại app: App đồ ăn
- Viết user story
- Có bao nhiêu role của user: guest, user, shipper, chủ nhà hàng, admin hệ thống: có admin, …
- Từ user story -> chuyển model trong DB -> gạch chân tất cả danh từ trong user story -> hầu hết danh từ là entities trong DB.
- Entity name -> số ít
- Table name trong DB -> số nhiều
- Ưu tiên những tính năng quan trọng nhất
- Có list model vs entities -> cần biết entity đó có gì -> hầu hết properties có trên UI -> nằm xong màn hình details, lưu ý không phải tất cả thành phần UI là properties hết, nó có thể là computed properties -> được tính toán từ dữ liệu đã lưu trong DB
- Mỗi entity nên có id, status, updated_at, created_at
- Khóa chính (primary key): đảm bảo dữ liệu ko trùng
- Build entity trước -> quan hệ sau
- Primary key -> ảnh hưởng tới thứ tự vật lí trong db -> nếu dùng uuid thì phải tìm vị trí để thêm vào nên sẽ chậm, do generate uuid, và tìm vị trí để insert vào db -> query trên primary key cho tốc độ nhanh nhất
- Timestamp bản chất trong db là một số
- Relationship: one to one, one to many, many to many
- Install MySQL in docker
- dbdiagram.io -> design db

————————————
**Bài tập**: viết crawl concurrent chỉ đc 5 routine cùng lúc, gợi ý dùng select