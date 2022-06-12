# CAP Theorem
Source: https://www.facebook.com/edu.200lab/posts/170211814882435

C.A.P và các hệ thống DB phổ biến. PV gặp câu này dễ tạch nếu bạn chưa biết.
CAP bao gồm: consistency, availability, và partition tolerance. 
Đây là 3 đặc tính rất nổi tiếng trong hệ thống phân tán (distributed system). Vì cha đẻ của lý thuyết này là một nhà khoa học máy tính lừng danh, Eric Brewer, nên nó còn được gọi là Brewer's theorem.
- Consistency: Tính nhất quán
Đặc tính này đảm bảo rằng với mọi request, hệ thống phải trả về dữ liệu mới nhất.
VD nhé: DB bạn có 3 nodes, nếu bạn ghi data vào node 1 ở timestamp 1.00, node 2 ở timestamp 1.01 thì dù read diễn ra ở node 3 thì vẫn trả về data ở node 2. Vì nó là mới nhất.
Vì thế nếu hệ thống DB đảm bảo đặc tính này, chúng cần thời gian để sync data từ các node để đảm bảo trả về dữ liệu chính xác.
- Availability: Tính sẵn sàng
Đặc tính này đảm bảo hệ thống phải luôn trả về thành công, bất kể đó có là dữ liệu mới nhất hay không.
Vẫn dùng ví dụ trên nhé nhưng lúc này Node 3 không cần phải sync hết data từ Node 1 và 2, cứ trả cái nó đang có cũng được.
- Partition Tolerance: ... nghĩ rớt não luôn mà vẫn ko biết dịch sao. Tạm gọi là: "buông tha cho nhau" được ko nhỉ.
Đặc tính này đảm bảo hệ thống vẫn phải hoạt động bình thường, dù cho network giữa chúng (một vài) đang gặp sự cố.
Vẫn là ví dụ cũ, nếu kết nối giữa 1 và 3 đang có lỗi, hệ thống có thể "kệ nó" và chạy tiếp, "buông tha" nó mấy đứa ơi. Có thể khi ấy data trả về sẽ không được đầy đủ, vì khả năng 1 phần data nằm bên node 1.
Thật ra trong hệ thống phân tán, thứ mà hay gặp sự cố nhất là về network. Khi ấy chúng ta đứng giữa 2 sự lựa chọn:
1. Cancel tiến trình, request hiện tại để đảm bảo tính nhất quán dữ liệu của hệ thống. Khi ấy tính sẵn sàng bị vi phạm, hay còn gọi là giảm availability.
2. Vẫn chày cối xử lý tiếp để đảm bảo tính sẵn sàng cao, nhưng vi phạm tính nhất quán, giảm consistency.
Từ đó trong một hệ thống phân tán nói chung, hay cả DB nói riêng thì cũng chỉ có thể làm tốt 2 trong 3 đặc tính trên.
Dưới đây là một chiếc hình mô tả các hệ thống DB hiện tại tương ứng với 2 đặc tính trong CAP.

![Image](https://scontent.fdad2-1.fna.fbcdn.net/v/t1.0-9/140920637_170209754882641_8590183221437985342_o.jpg?_nc_cat=107&ccb=2&_nc_sid=730e14&_nc_ohc=PG_puVC_QYQAX_k0pYp&_nc_ht=scontent.fdad2-1.fna&oh=4f01bfd20683957442795ede7cf04549&oe=60409D0D)
