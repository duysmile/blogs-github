# Composing Software
--------------
Nguồn tham khảo: [Link](https://medium.com/javascript-scene/composing-software-an-introduction-27b72500d6ea)
--------------
> Composition: "Là việc kết hợp các bộ phạn hoặc các yếu tố để tạo thành một tổng thể."

Ở trường thì tôi được dạy rằng phát triển phần mềm là "việc chia một vấn đề phức tạp thành những vấn đề nhỏ hơn, và kết hợp những giải pháp đơn giản để tạo ra giải pháp cho những vấn đề phức tạp".

Hầu hết các developer đều phải bứt đầu bứt tóc mới có thể tìm ra được câu trả lời cho 2 câu hỏi có thể là quan trọng nhất trong việc phát triển phần mềm:
- What is function composition? (Kết hợp chức năng là gì?)
- What is object composition? (Kết hợp đối tượng là gì?) 

Vấn đề là bạn không thể tránh composition chỉ vì bạn không để tâm đến nó. Bạn vẫn phải làm việc đó, nhưng là làm nó tệ hơn. Bạn viết code với nhiều bugs hơn, và làm cho những người khác khó hiểu hơn. Đây thực sự là một vấn đề lớn. Chúng ta dành nhiều thời gian hơn để bảo trì phần mềm hơn là chúng ta tạo ra nó từ đầu, và bugs ảnh hưởng đến rất nhiều người.

### Bạn tạo ra phần mềm mỗi ngày

Nếu bạn là một người phát triển phần mềm, bạn tạo ra các function và cấu trúc dữ liệu mỗi ngày, dù bạn có biết hay không. Bạn có thể làm việc này một cách có ý thức, hoặc là trong 'vô thức'.

Quá trình phát triển phần mềm là phân rã những vấn đề lớn thành những vấn đề nhỏ hơn, xây dựng các components để giải quyết các vấn đề đó rồi kết hợp chúng lại để có một ứng dụng hoàn chỉnh.

### Composing Functions

Function composition là quá trình apply một function cho đầu ra của một function khác. Trong đại số, có 2 function, f và g, `(f o g)(x) = f(g(x))`. Vòng tròn là toán tử kết hợp. 
Mỗi khi bạn viết code như thế này, bạn đang composing function:
```javascript
const g = n => n + 1;
const f = n => n * 2;

const doStuff = x => {
  const afterG = g(x);
  const afterF = f(afterG);
  return afterF;
};

doStuff(20);
```

Mỗi khi bạn viết promise chain, bạn cũng đang composing functions:
```javascript
const g = n => n + 1;
const  f = n => n * 2;

const wait = time => new Promise((resolve, reject) => {
  return setTimeout(resolve, time);
});

wait(300)
  .then(() => 20)
  .then(g)
  .then(f)
  .then(value => console.log(value));
```

Tương tự như vậy, mỗi khi bạn chain mảng các method calls, lodash, observables(RxJS, ...), bạn đang composing functions. Nếu bạn đang chaining, thì nghĩa là bạn đang composing. Nếu bạn pass giá trị trả về vào functions khác, bạn cũng đang composing. Nếu bạn gọi 2 method tuần tự, bạn cũng đang composing sử dụng `this` là dữ liệu đầu vào.

> Nếu bạn đang chaining, bạn đang composing.

Khi bạn tạo ra các functions một khi có chủ đích thì bạn sẽ làm nó tốt hơn.

Composing functions có chủ đích, bạn có thể cải thiện hàm `doStuff()` thành một hàng đơn giản hơn:
```javascript
  const g = n => n + 1;
  const f = n => n * 2;
  
  const doStuffBetter = x => f(g(x));
  doStuffBetter(20);
```

Một ý kiến phản đối thường gặp đối với cách này là nó khó debug hơn. Ví dụ, làm sao để viết lại đoạn code dưới sử dụng function composition:
```javascript
  const doStuff = x => {
    const afterG = g(x);
    console.log(`after g: ${afterG}`);
    const afterF = f(afterG);
    console.log(`after f: ${afterF}`);
    return afterF;
  };
  
  doStuff(20);
```

Đầu tiên, chúng ta tạo ra một abstract cho việc logging trong hàm `trace()`:
```javascript
  const trace = label => value => {
    console.log(`${ label }: ${ value }`);
    return value;
  };
```

Bây giờ bạn có thể dùng như này:
```javascript
  const doStuff = x => {
    const afterG = g(x);
    trace('after g')(afterG);
    const afterF = f(afterG);
    return trace('after f')(afterG);
  };
  
  doStuff(20);
```

Những thư viện phổ biến trong functional programming như Lodash và Ramda đều có những tính năng để tạo function composition dễ dàng. Bạn có thể viết lại function trên như sau:
```javascript
  import pipe from 'lodash/fp/flow';
  
  const doStuffBetter = pipe(
    g,
    trace('after g'),
    f,
    trace('after f')
  );
  
  doStuffBetter(20);
```

Nếu bạn muốn dùng cú pháp trên mà không phải import gì vào, thì hoàn toàn có thể định nghĩa `pipe` như sau:
```javascript
  // pipe(...fns: [...Function]) => x => y
  const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
```

Đừng lo lằng nếu bạn chưa theo kịp cách nó hoạt động. Chúng ta sẽ nói rõ hơn về function composition sau. Trên thực tế, việc này rất cần thiết, bạn sẽ thấy nó được định nghĩa và dùng nhiều lần trong suốt bài viết này. Mấu chốt là để bạn làm quen với nó để việc định nghĩa là sử dụng function composition trở nên tự động.

`pipe()` tạo ra một pipeline các functions, sau đó pass đầu ra của một function thành input của một cái khác. Khi bạn dùng `pipe()`(hoặc là hàm cũng có chức năng như vậy `compose()`). Bạn không cần những biến trung gian. Việc viết functions mà không cần mô tả tham số(arguments) được gọi là ***point-free style***. Để làm được việc này, bạn sẽ có thể gọi 1 functon để trả về một function mới, thay vì khai báo hàm một cách rõ ràng. Điều này có nghĩa là bạn không cần từ khóa `function` hoặc là cú pháp arrow(`=>`).

Điều này rất tuyệt vì bạn loại bỏ được những biến trung gian, biến trung gian làm cho những functions của bạn trở nên phức tạp không cần thiết.

Sau đây là vài lợi ích dể giảm độ phức tạp:

### Working Memory

Bộ não con người chỉ có thể dùng một lượng tài nguyên nhất định dùng cho [working memory](http://www.nature.com/neuro/journal/v17/n3/fig_tab/nn.3655_F2.html). Nếu mà thêm quá nhiều biến thì khả năng chúng ta để nhớ chính xác ý nghĩa của từng biến bị giảm đi đáng kể. 

Sử dụng pipe như ở trên, chúng ta đã loại bỏ được 3 biến, giải phóng gần một nửa working memory của chúng ta để dành cho những thứ khác. 

### Signal to noise ratio (Tỉ lệ tín hiệu nhiễu)

Code sạch đẹp rõ ràng cũng giúp cải thiện tỷ lệ tín hiệu nhiễu của code. Nó giống như việc nghe radio, khi radio không được điều chỉnh đúng, bạn sẽ nhận thấy rất nhiều tiếng ồn gây nhiễu, và rất khó khăn để nghe thấy nhạc. Khi bạn chỉnh đúng đài, thì nhiễu cũng biến mất, và bạn có một tín hiệu âm nhạc mạnh hơn.

Code cũng vậy thôi, code càng rõ ràng thì sẽ càng dễ hiểu. Vài đoạn code cho chúng ta nhiều thông tin hữu ích, một số khác chỉ chiếm không gian chớ chả đc gì. Nếu bạn giảm thiểu được số lượng code bạn sử dụng mà không làm mất đi ý nghĩa của nó thì sẽ làm cho code dễ phân tích, dễ hiểu hơn cho ai muốn đọc.

### Surface Area for Bugs (Giảm diện tích xuất hiện bug)

> Càng ít code = Càng ít chỗ để bug lộng hành = Càng ít bugs

### Composing Objects

> "Trong khoa học máy tính, một kiểu dữ liệu kết hợp (composite or compound data) là bất cứ kiểu dữ liệu nào mà được tạo nên từ những kiểu dữ liệu nguyên thủy của ngôn ngữ lập trình và những kiểu dữ liệu kết hợp khác. Việc tạo ra một kiểu dữ liệu kết hợp như này gọi là composition" ~ Wikipedia.

Có những kiểu nguyên thủy (primitives):
```javascript
  const firstName = 'Claude';
  const lastName = 'Debussy';
```

Và đây là một kiểu kết hợp (composite):
```javascript
  const fullName = {
    firstName,
    lastName
  };
```

Tương tự, tất cả những kiểu Arrays, Sets, Maps, WeakMaps, TypesArrays, ... đều là kiểu dữ liệu kết hợp. Bất cứ khi nào bạn xây dụng một cấu trúc dữ liệu non-primitive, bạn đang thực hiện một số kiểu object composition.

Trong 'The Gang of Four', "Bạn sẽ thấy việc object composition được áp dụng lặp đi lặp lại trong design patterns", và sau đó họ liệt kê 3 loại mối qua hệ object compositional, bao gồm ***delegation***(được sử dụng trong các mẫu trạng thái - state, chiến lược - stategy và visitor patterns),  ***accquaintance*** (khi một object biết về một object khác qua tham chiếu: a uses-a relationship, thường là pass một parameter, vd như là một network request handler có thể passed một tham chiếu tới logger để log request - request handler sử dụng logger), và ***aggregation***(khi những object con tạo thành 1 phần của object cha: a has-a relationship, vd như DOM children là những component elements của 1 DOM node - một DOM node có nhiều con).

Kế thừa class có thể dùng để khởi tạo composite objects, nhưng đây là một cách hạn chế và mong manh để làm việc này. Khi 'the Gang of Four' nói rằng "ủng hộ object composition hơn class inheritance", họ khuyên bạn dùng những cách tiếp cận linh hoạt để xây dựng composite object, hơn là cách tiếp cận cứng nhắc, tighlty-coupled(chặt chẽ) của kế thừa lớp (class inheritance).

Class inheritance chỉ là một cách tạo composite object. Tất cả các lớp sinh ra composite objects, nhưng không phải tất cả những composite object đều sinh ra từ class hay class inheritance. "Ủng hộ object composition hơn class inheritance" có nghĩa là bạn nên hình thành composite object từ những phần nhỏ, hơn là kế thừa tất cả những thuộc tính từ một ancestor(tổ tiên) trong hệ thống lớp phân cấp. Cái sau gây nên những vấn đề nổi tiếng trong thiết kế hướng đối tượng:
- The tight coupling problem: Bởi vì các lớp con phụ thuộc vào việc cài đặt lớp cha, class inheritance là phần chặt chẽ nhất tồn tại trong thiết kế hướng đối tượng.
- The fragile base class problem: Dựa vào tight coupling(liên kết chặt chẽ), sự thay đổi ở base class có thể phá vỡ số lượng lớn các lớp con. 
- The inflexible hierachy problem: With single ancestor taxonomies, given enough time and evolution, all class taxonomies are eventually wrong for new use-cases.
- The duplication by necessity problem: Dựa theo việc cấu trúc phân cấp không linh hoạt(inflexible hierachies), những use case mới thương được cài đặt duplication, hơn là extension(mở rộng), dẫn đến những class tương tự nhau bị chia ra không mong muốn. Một khi bị duplication, sẽ ko còn sự rõ ràng trong việc class mới nào nên xuất hiện từ đâu và tại sao.
- The gorilla/banana problem: "Vấn dề với ngôn ngữ hướng đối tượng là chúng có tất cả những môi trường ngầm này (implicit environment) mà chúng luôn gắn lấy. Bạn muốn một quả chuối nhưn bạn phải tạo ra một con gorilla giữ quả chuối và cả một khu rừng" - Joe Amstrong.

Hình thức phổ biến nhất của object composition trong Javascript là object concatennation(còn gọi là mixin composition). Nó hoạt động giống như kem vậy. Bạn bắt đầu với một object, sau đó trộn những tính năng mà bạn muốn vào. Thêm caramel, chocolate, ...

Xây dụng composites với class inheritance:
```javascript
  class Foo {
    constructor() {
      this.a  = 'a';
    }
  }
  
  class Bar extends Foo {
    constructor(options) {
      super(options);
      this.b = 'b';
    }
  }
  
  const myBar = new Bar(); // {a: 'a', b: 'b'}
```

Xây dựng composites với mixin composition:
```javascript
  const a = {
    a: 'a'
  };
  
  const b = {
    b: 'b'
  };
  
  const c = { ...a, ...b };
```

Chúng ta sẽ khám phá những style khác của object composition sâu hơn ở những bài sau. Bây giờ, bạn nên hiểu là:
1. Có nhiều cách để làm được việc này
2. Một vài cách tốt hơn những cái khác
3. Bạn có thể chọn cách đơn giản nhất, giải pháp linh hoạt nhất để giải quyết task.
