# Golang - Day7

## DB
DB Schema: https://gist.github.com/duysmile/ef0b531369d7bd53ce2c4b70f259d204

Bảng carts -> primary key: user_id + food_id, đặt user id trước vì business sẽ là search cart của user
Bảng cities -> mục đích grouping, sorting được chuẩn hơn

Ratings nên là 1 entity -> nên có id trong schema
Đối với mua bán => bảng order detail nê capture food và giá để khi thay đổi thì k ảnh hưởng dữ liệu cũ
Device token -> push notificiation

## Tips
Cách để thay đổi id trong api get -> tránh crawl data
- Tự gen id bằng cách encode - decode

Tăng security -> giảm performance

Không nên về data khi create -> chỉ về id -> khi client cần thì get lại

1 request chỉ nên hoàn thành nhiệm vụ của nó rồi trả về nhanh nhất, ko nên làm nhiều quá (ví dụ như create ko cần trả data mới về)

Tăng tải => phân trang

Dùng seek -> để get dữ liệu

Việc gắn like vào data restaurant ko phải nhiệm vụ của biz -> đó là nhiệm vụ của tầng thấp hơn, nhưng cần giữ tầng storage simple responsibility -> cần tầng repository