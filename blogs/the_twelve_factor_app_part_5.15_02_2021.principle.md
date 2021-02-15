# The Twelve-factor App - Build, release, run
5. Build, release, run

- Phân biệt kĩ càng giai đoạn build và run
- Một codebase được chuyển sang bản deploy khi trải qua 3 giai đoạn sau:
    - Giai đoạn build là phần chuyển từ code repo sang một gói thực thi (executable bundle) còn gọi là bản build. Sử dụng phiên bản của code tại một commit nhất định trong quá trình phát triển, giai đoạn build là giai đoạn cài đoạn các vendor dependencies và biên dịch các file nhị phân và assets.
    - Giai đoạn release sử dụng bản build được sinh ra ở giai đoạn build và kết hợp nó với những config của bản deploy hiện tại. Kết quả của giai đoạn này là là bản build và config sẵn sàng để có thể thực thi ngay lập tức trên môi trường thực thi.
    - Giai đoạn run (còn gọi là `runtime`) chạy ứng dụng trên môi trường thực thi, bằng cách khởi chạy một tập các process của ứng dụng dựa trên bản release đã chọn.
- Ứng dụng tuân theo 12 nguyên tắc phân biệt rõ ràng từng giai đoạn build, release, run. Ví dụ, không thể thay đổi code ở runtime vì không có cách nào truyền những thay đổi đó đến giai đoạn build được.

![https://12factor.net/images/release.png](https://12factor.net/images/release.png)

- Những công cụ deploy thường cung cấp cả các công cụ quản lí các bản release, đáng chú ý là khả năng roll back lại bản release trước đó.
- Mỗi bản release luôn có một ID release duy nhất, như là timestamp của bản release đó hoặc là một số tăng dần. Những bản release tạo thành một chuỗi liên tục và không thể thay đổi sau khi tạo ra.
- Những bản build được tạo ra bởi các nhà phát triển ứng dụng bất cứ khi nào code mới được deploy. Ngược lại quá trình runtime có thể được tự hoạt động trong các trường hợp như server reboot, hoặc là process bị crash và phải khởi động lại bởi process manager. Do đó quá trình run nên càng ít công đoạn càng tốt, vì những vấn đề ngăn ko cho ứng dụng chạy có thể khiển nó bị hư hại khi không có developer ở đó hỗ trợ. Giai đoạn build thì có thể phức tạp hơn vì lỗi luôn luôn trong tầm mắt của developer chịu trách nhiệm đưa lên deploy.
