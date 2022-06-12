# Bitwise

Source: https://www.facebook.com/edu.200lab/?ref=page_internal

SỬ DỤNG BITWISE ĐỂ THIẾT LẬP CẤU TRÚC DỮ LIỆU
Dựa vào kinh nghiệm làm việc của mình thì hiện tại khá ít anh em, dưới 2 năm kinh nghiệm, dùng bitwise. Nếu mình không lầm thì đây là môn bắt buộc trong trường. Vì thế có lẽ mọi người ai cũng biết nhưng lại không biết dùng thế nào. Việc ứng dụng chúng thực sự tiết kiệm rất nhiều không gian lưu trữ cũng như tốc độ tính toán.
1/ Khái niệm cơ bản về Bit:
Bit là đơn vị cơ sở vận hành của máy tính. Bit chỉ có 2 giá trị: 0 và 1, cứ 8 bits thì hợp thành 1 byte. Theo tác ở cấp độ bit ở level càng cao (tức higher astract layer như các application, website, mobile app) thì rất ít dùng. Hầu hết thì ta hay thấy những phép toán trên các kiểu dữ liệu: int, double, float, char, boolean, string.
Bitwise Operators là các toán tử vận hành ở cấp bit. Riêng bitwise sẽ có 6 toán tử: &, |, ^, <<, >>, và ~.
Trong giới hạn trên post này mình sẽ không đề cập chi tiết đầy đủ 6 toán tử bitwise mà chỉ đưa ra một số ví dụ.
2/ Bài toán phân quyền trên hệ thống
Bài toán phân quyền: READ (R), WRITE (W) và DELETE (D). 
Chúng ta thay vì tạo 3 biến boolean cho mỗi quyền, thì có thể dùng bitwise để tạo thành các tổ hợp: R, W, D, RW, RD, WD và RWD. Từ đó chỉ cần 1 biến duy nhất đại diện cho việc phân quyền, kiểu int.
          READ: 1 . Phép toán: (1 << 0). Kết quả Bit: 1.
          WRITE: 2 (1 << 1). Bit: 10
          DELETE: 4 (1 << 2). Bit: 100
… nếu thêm quyền nữa thì tăng số bên phải.
Toán tử "<<" (Left Shift) sẽ đẩy bit của toán hạng đầu tiên sang trái. Mỗi lần "đẩy" như vậy sẽ làm tăng thêm 1 bit, tương đương double giá trị của biến.
Từ đó để tổ hợp các quyền R, W và D thì ta dùng toán tử "|" (OR). Nguyên tắc 0 | 1 = 1, 0 | 0 = 0 và 1 | 1 = 1 từ đó:
          R | W = 01 | 10 = 11 (3) 
          R | D = 01 | 100 = 101 (5)
          W | D = 010 | 100 = 110 (6)
          R | W | D = 001 | 010 | 100 = 111 (7)
Việc này khá giống ta mang cộng giá trị của các quyền lại, chắc chắn rằng chúng sẽ không bao giờ bị trùng nhau.
Để làm ngược lại, tức là lấy ra 1 quyền bất kỳ trong 3 quyền, ví dụ ta muốn biết 111 (int 7) là có Write hay không? Ta dùng toán tử ">>" (Right Shift): đẩy bit qua phải, tương đương loại bỏ bit tận cùng.
          (R | W | D) >> 1 = 11 
Tới đây vì ta ko cần bit đầu tiên (bên trái) nên ta bỏ nó bằng cách dùng thêm "&" (And). Toán tử "&" sẽ cho ra kết quả là 1 nếu cả 2 bit đều là 1, ngược là 0.
((R | W | D) >> 1) & 2 tương đương với bit (111 >> 1) & 10 ta sẽ có được 1.
Đơn giản hơn: (R | W | D) & W = W. Tương đương trong bit 111 &  010 = 010 = 10 (giá trị W).
Và đương nhiên còn nhiều nữa như:
- Cấu trúc dữ liệu màu sắc RBR (24 bits).
- Cấu trúc dữ liệu của MongoID (12 bytes = 96 bits).
- Cấu trúc dữ liệu của IPv4, IPv6.
- Cấu trúc dữ liệu của các packets để giao tiếp qua mạng.
….
