# Naming convention

Source: https://www.facebook.com/edu.200lab/posts/163268852243398

REST API Naming Convention - Một lỗi cơ bản mà rất nhiều bạn mắc phải
Đây là phần nội dung của Section 03 trong khoá Golang - Food Delivery.
Thông thường trước buổi này các học viên sẽ được yêu cầu viết ra một danh sách các API cho app. Rất nhiều bạn đã submit những API sau:
GET /get_list_foods
POST /create_food
DELETE food/delete_food
POST user/create
POST user/update
//…
Nếu các bạn vẫn đang sử dụng những đường link/url API na ná như trên thì hôm nay 200Lab sẽ chia sẻ một "convention" để giúp các bạn chuẩn hoá lại:
GET /foods: lấy danh sách các món ăn (trả về mảng)
GET /foods?category-id=10: sử dụng filter với query string. Ex: category-id = 10.
POST /foods: tạo mới một món ăn (trả về id created)
GET /foods/{id}: lấy món ăn theo id (trả về object)
PUT /foods/{id}: update toàn bộ thông tin của 1 món ăn theo id
PATCH /foods/{id}: update một phần thông tin của 1 món ăn theo id
DELETE /foods/{id}: delete món ăn theo id
Từ đó, các bạn thấy rằng resource được viết dưới dạng số nhiều, không dùng các từ mang tính chất action (create, delete, update) trong URL.
Một số ví dụ khác:
GET /users/{id}/foods: lấy danh sách các món ăn được tạo bởi user id.
GET /users/{id}/orders lấy danh sách các orders được tạo bởi user id.
GET /users/{id}/orders/{order-id}: xem chi tiết theo order id được tạo bởi user id. (*)
(*) API này các bạn có thể dùng để phối hợp xác thực thông tin nếu order không phải của user tạo ra (VD: cột user_id trong db khác với giá trị {id}), chúng ta có thể trả về lỗi.
Một số trường hợp khác:
POST /cart/checkout: tiến hành checkout giỏ hàng của user (hiện hành / logged in).
PUT /user/config: thay đổi thiết lập của user (hiện hành / logged in).
GET /my-profile: lấy thông tin của user (hiện hành / logged in).
Với những trường hợp này, chúng ta có thể sử dụng resource ở dạng danh từ số ít và có thể dùng các từ mang tính chất chỉ ra công việc của API.
Một số quy ước khác:
- Toàn bộ API nên được quản lý theo version: Ex: /v1/users, v2/users. Từ đó hệ thống sẽ có thể hỗ trợ được nhiều version cùng lúc.
- Sử dụng viết thường (lower case) toàn bộ các URL.
- Không kết thúc API với một extension (đuôi file): .html, .xml, .json,…
- Không sử dụng underscore (_) trong URL. Thay vào đó là hyphen (-).
- Nếu API lấy collection của một resource, sau mỗi dấu "/" thường thể hiện mối quan hệ "tập con" của phần trước nó.
Chúc các bạn sớm chuẩn hoá được các API trong hệ thống của mình nhé.

![Image](https://scontent.fdad2-1.fna.fbcdn.net/v/t1.0-9/138410310_163265995577017_4935906568396480649_o.jpg?_nc_cat=101&ccb=2&_nc_sid=730e14&_nc_ohc=eKjgGpAJJkcAX8OVK_s&_nc_ht=scontent.fdad2-1.fna&oh=9900beffa692acdd31537a3f509768af&oe=60409F12)
