# Linux process management

Reference: https://www.tecmint.com/wp-content/uploads/2017/03/ProcessState.png

Trong bài viết này, chúng ta sẽ tìm hiểu cơ bản về process và một chút về quản lí process trong Linux sử dụng command.

Một process đại diện cho một chương trình đang thực thi, nó chạy một thực thể của chương trình. Nó được tạo thành từ các hướng dẫn (instruction) của chương trình, đọc dữ liệu từ file, những chương trình khác hoặc từ input của người dùng hệ thống.

### Những loại process

Có 2 loại cơ bản của process trong Linux:
- Foreground processes (còn được gọi là các interactive processes) - chúng được tạo ra và điều khiển thông qua một terminal session. Nói cách khác, phải có một người dùng kết nối tới hệ thống để bắt đầu những processes đó, chúng không thể tự khởi chạy như một phần của các functions/services hệ thống.

- Background processes (còn được gọi là non-interactive/automatic processes) - là những processes không kết nối đến một terminal, chúng không cần các user inputs.

### Daemons là gì?

Là những loại background processes đặc biệt được khởi chạy khi khởi động hệ thống và chạy mãi như một services; chúng không tắt (die). Chúng được khởi chạy như những system tasks (run as services), tự phát. Tuy nhiên, chúng có thể được điều khiển bởi một user thông quan init process.

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











































