# Hệ điều hành hoạt động như thế nào?

Reference: https://medium.com/cracking-the-data-science-interview/how-operating-systems-work-10-concepts-you-should-know-as-a-developer-8d63bb38331f

Bạn có nói được nhị phân không nhỉ? Bạn có hiểu được mã máy không? Nếu đưa bạn một bảng toàn là 0, 1 bạn có thể hiểu được ý nghĩa của nó không? Nếu bạn đi đến một đất nước mà bạn chưa từng được nghe ngôn ngữ của họ, hoặc là có nghe đến nhưng bạn không thực sự nói được, vậy bạn sẽ cần gì để giúp bạn giao tiếp với người bản địa đây?

Bạn sẽ cần một thông dịch viên. Chức năng của hệ điều hành giống như một thông dịch viên trong máy tính vậy. Nó chuyển những chuỗi giá trị 0/1, yes/no, và on/off sang ngôn ngữ có thể đọc để bạn hiểu được. Nó là tất cả việc này trong một luồng giao diện người dùng, hay GUI, mà bạn có thể di chuyển với chuột, click, và nhìn mọi thứ diễn ra ngay trước mắt.

Tùy vào phạm vị và độ sâu kiến thức, biết càng nhiều về các nguyên tắc cơ bản sẽ rất quan trọng trong việc làm cho chương trình, cấu trúc và flow của họ hoạt động tốt.

Tại sao? Khi bạn viết một chương trình và nó chạy chậm, bạn thấy code không có gì sai, vậy bạn tìm giải pháp ở đâu đây? Làm sao bạn debug được vấn đề nếu bạn không biết được cách mà hệ điều hành hoạt động? Bạn có đang truy cập quá nhiều file? Bộ nhớ có bị cạn kiệt và swap có đang trong trạng thái high usage? Nhưng bạn thậm chí còn không biết swap là gì? Hay là I/O bị blocking?

Và bạn muốn giao tiếp với máy khác. Làm sao bạn làm được qua local hay qua Internet? Sự khác nhau là gì? Tại sao những lập trình viên lại thích OS này hơn cái còn lại?

Trong quá trình trở thành một developer xịn sò, tôi đã biết đến khóa học của Georgia Tech [Introduction to Operating Systems](https://www.udacity.com/course/introduction-to-operating-systems--ud923). Khóa này dạy những abstractions, cơ chế, và những implementations căn bản của OS. Cốt lõi của khóa học này là phần lập trình đồng thời (concurrent programming) (threads và synchronization), giao tiếp inter-process, và một phần giới thiệu về distributed OSs.

Tôi muốn dùng bài viết này để chia sẻ những bài học từ khóa học này, đây là 10 khái niệm quan trọng trong hệ điều hành bạn cần học nếu muốn phát triển phần mềm tốt hơn.

## Hệ điều hành là gì?

Đầu tiên, hãy xác định hệ điều hành là gì đã. Một Hệ điều hành (Operating System) là một tập các phần mềm để quản lí phần cứng máy tính và cung cấp những services cho chương trình. Đặc biệt, nó làm ẩn đi những sự phức tạp của phần cứng, quản lí tài nguyên tính toán (computational resources) và cung cấp sự cô lập(isolation) và bảo vệ(proctection). Quan trọng nhất, nó có quyền truy cập vào tầng dưới phần cứng trực tiếp.

Những thành phần chính của một hệ điều hành là file system, scheduler, và device driver. Bạn có thể là đã dùng cả hệ điều hành Desktop (Windows, Mac, Linux) và Embedded (Android, iOS) trước đây.

Có 3 yếu tố chính của một hệ điều hành là (1)**Abstractions** (process, thread, file, socket, memory), (2) **Mechanisms** (create, schedule, open, write, allocate) và (3) **Policies** (LRU, EDF).

Có 2 nguyên tắc thiết kế hệ điều hành, một là (1) **Separation of mechanism and policy** dùng cách implement những cơ chế linh hoạt để hỗ trợ các policies, hai là (2) **Optimization for common case**: Hệ điều hành được sử dụng ở đâu? Người dùng sẽ muốn thực thi gì trên máy tính? Những yêu cầu của workload (khối lượng công việc) là gì?

Có 3 kiểu của một hệ điều hành thông dụng hiện nay. Đầu tiên là **Monolithic OS**, trong đó hệ điều hành hoàn toàn hoạt động trong không gian kerel và ở một mình trong chế độ giám sát (is alone in supervisor mode). Thứ hai là **Modular OS**, với kiểu này thì một vài phần của system core sẽ được đặt ở trong những file độc lập gọi là modules và có thể được thêm vào system tại run time. Và thứ 3 là **Micro OS**, còn ở kiểu này kernel được chia thành nhiều processes riêng biết, gọi là servers. Một vài servers chạy ở không gian kernel, một vài cái khác chạy ở không gian user.

Nào hãy cùng tìm hiểu những khái niệm chính chi tiết hơn nào!

### 1: Processes và Process Management

Một process đơn giản là một chương trình trong quá trình thực thi. Việc thực thi của một process phải tiến hành một cách tuần tự. Để dễ hình dung, thì chún ta viết một chương trình vào một file text, và khi thực thi chương trình, nó trở thành một process sẽ thực hiện tất cả các task được mô tả trong chương trình.

Khi một chương trình được tải và memory và nó trở thành một process, nó có thể được chia thành 4 phần - stack, heap, text và data. Ảnh dưới sẽ cho các bạn một bố cục đơn giản của một process bên trong main memory.
![Process](https://miro.medium.com/max/286/1*pplsGMeRKFcc0IHr1j3YwA.jpeg)

- **Stack**: Stack bao gồm những dữ liệu tạm như là tham số của method/function (method/function parameters), địa chỉ trả về (return address) và biến local (local variables).

- **Heap**: đây là bộ nhớ được cấp phát động cho một process suốt run time của nó.

- **Text**: bao gồm hoạt động hiện tại được biểu thị bởi giá trị của Program Counter và nội dung của các thanh ghi bộ xử lí (processor's registers).

- **Data**: phần này gồm các biển global và static.

Khi một process thực thi, nó trải qua những trạng thái khác nhau. Những giai đoạn có thể khác nhau ở các hệ điều hành, và tên gọi của các trạng thái cũng không phải là tên chuẩn. Nhưng nhìn chung, một process có thể có một trong 5 trạng thái tại một thời điểm:

![States](https://miro.medium.com/max/600/1*KTbvb5KA501gYqsfv39k6Q.jpeg)

- **Start**: trạng thái khởi tạo khi một process lần đầu được bắt đầu/ được tạo.

- **Ready**: process đang đợi để được gán cho một bộ xử lí (processor). Những ready processes đang đợi để bộ xử lí (processor) được phân bổ đến chúng bởi hệ điều hành để chúng có thể chạy. Một process có thể đến trạng thái này sau trạng thái **Start**, hoặc khi đang chạy mà bị ngắt bởi scheduler để gán CPU cho những process khác.

- **Running**: Một khi process được gán cho một bộ xử lí (processor) bởi scheduler của hệ điều hành, trạng thái của process được đặt là running và processor thực thi theo chỉ dẫn của nó.

- **Waiting**: Một process chuyển sang trạng thái waiting nếu nó cần phải đợi một tài nguyên nào đó, hoặc là đợi user input, hoặc đợi một file đến khi available.

- **Terminated hoặc exit**: Một khi process hoàn tất quá trình thực thi, hoặc nó bị terminated bởi hệ điều hành, nó chuyển sang trạng thái terminated để đợi được removed khởi main memory.































