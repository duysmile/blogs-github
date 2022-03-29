# Linux process management

Reference: https://www.tecmint.com/wp-content/uploads/2017/03/ProcessState.png

Trong bài viết này, chúng ta sẽ tìm hiểu cơ bản về process và một chút về quản lí process trong Linux sử dụng command.

Một process đại diện cho một chương trình đang thực thi, nó chạy một thực thể của chương trình. Nó được tạo thành từ các hướng dẫn (instruction) của chương trình, đọc dữ liệu từ file, những chương trình khác hoặc từ input của người dùng hệ thống.

### Những loại process

Có 2 loại cơ bản của process trong Linux:
- Foreground processes (còn được gọi là các interactive processes) - chúng được tạo ra và điều khiển thông qua một terminal session. Nói cách khác, phải có một người dùng kết nối tới hệ thống để bắt đầu những processes đó, chúng không thể tự khởi chạy như một phần của các functions/services hệ thống.

- Background processes (còn được gọi là non-interactive/automatic processes) - là những processes không kết nối đến một terminal, chúng không cần các user inputs.

### Daemons là gì?

Là những loại background processes đặc biệt được khởi chạy khi khởi động hệ thống và chạy mãi như một services; chúng không tắt (die). Chúng được khởi chạy như những system tasks (run as services), tự phát. Tuy nhiên, chúng có thể được điều khiển bởi một user thông qua init process.

![Process State](https://www.tecmint.com/wp-content/uploads/2017/03/ProcessState.png)

### Tạo một process trong Linux
Một process mới thường được tạo khi một process tạo ra một bản sao chính xác của chính nó trong bộ nhớ. Process con sẽ có cùng một trường với cha của nó, chỉ có process ID là khác nhau.

Có 2 cách được dùng để tạo một process mới trong Linux:
- Sử dụng System() Function - phương thức này đơn giản, tuy nhiên không hiệu quả và có thể dẫn đến các lỗ hổng bảo mật.

- Sử dụng fork() và exec() Function - công nghệ này nâng cao một chút nhưng chúng đem lại sự linh hoạt, tốc độ, cùng với tính bảo mật.

### Làm sao để Linux nhận diện được các processes?
Bởi vì Linux là một hệ thống nhiều người dùng, có nghĩa là nhiều người dùng có thể chạy nhiều chương trình trên hệ thống, mỗi thực thể đang chạy của chương trình phải được định danh duy nhất bởi kernel(nhân).

Và một chương tình được định danh bởi process ID(PID) và process ID cha (PPID), do đó process có thể được phân loại là:
- Parent processes: là những process tạo ra những process khác trong suốt run-time.

- Child processes: là những process được tạo bởi những processes khác trong suốt run-time.

### Init Process
Init process là mẹ (cha) của tất cả các process trong hệ thống, nó là chương trình đầu tiên được thực thi khi Linux system boots up; nó quản lí tất cả những process khác trên hệ thống. Nó được bởi tạo bởi chính kernel, do đó về nguyên tắc thì nó không có một parent process nào.

Init process luôn có process ID là 1. Nó hoạt động như một cha mẹ nuôi cho tất cả các processes "mồ côi".

Bạn có thể dùng lệnh `pidof` để tìm ID của một process:
```shell
# pidof systemd
# pidof top
# pidof httpd
```

![Command line](https://www.tecmint.com/wp-content/uploads/2017/03/Find-Linux-Process-ID.png)

Để tìm process ID và process ID cha của shell hiện tại, dùng lệnh:
```shell
$ echo $$
$ echo $PPID
```

![Command line](https://www.tecmint.com/wp-content/uploads/2017/03/Find-Linux-Parent-Process-ID.png)

### Khởi chạy một process trong Linux
Một khi bạn chạy một lệnh hay một chương trình (ví dụ như cloudcmd - CloudCommander), nó sẽ khởi chạy một process trong hệ thống. Bạn có thể khởi chạy một foreground (interactive) process như dưới đây, nó sẽ được kết nối đến terminal và người dùng có thể nhập input:

```shell
# cloudcmd
```

![Terminal](https://www.tecmint.com/wp-content/uploads/2017/03/Start-Linux-Interactive-Process.png)

### Linux background jobs
Để bắt đầu một process ở background(non-interactive), sử dụng kí tự `&`, thì process sẽ không đọc input từ một user cho đến khi đưa nó về lại foreground.
```shell
# cloudcmd &
# jobs
```

![Result](https://www.tecmint.com/wp-content/uploads/2017/03/Start-Linux-Process-in-Background.png)

Bạn cũng có thể sử dụng `Ctrl + Z` để gửi process đến background, nó sẽ gửi một tín hiệu **SIGSTOP** đến process, do đó sẽ dừng hoạt động của process đó và đưa nó vào trạng thái idle:
```shell
# tar -cf backup.tar /backups/* #press Ctrl+Z
# jobs
```

Để tiếp tục chạy lệnh đã bị ngưng lại ở trên trong background, sử dụng lệnh `bg`:
```shell
# bg
```

Để đưa một process ở background trở lại foreground , sử dụng lệnh `fg` cùng với jobs ID như sau:
```shell
# jobs
# fg %1
```

![Result](https://www.tecmint.com/wp-content/uploads/2017/03/Linux-Background-Process-Jobs.png)

### Những trạng thái của một process trong Linux
Trong suốt quá trình thực thi, một process thay đổi từ trạng thái này sang trạng thái khác phụ thuộc vào môi trường và hoàn cảnh của nó. Trong Linux, một process có những trạng thái sau:
- Running - tại đây nó hoặc là đang chạy (nó là process hiện tại trong hệ thống) hoặc nó sẵn sàng để chạy (nó đang đợi được gán cho một trong những CPUs).

- Waiting - ở trạng thái này, một process đang đợi một sự kiện xảy ra hoặc đợi một tài nguyên hệ thống. Ngoài ra, kernel cũng phân biệt 2 loại waiting processes: interruptible waiting process - có thể bị ngắt bởi tín hiệu và uninterruptible waiting processes - đang chờ trực tiếp trên điều kiện phần cứng và không thể bị ngắt bởi bất kì sự kiện/tín hiệu nào.

- Stopped - ở trạng thái này thì một process đã dừng, thường là khi nhận một tín hiệu. Ví dụ như khi một process đang được debug.

- Zombie - tại trạng thái này, một process đã chết, nó bị dừng lại nhưng vẫn có một entry trong process table.

### Làm sao để xem các process đang active trên Linux
Có một vài công cụ Linux phục vụ việc xem/liệt kê những processes đang chạy trên hệ thống, 2 lệnh thường dùng cho việc này là `ps` và `top`.
1. ps Command
Nó hiển thị thông tin về các active proceses trên hệ thống như dưới đây:
```shell
# ps
# ps -e | head
```

![ps](https://www.tecmint.com/wp-content/uploads/2017/03/ps-command.png)

2. top - System monitoring tool
top là một công cụ rất mạnh mẽ để giúp bạn theo dõi real-time trên một hệ thống đang chạy.
```shell
# top
```

![top](https://www.tecmint.com/wp-content/uploads/2017/03/top-command.png)

3. glances - System monitoring tool
`glances` là một công cụ giám sát hệ thống tương đối mới với nhiều tính năng nâng cao:
```shell
# glances
```

![glances](https://www.tecmint.com/wp-content/uploads/2017/03/glances.png)

### Làm sao để điều khiển processes trong Linux
Linux cũng có một vài lệnh để điều khiển process như kill, pkill, pgrep và killall, dưới đây là một vài ví dụ cơ bản về cách sử dụng chúng:
```shell
$ pgrep -u techmint top
$ kill 2308
$ pgrep -u techmint glances
$ pkill glances
```

![controling process](https://www.tecmint.com/wp-content/uploads/2017/03/Control-Linux-Processes.png)

### Gửi tín hiệu đến processes
Cách cơ bản để điều khiển process là gửi tín hiệu đến chúng. Có nhiều tín hiệu mà bạn có thể gửi đến một process, để xem hết các loại này thì dùng lệnh sau:
```shell
# kill -l
```

![kill list](https://www.tecmint.com/wp-content/uploads/2017/03/list-all-signals.png)

Để gửi một tín hiệu đến một process, sử dụng lệnh `kill`, `pkill` hoặc `pgrep` như đã mô tả ở trên. Nhưng những chương trình chỉ có thể phản hồi lại những tín hiệu nếu chúng được lập trình để nhận diện được các tín hiệu đó.

Và hầu hết các tín hiệu được dùng nội bộ bởi hệ thống, hoặc cho lập trình viên khi họ viết code. Những tín hiệu sau khá hữu ích cho người dùng hệ thống:
- SIGHUP 1 - gửi đến process khi terminal kiểm soát nó đóng lại.

- SIGINT 2 - gửi đến process bởi terminal kiểm soát nó khi một người dùng ngắt process bằng tổ hợp phím `Ctrl+C`

- SIGQUIT 3 - gửi đến process nếu người dùng gửi một tín hiệu thoát `Crtl+D`

- SIGKILL 9 - tín hiệu này lập tức terminates(kill) một process và process sẽ không thực hiện bất kì hành động dọn dẹp nào luôn.

- SIGTERM 15 - đây là một tín hiệu chấm dứt chương trình (lệnh `kill` sẽ gửi tín hiệu này mặc định)

- SIGTSIP 20 - gửi đến một process bởi terminal điều khiển nó để yêu cầu nó dùng lại (terminal stop) khi người dùng nhấn `Ctrl+Z`

Những lệnh kill dưới đây là ví dụ để kill ứng dụng Firefox sử dụng PID của nó sau khi nó freezes:
```shell
$ pidof firefox
$ kill -9 2687
OR
$ kill -KILL 2687
OR
$ kill -SIGKILL 2687
```

Để kill một ứng dụng bằng tên, dùng pkill hoặc killall như sau:
```shell
$ pkill firefox
$ killall firefox
```

### Thay đổi độ ưu tiên process trong Linux
Trong hệ thống Linux, tất cả các active process có một ưu tiên và một giá trị nice nhất định. Những process với ưu tiên cao hơn sẽ thường lấy nhiều CPU time hơn những process ưu tiên thấp.

Tuy nhiên, một người dùng hệ thống với quyền root có thể thay đổi nó với lệnh nice và renice.

Từ output của lệnh top thì cột NI hiển thị giá trị nice của process.
```shell
$ top
```

![top](https://www.tecmint.com/wp-content/uploads/2017/03/top-command.png)

Dùng lệnh `nice` để đặt giá trị nice cho một process. Ghi nhớ rằng người dùng bình thường có thể đặt giá trị nice từ giá trị từ 0 đến 20 cho những process của họ. Chỉ có root user mới có thể sử dụng giá trị âm cho nice.

Để `renice` độ ưu tiên cho một process, sử dụng `renice` như sau:
```shell
$ renice +8 2687
$ renice +8 2103
```

----------------


Reference: https://viblo.asia/p/process-may-tinh-va-nhung-dieu-co-the-chua-biet-phan-i-4P856A9alY3?fbclid=IwAR16JM7Xi7E8vtfwXFUBkvsvfenIOsDWAjdWP7s2BYV1Mh0bm9VvBnDWOCI
Một process sẽ bao gồm các thành phần sau:
- Stack là nơi để chứa các dữ liệu tạm thời ví dụ như params của method/function, local variables (biến cục bộ), địa chỉ trả về
- Heap là bộ nhớ được cấp phát động cho Process trong thời gian chạy (runtime)
- Text là nơi chứa thông tin các hoạt động hiện tại thể hiện bằng giá trị của bộ đếm chương trình (Program Counter) và nội dung các thanh ghi của bộ xử lý (Processor's Registers)
- Data là nơi chứa các biến toàn cục (Global variable) và biến tĩnh (Static variable)

1 tiến trình bao gồm nhiều thread xử lý thì việc xử lý các thread trong 1 Core(CPU) là concurrent không phải parallel. Việc xử lý parallel sẽ xảy ra khi các thread được phân bổ ra nhiều Core và việc xử lý này không dùng chung resource giữa các thread và ngược lại.
Link: http://tutorials.jenkov.com/java-concurrency/concurrency-vs-parallelism.html#:~:text=It is possible to have,CPUs are executed in parallel.

---------------



