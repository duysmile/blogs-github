# Best practices cho production: hiệu năng và độ tin cậy (perforname and reliability)

Reference: https://expressjs.com/en/advanced/best-practice-performance.html#use-gzip-compression

### Overview
Bài viết sẽ được chia làm 2 phần:
- Những điều cần làm trong code (phần dev):
  - Sử dụng gzip compression
  - Không dùng các hàm đồng bộ
  - Sử dụng logging đúng cách
  - Xử lí exception hợp lí
- Những việc cần làm ở môi trường/setup (phần ops):
  - Đặt NODE_ENV là "production"
  - Đảm bảo ứng dụng của bạn tự động restart
  - Chạy ứng dụng ở chế độ cluster
  - Cache kết quả request
  - Sử dụng load balancer
  - Sử dụng reverse proxy
  
### Những việc cần làm trong code

#### Dùng gzip compression

Gzip compressing có thể giảm được đáng kể kích thước response boyd và làm gia tăng tốc độ của web app. Sử dụng [compression](https://www.npmjs.com/package/compression) middleware để nén gzip trong ứng dụng Express. Ví dụ:

```
const compression = require('compression');
const express = require('express');
const app = express();
app.use(compression());
```
Đối với website với lượng traffic lớn trên production, cách tốt nhất để sử dụng compression là đặt nó ở lớp reverse proxy (xem phần sử dụng reverse proxy ở dưới). Trong trường hợp đó, bạn không cần dùng đến compression middleware. Để xem chi tiết về phần enabling gzip compression trên Nginx, có thể tìm hiểu thêm ở [đây](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)

#### Đừng dùng những functions đồng bộ

Những functions hay methods đồng bộ sẽ làm gián đoạn quá trình xử lí cho đến khi chúng return. Một cuộc gọi đến một function bất đồng bộ có thể return trong khoảng vài micro giây hay mili giây, tuy nhiên đối với những website có lượng traffic lớn, những cuộc gọi này cộng lại sẽ làm giảm hiệu năng của ứng dụng. Tránh những việc này ở production.

Mặc dù Node và rất nhiều modules cùng cấp cả phiên bản đồng bộ và bất đồng bộ cho những functions của chúng, hãy luôn dùng phiên bản bất đồng bộ trên production. Chỉ khi khởi động ban đầu thì có thể dùng hàm đồng bộ.

Nếu bạn đang dùng NodeJS 4.0+ hay io.js 2.1.0+, bạn có thể dùng cờ `--trace-sync-io` để in ra một cảnh báo và một stack trace bất cứ khi nào ứng dụng của bạn chạy một API đồng bộ. Dĩ nhiên, bạn có thể không muốn thấy mấy cảnh báo này ở production, nhưng đây là cách để chắc rằng code của bạn đã sẵn sàng cho production. Bạn có thể tham khảo thêm các tùy chọn cho node command-line ở [đây](https://nodejs.org/api/cli.html#cli_trace_sync_io).

#### Đặt logging đúng cách

Nhìn chung, có 2 lí do cho việc logging từ app: để phục vụ việc debugging và phục vụ việc logging hoạt động của app. Sử dụng `console.log()` hay `console.error()` để in log trên terminal là một cách phổ biến khi development. Nhưng những [hàm này](https://nodejs.org/api/console.html#console_console_1) là đồng bộ, khi đích đến là terminal hay một file, do đó chúng không phù hợp cho production, nếu bạn không pipe output đến một chương trình khác.

**Debugging**
Nếu bạn đang logging cho mục đích debugging, thì thay vì dùng `console.log()`, hãy dùng một module đặc biệt để debug như là [debug](https://www.npmjs.com/package/debug). Module này cho phép bạn sử dụng biến môi trường `DEBUG` để kiểm soát debug messages nào được gửi đến `console.error()`, nếu có. Để giữ cho ứng dụng của bạn bất đồng bộ, bạn vẫn cần pipe `console.error()` tới một chương trình khác. Nhưng mà bạn ko cần debug trên production mà phải ko?

**Hoạt động của ứng dụng**
Nếu bạn đang log hoạt động của ứng dụng(ví dụ, tracking traffic hay API calls), thay vì dùng `console.log()`. sử dụng một thư viện loggin như [Winston](https://www.npmjs.com/package/winston) hoặc [Bunyan](https://www.npmjs.com/package/bunyan). Chi tiết so sánh giữa 2 thư viện này, có thể xem ở blog StrongLoop theo link [này](https://strongloop.com/strongblog/compare-node-js-logging-winston-bunyan/).

#### Xử lí exception đúng cách

Ứng dụng Node crash khi nó gặp phải một uncaught exception.Không xử lí exceptions và thực hiện hành động thích hợp sẽ làm ứng dụng Express của bạn crash và offline. Nếu bạn dựa theo những lời khuyên trong "Đảm bảo ứng dụng của bạn tự động restart" ở dưới, thì ứng dụng sẽ được recover lại từ crash. May mắn là, ứng dụng Express thường chỉ mất một thời gian ngắn để khởi động. Tuy nhiên, bạn muốn tránh crash thì tốt nhất là nên xử lí exceptions hợp lí.

Để chắc rằng bạn xử lí tất cả exceptions, sử dụng những kỹ thuật sau:
- [Dùng try-catch](https://expressjs.com/en/advanced/best-practice-performance.html#use-try-catch)
- [Dùng promises](https://expressjs.com/en/advanced/best-practice-performance.html#use-promises)

Trước khi đi sâu là những topic này, bạn nên có hiểu biết căn bản về xử lí lỗi Node/Express: sử dụng error-first callbacks, và tuyên truyền lỗi (propagating) ở middleware. Node dùng một quy ước "error-first callbacks" để trả về lỗi từ những hàm bất đồng bộ, tham số đầu tiên của callback function là một object lỗi, tiếp sau là kết quả trả về trong tham số thành công. Để biểu thi không có lỗi, truyền null trong tham số đầu tiên. Callback function phải tuân theo quy ước error-first callback để xử lí lỗi một cách có ý nghĩa. Và trong Express, best practice là dùng hàm next() để thông báo lỗi qua chuỗi middleware.

Tìm hiểu thêm những nguyên tắc cơ bản của xử lí lỗi ở những link sau:
- [Xử lí lỗi trong Nodejs](https://www.joyent.com/node-js/production/design/errors)
- [Xây dựng ứng dụng Node mạnh mẽ: Xử lí lỗi](https://strongloop.com/strongblog/robust-node-applications-error-handling/)

**Những điều không nên làm**

Một việc bạn không nên làm là lắng nghe sự kiện `uncaughtException`, được emiited khi một exception trả về event loop. Thêm một event listener cho `uncaughtException` sẽ thay đổi hành vị mặc định của process đang gặp phải một ngoại lệ, process sẽ tiếp tục chạy mặc dù có exception. Nghe có vẻ là một cách tốt để ứng dụng của bạn không bị crash, nhưng tiếp tục chạy app sau khi có một uncaught exception là một điều nguy hiểm và không nên làm, bởi vì trang thái của process trở nên không đáng tin cậy và khó đoán.

Thêm nữa, sử dụng `uncaughtException` được chính thức công nhận là [crude](https://nodejs.org/api/process.html#process_event_uncaughtexception) (thô). Do đó lắng nghe `uncaughtException` là một ý tưởng tồi. Đó là lí do chúng tôi đề suất những thứ như multi processes và supervisors(giám sát): crashing và restarting thường là cách đáng tin cậy để recover khi có lỗi.

Chúng tôi cũng không đề nghị dùng [domains](https://nodejs.org/api/domain.html). Nó thường không giải quyết được vấn đề và là một module đã bị deprecated.

**Dùng try-catch**

Try-catch là một kiến trúc của JS dùng để catch exceptions trong code đồng bộ. Ví dụ dùng try-catch để xử lí JSON parsing lỗi như ở dưới đây.

Sử dụng những công cụ như [JSHint](https://jshint.com/) hay [JSLint](http://www.jslint.com/) để giúp bạn tìm những implicit exceptions (ngoại lệ ngầm định) như [reference errors on undefined variables](https://jshint.com/docs/options/#undef).

Đây là một ví dụ sử dụng try-catch để xử lí một process-crashing exception tiềm tàng. Hàm middleware này chấp nhận một query field parameter tên "params" là một JSON object.

```javascript
app.get('/search', function(req, res) {
  setImmediate(function() {
    var jsonStr = req.query.params;
    try {
      var jsonObj = JSOn.parse(jsonStr)
      res.send('success')
    } catch(e) {
      res.status(400).send('Invalid JSON string')
    }
  })
}
```

Tuy nhiên, try-catch chỉ hoạt động đối với code đồng bộ, Bởi vì Node platform chủ yếu là bất đồng bộ (đặc biệt trên môi trường production), try-catch sẽ không catch được nhiều exceptions.

**Dùng promises**

Promises sẽ xử lí bất kì exceptions nào (cả tường minh (explicit) hay ngầm định (implicit)) trong block code bất đồng bộ dùng `then()`. Chỉ cần thêm `.catch(next)` vào cuối của chuỗi promise. Ví dụ:

```javascript
app.get('/', function(req, res, next) {
  queryDb()
    .then(function(data){
      return makeCsv(data)
    })
    .then(function(csv) {
      // handle csv
    })
    .catch(next)
})

app.use(function(err, req, res, next){
  // handle error
})
```

Bây giờ tất cả lỗi bất đồng bộ và đồng bộ được thông báo tới error middleware.
Tuy nhiên, có 2 việc phải cẩn thận:
1. Tất cả code bất đồng bộ phải trả về promises (ngoại trừ emitters). Nếu một thư viện cụ thể không trả về promises, chuyển đổi chúng sang base object bằng cách dùng một helper function như [Bluebird.promisifyAll()](http://bluebirdjs.com/docs/api/promise.promisifyall.html).

2. Event emitters (như streams) có thể vẫn xảy ra uncaught exceptions. Do đó đảm bảo rằng bạn đẳng lí error event đúng cách, ví dụ:
```javascript
const wrap = fn => (...args) => fn(...args).catch(args[2])

app.get('/', wrap(async (req, res, next) => {
  let company = await getCompanyById(req.query.id)
  let stream = getLogoByStreamId(company.id);
  stream.on('error', next).pipe(res)
}))
```

Hàm `wrap()` là một wrapper để catch rejected promises và gọi `next()` với error là đối số đầu tiên. Chi tiết có thể xem ở [đây](https://strongloop.com/strongblog/async-error-handling-expressjs-es7-promises-generators/#cleaner-code-with-generators).

Để xem nhiều thông tin hơn về xử lí lỗi dùng promises thì có thể xem ở [đây](https://strongloop.com/strongblog/promises-in-node-js-with-q-an-alternative-to-callbacks/).

### Những việc cần làm với môi trường/setup




































