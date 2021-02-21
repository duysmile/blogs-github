# The Twelve-factor App - Dev/prod parity

- Giữ cho các môi trường development, staging và production giống nhau nhất có thể.
- Trong lịch sử, đã có những khoảng cách đáng kể giữa môi trường development (một developer thực hiện các chỉnh sửa trực tiếp đối với bản deploy nội bộ của ứng dụng) và môi trường production (bản deploy đang chạy được truy cập bởi người dùng). Khoảng cách này thể hiện trong 3 phần:
    - **Khoảng cách thời gian**: Một developer có thể làm việc nhiều ngày, tuần hay thậm chí nhiều tháng để đưa lên production.
    - **Khoảng cách về con người**: Developer viết code, kĩ sư vận hành thì deploy.
    - **Khoảng cách công cụ:** Developer có thể sử dụng stack như là Nginx, SQLite và OS X, trong khi đó thì ở môi trường production đang sử dụng Apache, MySQL và Linux.
    - Ứng dụng tuân thủ 12 nguyên tắc được thiết kế để việc triển khai liên tục (continuous deployment) giữ cho khoảng cách giữa môi trường development và môi trường production là nhỏ nhất. Cùng xem mô tả dưới đây:
        - Làm cho khoảng cách thời gian nhỏ lại: một developer có thể việt code và có bản deploy chỉ vài giờ hay thậm chí vài phút sau đó.
        - Làm cho khoảng cách về con người nhỏ lại: developer viết code tham gia chặt chẽ vào việc triển khai và theo dõi hành vi của nó trên production.
        - Làm cho khoảng cách công cụ nhỏ lại: giữ môi trường development và môi trường production giống nhau nhất có thể.
    - Tổng kết lại trong bản sau:

    [Summary](https://www.notion.so/f8d3d2624f8e494188e7af7ebcb73395)

    - Những service hỗ trợ, như là database của ứng dụng, hệ thống queueing, hoặc cache, là nơi mà tính ngang bằng dev/prod (dev/prod parity) rất quan trọng. Nhiều ngôn ngữ yêu cầu thư viện tương ứng để truy cập vào các service hỗ trợ, bao gồm cả những *adapters* cho những loại service khác nhau. Một vài ví dụ:

    [Example](https://www.notion.so/0e2b3380c79b4127ba79e33abc1608d1)

    - Developer đôi khi thích thú với việc sử dụng một service hỗ trợ nhẹ ở môi trường local, trong khi những service hỗ trợ khác mạnh mẽ và an toàn hơn được sử dụng trong môi trường prod. Ví dụ SQLite ở local và PostgreSQL trong môi trường prod, hoặc sử dụng trực tiếp bộ nhớ đệm cho development và Memcached cho prod.
    - Ứng dụng tuân thủ 12 nguyên tắc không cho phép sử dụng service hỗ trợ khác nhau giữa môi trường development và prod, mặc dù các adapter về lí thuyết có thể trừu tượng bất kì sự khác nhau nào giữa các service. Sự khác biệt giữa các service hỗ trợ làm các điểm không tương thích nhỏ dần tăng lên, dẫn đến code hoạt động và pass test ở môi trường development hoặc staging nhưng fail ở prod. Những kiểu lỗi này tạo ra xung đột làm mất tác dụng của việc triển khai liên tục (CD). Cái giá phải trả cho xung đột này và ảnh hưởng của việc này thường rất tốn kém trong quá trình phát triển ứng dụng.
    - Những service hỗ trợ nhẹ thường không được ưu tiên như những service tương tự. Các service hỗ trợ hiện đại như Memcached, PostgreSQL, hay RabbitMQ không khó để cài đặt và chạy nhờ có các hệ thống packagin hiện đại như Homebrew hay apt-get. Ngoài ra thì các công cụ cung cấp khai báo (declarative provisioning tools) như Chef hay Puppet kết hợp với các môi trường ảo hóa nhẹ như Docker hay Vagrant cho phép developer chạy môi trường local gần như giống hệt với production. Chi phí để cài đặt và sử dụng những hệ thống này thấp hơn nhiều so với lợi ích mà nó đem lại.
    - Adapter cho những service khác nhau vẫn rất hữu dụng, bởi vì chúng làm cho việc thay đổi sang một service hỗ trợ khác đỡ đau khổ hơn. Nhưng tất cả những bản deploy của ứng dụng (môi trường development, staging hay prod) nên dùng cùng loại và phiên bản của các service hỗ trợ.
