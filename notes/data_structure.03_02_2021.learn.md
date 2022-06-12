# Data structure

Source: https://www.facebook.com/edu.200lab/?ref=page_internal

CẤU TRÚC DỮ LIỆU ĐÃ GÓP PHẦN TỐI ƯU ỨNG DỤNG CỦA BẠN NHƯ THẾ NÀO?
Rất nhiều công ty (đa số là chuyên technology) yêu cầu ứng viên biết giải thuật và cấu trúc dữ liệu. Nghe lạ nhỉ, không biết thì có sao đâu, cũng chỉ là thiết lập vài 3 cái object, array, map này nọ thôi mà.
Lý do là những cty này có chuẩn mực chất lượng phần mềm rất cao, engineer của họ khi cần có thể lập trình được chứ ko chỉ develop trên những cái có sẵn. Mặt khác nó thể hiện tư duy lập trình và phát triển phần mền của ứng viên, khả năng tiến xa và hiểu sâu các công nghệ của họ trong tương lai.
Trong phạm vi bài này mình sẽ nêu vài case study trên thực tế để các bạn dễ hình dung: (chứ còn nhiều và phức tạp lắm).
TỐI ƯU MẢNG 2 CHIỀU VỚI MẢNG 1 CHIỀU
Lưu ý: nếu mỗi phần tử có array size khác nhau thì không được.
Nếu khi nào bạn có nhu cầu dùng mảng 2 chiều (hay còn được gọi là array chứa array) thì có thể nghĩ đến mảng 1 chiều là đủ.
VD ta cần mảng 10 dòng 10 cột. i và j là index lần lượt ở cột và dòng. Từ đó truy xuất mảng sẽ có dạng arr[i][j].
Ờ thì có gì đâu?! Chạy tốt mà !! ... Vấn là ở khi ta cấp phát memory 10 array thì mỗi cái nằm mỗi nơi, không có liên tục, sẽ gây ảnh hưởng tới tốc độ truy xuất.
Để tối ưu hơn: chúng ta chỉ cần 1 array có 10*10 = 100 phần tử là được. Khi đó vị trí ở (i,j) sẽ là 1 index duy nhất được tính như sau:
              index = (i*10) + j
100 phần tử đặt liền kề nhau, hoặc 100 pointer liền kề nhau đương nhiên lý tưởng hơn nhiều. Nhỉ ?!
Một ứng dụng hay thấy nhất là dữ liệu hình ảnh không bao h có [][]byte mà chỉ cần []byte thôi. Hoặc nếu các bạn làm game cờ caro, cờ tướng/vua thì bàn cờ cũng nên là mảng 1 chiều thôi nhé.
TỐI ƯU LƯU TRỮ CHO COLOR HOẶC IP
Okie vấn đề này bên backend gặp nhiều hơn. Bây giờ giả sử chúng ta có 10tr dòng dữ liệu, trong đó có thông tin màu sắc (red,green,blue) hay có IPv4 (dạng a.b.c.d) thì sao nhỉ?
Có 2 cách lưu thường thấy: lưu 3 cột R,G và B hoặc 1 cột là string chứa dạng hex của màu ("ffeedd"). Từ đó: nếu lưu 3 cột kiểu số nguyên ta mất ít nhất 4x3 =12bytes. Còn nếu là string thì 1*6= 6bytes.
Thực ra chúng ta chỉ cần 1 cột số nguyên (4bytes) là đủ rồi. Vấn đề là làm sao để quy đổi thôi.
Nói về màu sắc. Ta có 3 channel, mỗi channel có độ lớn 8bit = 2^8 = 256 giá trị (0-255). Mỗi màu có tổng độ lớn là 24bit. Các bạn có thể hiểu là 24 chữ số 0 và 1 liền kề nhau. 
Tính ra bytes thực tế chỉ là 3 thôi.
Cách đổi rất đơn giản là dùng các phép bitwise:
                  color = R << 16 | G << 8 | B
Cách làm tương tự với IPv4/v6 🙂
À... Mà nếu cần đổi ngược lại thì sao nhỉ ^^.
                  R = color >> 16
                  G = color >> 8 & 0xff
                  B = color & 0xff
Phương án này một mặt đã giảm rất đáng kể dung lương lưu trữ, một mặt nếu ta đánh index cho cột số nguyên thì sẽ luôn hiệu quả hơn rất nhiều so với đánh cho 3 cột hoặc cột string.
LINKED LIST CHO NHỮNG THỨ KHÔNG CẦN TRUY XUẤT QUA INDEX HOẶC CÓ TẦN XUẤT REMOVE/ADD CAO
Mình nghĩ là không nên nói về việc cái array khi mà append và remove item thì nó ra là sao trong bài vì sẽ khá dài. Nhưng cơ bản thì mọi người có thể hiểu là array là 1 dãy memory liên tục, truy xuất index rất nhanh. Khi ta remove/add 1 element ra khỏi array, tuỳ ngôn ngữ nhưng đa số là sẽ khởi tạo 1 array mới.
Còn linked list thì nó cũng là một list, tuy nhiên phần tử có có liên kết (link) đến một phần tử khác giống như một đoàn tàu xe lửa. Đặt tính này giúp cho linked-list remove/add rất nhanh, vì chỉ cần thay đổi liên kết. Mặc khác truy xuất bằng index thì rất chậm vì phải travel từng element.
Okie trong trường dạy là thế nhưng có case nào đâu mà xài!! Thật ra là có, chỉ là chúng ta có muốn hay không thôi.
Hãy nghĩ về carousel component chúng ta thường dùng. Mình đã dùng linked-list cho nó và thấy ổn hơn nhiều. Tạo sao vậy: vì chúng ta hầu hết thời gian chỉ cần next(), back() hoặc goTo(step) và phân trang (load thêm để nối vào), thỉnh thoảng thì cần remove 1 item bất kỳ ra. Thêm nữa là nếu ta cần danh sách là 1 vòng tròn để loop,...
Yes, đây chính là đất diễn của linked-list.
Ngoài ra chúng ta còn có các use case: breadcumb, category, folder (tree),... À và cái cây DOM (DOM Tree) cũng chính là một hiện thực của cái linked-list đó các bạn. Cơ chế cấp phép bô nhớ cho process trong linux cũng là linked-list. À thôi nói hồi đi đâu luôn rồi.
TÚM LẠI
Nếu các bạn không hề dùng những thứ mình kể trên, ko sao hết, cứ giải quyết được vấn đề đúng và đủ là okie rồi. Còn nếu các bạn muốn tiến xa hơn, hoặc apply và các cty big tech thì có thể dành thời gian hơn cho chúng. Suy cho cùng thì chúng cũng thú vị và có phần xinh đẹp (beautiful) đấy chứ 🙂
