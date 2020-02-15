### Kiểm tra và điều chỉnh hiệu năng NODEJS
Reference: https://stackify.com/node-js-performance-tuning/

Chúng ta đều biết Nodejs có một hiệu năng rất đáng kinh ngạc. Nhưng dù là với ngôn ngữ nào thì để đạt được hiệu năng mong đợi thì chúng tra vẫn phải thực hiện những bài test hiệu năng. Hôm nay, chúng ta sẽ cùng đi sâu tìm hiểu để thiết lập và chạy thử nghiệm hiệu năng và phân tích kết quả để bạn có thể tạo ra các ứng dụng Nodejs nhanh như chớp :))

Cách tốt nhất để hiểu việc cách kiểm tra hiệu năng ứng dụng của bạn là qua các ví dụ.

Bạn có thể dùng Nodejs với nhiều mục đích: viết scripts để thực hiện một vài tasks, chạy một web server, hoặc là serving những file tĩnh, hay một website. Hôm nay chúng ta sẽ test một Nodejs HTTP web API. Nhưng nếu bạn đang build một thứ khác với Node thì đừng lo vì những nguyên lí thì hầu như là giống nhau.

### Bản chất của Nodejs

Trước khi bắt đầu, hãy cùng điểm qua một trong những đặc trưng độc đáo của hiệu năng Nodejs. Chúng ta cần phải có hiểu biết sâu về những đặc trưng này khi chạy những bài test hiệu năng.

Vậy tôi đang nói về cái gì?

Đáng chú ý nhất của ứng dụng Nodejs là `single-threaded`, `run-to-completion behavior` - tất cả tạo nên một thứ gọi là event loop. Hãy cùng chia nhỏ ra để tìm hiểu xem chúng thực sự là gì.

Hãy bắt đầu với single threading. Threading, là một khái niệm cho phép xử lí đồng thời (concurrent processing) trong một ứng dụng. Nodejs ko có khả năng này, hay ít nhất là không phải theo nghĩa truyền thống(traditional sense). Thay vào đó, viết một ứng dụng để phục vụ nhiều tác vụ cùng lúc, chúng ta có thể viết code bất đồng bộ (asynchronous) và dùng event loop.

### Event Loop

Event loop thực ra là gì?

Event loop là một cơ chế để Nodejs chia những long-running processes thành các phân nhỏ. Nó hoạt động giống như nhịp tim: mỗi một vài mili giây, Nodejs sẽ kiểm tra `work queue` để bắt đầu một task mới. Nếu có `work`, nó sẽ mang những `work` này lên `call stack` để thực thực đến khi kết thúc (run to completion).
![Event loop](https://stackify.com/wp-content/uploads/2019/01/Node.js-Event-Loop.png)

Bằng cách chia nhỏ các tác vụ, Nodejs có thể đa tác vụ, đây là sự thay thế cho threading mà bạn có thể dùng. Có nghĩa là, khi có một task đang đợi, một task khác có thể bắt đầu chạy. Do đó thay vì threading, chúng ta sử dụng code bất đồng bộ, bằng cách sử dụng trong cách lập trình như `callback`, `promises` và `async/await`. Hầu hết các Node APIs đều có thể thực thi đồng bộ và bất đồng bộ.

Và rồi có lẽ bạn vẫn băn khoăn là tất cả các thuật ngữ này có liên quan gì đến hiệu suất?

Để tui giải thích ...

Hiệu năng Nodejs và event loop

Tưởng tượng bạn xây dựng một ứng dụng Nodejs với 2 endpoint: một để tải file, và một để lấy thông tin user. API thông tin user sẽ có thể bị request đáng kể hơn là tải file, và nếu nó ko đáp ứng đủ nhanh, nó có thể sẽ block mỗi khi user load page -> ko tốt xíu nào.

API tải file được sử dụng không thường xuyên. Ngoài ra, mong đợi của người dùng là tác vụ upload có thể mất thời gian, nhưng không thể chấp nhận việc load page chậm được. Nếu bạn không nghĩ đến event loop khi lập trình, thì khi upload file, Nodejs có thể lấy hết tài nguyên hệ thống và có thể block tất cả những user khác sử dụng ứng dụng của bạn :))

Đó chính là lí do bạn cần phải hiểu về cơ chế single threaded của Nodejs. Khi thay đổi ứng dụng của mình, cần phải xem xét lại behavior này. Chúng ta muốn tránh thực hiện các long-running tasks một cách đồng bộ, như là thực hiện các network requests, ghi files, hoặc là thực hiện các tính toán nặng.

Bây giờ chúng ta đã biết về cơ chế single-threaded của Nodejs, chúng ta có thể tận dụng những lợi thế này. Hãy cùng đi từng bước để cài đặt, chạy, và phân tích một bài test hiệu năng ứng dụng Nodejs của bạn để đảm bảo rằng bạn đã làm hết sức để tận dụng hết hiệu năng của Nodejs.

### Bước 1: Chọn một công cụ để test performance

Đầu tiên, bạn sẽ muốn chọn một công cụ cho phép bạn chạy những performance test. Có rất nhiều công cụ, tất cả đều có ưu nhược điểm để điều chỉnh hiệu suất của Nodejs. Một điều quan trọng cần xem xét là mặc dù bạn đang test một ứng dụng Nodejs nếu bạn đang test hiệu năng trong môi trường network, thì không vấn đề gì nếu công cụ performance test của bạn được viết bằng Nodejs.

Để test hiệu năng cho HTTP cơ bản, tôi thích dùng [Artillery](https://artillery.ac), một công cụ performance testing đơn giản được viết bằng Nodejs. Nó cũng rất tốt để chạy các performance tests cho các API request. Artillery hoạt động bằng cách viết một file cấu hình để định nghĩa load profile của bạn. Bạn sẽ có Artillery biết endpoint nào bạn muốn request, ở mức nào, trong khoảng thời gian như nào, vv.

Một script test có thể như sau
```
config:
  target: 'https://artillery.io'
  phases:
  - duration: 60
  arrivalRate: 20
  defaults:
    headers:
      x-my-service-auth: '123123123123123123'
scenarios:
- flow:
- get:
url: "/docs"
```

Ở đây, bạn đang yêu cầu trang Artillery trong khoảng 60s với 20 new users truy cập vào URL.

Sau đó, chạy test, bạn chỉ cần chạy lệnh:
> artillery run your_config.yml
Artillery sẽ tạo nhiều request tới ứng dụng của bạn như bạn đã cài đặt cho nó. Điều này rất tiện để xây dựng một performance test profile giả định giống như môi trường production.

### Bước 2: Tạo một performance test profile

Một performance test profile như đã nói ở trên là định nghĩa cách hoạt động của performance test. Bạn sẽ muốn mô phỏng lượng traffic giống như trên môi trường production, hoặc là giống như mong đợi, nếu như có thể điều chỉnh hiệu suất Nodejs một cách chính xác. 





























































