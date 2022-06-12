# Hôm nay mình học Redux

Đầu tiên thì mình đang phải vật vã để init một site với React, và khi bắt đầu mình rất mông lung với việc quản lí một trang lớn như thế này, mình có nên dùng Redux để quản lí các trạng thái(state), có nên áp dụng nó vào project hay không? Và cuối cùng thì mình quyết định là sẽ vừa lần mò, vừa áp dụng nó vào từng chút nào, dù chưa biết mình cần đến nó hay không, nhưng trước hết, mình phải biết Redux là gì, và nó giúp được gì cho mình đã. Ok, let's go, cùng tìm hiểu nó là gì nào :D

------

Nguồn tham khảo: [Ở đây nè](https://blog.logrocket.com/why-use-redux-reasons-with-clear-examples-d21bffd5835/)

------

Redux thì không phải là một cái gì đó mới, thực ra nó đã rất phổ biến rồi. Tuy nhiên, nếu mà bạn vẫn chưa biết Redux là gì, và tại sao lại cần phải sử dụng nó, thì bạn đến đúng chỗ rồi đó :)). À mà cho dù là bạn biết Redux nhưng vẫn còn đang băn khoăn về việc có nên sử dụng nó (giống như mình) thì cũng nên đọc luôn nghen.

Ok, giờ chúng ta sẽ cùng xem xét những lợi ích của việc dùng Redux. Nhưng trước tiên thì hãy cùng xem qua những khái niệm cơ bản của Redux và chức năng của nó. Sau đó chúng ta sẽ xem thử Redux sẽ giúp được gì trong việc xây dựng web app của mình thông qua vài ví dụ đơn giản ha.

### Redux là gì?

>Redux là một predictable state container cho những ứng dụng Javascript. Nó giúp bạn tạo ra nhũng app hoạt động ổn định, chạy trên những môi trường khác nhau, và dễ dàng để test - [Redux homepage](https://redux.js.org/)

Aaaa, nói một cách đơn giản thì Redux là một công cụ để quản lí state thôi. Redux hầu như được dùng chung với React, tuy nhiên thì nó cũng có thể kết hợp với những JS framework hay JS library khác nữa. Nhìn chung thì Redux rất nhẹ, chỉ tầm 2KB, do đó bạn không cần lo lắng về việc nó sẽ làm cho app asset của bạn phình to ra.

Với Redux, thì state của ứng dụng sẽ được giữ ở store và mỗi component có thể truy cập bất kì state nào nó cần từ store. Giờ hãy cùng đi sâu vào câu hỏi: 'Tại sao lại cần đến một công cụ quản lí state?'.

### Tại sao bạn cần một công cụ quản lí state?

Hầu hết các thư viện như React, Angular,... được xây dựng theo hướng components, và việc quản lí state diễn ra nội bộ (internally), bên trong các component mà không cần đến bất kì một thư viện hay công cụ nào khác. Việc này vẫn diễn ra tốt đẹp đối với những ứng dụng với ít components nhưng khi mà ứng dụng của bạn lớn dần lên, việc quản lí state chia sẻ giữa các components trở nên rối rắm @@.

Trong app mà dữ liệu được shared giữa các components, thực sự rất khó để bit ở đâu thì nên có state. Lí tưởng nhất thì dữ liệu trong một component chỉ nên tồn tại trong một component. Vì vậy, việc chia sẻ dữ liệu giữa các thành phần sẽ trở nên khó khăn hơn.

Ví dụ, trong React, để chia sẻ dữ liệu, một state phải tồn tại trong component cha. Một phương thức để cập nhật state này được cung cấp bởi component cha và truyền dưới dạng próp cho các components con.

Đây là một ví dụ đơn giản về component Login trong React. Đầu vào của component Login ảnh hưởng đến những gì được hiển thị trong các thành phần liên quan của nó, component Status:

```javascript
class App extends React.Component {
  constructor(props) {
    super(props);
    
    // Đầu tiên Parent sẽ tạo một state để truyền đi
    this.state = { userStatus: 'NOT LOGGER IN' };
    this.setStatus = this.setStatus.bind(this);
  }
  
  // Phương thức cung cấp để thay đổi trạng thái của userStatus
  setStatus(username, password) {
    const newUsers = users;
    newUsers.map(user => {
      if (user.username === username && user.password === password) {
        this.setState({
          userStatus: "LOGGED IN"
        });
      }
    });
  }
  
  render() {
    return (
      <div>
        // trạng thái được truyền dưới dạng props, được cập nhật 
        // bất cứ khi nào component con thay đổi đầu vào
        <Status status={this.state.userStatus} />
        // method này được truyền dưới dạng props cho component con
        // để thay đổi trạng thái của userStatus
        <Login handleSubmit={this.setStatus} />
      </div>
    );
  }
}
```
> Lưu ý, dữ liệu này không cần thiết ở parent component, nhưng bởi vì những component con cần chia sẻ dữ liệu, nên nó cần cung cấp một state.

Bây giờ thử tưởng tượng điều gì sẽ xảy ra khi một state được chia sẻ giữa các components cách xa nhau trong component tree. State phải được truyền từ một component tới một component khác cho đến khi nó đến được nơi cần đến (quá chi là mệt mỏi).

Về cơ bản thì state phải được đưa lên parent component gần nhất và tiếp theo cho đến khi nó có cùng gốc cho những component cùng cần state đó mà sau đó thì nó sẽ được truyền đi. Điều này làm cho state rất khó để bảo trì và đoán trước được giá trị của nó. Và điều này cũng đồng nghĩa với việc truyền dữ liệu qua những component không liên quan, không cần đến nó.

Rõ ràng là việc quản lí state trở nên lộn xộn khi ứng dụng trở nên phức tạp. Đây chính là lí do tại sao lại bạn lại cần đến một công cụ quản lí state như Redux, nó giúp có việc duy trì các state dễ dàng hơn. Ok, giờ thì hãy cùng xem qua những khái niệm của Redux trước khi xem xét những lợi ích của nó.

### Hiểu cách Redux hoạt động

Cách Redux hoạt động khá đơn giản. Có một store trung tâm giữ toàn bộ state của ứng dụng. Mỗi component có thể truy cập state trong store mà không cần phải thông qua việc truyền qua props từ component này sang component khác.

Có 3 phần được xây dựng trong Redux: actions, store và reducers. Cùng xem thử tác dụng của mỗi phần nào! Điều này rất quan trọng để giúp bạn hiểu rõ lợi ích của Redux và cách sử dụng nó. Chúng ta sẽ cài đặt một ví dụ đơn giản cho Login component ở trên nhưng là với Redux.

### Actions trong Redux

Đơn giản, actions là những event. Chúng gửi dữ liệu từ ứng dụng của bạn đến Redux store. Dữ liệu có thể từ những tương tác người dùng, từ API hoặc là từ form.

Actions được gửi thông qua method `store.dispatch()`. Actions thực ra là những plain JS object và chúng phải có thuộc tính `type` để chỉ định loại action cần thực hiện. Chúng cũng có thể gắn thêm payload chưa những thông tin cần được xử lí bởi action. Actions được tạo ra thông qua action creator.

Đây là một ví dụ về một action khi login:
```javascript
{
  type: 'LOGIN',
  payload: {
    username: 'foo',
    password: 'bar'
  }
}
```

Đây là một ví dụ về action creator của nó:
```javascript
const setLoginStatus = (name, password) => {
  return {
    type: 'LOGIN',
    payload: {
      username: name,
      password: password
    }
  };
}
```

Như bạn thấy thì action phải chưa thuộc tính `type` và những payload khác cần lưu trữ.

### Reducers trong Redux

Reducers là những functions thuần túy nhận tham số là state hiện tại của app, thực hiện một action và trả về một state mới. Những state được lưu trữ dưới dạng object và chúng chỉ định state của app thay đổi như thế nào để đáp ứng action được gửi đến store.

Việc này dựa vào reduce function trong JS khi một single value được lấy từ nhiều giá trị sau khi một callback function được thực hiện.

Đây là một ví dụ về cách `reducer` hoạt động trong Redux:
```javascript
const LoginComponent = (state = initialState, action) => {
  switch(action.type) {
    // reducer này xử lí bất kì action nào có type `LOGIN`
    case 'LOGIN':
      return state.map(user => {
        if (user.username !== action.username) {
          return user;       
        }
        
        if (user.password === action.password) {
          return {
            ...user,
            login_status: 'LOGGED IN'
          };
        }
      });
    default: 
      return state;
  }
};
```

> Reducers nhận vào một state của app và trả về một state mới dựa trên action được truyền vào.

Như những function thuần túy, chúng không thay đổi dât trong object truyền vào hay thực hiện bất kì một tác dụng phụ nào trong app. Nhận vào cùng một object, nó sẽ luôn luôn cho cùng một kết quả.

### Store trong Redux

Store nắm giữ state của app. Chỉ có một store trong bất kì một ứng dụng Redux nào. Bạn có thể truy cập state, thay đổi state, và register hay unregister listeners thông qua những helper methods.

Cùng tạo thử một store cho login app nào:
```javascript
const store = createStore(LoginComponent);
```

Actions thực hiện trên state sẽ luôn trả về một state mới. Do đó, state sẽ rất dễ quản lí và dự đoán.

Bây giờ chúng ta đã biết một chút về Redux, hãy trở lại với ví dụ Login Component chúng ta đã cài đặt ở trên và vận dụng Redux để cải thiện nó.
```javascript
class App extends React.Component {
  render() {
    return (
      <div>
        <Status user={this.props.user.name} />
        <Login login={this.props.setLoginStatus} />
      </div>
    );
  }
}
```
Với Redux có một state chung ở store và mỗi component có thể truy cập vào state. Việc này sẽ giúp loại bỏ việc truyền state từ component này sang component khác.

Khi sử dụng Redux với React, states sẽ không còn phải đưa lên trên (component cha), do đó sẽ giúp bạn dễ dàng theo dõi action nào gây ra thay đổi. Như bạn thấy ở trên, component không cần phải cung cấp bất kì state hay method nào cho những components con để chia sẻ dữ liệu giữa chúng. Mọi thứ đều được xử lí bởi Redux. Điều này giúp đơn giản hóa ứng dụng của bạn và giúp việc bảo trì dễ dàng hơn.

Trong khi đây là lợi ích chính và lí do tại sao bạn nên sử dụng Redux, đây là tóm tắt những lí do khác mà bạn nên dùng Redux:

### Những lợi ích khác của việc sử dụng Redux:
1. Redux làm cho state có thể dự đoán được.
- Trong Redux, state luôn luôn dự đoán được. Nếu với cùng một dữ liệu và action được truyền vào reducer, kết quả sẽ luôn luôn giống nhau. State cũng là immutable và không bao giờ thay đổi. Điều này cho phép việc thực hiện các tác vụ khó khăn như undo hay redo. Cũng có thể thực hiện việc 'du hành thời gian', đó là khả năng di chuyển qua lại giữa các state và xem kết quả trong thời gian thực.

2. Maintainability
- Redux nghiêm ngặt trong việc cách tổ chức code do đó sẽ dễ dàng để những người có kiến thức về Redux có thể hiểu được cấu trúc của bất kì ứng dụng Redux nào. Điều này nhìn chung khiến Redux để bảo trì hơn.

3. Debuggable for days
- Redux làm cho việc debug dễ hơn. Bằng việc logging actions và state, sẽ dễ dàng để hiểu lỗi code, lỗi network và những bugs khác ở trong form có trong khi sản phẩm chạy.

4. Dễ kiểm tra
- Dễ dàng test ứng dụng Redux vì functions được sử dụng để thay đổi state là những pure functions.

5. Bạn có thể lưu trữ state của app trong local storage và restore nó sau khi refresh. Việc này thực sự rất tiện lợi.

6. Redux có thể được dùng để server-side rendering. Bạn có thể xử lí initial render cho app bằng cách gửi state của app tới server cùng với phản hồi với yêu cầu từ server. Những components được yêu cầu sẽ được render trong HTML và gửi tới clients.






