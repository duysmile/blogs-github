# 20 câu lệnh để theo dõi hiệu năng trên Linux

Nguồn: https://www.tecmint.com/command-line-tools-to-monitor-linux-performance/

Thực sự thì việc monitor và debug những vấn đề về hiệu năng trên Linux mỗi ngày là việc rất khó khăn cho mỗi System hoặc Network administrator. Vì vậy sau đây là 20 công cụ monitoring bằng command line hữu ích cho bất kì một Linux/Unix System Administrator.

1. **Top - Linux Process Monitoring**
Lệnh `top` là một chương trình theo dõi hiệu năng được dùng thường xuyên bởi các nhà quản trị hệ thống để theo dõi hiệu năng Linux và nó có ở trên hầu hết các hệ điều hành nhân Linux/Unix. `top` dùng để hiển thị tất cả các process đang chạy và active theo thời gian thực trong một danh sách có thứ tự và được cập nhật thường xuyên. Nó thể hiện CPU usage, Memory usage, Swap Memory, Cache Size, Bufffer Size, Process ID, User, Command và nhiều thứ nữa. Nó cũng cho thấy việc sử dụng bộ nhớ và cpu cao của những process đang chạy. Lệnh `top` rất hữu ích cho người quản trị hệ thống để theo dõi và đưa ra những hành động đúng đắn khi cần thiết.
> \# top

![top](https://www.tecmint.com/wp-content/uploads/2012/08/Top-Command.jpg)

2. **VmStat - Virtual Memory Statistics**
Lệnh `VmStat` trong Linux được dùng để hiển thị thống kê về bộ nhớ ảo (virtual memory), kernel threads, disks, system processes, I/O blocks, interrupts, CPU activity và nhiều thứ khác. Mặc định thì lệnh vmstat không có sẵn trên những hệ thống Linux mà bạn cần phải cài một package gọi là `sysstat` có bao gồm lệnh vmstat.
> \# vmstat

3. **Lsof - list open files**
Lệnh `lsof` được dùng trong nhiều hệ thống nhân Linux/Unix để hiển thị tất cả các open files và processes. Những open files bao gồm dish files, network sockets, pipes, devices và processes. Một trong những lí do chính để sử dụng lệnh nàu là khi disk không thể bị unmounted và hiển thị lỗi và file đó đang được mở hoặc đang sử dụng. Với câu lệnh này bạn có thể dễ dàng biết được file nào đang được dùng.

> \# lsof

4. **Tcpdump - Network Packet Analyzer**
`Tcpdump` một trong những câu lệnh được sử dụng rộng rãi để phân tích và chặn bắt network packets. Nó dùng để lọc các gói tin TCP/IP nhận được và gửi đi trên một giao diện cụ thể thông qua mạng. Nó cũng cung cấp một lựa chọn để lưu những gói tin bắt được trong một file để phân tích sau này. Tcpdump có trên hầu hết các bản Linux phổ biến.

> \# tcpdump -i eth0

5. **Netstat - network statistics**
`Netstat` là công cụ để theo dõi incoming và outgoing network packets, thống kê và giao diện thống kê. Đây là một công cụ rất hữu ích cho mọi nhà quản trị hệ thống để theo dõi hiệu năng mạng và giải quyết các vấn đề liên quan đến mạng.

> \# netstat -a | more

6. **Htop - Linux process monitoring**
`Htop` là một công cụ nâng cao dùng để tương tác và theo dõi theo thời gian thực các tiến trình trên Linux. Nó tương tự như lệnh `top` nhưng có thêm vài tính năng hơn như giao diện người dùng thân thiện để quản lí process, shortcut keys, góc nhìn theo chiều ngang và chiều dọc của mỗi process, ... Htop là một công cụ bên thứ ba và không có sẵn trên những hệ thống Lĩ, bạn cần phải cài đặt nó.

> \# htop

7. **Iotop - Monitor Linux Disk I/O**
`iotop` cũng khá tương tự với `top` và `htop`, nhưng nó có chức năng để giám sát và hiện thị Disk I/O và processes theo thời gian thực. Công cụ này rất hữu ích để tìm kiếm chính xác process và những process sử dụng nhiều việc đọc ghi đĩa.

> \# iotop

![iotop](https://www.tecmint.com/wp-content/uploads/2012/07/iotop-Screen.jpg)

8. **Iostat - Input/Output Statistics**
`iostat` là một công cụ đơn giản dùng để thu thập và hiển thị thống kê thiết bị lưu trữ đầu vào đầu ra. Công cụ này thường dùng để theo dõi những vấn đề hiệu năng của các thiết bị lưu trữ bao gồm devices, local disks, remote disks như NFS.
> \# iostat

9. **IPTraf - Realtime IP LAN monitoring**
`IPTraf` là một công cụ console mã nguồn mở để theo dõi network (IP LAN) theo thời gian thực cho Linux. Nó thu thập nhiều thông tin như IP traffic monitor được gửi qua mạng, bao gồm thông tin TCP flag, ICMP details, những TCP/UDP traffic bị sự cố, TCP connection packet và byte counts. Nó cũng thu thập thông tin chung và detaled interface statistics của TCP, UDP, IP, ICMP, non-IP, IP checksum errors, interface activity, vv.

10. **Psacct hoặc Acct - Monitor User Activity**
`psacct` hoặc `acct` rất hữu ích để theo dõi hoạt động của mỗi user trên hệ thống. Cả những daemons chạy background và theo dõi sát sao hoạt động chung của mỗi user trên hệ thống và cả những tài nguyên đang được họ sử dụng.

Những công cụ này dùng để theo dõi hoạt động người dùng như họ đang làm gì, những lệnh họ gặp vấn đề, lượng tài nguyên sử dụng, và thời gian active của họ trên hệ thống.




































