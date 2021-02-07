# Circuit Breaker

Trong những hệ thống micro-services thì ngoài việc được tách biệt về logic, tài nguyên, dễ quản lí từng service cũng như mở rộng, thì nó cũng đem lại rất nhiều những vấn đề phức tạp. 
Một trong những vấn đề này là việc giao tiếp giữa các service có thể bị ảnh hưởng bởi nhiều yếu tố như đường truyền mạng, tải của ứng dụng (service), ... có thể dẫn đến việc các service không thể giao tiếp được với nhau, khi đó cơ chế tự kết nối lại (hoặc tự động gửi yêu cầu lại) có thể giải quyết được vấn đề, tuy nhiên trong một vài trường hợp nếu như dịch vụ đó bị dừng hoàn toàn hoặc sự cố không thể khắc phục ngay được thì chúng ta cần phải có một phương án khác để giúp hệ thống tiếp tục vận hành. Đó chính là Circuit Breaker.

![https://comdy.vn/content/images/2020/11/circuit-breaker.png](https://comdy.vn/content/images/2020/11/circuit-breaker.png)

Circuit Breaker là một design pattern cho phép service 1 sẽ tự động trả về một phản hồi dự phòng khi nhận thấy service 2 đang gặp lỗi hoặc timeout thay vì thử đi thử lại việc gọi request đến service 2. Và nên đặc biệt chú ý ghi log đầy đủ để có thể phục vụ việc alert cũng như điều tra nguyên nhân lỗi sau này.

Khi nào nên sử dụng Circuit Breaker:

- Khi hệ thống phụ thuộc vào các dịch vụ từ xa (remote service) và chúng có thể gặp lỗi trong một số trường hợp
- Khi một service có mức độ phụ thuộc cao

Khi nào không nên dùng:

- Khi chúng ta đang xử lí các phụ thuộc nội bộ, việc dùng Circuit Breaker có thể làm tăng chi phí.
