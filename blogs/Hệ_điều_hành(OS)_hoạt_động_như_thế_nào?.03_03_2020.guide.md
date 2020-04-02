# Hệ điều hành hoạt động như thế nào?

Reference: https://medium.com/cracking-the-data-science-interview/how-operating-systems-work-10-concepts-you-should-know-as-a-developer-8d63bb38331f

Bạn có nói được nhị phân không nhỉ? Bạn có hiểu được mã máy không? Nếu đưa bạn một bảng toàn là 0, 1 bạn có thể hiểu được ý nghĩa của nó không? Nếu bạn đi đến một đất nước mà bạn chưa từng được nghe ngôn ngữ của họ, hoặc là có nghe đến nhưng bạn không thực sự nói được, vậy bạn sẽ cần gì để giúp bạn giao tiếp với người bản địa đây?

Bạn sẽ cần một thông dịch viên. Chức năng của hệ điều hành giống như một thông dịch viên trong máy tính vậy. Nó chuyển những chuỗi giá trị 0/1, yes/no, và on/off sang ngôn ngữ có thể đọc để bạn hiểu được. Nó làm tất cả việc này trong một luồng giao diện người dùng, hay GUI, mà bạn có thể di chuyển với chuột, click, và nhìn mọi thứ diễn ra ngay trước mắt.

Tùy vào phạm vị và độ sâu kiến thức, biết càng nhiều về các nguyên tắc cơ bản sẽ rất quan trọng trong việc làm cho chương trình, cấu trúc và flow của ch hoạt động tốt.

Tại sao? Khi bạn viết một chương trình và nó chạy chậm, bạn thấy code không có gì sai, vậy bạn tìm giải pháp ở đâu đây? Làm sao bạn debug được vấn đề nếu bạn không biết được cách mà hệ điều hành hoạt động? Bạn có đang truy cập quá nhiều file? Bộ nhớ có bị cạn kiệt và swap có đang trong trạng thái high usage? Nhưng bạn thậm chí còn không biết swap là gì? Hay là I/O bị blocking?

Và bạn muốn giao tiếp với máy khác. Làm sao bạn làm được qua local hay qua Internet? Sự khác nhau là gì? Tại sao những lập trình viên lại thích OS này hơn cái còn lại?

Trong quá trình trở thành một developer xịn sò, tôi đã biết đến khóa học của Georgia Tech [Introduction to Operating Systems](https://www.udacity.com/course/introduction-to-operating-systems--ud923). Khóa này dạy những abstractions, cơ chế, và những implementations căn bản của OS. Cốt lõi của khóa học này là phần lập trình đồng thời (concurrent programming) (threads và synchronization), giao tiếp inter-process, và một phần giới thiệu về distributed OSs.

Tôi muốn dùng bài viết này để chia sẻ những bài học từ khóa học này, đây là 10 khái niệm quan trọng trong hệ điều hành bạn cần học nếu muốn phát triển phần mềm tốt hơn.

## Hệ điều hành là gì?

Đầu tiên, hãy xác định hệ điều hành là gì đã. Một Hệ điều hành (Operating System) là một tập các phần mềm để quản lí phần cứng máy tính và cung cấp những services cho chương trình. Đặc biệt, nó làm ẩn đi những sự phức tạp của phần cứng, quản lí tài nguyên tính toán (computational resources) và cung cấp sự cô lập(isolation) và bảo vệ(proctection). Quan trọng nhất, nó có quyền truy cập vào tầng dưới phần cứng trực tiếp.

Những thành phần chính của một hệ điều hành là file system, scheduler, và device driver. Bạn có thể là đã dùng cả hệ điều hành Desktop (Windows, Mac, Linux) và Embedded (Android, iOS) trước đây.

Có 3 yếu tố chính của một hệ điều hành là (1)**Abstractions** (process, thread, file, socket, memory), (2) **Mechanisms** (create, schedule, open, write, allocate) và (3) **Policies** (LRU, EDF).

Có 2 nguyên tắc thiết kế hệ điều hành, một là (1) **Separation of mechanism and policy** dùng cách implement những cơ chế linh hoạt để hỗ trợ các policies, hai là (2) **Optimization for common case**: Hệ điều hành được sử dụng ở đâu? Người dùng sẽ muốn thực thi gì trên máy tính? Những yêu cầu của workload (khối lượng công việc) là gì?

Có 3 kiểu của một hệ điều hành thông dụng hiện nay. Đầu tiên là **Monolithic OS**, trong đó hệ điều hành hoàn toàn hoạt động trong không gian kerel và ở một mình trong chế độ giám sát (is alone in supervisor mode). Thứ hai là **Modular OS**, với kiểu này thì một vài phần của system core sẽ được đặt ở trong những file độc lập gọi là modules và có thể được thêm vào system tại run time. Và thứ 3 là **Micro OS**, còn ở kiểu này kernel được chia thành nhiều processes riêng bit, gọi là servers. Một vài servers chạy ở không gian kernel, một vài cái khác chạy ở không gian user.

Nào hãy cùng tìm hiểu những khái niệm chính chi tiết hơn nào!

### 1: Processes và Process Management

Một process đơn giản là một chương trình trong quá trình thực thi. Việc thực thi của một process phải tiến hành một cách tuần tự. Để dễ hình dung, thì chúng ta viết một chương trình vào một file text, và khi thực thi chương trình, nó trở thành một process sẽ thực hiện tất cả các task được mô tả trong chương trình.

Khi một chương trình được tải vào memory và nó trở thành một process, nó có thể được chia thành 4 phần - stack, heap, text và data. Ảnh dưới sẽ cho các bạn một bố cục đơn giản của một process bên trong main memory.
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

- **Terminated hoặc exit**: Một khi process hoàn tất quá trình thực thi, hoặc nó bị terminated bởi hệ điều hành, nó chuyển sang trạng thái terminated để đợi được removed khỏi main memory.

Một Process Control Block là một cấu trúc dữ liệu được duy trì bởi Hệ điều hành cho mỗi process. PCB được định danh bởi một process ID (PID) là số nguyên. Một PCB giữ tất cả các thông tin cần để theo dõi một process như list bên dưới:
![PCB](https://miro.medium.com/max/273/1*iRRLvW9or49SYRAm9HvR0Q.jpeg)

- **Process State**: trang thái hiện tại của process - có thể là ready, running, waiting, ...

- **Process Privileges**: đây là bắt buộc để cho phép hoặc không cho phép truy cập vào tài nguyên hệ thống.

- **Process ID**: Định danh duy nhất cho mỗi prcess trong hệ điều hành.

- **Pointer**: Một con trỏ đến process cha.

- **Program Counter**: Program Counter là một con trỏ tới địa chỉ cho chỉ dẫn tiếp theo được thực thi cho process. (pointer to the address of the next instruction to be executed for this process).

- **CPU Registers**: Nhiều thanh ghi CPU khác nhau nơi những processes cần được lưu trữ cho quá trình thực thi ở trạng thái running.

- **CPU Scheduling Information**: Ưu tiên process và những thông tin scheduling khác cần thiết để lên lịch cho process (schedule the process).

- **Memory Management Information**: Bao gồm thông tin của page table, memory limits, segment table, phụ thuộc vào memory được dùng bởi hệ điều hành.

- **Accounting Information**: bao gồm số lượng CPU sử dụng cho process execution, time limits, execution ID, ...

- **IO Status Information**: Bao gồm một danh sách các thiết bị I/O được cấp phát cho process.

### 2: Threads và Concurrency

Một thread là một luồng thực thi thông qua process code. Nó có một program counter để theo dõi chỉ dẫn nào được thực thi tiếp theo. Nó cùng có thanh ghi hệ thống (system registers) để giữ những biến hiện đang hoạt động, và một stack chứa lịch sử thực thi.

Một thread chia sẻ với những peer(ngang hàng) thread của nó nhiều thông tin như code segment, data segment, và mở files. Khi một thread thay đổi một bộ nhớ một item code segment thì tất cả các thread còn lại có thể thấy được.

Một thread còn được gọi là một process nhẹ (**lightweight process**). Những thread giúp việc cải thiện hiệu năng của ứng dụng nhờ cơ chế song song (paralleism). Những thread đại diện cho cách tiếp cận để cải thiện hiệu năng của hệ điều hành nhờ việc giảm chi phí (overhead). Một thread tương đương với một classical process.

Một thread thuộc về chính xác một process,và không có thread nào có thể tồn tại được ở ngoài process. Mỗi thread đại diện cho một luồng điều khiển tách biệt. Những thread được sử dụng thành công trong việc tạo ra các network servers và webservers. Chúng cũng cung cấp nền tảng thích hợp cho việc xử lí song song của ứng dụng trên các vi xử lí chia sẻ bộ nhớ (shared memory multiprocessors).

![Threads](https://miro.medium.com/max/704/1*U3WUG21SOB1XPVj3djkZjw.jpeg)

Ưu điểm của threads:
- Chúng giảm thiểu thời gian chuyển đổi context (context switching time).
- Sử dụng chúng cung cấp việc xử lí đồng thời trong một process (concurrency within a process).
- Chúng cung cấp cách thức giao tiếp hiệu quả.
- Việc này rất tiết kiêm cho việc tạo và thay đổi context threads.
- Thread cho phép sử dụng các kiến trúc multiprocessors để tăng quy mô mở rộng cũng như hiệu quả cao hơn.

Thread được implement theo 2 cách sau:
- **User Level Threads**: những thread được user quản lí.
- **Kernel Level Threads**: những thread được hệ điều hành quản lí hoạt động trên một kernel, và core của hệ điều hành.

#### User Level Threads

Trong trường hợp này, thread management kernel không nhận thức được sự tồn tại của thread. Thread library chứa code để tạo và hủy bỏ threads, gửi messages và dữ liệu giữa các thread, để scheduling thread execution, và để lưu trữ và khôi phục lại những thread contexts. Ứng dụng bắt đầu với một single thread.

![User Level Threads](https://miro.medium.com/max/462/1*G_e42CKNsmdNmx3gOKZb1A.png)

Ưu điểm: 
- Việc chuyển đổi thread không cần dùng đến đặc quyền ở Kernel mode (kernel mode privileges).
- User level thread có thể chạy trên bất kì hệ điều hành nào
- Scheduling có thể là một ứng dụng cụ thể ở user level thread.
- User level thread được tạo và quản lí nhanh.

Nhược điểm:
- Trong một hệ điều hành thông thường, hầu hết các cuộc gọi hệ thống (system calls) đều bị chặn.
- Ứng dụng đa luồng không thể tận dụng lợi thế của multiprocessing.

#### Kernel Level Threads

Trong trường hợp này, thread management được thực hiện bởi Kernel. Không có thread management code ở khu vực ứng dụng (application area). Những kernel threads được hỗ trợ trực tiếp bởi hệ điều hành. Bắt kì ứng dụng nào cũng có thể được lập trình để chạy đa luồng. Tất cả các thread bên trong ứng dụng được hỗ trợ trong một single process.

Kernel duy trì thông tin context cho toàn bộ process và cho các thread riêng lẻ trong process. Scheduling bởi Kernel được thực hiện trên cơ sở thread. Kernle thực hiện việc tạo thread, scheduling, và quản lí trên không gian Kernel. Những kernel thread thường tạo và quản lí chậm hơn user threads.

![Kernel threads](https://miro.medium.com/max/461/1*I_kV4ApQKtK4Lajh79tDAg.png)

Ưu điểm:
- Kernel có thể đồng thời schedule nhiều thread từ cùng một proces trên nhiều process.
- Nếu một thread trong một process bị chặn (blocked), Kernel có thể schedule một thread khác cuả cùng process.
- Các kernel routines có thể được đa luồng. 

Nhược điểm:
- Những kernel thread thường được tạo và quản lí lâu hơn user threads.
- Chuyển điều khiển từ một thread sang một thread khác trong cùng một process yêu cầu chuyển đổi chế độ sang Kernel.

### 3: Scheduling

Quá trình scheduling là trách nhiệm của process manager để xử lí loại bỏ các quá trình đang chạy khỏi CPU và lựa chọn một process khác trên một nền tảng chiến lược cụ thể.

Process scheduling là một phần thiết yếu của một hệ điều hành multiprogramming. Những hệ điều hành này cho phép nhiều hơn một process được load vào executable memory tại một thời điểm, và process được load chia sẻ CPU sử dụng time multiplexing (ghép kênh thời gian).

Hệ điều hành duy trì tất cả các Process Control Blocks (PCBs) trong **Process Scheduling Queues**. Hệ điều hành duy trì một queue tách biệt cho mỗi trạng thái process,và PCBs của tất cả các process trong cùng một trạng thái thực thi được đặt cùng một queue. Khi một trạng thái của proces thay đổi, PCB của nó cũng được gỡ ra khởi queue hiện tại và di chuyển sang một queue trạng thái mới.

Hệ điều hành duy trì những những process scheduling quan trọng sau:
- **Job queue**: queue này giữ tất cả các process trong hệ thống.
- **Ready queue**: queue này giữ một tập tất cả các process ở trong main memory, sẵn sàng và đợi để  thực thi. Một process mới luôn được đặt trong queue này.
- **Device queues**: những processes bị block do không có sẵn trong thiết bị I/O tạo thành trong queue này.
![Process Scheduling Queues](https://miro.medium.com/max/960/1*9m161tRz3WesprnldVeYhw.jpeg)

Hệ điều hành dùng các chính sách khác nhau để quản lí mỗi queue (FIFO, Round Robin, Priority, vv). Scheduler của hệ điều hành xác định cách chuyển process giữa ready và run queue chỉ có thể có một entry trên mỗi processor core trong hệ thống. Trong sơ đồ ở trên, nó được kết hợp với CPU.

2 mô hình trạng thái process tương ứng với trạng thái running và non-running:
- **Running**: Khi một process mới được tạo, nó vào hệ thống trong trạng thái running.

- **Not Running**: Những proces không chạy được giữ trong queue, chờ đến lượt được thực thi. Mỗi entry trong queue là một con trỏ đến một process cụ thể. Queue được implement bằng cách sử dụng danh sách liên kết. Công dụng của bộ điều phối (dispatcher) là như sau: Khi một process bị ngắt, process đó được chuyển vào waiting queue. Nếu process đó được hoàn thành hoặc bị hủy bỏ, process sẽ bị bỏ đi. Trong 2 trường hợp, dispatcher sau đó sẽ chọn một process từ queue để thực thi.

![Context Switch](https://miro.medium.com/max/297/1*4kcmdNBAQo-Hzo4G9L6g8Q.jpeg)

Một **context switch** (chuyển đổi context) là một cơ chế để lưu trữ và khôi phục trạng thái hoặc context của một CPU trong Process Control Block. Nó cho phép một quá trình thực thi process được tiếp tục từ cùng một điểm sau đó. Sử dụng công nghệ này, một context switcher cho phép nhiều process chia sẻ một single CPU. Context switching là một tính năng thiết yếu của một hệ điều hành multitasking.

Khi một scheduler chuyển CPU từ thực thi một process sang một process khác, trạng thái từ process hiện tại đang chạy được lưu trữ vào process control block. Sau đó, trạng thái của process tiếp theo được tải vào PCB của nó và được dùng để set PC, registers, vv. Tại thời điểm này, process thứ hai bắt đầu thực thi.

Những context switches là những tính toán chuyên sâu, vì trạng thái register và memory phải được lưu trữ và khôi phục. Để tránh thời lượng context switching, một vài hệ thống phần cứng sử dụng 2 hoặc nhiều bộ thanh ghi bộ xử lí (sets of processor registers).

Khi process được chuyển, thông tin sau được lưu trữ cho những lần dùng sau: Program Counter, Scheduling Information, Base và Limit Register Value, Currently Used Register, Changed State, I/O State Information, và Accounting Information.

### 4: Memory Management

Memory Management là một tính năng của hệ điều hành để xử lí và quản lí bộ nhớ chính (primary memory). Nó chuyển những process qua lại giữa main memory và disk trong quá trình thực thi.

Memory Management theo dõi từng vị trí bộ nhớ, bất kể là nó có được cấp phát cho process nào hay ko. Nó kiểm tra lượng bộ nhớ đã được cấp phát cho những processes. Nó quyết định process nào sẽ lấy bộ nhớ tại thời điểm nào. Và nó theo dõi bất cứ khi nào bộ nhớ được free hoặc không được cấp phát, và tương ứng cập nhật trạng thái.

![Memory Management](https://miro.medium.com/max/1393/1*Vy54VZkKuf4zbd7WjdS75Q.png)

Không gian địa chỉ của process là một tập các địa chỉ logic mà process tham chiếu trong code của nó. Ví dụ, khi địa chỉ 32 bit được dùng, những địa chỉ từ 0 đến 0x7fffffffm đó là 2^31 số có thể dùng, với tổng kích thước lí thuyết là 2 gigabytes.

Hệ điều hành quan tập việc mapping địa chỉ logic đến địa chỉ vật lí tại thời điểm cấp phát bộ nhớ cho chương trình. Có 3 loại địa chỉ được sử dụng trong chương trình trước và sau khi bộ nhớ được cấp phát:
- **Symbolic addresses**: Những địa chỉ được dùng trong source code. Tên biến, constants, và những instruction labels là những phần tử cơ bản của không gian symbolic address.

- **Relative addresses**: Tại thời điểm biên dịch, một compiler chuyển đổi symbolic addresses sang relative addresses.

- **Physical addresses**: Loader tạo những địa chỉ này tại thời điểm khi chương trình được load vào main memory.

Địa chỉ vật lí và địa chỉ ảo giống nhau ở address binding schemes trong compile-time và load-time. Những địa chỉ vật lí và địa chỉ ảo khác ở address binding schemes trong execution-time.

Tập hợp tất các các địa chỉ logic được tạo bởi một chương trình được gọi là **logical address space**. Tập hợp tất cả địa chỉ vật lí tương ứng với những địa chỉ logic này được gọi **physical address space**.

### 5: Inter-Process Communication

Có 2 loại process: độc lập (independent) và hợp tác (cooperating). Một process độc lập không bị ảnh hưởng khi một process khác thực thi, trong khi một process hợp tác có thể bị ảnh hưởng bởi việc thực thi các process khác.

Bạn có thể nghĩ là chắc những process độc lập này sẽ thực thi rất hiệu quả. Nhưng thực tế, có nhiều trường hợp khi bản chất một process kết hợp có thể được sử dụng để tăng tốc độ tính toán, tiện lợi, và chia module. Inter-process communication (IPC) là một cơ chế cho phép những process giao tiếp với nhau và đồng bộ hoạt động của chúng. Giao tiếp giữa các process có thể được xem như là một phương thức hợp tác giữa chúng.

Process có thể giao tiếp với nhau bằng 2 cách: Shared Memory và Message Parsing.

**Shared Memory Method**

Có 2 process là Producer và Consumer. Producer tạo ra các item và Consumer tiêu thụ các item đó. 2 process này chia sẻ chung một không gian hoặc vị trí bộ nhớ là "buffer", nơi mà item được sản xuất bởi Producer được lưu trữ và từ đó Consumer tiêu thụ item mà nó cần.

Có 2 phiên bản của vấn đề này: đầu tiên là vấn đề không giới hạn buffer, khi đó Producer có thể tiếp tục sản xuất item và không có giới hạn kích thước của buffer. Vấn đề thứ 2 là giới hạn buffer, khi mà Producer sản xuất một số lượng nhất định các item và sau đó nó bắt đầu chờ Consumer tiêu thụ chúng.

![Shared Memory](https://miro.medium.com/max/1024/1*IIsWsFK8f50jZleW3XS1Bg.png)

Với vấn đề giới hạn buffer, Producer và Consumer sẽ chia sẻ chung memory. Sau đó Producer sẽ bắt đầu sản xuất item. Nếu tổng số lượng item được sản xuất bằng với kích thước của buffer, Producer sẽ chờ đến khi chúng được tiêu thụ hết bởi Consumer.

Tương tự, Consumer đầu tiên kiểm tra xem thử item có chưa, nếu chưa thì Consumer sẽ đợi đến khi Producer sản xuất nó. Nếu có item ở đó thì Consumer sẽ tiêu thụ chúng.

**Message Parsing Method**

Trong method này, những process giao tiếp với nhau mà không cần bất kì shared memory nào. Nếu 2 process p1 và p2 muốn giao tiếp với nhau chúng sẽ tiến hành như sau:
- Thiết lập một liên kết giao tiếp (nếu một liên kết đã tồn tại, không cần thiết lập lại).
- Bắt đầu trao đổi message sử dụng basic primitives. Chúng ta cần ít nhất 2 primitives là: **send** (message, destination) hoặc **send** (message) và **receive** (message, host) hoặc **receive** (message).

![Big Picture](https://miro.medium.com/max/960/1*YhCCa1GchPowFuKKGm2b0Q.jpeg)

Kích thước message có thể cố định hoặc tùy biến. Nếu nó là kích thước cố định, thì Hệ điều hành sẽ được thiết kế dễ dàng hơn nhưng lại phức tạp cho lập trình viên. Nếu là kích thuớc tùy biến, thì nó sẽ dễ dàng cho ltv hơn nhưng lại thốn đối với người thiết kế hệ điều hành. Một chuẩn message có 2 phần: một **header** và một **body**.

**Header** được dùng để lưu trữ Message type, destination id, source id, message length, và thông tin điều khiển. Thông tin điều khiển bao gồm thông tin như là phải làm gì nếu hết dung lượng buffer, số thứ tự và mức độ ưu tiên của nó. Nói chung, message được gửi bằng FIFO style.

### 6: Input/Output Management

Một trong những việc quan trọng của Hệ điều hành là quản lí nhiều thiết bị input/output(I/O), bao gồm chuột, bàn phím, touch pad, disk drives, display adapters, USB devices, Bit-mapped screen, LED, Analog-to-digital converter, on/off switch, network connections, audio I/O, printers, v.v.

Cần có một hệ thống I/O để nhận yêu cầu I/O của ứng dụng và gửi nó đến thiết bị vật lí, sau đó nhận bất kì response gì trở về từ thiết bị và gửi nó đến ứng dụng. Những thiết bị I/O được chia làm 2 loại:
- **Block devices**: Một block device là một thiết bị với drivers giao tiếp bằng cách gửi toàn bộ khối dữ liệu. Ví dụ, hard disks, USB cameras, Disk-On-Key, vv.

- **Character Devices**: Character device là một thiết bị mà driver giao tiếp bằng cách gửi và nhận từng kí tự đơn (bytes, octets). Ví dụ, serial ports, parallel ports, sound cards, vv.

![I/O](https://miro.medium.com/max/600/1*Ynj0zqoSq5JpbdFfARgNSg.jpeg)

CPU phải có cách để gửi thông từ và đến I/O device. Có 3 cách tiếp cận để giao tiếp với CPU và Device.

1. **Special Instruction I/O**

Cách này dùng các hướng dẫn CPU được tạo riêng để điều khiển các thiết bị I/O. Những hướng dẫn này thường cho phép dữ liệu được gửi tới một thiết bị I/O và được đọc từ một thiết bị I/O.

2. **Memory-mapped I/O**

Khi dùng memory-mapped I/O, cùng một không gian địa chỉ được chia sẻ bởi bộ nhớ và những thiết bị I/O. Thiết bị được kết nối trực tiếp tới vị trí của main memory để thiết bị I/O có thể truyền khối dữ liệu từ/đến bộ nhớ mà không cần thông qua CPU.

![Memory-mapped I/O](https://miro.medium.com/max/393/1*PcMx51Qe8R29LaXMXnHWvQ.jpeg)

Khi dùng memory-mapped I/O, hệ điều hành cấp phát bộ đệm trong memory và thông báo với thiết bị I/O để sử dụng bộ đệm đó để gửi dữ liệu tới CPU. Thiết bị I/O hoạt động bất đồng bộ với CPU, và làm gián đoạn CPU khi hoàn thành.

Lợi ích của phương thức này là mọi hướng dẫn có thể truy cập bộ nhớ đều có thể được sử dụng để thao tác với thiết bị I/O. Memory-based I/O được dùng hầu hết cho các thiết bị I/O tốc độ cao như disks và giao tiếp interfaces.

3. **Direct memory access (DMA)**

Những thiết bị chậm như bàn phím sẽ tạo ra một sự gián đoạn tới main CPU sau khi mỗi byte được truyền đi. Nếu là một thiết bị nhanh, như disk, tạo ra một gián đoạn cho mỗi bute, hệ điều hành sẽ phải dành hầu hết thời gian để xử lí các gián đoạn. Do đó một máy thông thường sử dụng phần cứng truy cập bộ nhớ trực tiếp (DMA) để giảm thiểu chi phí.

DMA có nghĩa là cấp quyền cho module I/O để đọc và ghi vào memory mà không cần tham gia. DMA module tự điều khiển trao đổi dữ liệu giữa main memory và thiết bị I/O. CPU chỉ tham gia và đầu và cuối quá trình trao đổi và gián đoạn chỉ sau khi một khối dữ liệu được gửi hết.

DMA cần một phần cứng đặc biệt gọi DMA controller(DMAC) để quản lí dữ liệu chuyển đi và phân xử truy cập vào system bus. Những controller được lập trình với nguồn và đích là những con trỏ (nơi để đọc và viết dữ liệu), những bộ đếm để theo dõi số lượng bytes được truyền đi, và những cài đặt khác. Chúng bao gồm loại I/O và memory và những gián đoạn, và trạng thái cho những chu kì CPU.

### 7: Virtualization

Ảo hóa là một công nghệ cho phép bạn tạo nhiều môi trường giả lập hoặc những tài nguyên chuyên dụng cho một hệ thống phần cứng vật lí.

Phần mềm có tên là **hypervisor** kết nối trực tiếp tới phần cứng đó và cho phép bạn chia một hệ thành thành nhiều môi trường  tách biệt, phân biệt, và bảo mật được biết đến với tên là máy ảo (virtual machines VMs). Những máy ảo phụ thuộc vào khả năng của hypervisor để chia tài nguyên máy từ phần cứng và phân phối chúng thích hợp.

Thông thường thì máy tính vật lí được trang bị hypervisor được gọi là **host**, trong khi những máy ảo khác sử dụng tài nguyên của nó được gọi là **guest**. Những guests này sử dụng tài nguyên máy - như CPU, memory, và storage - dễ dàng. Operators có thể quản lí những thực thể ảo của CPU, memory, storage và những tài nguyên khác để những guests nhận được tài nguyên chúng cần.

Ý tưởng là tất cả các máy ảo liên quản được quản lí thông qua một bảng điều khiển quản lí ảo hóa trên nền web, để làm tăng tốc chúng. Ảo hóa cho phép bạn đưa ra mức độ xử lí, dung lượng lưu trữ, và bộ nhớ để cung cấp cho máy ảo, và những môi trường được bảo vệ tốt hơn do các máy ảo được tách biệt ra khỏi phần cứng hỗ trợ chúng và tách biệt với nhau.

Nói một cách đơn giản, ảo hóa tạo ra các môi trường và tài nguyên bạn cần từ những tài nguyên chưa được sử dụng.

Những loại ảo hóa:
1. **Data virtualization**: Dữ liệu được truyền khắp nơi có thể được tập hợp thành một nguồn duy nhất. Ảo hóa dữ liệu cho phép những công ty có thể coi dữ liệu là nguồn cung cấp động - cung cấp khả năng xử lí có thể kết hợp dữ liệu từ nhiều nguồn lại với nhau, dễ dàng cung cấp nguồn dữ liệu mới và chuyển đổi dữ liệu theo nhu cầu người dùng. Những công cụ ảo hóa dữ liệu cài đặt ở nhiều nguồn dữ liệu và cho phép chúng được coi như là một nguồn duy nhất. Chúng vận chuyển những dữ liệu cần thiết - từ mẫu yêu cầu - tại đúng thời điểm đến với bất kì ứng dụng hoặc người dùng nào.

2. **Desktop Virtualization**: Rất dễ bị nhầm lẫn với operating system virtualization - cho phép bạn triển khai nhiều hệ điều hành trên một máy tính vật lí - desktop virtualization cho phép một admin trung tâm (hoặc một công chuyện admin tự động) triển khai những mỗi trường giả lập như hàng trăm máy tính vật lí cùng lúc. Không giống như những môi trường desktop truyền thống được cài đặt, cấu hình, và cập nhật một cách vật lí trên mỗi máy tính, desktop virtualization cho phép những admin thực hiện cấu hình, cập nhật, kiểm tra bảo mật hàng loạt trên tất cả các máy ảo.

3. **Server Virtualization**: Những servers là những máy tính được thiết kế để xử lí lượng lớn những yêu cầu cụ thể thực sự tốt để những máy tính khác như laptops hay desktop có thể thực hiện những công việc khác. Ảo hóa một server cho phép nó có thể làm được nhiều  tính năng cụ thể hơn và liên quna đến việc phân vùng nó để các thành phần có thể được sử dụng để phục vụ cho nhiều chức năng.

4. **Operating System Virtualization**: Ảo hóa hệ điều hành xảy ra tại kernel - bộ quản lí tác vụ trung tâm của hệ điều hành. Nó là cách hữu ích để chạy Linux và Window song song. Doanh nghiệp cũng có thể đẩy hệ điều hành ảo vào máy tính, qua đó: (1) giảm phần lớn chi phí phần cứng, vì các máy tính không yêu cầu những khả năng vượt trội như vậy, (2) tăng bảo mật, vì tất cả các thực thể ảo có thể được quan sát và cô lập, và (3) giới hạn thời gian dành cho những dịch vụ IT như cập nhật phần mềm.

5. **Networks Functions Virtualization**: Ảo hóa chức năng mạng (NFV) chia những chức năng chính của mạng (như dịch vụ thư mục, chia sẻ file, và cầu hình IP) để chúng có thể được phân phối giữa các môi trường. Một khi những chức năng phần mềm độc lập với các máy vật lí chứa chúng, những tính năng cụ thể có thể được đóng gói với nhau vào một mạng mới và gán vào một môi trường. Ảo hóa mạng làm giảm số lượng thành phần vật lí - như switches, routers, servers, cables, và hubs - những thứ cần thiết để có thể tạo ra nhiều mạng độc lập, và nó đặc biệt phổ biến trong ngành công nghiệp viễn thông.

### 8 - Distributes File Systems

Những hệ thống tập tin phân tán là một ứng dụng dựa trên client/server cho phép client truy cập và xử lí dữ liệu trên server như là nó nằm trên chính máy tính của họ. Khi một user truy cập vào một file trên server, server sẽ gửi cho user một bản sao của file, nó được cached trên máy tính của user trong khi dữ liệu được xử lí và trả về cho server.

Ý tưởng là một hệ thống tập tin phân tán tổ chức các dịch vụ file và thư mục của những server riêng thành một thư mục global theo cái cách truy cập dữ liệu từ xa là không được đặt ở một vị trí cụ thể nhưng giống hệt nhau đối vs bất kì clients nào. Tất cả các file có thể được truy cập bởi tất cả user của hệ thống và tổ chức global file system được phân cấp và dựa trên thư mục.

![Distributed File System](https://miro.medium.com/max/1963/1*_PoxQY_lgEFwXsoxkpYrOw.png)

Vì nhiều hơn một client có thể truy cập đồng thời cùng một dữ liệu, server phải có một cơ chế tại chỗ (chằng hạn như việc duy trì thông tin về thời gian truy cập) để tổ chức các cập nhật để cho client luôn nhận được phiên bản mới nhất của dữ liệu, và dữ liệu không bị conflict. Hệ thống file phân tán thường sử dụng file hoặc database replication(nhân rộng) (phân phối bản sao của dữ liệu đến nhiều servers) để tránh việc truy cập không được dữ liệu.

Sun Microsystems’ Network File System (NFS), Novell NetWare, Microsoft’s Distributed File System, và IBM’s DFS là một vài ví dụ về hệ thống file phân tán.

### 9 - Distributed Shared Memory (DSM)
Bộ nhớ chia sẻ phân tán là một thành phần quản lí tài nguyên của một hệ điều hành phân tán thực hiện việc implement shared memory model trong những hệ thống phân tán không có shared memory vật lí. Shared memory cung cấp một không gian địa chỉ ảo được chia sẻ cho tất cả các máy trong hệ phân tán.

Trong DSM, dữ liệu được truy cập từ cùng một không gian được chia sẻ giống như cách mà bộ nhớ ảo được truy cập. Dữ liệu được di chuyển giữa secondary và main memory, và giữ distributed main memories của các nodes khác nhau. Quyền sở hữu các trang trong bộ nhớ bắt đầu ở một số trạng thái được xác định trước nhưng thay đổi trong quá trình hoạt động. Việc thay đổi quyền sở hữu diễn ra khi dữ liệu di chuyển từ một node này sang node khác bởi một quá trình truy cập cụ thể.

![Distributed shared memory](https://miro.medium.com/max/1058/1*UZAEpx8VAcEVr_AZGzrkpQ.jpeg)

Ưu điểm của distributed shared memory: 
- Ẩn đường đi của dữ liệu và cung cấp một abstraction đơn giản hơn để chia sẻ dữ liệu. Những lập trình viên không cần quan tâm đến việc truyền dữ liệu giữa các máy như khi sử dụng message passing model.

- Cho phép gửi đi các cấu trúc phức tạp như tham chiếu, đơn giản hóa các thuật toán phát triển của các ứng dụng phân tán.

- Tận dụng lợi thế của "locality of reference" (tham chiếu local) bằng việc di chuyển toàn bộ page chứa dữ liệu thay vị một phần dữ liệu.

- Rẻ hơn việc xây dựng một hệ thống đa nhân xử lí. Ý tưởng này có thể được thực hiện trên các phần cứng thông thường và không yêu cầu bất cứ thứ gì phức tạp để kết nối shared memory tới processors.

- Dung lượng bộ nhớ lớn hơn cho các chương trình, bằng cách kết hợp tất các bộ nhớ vật lí của các nodes. Bộ nhớ lớn này không phát sinh độ trễ khi hoán đổi đĩa như trong hệ thống phân tán truyền thống.

- Không giới hạn các node được sử dụng. Không giống như hệ thống multiprocessors mà main memory được truy cập thông qua common bus, do đó giới hạn kích thước của hệ thống multiprocessors.

- Chương trình được viết cho shared memory của multiprocessor có thể chạy được trên những hệ thống DSM.

Có 2 cách khác nhau để thông báo cho các node về ai là chủ sở hữu của page: invalidation và broadcast. Invalidation là một phương thức để vô hiệu hóa một page khi một vài process yêu cầu quyền write page đó và trở thành chủ sở hữu mới. Bằng cách này, lần tới, những process khác cố gắng read hoặc write tới một bản sao của trang mà nó nghĩ là có quyền, page sẽ không available và process sẽ phải yêu cầu lại để truy cập tới trang đó. Broadcasting sẽ tự động cập nhật tất cả các bản sao của một memory page khi một process write nó. Nó cũng được gọi là write-update. Phương thức này khó thực hiện hơn bởi vì giá trị mới cần được gửi đến thay vì một thông báo không hợp lệ.

### 10 - Cloud Computing

Càng ngày chúng ta càng thấy công nghệ càng chuyển dần sang cloud. Nó không chỉ là một mốt nhất thời - Sự dịch chuyển từ mô hình phần mềm truyền thống sang internet đã tăng đều trong 10 năm qua. Nhìn về tương lai, thập kỉ tiếp theo của cloud computing hứa hẹn những cách mới để cộng tác ở khắp mọi nơi, thông qua các thiết bị di động.

Vậy cloud computing là gì ? Bản chất, cloud computing là một loại outsourcing của các chương trình phần mềm. Sử dụng cloud computing, những người dùng có thể truy cập phần mềm và ứng dụng ở bất cứ đâu họ cần, vì nó được hosted ở một bên khác - trên "cloud". Điều này có nghĩa là họ không cần quan tâm về những thứ như storage, power, chỉ cần tận hưởng kết quả cuối cùng.

Những ứng dụng business truyền thống vẫn luôn rất phức tạp và đắt đỏ. Số lượng và sự đa dạng phần cứng để chạy chúng rất đáng lo ngại. Bạn cần một team chuyên gia để cài dặt, cấu hình, kiểm tra, chạy, bảo mật và cập nhật chúng. Khi bạn nhân rộng effort này lên theo hàng trăm ứng dụng, không dễ để thấy vì sao những công ty với đội ngũ IT tốt nhất không có được ứng dụng họ cần. Những doanh nghiệp vừa và nhỏ thì không có cơ hội.

Với cloud computing, bạn có thể loại bỏ những đau đầu đến từ việc lưu trữ dữ liệu của bạn, bởi vì bạn không quản lí phần cứng và phần mềm - nó trở thành trách nhiệm cuả một nhà cung cấp có kinh nghiệm như Salesforce và AWS. Cơ sơ hạ tầng chia sẻ nghĩa là nó hoạt động như một tiện ích: bạn trả tiền cho cái bạn cần, nâng cấp là tự động, và việc scaling up hoặc down là rất đơn giản.

Những ứng dụng cloud-based có thể up và chạy trong nhiều ngày hoặc nhiều tuần, và chúng tốn ít chi phí hơn. Với một cloud app, bạn chỉ cần mở một trình duyệt, đăng nhập, tùy chỉnh ứng dụng, và bắt đầu dùng nó. Các doanh nghiệp đang chạy tất cả các ứng dụng trên cloud như quản lí quan hệ khách hàng (CRM), HR, accounting và hơn nữa.

Khi cloud computing phát triển phổ biến, hàng ngàn công ty dễ dàng đổi thương hiệu của họ từ sản phẩm và dịch vụ non-cloud sang "cloud computing". Luôn đào sâu hơn khi đánh giá các dịch vụ cloud và lưu ý nếu bạn phải mua và quản lí phần cứng và phần mềm, thì cái bạn đang thấy không hẳn là cloud computing mà là một false cloud.

----------------------


































