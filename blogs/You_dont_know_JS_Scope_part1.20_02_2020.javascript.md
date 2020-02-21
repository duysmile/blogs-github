# Scope là gì?
Reference: https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch1.md

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

Vậy có phải 2 mô hình này loại trừ lẫn nhau? Ừmm, nhìn chung thì đúng là vậy. Tuy nhiên, vẫn đề có nhiều sắc thái hơn, bởi vì quá trình thông dịch thực sự có thể có những hình thức khác hơn là chỉ xử lí từng dòng từng dòng trong source code. Những JS engine hiện đại dùng nhiều biến thể của cả thông dịch và biên dịch để xử lí chương trình.

JS được miêu tả chính xác nhất là một ngôn ngữ biên dịch (**compiled language**).

### Biên dịch mã (Compiling code)

Nhưng trước tiên, tại sao việc JS có được biên dịch hay không lại ko thực sự là vấn đề?

Scope chủ yếu được xác định trong suốt quá trình biên dịch, do đó bạn không thể thực sự hiểu về scope nếu không khám phá quá trình biên dịch.

Trong lí thuyết biên dịch cổ điển, một chương trình được xử lí bởi một compiler trong 3 giai đoạn:
1. **Tokenizing/Lexing**: chia tách chuỗi các kí tjw thành các đoạn có nghĩa (đối với ngôn ngữ), gọi là tokens. Ví dụ, xem xét chương trình: `var a = 2;`. Chương trình này sẽ được chia thành những tokens sau đây: `var`, `a`, `=`, `2` và `;`. Khoảng trắng có thể có hoặc không được lưu như là một token, tùy thuộc vào việc nó có ý nghĩa hay không.

(Sự khác nhau giữa tokenizing và lexing là tinh tế (subtle) và hàn lâm (academic), nhưng nó tập trung vào việc những token này được xác định theo hướng stateless hay stateful. Đơn giản là, nếu tokenizer gọi những nguyên tắc phân tích stateful (stateful parsing rules) để tìm ra liệu `a` có nên được xem xét là một token riêng biệt hay chỉ là một phần của một token khác, thì nó gọi là **lexing**)

2. **Parsing**: Lấy một luồng (array) của token và đưa nó tới một cây của các phần tử lồng nhau(tree of nested elements), nơi đại diện cho cấu trúc ngữ pháp của chương trình. Cây này được gọi là "AST" (Abstract Syntax Tree).

Ví dụ, cây cho đoạn lệnh `var a = 2;` có thể bắt đầu với node đỉnh gọi là `VariableDeclaration`, với một node con gọi là `Identifier` (có giá trị là `a`), và một node con khác được gọi là `AssignmentExpression` mà bản thân nó có một node con gọi là `NumericLiteral` (có giá trị là `2`).

3. **Code Generation**: Nhận một AST và chuyển nó thành mã thực thi. Phần này khác nhau tùy theo ngôn ngữ, nền tảng mà nó nhắm tới.

JS engine thì dùng AST được mô tả ở trên cho đoạn `var a = 2;` và chuyển nó thành một tập các chỉ dẫn máy (machine instructions) để thực sự tạo ra một biến `a` (bao gồm cấp phát bộ nhớ, vv) và sau đó lưu giá trị của a vào `a`.

> **Lưu ý**: Các chi tiết triển khai của JS engine(sử dụng các tài nguyên hệ thống, vv) sâu hơn, phức tạp hơn nhiều so với những điều chúng ta nói ở đây. Chúng ta sẽ vẫn chỉ tập trong vào những hành vi quan sát được (observable behavior) của chương trình và để JS engine quản lí những công việc trừu tượng ở tầng hệ thống (system-level abstractions.

JS engine phức tạp hơn nhiều so với 3 giai đoạn trên. Trong quá trình phân tích và code-generation, có những bước tối ưu hiệu năng xử lí, bao gồm việc loại bỏ các yếu tố dư thừa, vv. Thực tế, code thậm chí có thể được re-compiled và re-optimized trong suốt quá trình thực thi.

Do đó, mình chỉ nói những nét chính ở đây. Nhưng bạn sẽ thấy ngay tại sao những chi tiết này lại được đề cập, thậm chí ở tầng cao (high level), là có liên quan.

JS engines không có nhiều thời gian để tối ưu, bởi vì quá trình biên dịch JS không xảy ra trong 1 bước build trước thời hạn, như những ngôn ngữ khác. Nó thường chỉ xảy ra trong chỉ vài micro giây (hoặc ít hơn!) trước khi mã được thực thi. Để đảm bảo hiệu suất nhanh nhất dưới những ràng buộc này, JS engines sử dụng tất cả các thủ thuật (như JITs để lazy compile và thậm chí là hot re-compile, vv) mà chúng nằm ngoài phạm vi thảo luận của chúng ta.

### Yêu cầu: 2 giai đoạn

Nói một cách đơn giản nhất, phần quan trọng nhất quan sát được mà chúng ta có thể làm đối với việc xử lí chương trình JS là nó xảy ra trong (ít nhất) 2 giai đoạn: phân tích/biên dịch (parsing/compilation) trước, sau đó thực thi.

Sự tách biệt của giai đoạn parsing/compilation với giai đoạn thực thi tiếp theo là thực tế có thể quan sát được, không phải là lí thuyết hay ý tưởng. Mặc dù phần dặc tả kĩ thuật của JS ko yêu cầu rõ ràng quá trình "biên dịch", nhưng nó yêu cầu hành vi về bản chất chỉ thực hiện một nhịp (cadence) biên dịch rồi thực thi (compile-then-execute).

Có 3 đặc trưng chương trình bạn có thể quan sát để chứng minh điều này: lỗi cú pháp (syntax error), những lỗi sớm (early errors), và hoisting (sẽ được bàn đến ở chương 3).

#### Lỗi cú pháp từ đầu

Xem xét chương trình sau:
```javascript
var greeting = "Hello";
console.log(greeting);

greeting = ."Hi";
// Syntax Error: unexpected token.
```

Chương trình này ko sinh ra bất kì output nào (`"Hello"` sẽ ko được in ra), thay vào đó nó sẽ throw một `SyntaxError` về một token không được mong đợi là `.` ngay trước chuỗi `"Hi"`. Vì lỗi cú pháp xảy ra sau câu lệnh `console.log(...)`, nên nếu JS thực thi từ trên xuống dưới thì `"Hello"` phải được in ra trước khi xảy ra lỗi cú pháp. Nhưng điều này ko xảy ra. Thực tế, cách duy nhất để JS engine có thể biết về lỗi cú pháp ở dòng 3, trước khi thực thi 2 dòng đầu, là bởi JS engine đầu tiên phân tích chương trình (parsing) trước khi thực thi nó.

#### Lỗi sớm (early errors)

Tiếp theo, cùng xem đoạn mã sau:
```javascript
console.log("Howdy");

saySomething("Hello", "Hi");
// Uncaught SyntaxError: Duplicate parameter name not allowed in this context

function saySomething(greeting, greeting) {
  "use strict";
  console.log(greeting);
}
```

Và `"Howdy"` cũng ko được in ra, mặc dù nó là một câu lệnh đúng.

Thay vào đó, giống như đoạn mã ở phần trước, `SyntaxError` được phát hiện trước khi chương trình thực thi. Trong trường hợp này, nó là bởi vì strict-mode (chỉ được dùng trong hàm `saySomething(...)`) đã nghiêm cấm việc này, trong số nhiều thứ khác, thì hàm có tên tham số trùng nhau, điều này được cho phép ở chế độ non-strict. Lỗi ném ra không phải là lỗi cú pháp đúng nghĩa như là một chuỗi token không đúng đinh dạng (như `".Hi"` ở trên), mà là lỗi được yêu cầu bởi đặc tả kĩ thuật để ném ra một "early error" (cho mã strict-mode) trước khi việc thực thi bắt đầu.

Nhưng làm cách nào mà JS engine biết là parameter `greeting` bị trùng lặp? Làm sao nó biết là hàm `saySomething(...)` ở strict-mode trong khi xử lí danh sách parameters(`use strict` xuất hiện sau, trong phần thân hàm)?

Một lần nữa, chỉ có một cách lí giải cho những câu hỏi này là đoạn mã phải được phân tích đầy đủ trước khi thực thi.

#### Hoisting

Cuối cùng, cùng xem đoạn code sau:
```javascript
function saySomething() {
  var greeting = "Hello";
  {
    greeting = "Howdy"; // <-- the error is here 
    let greeting = "Hi";
    console.log(greeting);
  }
}


saySomething()
// ReferenceError: Cannot access 'greeting' before initialization
```

Lưu ý là `ReferenceError` xảy ra từ dòng lệnh `greeting = "Howdy"`. Biến `greeting` cho câu lệnh này có định nghĩa ở dòng kế tiếp, `let greeting = "Hi"`, thay vì từ câu lênh `var greeting = "Hello"`.

Cách duy nhất để JS engine có thể biết, ở dòng bị lỗi, là câu lệnh tiếp theo sẽ định nghĩa một biến block-scoped với cùng tên (`greeting`) là JS engine đã xử lí mã này trong một lần trước đó, và đã cài đặt tất cả scope và những kết hợp biến. Việc xử lí scopes và khai báo chỉ có thể thực hiện chính xác bằng việc phân tích chương trình trước khi thực thi, và nó gọi là "hoisting".

`ReferenceError` ở đây đến từ đòng `greeting = "Howdy"` truy cập biến `greeting` quá sớm, một xung đột được đề cập là Temporal Dead Zone(TDZ) - Vùng chết tạm :)). Chương 3 sẽ nói rõ hơn cái này.

> Lưu ý: Khai báo `let` và `const` được khẳng định là ko bị hoisted, như một lời giải thích cho hành vi TDZ vừa được minh họa. Nhưng nó ko chính xác. Nếu `let` và `const` ko bị hoisted, thì lệnh gán `greeting = "Howdy"` đơn giản là chỉ tham chiếu đến khai báo từ lệnh `var greeting` trong phần scope ở ngoài (hàm), và sẽ ko ném ra lỗi. Nói cách khác, block-scoped `greeting` chưa từng tồn tại. Tuy nhiên, sự hiện diện của lỗi TDZ chứng minh là block-scoped `greeting` thực sự phải tồn tại, và do đó phải được hoisted lên top của block scoped! Đừng lo lắng nếu bạn vẫn bối rối về điều này. Chúng ra sẽ tìm hiểu ở chương 3.

Hi vọng bạn đã tin rằng chương trình JS được phân tích trước khi thực thi. Nhưng mà có chứng minh được chúng đã được biên dịch?

Đây là một câu hỏi thú vị để suy ngẫm. JS có thể đã phân tích chương trình, nhưng sau đó thực thi chương trình bằng cách diễn giải các hoạt động (interpreting operations) được trình bày trong AST mà ko cần biên dịch chương trình trước có được không? Có, điều này là có thể. Nhưng điều này cực kì khó xảy ra, chủ yếu là vì nó sẽ hoạt động kém hiệu quả.

Khó để tưởng tượng một kịch bản mà JS engine chất lượng sẽ giải quyết tất cả các rắc rối khi phân tích chương trình thành một AST, nhưng sau đó không chuyển đổi (hay chúng ta gọi là "biên dịch") AST đó thành thể hiện hiệu quả nhất (most efficient representation) (là binary) để engine thực thi.

Rất nhiều người nỗ lực để giải thích kĩ càng thuật ngữ này, và có nhiều sắc thái xen kẽ như là "well, actually, ...". Nhưng trong tinh thần và thực tế, điều engine làm trong khi xử lí chương trình JS là quá trình giống với biên dịch hơn là ko :))

Phân loại JS là một ngôn ngữ biên dịch không phải là về mô hình phân phối (distribution model) cho những biểu diễn thực thi nhị phân (binary executable representation) (hoặc byte-code), mà là về việc có một sự khác biết rõ ràng trong tâm trí về giai đoạn mà mã JS được xử lí và phân tích, những điều có thể quan sát được và ko thể chối cãi xảy ra trước khi mã bắt được được thực thi. Cần phải hiểu về cách mà JS engine làm với mã của chúng ta nếu muốn hiểu JS và scope thật cặn kẽ.

### Compiler Speak

Vì giờ chúng ta đã hiểu được 2 giai đoạn xử lí của một chương trình JS (compile và sau đó execute), hãy tìm hiểu tiếp cách mà JS engine nhận diện biết và xác định những scopes của một chương trình khi nó được compiled.

Đầu tiền, hãy định nghĩa một chương trình JS đơn giản dùng để phân tích cho những chương sau nữa ha:
```javascript
var students = [
  { id: 14, name: "Kyle" },
  { id: 73, name: "Suzy" },
  { id: 112, name: "Frank" },
  { id: 6, name: "Sarah" },
];

function getStudentName(studentID) {
  for (let student of students) {
    if (student.id == studentID) {
      return student.name;
    }
  }
}

var nextStudent = getStudentName(73);
console.log(nextStudent);
// Suzy
```

Khác với khai báo, tất cả những gì xảy ra với biến/định danh (variables/identifiers) trong một chương trình rơi vào một trong 2 "vai trò"(roles): hoặc là chúng là đích(target) của một lệnh gán, hoặc là chúng là nguồn(source) của một giá trị.

(Khi tôi lần đầu học về lí thuyết compiler ở trình độ khoa học máy tính, chúng tôi được dạy về khái niệm "LHS" (còn gọi là target) và "RHS"(còn gọi là source) cho những vai trò tương ứng. Bạn có thể đoán được từ "L" sang "R", các từ viết tắt này nghĩa là "Left-hand side" và Right-hand side", tượng trưng cho biểu thức ở bên phải và bên trái của dấu `=`. Tuy nhiên, targets và sources trong lệnh gán không phải lúc nào cũng xuất hiện ở bên trái hoặc bên phải dấu `=`, do đó để chắc ăn thì chúng ta nên biết về khái niệm này là *target | source* thay vì *trái | phải* nhá).

Làm sao để biết một biến là *target*? Kiểm tra nếu có một giá trị được gán vào nó, nếu có thì nó là *target*. Nếu không, thì biến đó là *source*.

Để JS engine xử lí một chương trình, nó phải gán nhãn mỗi lần xuất hiện của một biến là target hay source trước. Chúng ta sẽ tìm hiểu về vai trò của chúng.

#### Targets

Một biến *target* là như thế nào. Xem ví dụ nè:
```javascript
students = [
  /* ... */
]
```

Câu lệnh này là một lệnh gán rất rõ ràng, nên nhớ phần `var students` được xử lí hoàn toàn như một khai báo ở compile time, và không liên quan trong quá trình thực thi, tương tự với lệnh `nextStudent = getStudentName(73)`.

Có 3 phần tử target khác trong mã có lẽ ít rõ ràng hơn.
```javascript
for (let student ò students)
```

Câu lệnh gán một giá trị cho `student` trong mỗi vòng lặp. Một target khác nữa:
```javascript
getStudentName(73)
```
Nhưng làm sao mà nó là một lệnh gán tới một *target* được @@ ? Hãy nhìn kĩ nè: đối số `73` được gán cho tham số `studentID`. 

Và có một cái *target* cuối cùng trong chương trình. Bạn có tìm ra ko?
..
..
..
Rồi có tìm ra không :)) 
```javascript
function getStudentName(studentID) {...}
```

Một khai báo `function` là một trường hợp đặc biệt của tham chiếu *target*. Bạn có thể nghĩ về nó như là `var getStudentName = function(studentID){}`, nhưng mà hổng phải vậy :P. Một định danh `getStudentName` được khai báo (tại compile-time), nhưng phần `= function(studentID)` được xử lí ở quá trình biên dịch(compilation), liên kết giữa `getStudentName` và hàm được tự động thiết lập ở phần đầu của scope thay vì chờ một lệnh gán `=` được thực thi.

> Note: việc gán hàm tự động từ khai báo `function` được gọi là "function hoisting", và sẽ được nói đến ở chương 5.

#### Sources

Vậy là chúng ta đã tìm ra tất cả 5 target references trong chương trình. Những tham chiếu biến còn lại là source references(bởi vì đó là lựa chọn duy nhất :))

Trong lệnh `for (let student of students)` chúng ta nói `student` là target. nhưng `students` lại là source reference. Trong câu lệnh `if(student.id == studentID)`, cả `student` và `studentID` đều là source references. `student` cũng là source reference trong lệnh `return student.name`.

Trong `getStudentName(73)`, `getStudentName` là một source reference(mà chúng ta hi vọng là sẽ giải quyết một giá trị tham chiếu hàm). Trong `console.log(nextStudent)`, `console` là một source reference, và `nextStudent` cũng vậy.

> NOTE: Nếu bạn còn lăn tăn, thì `id`, `name`, `log` tất cả đều là thuộc tính (properties) chứ không phải là tham chiếu biến.

Tầm quan trọng của việc hiểu rõ target và source là gì? Trong phần 2 chúng ta sẽ xem lại topic này và trình bày cách vai trò của một biến ảnh hưởng đến việc tra cứu nó (cụ thể là nếu việc tra cứu thất bại).

### Cheating: Sửa đổi Run-Time scope

Bây giờ nên chốt lại là scope được xác định khi chương trình được compiled, và ko nên bị ảnh hưởng bởi bất kì điều kiện run-time nào. Tuy nhiên, nếu ở non-strict-mode, về mặt kỹ thuật có 2 cách để cheat nguyên tắc này, và thay đổi scope trong suốt run-time.

Cả hai kỹ thuật này đều không nên sử dụng - chúng đều là những ý tưởng tồi, và bạn nên dùng strict-mode bất chấp đi, nhưng quan trọng là phải ý thức được chúng trong trường hợp bạn chạy những đoạn mã này.

Hàm `eval(...)` nhận vào một chuỗi mã để compile và execute trong suốt run-time của chương trình. Nếu chuỗi code đó có khai `var` hay `function` trong đó, những khai báo này sẽ thay đổi scope mà hàm `eval(...)` đang thực thi trong đó:
```javascript
function badIdea() {
  eval("var oops = 'Ugh!';");
  console.log(oops);
}

badIdea();
// Ugh!
```

Nếu không có lệnh `eval(...)` thì biến `oops` trong `console.log(oops)` cũng ko tồn tại, và sẽ ném ra một Reference Error. Nhưng `eval(...)` thay đổi scope của hàm `badIdea` tại run-time. Đó là một ý tưởng tối vì nhiều lí do, bao gồm lí do về  ảnh hưởng hiệu năng của việc thay đổi scope đã được compiled và optimized, mỗi khi hàm `badIdea()` chạy. Nên ĐỪNG làm thế!

Cheat thứ hai là từ khóa `with`, về cơ bản nó đưa một object vào một local scope - những thuộc tính của nó được xem như những định danh (identifiers) trong scope block.
```javascript
var badIdea = {
  oops: 'Ugh!'
};

with(badIdea) {
  console.log(oops);
  // Ugh!
}
```

Thì global scope không bị thay đổi ở đây, nhưng `badIdea` đã trở thành một scope ở run-time thay vì compile-time. Một lần nữa, đây là một ý tưởng tồi, cho hiệu năng và tính dễ đọc hiểu. ĐỪNG LÀM THẾ!

Bằng mọi giá, tránh `eval(...)` (hay ít nhất, là `eval(...)` tạo các khai báo) và `with`. Như đã mô tả, những cheats này ko thể dùng nếu bạn dùng strict-mode, nên cứ dùng strict-mode là xong, yên tâm.

### Lexical Scope
Chúng ta đã chứng minh scope của JS được xác định tại compile-time, thuật ngữ cho kiểu scope này là "lexical scope". "Lexical" liên quan với giai đoạn "lexing" của quá trình biên dịch (compilation), như đã thảo luận ở phần trước.

Để thu hẹp nội dung chương trình thành một kết luận hữu ích, ý tưởng chính của "lexical scope" là nó được kiểm soát hoàn toàn bởi vị trí của các hàm, blocks, và khai báo biến, liên quan với nhau.

Nếu bạn đặt khai báo biến ở trong hàm, compiler xử lí khai báo này khi nó phân tích hàm, và gắn khai báo này với function scope. Nếu một biến là được khai báo block-scope (`let/const`), sau đó nó được gắn với block gần nhất (`{ .. }`), thay vì với function gần nhất (như với var).

Hơn nữa, một tham chiếu (vai trò là target hay source) cho một biến phải được giải quyết (resolved) khi đến từ một trong những scope mà tồn tại lexically với nó; ngược lại thì biến sẽ được cho là không đc khai báo (thường dẫn đến lỗi). Nếu biến không được khai báo thì tại scope hiện tại, thì scope bên ngoài/kèm theo tiếp theo sẽ được tìm kiếm. Quá trình đi từng bước ra ngoài một cấp độ của scope lồng nhau tiếp tục đến khi có một khai báo biến phù hợp được tìm thấy, hoặc là khi ra đến global scope và ko còn nơi nào để đi nữa.

Điều quan trọng cần lưu ý là quá trình biên dịch không thực sự làm điều gì về bộ nhớ dữ trữ (reserving memory) cho scopes và biến cả. Chưa có chương trình nào được executed.

Thay vào đó, quá trình biên dịch tạo ra một map tất cả các lexical scopes để đưa ra những gì chương trình cần khi execute. Bạn có thể hình dung việc này như là việc chèn code trong run-time, nó định nghĩa tất cả scope (còn gọi là "lexical environment") và đăng kí tất cả các định danh (biến) cho mỗi scope.

Mặt khác, trong khi scopes được xác định trong quá trình biên dịch, chúng không thực sự được tạo cho đến run-time, tại mỗi thời điểm một scope cần chạy. Trong chương tiếp thì chúng ta sẽ phát thảo nền tảng các khái niệm cho lexcial scope.
----------------------
