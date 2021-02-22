# The Twelve-factor App - Logs

- Xem log như là những event streams
- Log cung cấp khả năng xem lại các hoạt động của ứng dụng đang chạy. Trong môi trường server chúng thường được viết vào một file trên disk (một "logfile"); nhưng đó chỉ là một định dạng output thôi.
- Log là những luồng các sự kiện tổng hợp, theo trình tự thời gian được thu tập từ những luồng output của tất cả các process đang chạy và những service hỗ trợ. Log nguyên bản thường là một định dạng text với một sự kiện ứng với một dòng (dù dấu vết của các exception thì thường dài nhiều dòng). Log không có bắt đầu hay kết thúc cố định, nhưng nó sẽ chạy liên tục miễn là ứng dụng còn hoạt động.
- Một ứng dụng tuân theo 12 nguyên tắc không bao giờ quan tâm đến việc định tuyến hay lưu trữ output stream của nó. Nó không nên cố gắng ghi hay quản lí logfiles. Thay vào đó, mỗi process đang chạy ghi vào luồng sự kiện của nó, không bộ đệm, vào `stdout`. Trong môi trường local, developer sẽ xem luồng này trên terminal để nắm bắt hành vi của ứng dụng.
- Trong những bản deploy staging hay prod thì mỗi luồng của process sẽ được lưu trữ bởi môi trường thực thi, thu thập cùng với tất cả các luồng của ứng dụng, và định hướng đến một hoặc nhiều điểm cuối để xem và lưu trữ lâu dài. Những điểm lưu trữ này không tồn tại trong config của ứng dụng, thay vào đó được quản lí hoàn toàn bằng môi trường thực thi. Những mã nguồn mở điều hướng log (như Logplex và Fluentd) được sinh ra cho mục đích này.
- Luồng sự kiện của một ứng dụng có thể được điều hướng đến 1 file, hoặc theo dõi realtime trên terminal. Đáng kể nhất là luồng đó có thẻ được gửi đến một hệ thống log indexing và phân tích như Splunk, hoặc là một hệ thống data warehousing như Hadoop/Hive. Những hệ thống này cùng cấp sức mạnh và sự linh hoạt lớn cho việc nắm bắt kĩ càng hoạt động của ứng dụng theo thời gian, bao gồm:
    - Tìm một sự kiện cụ thể trong quá khứ
    - Vẽ đồ thị quy mô lớn về các xu hướng (như là số request mỗi phút).
    - Chủ động cảnh báo dựa trên kinh nghiệm của người dùng định nghĩa (như là việc cảnh báo khi số lượng lỗi mỗi phút đạt ngưỡng).
