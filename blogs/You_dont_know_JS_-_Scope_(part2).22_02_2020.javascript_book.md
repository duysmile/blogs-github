### Chương 2: Minh họa Lexical Scope

Ở phần 1, chúng ta đã tìm hiểu cách scope được xác định trong quá trình biên dịch code, một mô hình gọi là "lexical scope".

Để lý giải chính xác về chương trình của chúng ta, điều quan trọng là phải có một nền tảng khái niệm vững chắc về cách thức hoạt động của scope. Chương này sẽ minh họa scope qua vài ẩn dụ. Mục đích là để bạn nghĩ về cách mà chương trình được xử lí bởi JS engine một cách gần với thực tế nhất.

### Những viên bị, vài cái xô và bong bóng, ... Trời ơi!
Reference: https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch2.md

Một ẩn dụ tôi thấy khá hiệu quá để hiểu về scope là việc sắp xếp các viên bi màu vào xô có màu phù hợp vs chúng.

Tưởng tượng rằng bạn đi qua một đống bi, và lưu ý rằng tất cả bi màu đỏ, xanh lá, và xanh dương. Để sắp xếp tất cả bi, hãy bỏ những viên đỏ vào xô đỏ, xanh lá và xô xanh lá, và bi xanh dương và xô xanh dương. Sau khi sắp xếp, sau này khi cần bi xanh bạn biết xô xanh là nơi cần tìm.

Trong phép ẩn dụ này, những viên bi là biến trong chương trình. Những cái xô là scopes (functions và blocks), mà màu sắc được chúng ta quy định chỉ  là với mục đích thảo luận. Do đó, màu sắc của mõi viên bi được xác định theo màu scope mà ban đầu viên bi được tạo ra.

Hãy cùng chú thích ví dụ chương trình đang chạy từ Phần 1 với scope nhãn màu:
```javascript
// outer/global scope: RED

var students = [
  { id: 14, name: "Kyle" },
  { id: 143, name: "Kyle12" },
  { id: 141, name: "Kyle2" },
  { id: 142, name: "Kyle1" },
];

function getStudentName(studentID) {
  //function scope: BLUE
  for (let student of students) {
    // loop scope: GREEN
    if (student.id === studentID) {
      return student.name;
    }    
  }
}

var nextStudent = getStudentName(73);

console.log(nextStudent);
```

Chúng ta đã chỉ định 3 scope màu với những đoạn chú thích: RED (outermost global scope), BLUE (scope của hàm `getStudentName(..)`), và GREEN (scope của/trong vòng lặp `for`). Nhưng nó vẫn còn khó để nhận ra ranh giới của các xô scope này khi xem code.

Mô hình thứ 2 sẽ giúp chúng ta hình dung xanh giới của những scope của chương trình bằng cách vẽ các bong bóng màu (còn gọi là xô/thùng) xung quanh:
![scope colored bubbles](https://i.ibb.co/YQ2gfDh/fig2.png)

1. **Bubble 1** (RED) bao gồm global scope, gồm có 3 định danh/biến: `students`(dòng 1), `getStudentName`(dòng 8), và `nextStudent`(dòng 16).

2. **Bubble 2** bao gồm scope của hàm `getStudentName(..)`(dòng 8), nó chỉ có một định danh/biến: là tham số(parameter) `studentID`(dòng 8).

3. **Bubble 3** gồm scope của vòng lặp `for`(dòng 9), chỉ có một định danh/biến: `student`(dòng 9).

> NOTE: về mặt kỹ thuật, tham số `studentID` không chính xác trong scope BLUE(2). Chúng ta sẽ tháo gỡ chỗ rối này trong phần 'Implied Scopes' ở phụ lục A nhé.

Bong bóng scope được xác định suốt quá trình biên dịch dựa trên nơi functions/blocks scope được viết, lồng vào nhau, vv. Mỗi bong bóng scope hoàn toàn được chứa trong scope cha của nó - một scope không bao giờ là một phần trong hai outer scope khác nhau.

Mỗi viên bi (biến/định danh) được tô màu dựa trên bong bóng(bucket) mà nó được khai báo ở trong, không phải màu của scope mà nó có thể được truy cập từ (ví dụ `students` ở dòng 9 và `studentID` ở dòng 10).

> NOTE: Nhớ rằng chúng ta khẳng định ở phần 1 là `id`, `name`, và `log` đều là thuộc tính(properties) chứ không phải biến, nói cách khác, chúng không phải là bi trong xô, do đó chúng không lấy màu dựa trên bất nguyên tắc nào mà chúng ta thảo luận trong cuốn sách này. Để hiểu về việc truy cập thuộc tính được xử lí, hãy xem cuốn 3 *Object & Classes*.

Khi JS engine xử lí một chương trình (suối quá trình biên dịch), và tìm một khai báo cho biến, nó thực chất sẽ hỏi: "Tôi đang ở trong scope màu nào (bong bóng, xô)?". Biến được chỉ định cùng màu nghĩa là nó thuộc về cái xô/bong bóng đó.
(Dịch là xô với bong bóng mệt quá thì mình để tiếng anh nhé, bucket/bubble)

Bucket GREEN(3) được lồng vào toàn bộ trong bucket BLUE(2),và tương tự bucket BLUE(2) thì được lồng toàn bộ trong bucket RED(1). Những scopes có thể lồng vào nhau như thế, đến bất kì độ sâu nào (depth of nesting) mà chương trình cần.

Tham chiếu thới biến/định danh (không phải khai báo) có thể được tạo nếu khai báo của chúng nằm trong scope hiện tại, hoặc bất kì scope nào ở trên/ngoài scope hiện tại, nhưng không bao giờ cho những khai báo từ scope thấp hơn/lồng nhau. Do đó một biểu hiện (expression) trong bucket RED(1) chỉ có thể truy cập tới RED(1) marbles(bi), chứ không được truy cập tới bi ở BLUE(2) hay GREEN(3). Một expression trong BLUE(2) có tham chiếu tới bi ở BLUE(2) hoặc RED(1) chứ không có ở GREEN(3). Và một expression ở bucket GREEN(3) có thể truy cập tới bi ở RED(1), BLUE(2) và GREEN(3).

Chúng ta khái niệm hóa quá trình xác định những viên bi màu không khai báo này suốt runtime là một lookup. Vì tham chiếu biến `students` trong câu lệnh vòng lặp `for` ở dòng 9 không phải là một khai báo, nó không có màu. Dó dó chúng ta hỏi cái bucket scope hiện tại là BLUE(2) nếu nó có một bi nào trùng với tên này. Vì ở đó không có, nên lookup tiếp tục với scope tiếp theo ở ngoài/bao bọc: RED(1). Ở bucket RED(1) có một bi tên là `students`, do đó biến của lệnh lặp `students` được xác định là một bi ở RED(1).

Lệnh `if (student.id == studentID)` ở dòng 10 tương tự được xác định để tham chiếu đến bi `student` ở GREEN(3) và một bi `studentID` ở BLUE(2).

> NOTE: JS engine không thường xuyên xác định những bi màu này suốt run-time, "lookup" ở đây là một thiết bị tu từ (rhetorical device) để giúp bạn hiểu những khái niệm. Suốt quá trình biên dịch, hầu hết hoặc tất cả các tham chiếu biến sẽ đến từ các scope buckets đã biết, vì vậy màu sắc của chúng được xác định tại đó, và được lưu trữ với mỗi tham chiếu bi (marble reference) để tránh việc lookup không cần thiết khi chương trình chạy. Chúng ta sẽ tìm hiểu nhiều hơn trong chương tiếp nhá.

Những ý chính rút ra được từ marbles và buckets (and bubbles):
- Những biến được định nghĩa trong những scope nhất định, có thể được xem như những viên bi màu trong những xô màu tương ứng.

- Bất kì tham chiếu tói một biến có cùng tên nào trong scope, hoặc bất kì scope lồng nhau sâu hơn nào (deeper nested scope), sẽ là một bi cùng màu - trừ khi một scope can thiệp (intervening scope) làm mờ (shadows) khai báo biến, xem ở chương 3 "Shadowing".

- Việc xác định của những buckets màu, và những bi mà chúng chứa, xảy ra tron suốt quá trình biên dịch. Thông tin này được dùng cho việc "lookups" biến (bi màu) suốt quá trình thực thi code.

### Một cuộc trò chuyện giữa những người bạn

-------------

Một ẩn dụ hữu ích khác cho quá trình phân tích biến và scope mà chúng xuất phát là tưởng tượng các cuộc hội thoại khác nhau diễn ra bên trong engine mà code được xử lí và thực thi. Bạn có thể "lắng nghe" trên những cuộc trò chuyện này để có được một nền tảng khái niệm tốt hơn về cách scopes hoạt động.

Chúng ta hãy gặp những thành viên của JS engine, sẽ có những cuộc hội thoại khi họ xử lí chương trình:
1. *Engine*: có trách nhiệm biên dịch và thực thi từ đầu đến cuối cho chương trình JS.

2. *Compiler*: một trong những người bạn của Engine, xử lí tất cả các công việc như parsing và code-generation (xem phần trước).

3. *Scope Manager*: một người bạn khác của Engine; thu thập và duy trì một list look-up của tất cả các biến biến/định danh, và thi hành một tập các nguyên tắc về cách mà chúng được truy cập để thực thi code.

Để bạn có thể hiểu đầy đủ về cách JS hoạt động, bạn cần bắt đầu nghĩ như Engine (và những người bạn) nghĩ, hỏi những câu hỏi mà chúng hỏi, và trả lời câu hỏi của chúng tương tự vậy.

Để khám phá những cuộc trò chuyện này, nhớ lại chương trình đang chạy của chúng ta:

```javascript
var students = [
  { id: 14, name: "Kyle" },
  { id: 73, name: "Suzy" },
  { id: 112, name: "Frank" },
  { id: 6, name: "Sarah" },
];

function getStudentName(studentID) {
  for (let student of students) {
    if (student.id === studentID) {
      return student.name;
    }
  }
}

var nextStudent = getStudentName(73);

console.log(nextStudent);
// Suzy
```

Cùng xem xét cách JS xử lí chương trình trên, cụ thể bắt đầu từ dòng lệnh đầu tiên. Mảng và nội dung của nó chỉ là các giá trị JS cơ bản (và do đó nó không bị ảnh hưởng bởi bất kỳ mối quan tâm nào với scope), do đó trọng tâm của chúng ta sẽ nằm ở khai báo `var students = [...]` và các phần khởi tạo - gán (initialization-assignment).



























