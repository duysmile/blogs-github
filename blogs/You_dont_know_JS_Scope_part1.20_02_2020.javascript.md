# Scope là gì?

Vào thời điểm bạn biết những chương trình đầu tiên, bạn có lẽ bắt đầu cảm thấy thoải mái với việc tạo mới biến và lưu trữ giá trị trong biến. Làm việc với biến là một trong những việc căn bản nhất trong lập trình.

Những bạn có thể không xem xét kĩ càng cơ chế bên dưới được sử dụng bởi engine để tổ chức và quản lí biến. Tôi không nói về việc quản lí bộ nhớ trên máy tính mà là: cách JS biết được biến nào được truy cập bởi những câu lệnh, và làm sao nó xử lí được những biến có cùng tên @@ ?

Câu trả lời cho câu hỏi dạng như này là một tập các quy tắc được xác định rõ ràng gọi là `scope`. Chúng ta sẽ khai thác tất cả các khía cạnh của scope, cách nó hoạt động, nó dùng để làm gì, những trường hợp cần tránh, và sau đó hướng tới các pattern phổ biến để chỉ dẫn(guide) cấu trúc của chương trình.

Và bước đầu tiên là giải mã cách mà JS engine xử lí chương trình của chúng ta trước khi chạy.

Trong series này chúng ta sẽ tập trung vào 3 trụ cột đầu tiên của JS: scope, function closures, và sức mạnh của module design pattern.

JS được phân loại là một ngôn ngữ kịch bản thông dịch (interpreted scripting language), do đó hầu hết các chương trình JS được xử lí một lần (single) và từ trên xuống dưới (top-down). Nhưng trong thực tế, JS phân tích/biên dịch (parsed/compiled) trong một giai đoạn riêng biệt trước khi bắt đầu xử lí. Quyết định của người viết code về nơi đặt biến, hàm, và những blocks với sự liên hệ lẫn nhau được phân tích dựa trên những nguyên tắc của scope, trong suốt giai đoạn phân tích/biên dịch ban đầu (initial parsing/compilation). Bố cục kết quả scope (resulting scope layout) thường không bị ảnh hưởng bởi các điều kiện run-time.

Những hàm JS cũng là những giá trị (first-class values), chúng có thể được gán và truyền qua lại giống như numbers hay strings. Nhưng vì các hàm này giữ và truy cập vào các biến, chúng duy trì scope ban đầu của chúng bất kể vị trí ở đâu trong chương trình mà hàm được thực thi. Đây gọi là closure.

Những module là cấu trúc tổ chức code được đặc trưng bởi một tập các public method được quyền truy cập đặc quyền (thông qua closure) để ẩn các biến và hàm trong scope nội bộ của module.

### Biên dịch và thông dịch (Compiled vs Interpreted)

Bạn có thể đã nghe về việc biên dịch code trước đây, nhưng có lẽ nó giống như là một chiếc hộp đen bí ẩn nơi source code đi vào một đầu và đầu ra là một chương trình.

Nó không phải là bí ẩn hay ma thuật. Biên dịch code là một tập các bước xử lí những đoạn text của code và chuyển chúng thành một danh sách các chỉ dẫn để máy tính có thể hiểu. Điển hình là, toàn bộ source code được chuyển đổi cùng lúc, và những kết quả chỉ dẫn đó được lưu thành output (thường trong file) mà sau này có thể được thực thi.

Bạn có thể nghe rằng code có thể được thông dịch, nhưng mà nó khác với biên dịch như thế nào?

Quá trình thông dịch cũng thực hiện một tác vụ tương tự như biên dịch, đó là chuyển đổi chương trình của bạn sang chỉ dẫn mà máy tính có thể hiểu. Nhưng quá trình xử lí thì khá khác nhau. Không giống như khi chương trình được biên dịch tất cả một lần, với quá trình thông dịch source code thì việc chuyển đổi điển hình là từng dòng một (line-by-line), mỗi dòng hay câu lệnh được thực thi ngay lập tức trước khi tiến hành xử lí dòng tiếp theo trong source code.

2 hình dưới đâu minh họa chương trình khi biên dịch và thông dịch!.
![Compilation vs Interpretation](https://i.ibb.co/M1xkps5/fig1-1.png)



























