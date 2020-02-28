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

Chúng ta thường thấy nó chỉ là một câu lệnh duy nhất, nhưng đó không phải cách mà người bạn Engine của chúng ta thấy. Thực tế, Engine nhìn thấy 2 hoạt động riêng biệt, một là *Compiler* sẽ xử lí suốt quá trình biên dịch, và còn lại là Engine sẽ xử lí khi thực thi.

Việc đầu tiên Compiler làm với chương trình là thực hiện lexing để chia chúng thành tokens, và những tokens này sẽ được parse sau đó sang tree (AST).

Một khi *Compiler* đến công đoạn code-generation, có nhiều chi tiết rõ ràng để xem hơn. Một giả định hợp lí là Compiler sẽ tạo ra code cho dòng lệnh đầu tiên là: "Cấp phát bộ nhớ cho một biến, có nhãn là `students`, sau đó gắn một tham chiếu đến mảng vào biến đó". Nhưng còn nhiều thứ hơn về nó nữa.

Đây là cách mà Compiler xử lí câu lệnh:
1. Gặp lệnh `var students`, Compiler sẽ hỏi *Scope Manager* để xem biến tên `students` đã tồn tại trong scope bucket này chưa. Nếu rồi thì Compiler sẽ từ chối khai báo này và chạy tiếp. Ngược lại, Compiler sẽ tạo ra code mà (tại thời điểm thực thi) sẽ yêu cầu *Scope Manager* để tạo một biến mới tên là `students` trong scope bucket này.

2. Compiler sau đó tạo ra code cho Engine để thực thi sau này, để xử lí lệnh gán `students = []`. Đoạn code Engine chạy đầu tiên sẽ hỏi Scope Manager xem biến `students` có thể truy cập ở scope bucket hiện tại không. Nếu không, Engine sẽ tiếp tục tìm kiếm chỗ khác (xem "Nested Scope" ở dưới). Một khi Engine tìm thấy biến, nó gán tham chiếu của mảng `[...]` cho biến.

Ở dạng một cuộc đối thoại, giai đoạn đầu tiên của quá trình biên dịch cho chương trình có thể diễn ra giữa Compiler và Scope Manager như này:
> **Compiler**: Này Scope Manager (của global scope), tôi tìm thấy một khai báo và một định danh (identifier) gọi là `students`, ông nghe qua về nó chưa?

> **(Global) Scope Manager**: Chwe nghe bao giờ, để tôi tạo nó ra cho ông.

> **Compiler**: Này Scope Manager, tôi thấy một khai báo cho một định danh tên là `getStudentName`, ông nghe thấy bao giờ chưa?

> **(Global) Scope Manager**: Chưa, nhưng mà để tôi tạo nó ra cho ông.

> **Compiler**: Này Scope Manager, `getStudentName` trỏ đến một function, do đó chúng ta cần một scope bucket mới.

> **(Function) Scope Manager**: Ok, đây nè.

> **Compiler**: Này Scope Manager(của function), tôi thấy một khai báo tham số cho `studentID`, ông nghe thấy bao giờ chưa?

> **(Function) Scope Manager**: Chưa, nhưng mà giờ tôi sẽ đăng kí nó trong scope này.

> **Compiler**: Này Scope Manager(của function), tôi thấy một vòng lặp `for` cần một scoper bucket của riêng nó.

> ...

Cuộc hội thoại là một cuộc trao đổi câu hỏi và câu trả lời, nơi Compiler sẽ hỏi Scope Manager hiện tại xem đã gặp khai báo định danh mà nó đang gặp phải chưa. Nếu chưa, Scope Manager tạo ra biến trong scope. Nếu câu trả lời là có, thì nó sẽ bỏ qua vì không có việc gì cần đến Scope Manager nữa.

Compiler cũng báo hiệu khi nó đi qua function hay block scope, do đó một scope bucker nới và Scope Manager được khởi tạo.

Sau đó, khi đến giai đoạn thực thi chương trình, cuộc hội thoại sẽ tiến hành giữa Engine và Scope Manager, và có thể hình dung như này:

> **Engine**: Này Scope Manager(của global scope), trước khi chúng ta bắt đầu, ông có thể tìm định danh `getStudentName` để tôi có thể gán một function cho nó không?

> **(Global) Scope Manager**: Yep, đây nè

> **Engine**: Này Scope Manager, tôi thấy một một target reference (tham chiếu đích) cho `students`, ông nghe thấy về nó chưa?

> **(Global) Scope Manager**: Rồi, nó được khai báo ở scope này, nó được khởi tạo là `undefined`, nó sẵn sàng để được gán rồi. Đây nè.

> **Engine**: Này Scope Manager (của global scope), Tôi thấy một target reference `nextStudent` nữa nè, ông có biết không?

> **(Global) Scope Manager**: Biết chớ, nó được khai báo ở scope này, nó được khởi tạo là `undefined`, nó sẵn sàng để được gán rồi. Đây nè.

> **Engine**: Nè Scope Manager (của global scope), tôi thấy một source reference (tham chiếu nguồn) cho `getStudentName`, ông có biết không?

> **(Global) Scope Manager**: Biết nè, nó được khai báo trong scope này luôn, của ông đây.

> **Engine**: Tuyệt, giá trị của `getStudentName` là một function, vậy giờ tôi sắp thực thi nó đây.

> **Engine**: Này Scope Manager, bây giờ chúng tôi cần khởi tạo một function scope.

> ...

Cuộc hội thoại là một cuộc trao đổi câu hỏi và câu trả lời, nơi Engine đầu tiên sẽ hỏi Scope Manager hiện tại để tìm kiếm định danh được hoisted `getStudentName`, để liên kết function cho nó. Engine sau đó tiến hành hỏi Scope Manager về target reference cho `students`, và cứ thế tiếp tục.

Để xem xét lại và tổng kết cách một câu lệnh như `var students = [...]` được xử lí, chúng ta có 2 bước sau:
1. Compiler thiết lập khai báo của biến trong scope (vì nó chưa được khai báo trong scope hiện tại).
2. Khi Engine đang thực thi, vì khai báo có một lệnh khởi tạo, Engine sẽ hỏi Scope Manager để tìm biến đó, và gán cho nó một khi tìm thấy.

-----------------------------------

### Nested Scope

Khi đến thời gian thực thi hàm `getStudentName()`, Engine yêu cầu một Scope Manager instance cho function scope này, và nó sẽ tiến hành tìm kiếm tham số (`studentID`) để gán giá trị đối số là `73`, và cứ tiếp tục thế.

Function scope cho `getStudentName(...)` được lồng trong global scope. Block scope cho vòng lặp `for` cũng tương tự được lồng trong function scope. Scope có thể được lexically nested đến bất cứ độ sâu nào mà chương trình định nghĩa.

Mỗi scope nhận một instance Scope Manager của nó mỗi khi scope đó được thực thi (một hoặc nhiều lần). Mỗi scope tự động có tất cả những định danh (identifiers) của nó đã được đăng kí (cái này gọi là "variable hoisting, xem ở phần 5).

Tại phần đầu của scope, nếu bất kì identifiers nào đến từ khai báo `function`. biến đó sẽ được tự động khởi tạo đến tham chiếu function liên quan đến nó. Và nếu bất kì identifier được khai báo `var` (khác với `let`/ `const`) những biến đó sẽ được tự động khởi tạo bằng `undefined` để có thể được sử dụng, ngược lại biến được vẫn chưa được khởi tạo (còn gọi là, ở trong "TDZ" của nó, sao phần 5) và không thể được dùng cho đến khi phần khai báo của khởi tạo của nó được thực thi.

Trong lệnh `for (let student of students) {`, `student` là một source reference mà phải được tìm thấy. Nhưng làm sao lookup được xử lí, vì scope của function sẽ không tìm thấy một identifier như vậy.

Để hiểu điều này, hãy tưởng tượng về một cuộc hội thoại như này:
> **Engine**: Này Scope Manager (cho function), tôi có một source reference cho `students`, ông có biết nó không?

> **(Function) Scope Manager**: Chưa nghe bao giờ. Hãy thử scope kế tiếp bên ngoài thử (outer scope).

> **Engine**: Này Scope Manager (cho global scope), tôi có một source reference cho `students`, ông có nghe bao giờ chưa?

> **(Global) Scope Manager**: Yep, nó được khai báo rồi, của ông đây.

Một trong những khía cạnh quan trọng của lexical scope là bất cứ khi nào một identifier reference không thể được tìm thấy trong scope hiện tại, thì outer scope kế tiếp trong nesting sẽ được tìm đến; quá trình này sẽ được lặp lại cho đến khi một câu trả lời được tìm thấy hoặc không còn scope nào để tìm nữa.

#### Lookup Failures

Khi Engine tìm hết tất cả các scope hiện có và vẫn không thể giải quyết được việc lookup cho identifier, một error condition sẽ tồn tại. Tuy nhiên tùy vào chế độ của chương trình (có phải là strict-mode không) và vài trò của biến (target hay source, xem phần 1), thì error condition này sẽ được xử lí khác nhau.

**Undefined Mess**

Nếu một biến là source, một lookup cho identifier không được giải quyết sẽ được xem xét là một biến không được khai báo (undeclared)(unknown, missing), và kết quả luôn là một `Reference Error` được ném ra. Còn nêu biến là một target, và code tại thời điểm đó chạy ở strict-mode, biến được xem xét không được khai báo và tương tự sẽ ném ra `ReferenceError`.

Một error message cho một điều kiện biến không được khai báo, trong hầu hết môi trường JS, sẽ giống như báo cáo, "ReferenceError: XYZ is not defined". Cụm từ "not defined" gần như giống với khái niệm "undefined" trong tiếng Anh. Nhưng hai cái này lại rất khác nhau trong JS và error message này rất có thể làm bạn hiểu nhầm.

"Not defined" thực sự nghĩa là "chưa được khai báo"(not declared), hay đúng hơn là "undeclared", như khi một biến không có khai báo tương ứng trong bất kì scope có sẵn nào. Ngược lại, "undefined" có nghĩa là một biến được tìm thấy (đã khai báo - declared), nhưng biến không có giá trị trong nó tại thời điểm hiện tại, do đó mặc định nó có gía trị là `undefined`.

Và còn làm cho việc này dễ nhầm lẫn hơn nữa, toán tử `typeof` của JS trả về `"undefined"` cho cả biến thuộc một trong 2 loại:
```javascript
var studentName;
typeof studentName; // "undefined"

typeof doesntExist; // "undefined"
```

Hai tham chiếu biến trong tình huống này rất khác nhau, nhưng mà JS làm chúng ta hơi hoảng loạn :)) Không may là các JS developer chỉ cần phải chú ý đừng để bị lừa.

**Gl

































