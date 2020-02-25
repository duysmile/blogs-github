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

#### Đặt NODE_ENV là 'production'

Biến môi trường NODE_ENV dùng để chỉ định môi trường chạy ứng dụng (thường thì đó là development hoặc production). Một trong những việc đơn giản nhất bạn có thể làm để cải thiện hiệu năng là đặt NODE_ENV là "production".

Khi đặt NODE_ENV là "production" thì Express sẽ:
- Cache view templates.
- Cache CSS files được tạo bởi CSS extensions.
- Tạo ra ít error message dài dòng.

Các bài [test](https://www.dynatrace.com/news/blog/the-drastic-effects-of-omitting-node-env-in-your-express-js-applications/) chỉ ra rằng chỉ cần cải thiện hiệu năng ứng dụng theo hệ số  3 (a factor of three)!

Nếu bạn cần viết code cho môi trường cụ thể, bạn có thể check giá trị của NODE_ENV với process.env.NODE_ENV. **Hãy lưu ý** là việc kiểm tra giá trị của bất kì biến môi trường sẽ đều ảnh hưởng đến hiệu suất nên hãy thực hiện một cách tiết kiệm thôi.

Khi development, bạn thường đặt biến môi trường trong khi tương tác với shell, ví dụ bằng cách **export** hoặc từ file **.bash_profile**. Nhưng nhìn chung bạn không nên làm thế trên server production; thay vào đó hãy dùng OS's init system(systemd hay Upstart). Phần tiếp theo cung cấp chi tiết hơn về việc dùng init system, nhưng việc cài đặt NODE_ENV vẫn rất quan trọng cho hiệu năng (và dễ làm), cần được lưu ý nhé.

Với Upstart, dùng từ khóa env trong job file của bạn. Ví dụ:
```shell
# /etc/init/env.conf
env NODE_ENV=production
```
Để xem chi tiết hơn, bạn có thể tham khảo ở [đây](http://upstart.ubuntu.com/cookbook/#environment-variables).

Với systemd, dùng **Environment** trực tiếp trong unit file của bạn. Ví dụ:
```shell
# /etc/systemd/system/myservice.service
Environment=NODE_ENV=production
```

Để xem chi tiết hơn thì bạn có thể tham khảo ở [đây](https://coreos.com/os/docs/latest/using-environment-variables-in-systemd-units.html).

#### Đảm bảo ứng dụng của bạn tự động restart

Trên production, bạn không muốn ứng dụng của bạn offline, rõ ràng là vậy. Do đó bạn cần phải đảm bảo nó sẽ restart nếu ứng dụng crash và nếu server tự crash. Mặc dù bạn hi vọng những việc đó không xảy ra, nhưng thực tế bạn phải tính đến cả 2 việc này bằng cách:
- Dùng một process manager để restart ứng dụng (và Node) khi nó bị crash.
- Dùng init system được cung cấp bởi OS để restart process manager khi OS bị crash. Nó cũng có thể dùng init system thay cho process manager.

Ứng dụng Node crash nếu gặp phải một uncaught exception. Điều quan trọng nhất bạn cần làm là đảm bảo ứng dụng được test kĩ càng và xử lí tất cả exceptions (xem ở phần xử lí exception ở trên nhé). Nhưng để cho an toàn thì tốt nhất bạn vẫn nên đặt một cơ chế để đảm bảo nếu ứng dụng bị crash thì nó sẽ tự động restart.

**Dùng một process manager**

Trong development, bạn bắt đầu ứng dụng một cách đơn giản bằng câu lệnh `node server.js` hoặc gì đó tương tự. Nhưng khi ở trên production thì việc làm vậy là một thảm họa. Nếu ứng dụng bị crash, nó sẽ offline cho đến khi bạn restart. Để đảm bảo ứng dụng restart nếu bị crash, dùng một process manager. Một process manager là một "container"  cho ứng dụng để hỗ trợ deployment, cung cấp tính sẵn sàng cao (high availability), và có phép bạn quản lí ứng khi trong runtime.

Ngoài việc restart khi ứng dụng bị crash, process manager còn cho phép bạn:
- Có được cái nhìn sâu sắc về hiệu năng runtime và tiêu thụ tài nguyên (runtime performance và resource consumption).
- Thay đổi cài đặt động để cải thiện hiệu năng.
- Kiểm soát clustering (StrongLoop PM và pm2).

Những process manager phổ biến nhất của Node là:
- StrongLoop Process Manager
- PM2
- Forever

Để biết thêm chi tiết so sánh từng tính năng của 3 process manager trên có thể xem ở [đây](http://strong-pm.io/compare/). Chi tiết chỉ dẫn cho cả ba có thể xem ở [đây](https://expressjs.com/en/advanced/pm.html).

Dùng bất cứ process manager nào thì cùng phải giữ cho ứng dụng luôn up, thậm chí là khi nó cứ crash mãi.

Tuy nhiên, StrongLoop PM có nhiều tính năng thích hợp với production deployment. Bạn có thể dùng nó và những công cụ liên quan của StrongLoop để:
- Xây dựng và đóng gói ứng dụng local, sau đó deploy nó một cách bảo mật lên hệ thống production.
- Tự động restart app khi nó crash bất kể lí do gì.
- Quản lí cluster từ xa.
- Xem CPU profiles và heap snapshots để tối ưu hiệu năng và chẩn đoán memory leaks.
- Xem các performance metric cho ứng dụng.
- Để dàng mở rộng quy mô cho nhiều host với việc tích hợp điều khiển cho Nginx load balancer.

Như được giải thích dưới đây, khi bạn cài đặt StrongLoop PM như một operating system service sử dụng init system, nó sẽ tự động restart khi hệ thống restart. Do đó nó sẽ giữ cho những application processes và clusters sống mãi.

**Dùng init system**

Lớp tin cậy tiếp theo để đảm bảo ứng dụng của bạn restart khi server restart. Hệ thống có thể go down bởi nhiều lí do. Để đảm bảo ứng dụng restart nếu server crash, sử dụng init system được xây dựng sẵn trên OS. Hai init system chính được dùng ngày nay là [systemd](https://wiki.debian.org/systemd) và [Upstart](http://upstart.ubuntu.com/).

Có 2 cách để dùng init systems trong ứng dụng Express:
- Chạy ứng dụng với một process manager, và cài đặt process manager như một service với init system. Process manager sẽ restart ứng dụng khi nó bị crash, và init system sẽ restart process manager khi OS restart. Đây là cách được đề xuất.

- Chạy ứng dụng (và Node) trực tiếp với init system. Cách này đơn giản hơn, nhưng bạn sẽ không có được những lơi ích mà process manager đem lại.

**Systemd**

Systemd là một `Linux system and service manager`. Hầu hết các bản Linux đều chọn systemd làm init system mặc định.

Một file cấu hình systemd service gọi là **unit file**, với tên file kết thúc bởi .service. Đây là một ví dụ unit file để quản lí trực tiếp ứng dụng Node (thay thế đoạn text in đậm với giá trị cho hệ thống và ứng dụng của bạn):
```shell
[Unit]
Description=Awesome Express App


[Service]
Type=simple
ExecStart=/usr/local/bin/node /projects/myapp/index.js
WorkingDirectory=/projects/myapp

User=nobody
Group=nogroup

# Environment variables:
Environment=NODE_ENV=production

# Allow many incoming connections
LimitNOFILE=infinity

#Allow core dumps for debugging
LimitCORE=infinity

StandardInput=null
StandardOutput=syslog
StandardError=syslog
Restart=always


[Install]
WantedBy=multi-user.target
```

Chi tiết về systemd có thể xem thêm ở [đây](https://www.freedesktop.org/software/systemd/man/systemd.unit.html).

**StrongLoop PM as a systemd service**

Bạn có thể dễ dàng cài đặt StrongLoop Process Manager như một systemd service. Sau khi cài đặt, thì khi bạn restart, nó sẽ tự động restart StrongLoop PM, và StrongLoop sẽ restart tất cả các ứng dụng nó quản lí.

Để cài StrongLoop PM như một systemd service:
> $ sudo sl-pm-install --systemd

Và sau đó chạy service với lệnh sau:
> $ sudo /usr/bin/systemctl start strong-pm

Chi tiết xem thêm ở [đây](https://loopback.io/doc/#Settingupaproductionhost-RHEL7+,Ubuntu15.04or15.10).

**Upstart**

Upstart là một công cụ hệ thống có trên nhiều phiên bản Linux để chạy những tasks và services suốt quá trình khởi chạy hệ thống, tắt chúng khi shutdown và giám sát chúng. Bạn có thể cấu hình ứng dụng Express hoặc process manager như một service và sau đó Upstart sẽ tự động restart nó khi bị crash.

Và Upstart service được định nghĩa như một file cấu hình job (cũng được gọi là một "job") với tên file kết thúc với `.conf`. Ví dụ sau đây chỉ ra cách để tạo một job gọi là "myapp" cho một ứng dụng tên "myapp" với main file nằm ở /projects/myapp/index.js.

Tạo một file tên là myapp.conf ở /etc/init với nội dung sau (thay thế những từ in đậm bằng giá trị tương ứng cho hệ thống và ứng dụng của bạn):
```shell
# When to start the process
start on runlevel [2345]

# When to stop the process
stop on runlevel [016]

# Increase file descriptor limit to be able to handle more requests
limit nofile 50000 50000

# Use production mode
env NODE_ENV=production

# Run as www-data
setuid www-data
setgid www-data

# Run from inside the app dir
chdir /projects/myapp

# The process to start
exec /usr/local/bin/node /projects/myapp/index.js

# Restart the process if it is down
respawn

# Limit restart attempt to 10 times within 10 seconds
respawn limit 10 10
```

NOTE: Đoạn script này yêu cầu Upstart 1.4 hoặc mới hơn, hỗ trợ trên Ubuntu 12.04-14.10.

Vì job được cấu hình để chạy khi system start, ứng dụng của bạn sẽ chạy cùng với hệ điều hành, và tự động restart nếu app bị crash hoặc system down.

Bên cạnh biệc tự động restart app, Upstart cho phép bạn dùng những lệnh sau:
- **start myapp** - Khởi chạy app
- **restart myapp** - Restart app
- **stop myapp** - Dừng app

Chi tiết có thêm xem thêm ở [đây](http://upstart.ubuntu.com/cookbook/).

**StrongLopp PM như một Upstart service**

Bạn có thể dễ dàng cài đặt StrongLopp Process Manager như một Upstart service. Sau khi cài xong, khi server restart, nó sẽ tự động restart StrongLopp PM, và StrongLoop PM sẽ restart tất cả ứng dụng nó quản lí.

Để cài StrongLoop PM như một Upstart 1.4 service:
> $ sudo sl-pm-install

Sau đó chạy service với lệnh:
> $ sudo /sbin/initctl start strong-pm

NOTE: Trên những hệ thống không hỗ trợ Upstart 1.4, lệnh sẽ khác một chút. Xem phần [Cài đặt production host](https://loopback.io/doc/#Settingupaproductionhost-RHELLinux5and6,Ubuntu10.04-.10,11.04-.10) để biết thêm chi tiết.

#### Chạy ứng dụng trong một cluster

Trong một multi-core system, bạn có thể tăng hiệu năng của ứng dụng NODE nhiều lần bằng cách chạy một cluster of processes (cụm các process). Một cluster sẽ chạy nhiều instance của ứng dụng, một instance tương ứng với một CPU core, do đó phân tải và tasks giữa các instance.

![Cluster](https://expressjs.com/images/clustering.png)

QUAN TRỌNG: vì những instance của ứng dụng chạy trên mỗi process riêng biệt, chúng không chia sẻ không gian bộ nhớ với nhau. Do đó, những đối tượng là local cho mỗi instance của app. Vì thế bạn không thể duy trì trạng thái trong application code được. Tuy nhiên bạn có thể dùng một in-memory datastore như Redis để lưu trữ session-related data và state. Cách này áp dụng cho tất cả các hình thức mở rộng theo chiều ngang(horizontal scaling), dù là clustering với nhiều processes hay nhiều server vật lí.

Trong clustered app, worker processes có thể crash độc lập mà không ảnh hưởng tới những processes còn lại. Bên cạnh những lợi ích về performance thì việc cô lập lỗi cũng là lí do khác để chạy một cluster cho những processes app. Bất cứ khi nào một worker process crash luôn đảm bảo log lại event và spawn một process mới sử dụng cluster.fork().

**Sử dụng Node cluster module**

Clustering có thể được thiết lập bằng Node's cluster module. Nó cho phép master process tạo những worker processes và phân phối những kết nối đến từng workers. Tuy nhiên, thay vì sử dụng module này trực tiếp, sẽ tốt hơn nếu bạn chọn dùng một trong những công cụ thực hiện việc này tự động, ví dụ như `node-pm` hay là `cluster-service`.

**Sử dụng StrongLoop PM**

Nếu bạn deploy ứng dụng bằng StrongLoop PM, bạn có thể tận dụng lợi ích của clustering mà không cần chỉnh sửa code.

Khi StrongLoop PM chạy ứng dụng, nó tự động chạy một cluster với số lượng worker bằng với số CPU cores trên hệ thống. Bạn có thể chỉnh tay số lượng worker processes trên cluster bằng slc command line tool mà không cần dừng app.

Ví dụ, giả sử bạn deploy ứng dụng tới prod.foo.com và StrongLoop PM đang lắng nghe cổng 8701(mặc định), sau đó bạn set cluster size thành 8 sử dụng slc:
> $ slc ctl -C http://prod.foo.com:8701 set-size my-app 8

Để xem chi tiết về clustering với StrongLoop PM, thì có thể tham khảo ở [đây](https://loopback.io/doc/).

**Dùng PM2**

Nếu bạn deploy ứng dụng dùng PM2, bạn có thể tận dụng ích lợi của clustering mà không cần chỉnh sửa code. Bạn nên đảm bảo ứng dụng của mình là stateless trước, có nghĩa là không có local data nào được lưu trong process(ví dụ như sessions, websocket connection và những thứ tương tự).

Khi chạy một ứng dụng với PM2, bạn có thể bật **cluster mode** để chạy một cluster với một số lượng instances mà bạn chọn, và tương ứng với số lượng CPUs hiện có trên máy. Bạn có thể tự thay đổi số lượng processes trong cluster sử dụng command line tool của *pm2* mà không cần dừng app.

Để bật cluster mode, chạy ứng dụng của bạn như sau:
```shell
# Start 4 worker processes
$ pm2 start app.js -i 4
# Auto-detect number of available CPUs and start that many worker processes
$ pm2 start app.js -i max
```

Có thể cấu hình những cái này trong PM2 process file(ecosystem.config.js hoặc tương tự) bằng việc cài đặt **exec_mode** thành **cluster** và **instances** thành số workers cần chạy.

Một khi ứng dụng đã chạy, thì có thể mở rộng bằng cách:
```shell
# Add 3 more workers
$ pm2 scale app +3
# Scale to a specific number of workers
$ pm2 scale app 2
```

Chi tiết xem ở phần [Cluster Mode](https://pm2.keymetrics.io/docs/usage/cluster-mode/).

#### Cache kết quả của request

Một chiến lược khác để cải thiện performance trên production là cache lại kết quả của request, do đó ứng dụng của bạn không phải lặp lại những tính toán để phục vụ những request giống nhau.

Sử dụng một caching service như Vanish hay Nginx (có thể xem [Nginx Caching](https://serversforhackers.com/c/nginx-caching)) để cải thiện đáng kể tốc độ và hiệu năng của ứng dụng.

#### Sử dụng một load balancer

Dù có tối ưu ứng dụng như thế nào, thì một instance cũng chỉ có thể xử lí một số lượng giới hạn tải và traffic. Một cách để mở rộng ứng dụng là chạy nhiều instance của nó và phân phối tải thông qua một load balancer. Cài đặt một load balancer có thể cải thiện hiệu năng và tốc độ của ứng dụng, và cho phép nó mở rộng hơn là với một instance.

Một load balancer thường là một reverse proxy để điều phối traffic đến và từ nhiều application instances và servers. Bạn có thể dễ dàng cài đặt ứng dụng của bạn bằng cách dùng Nginx hoặc HAProxy.

Với load balancing, bạn có thể sẽ phải đảm bảo những request được liên kết với một session ID cụ thể kết nối với process nguồn gốc của chúng. Việc này được gọi là **affinity** hay **sticky sessions** và cũng có thể được giải qiueest bằng data store như Redis như ở trên cho session data(phụ thuộc vào ứng dụng của bạn). Để thảo luận thêm có thể xem ở [đây](https://socket.io/docs/using-multiple-nodes/).

#### Dùng một reverse proxy

Một reverse proxy ở trước một web app và thực hiện những tác vụ hỗ trợ trên request, bên cạnh đó thực hiện đưa request đến app. Nó có thể xử lí những trang lỗi, compression, caching, serving files, và load balancing.

Việc đưa những tác vụ không cần yêu cầu kiến thức về state của ứng dụng cho reverse proxy giúp cho Express được giải phóng và chỉ tập trung thực hiện các tác vụ chuyên biệt của ứng dụng. Vì lí do đó, chúng tôi đề xuất việc chạy Express phía sau một reverse proxy như Nginx hay HAProxy trên production.

==============
