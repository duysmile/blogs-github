# Sidecar

Reference:

- [https://comdy.vn/design-pattern/cac-design-pattern-software-architecture-hien-dai/](https://comdy.vn/design-pattern/cac-design-pattern-software-architecture-hien-dai/)
- [https://docs.microsoft.com/en-us/azure/architecture/patterns/sidecar](https://docs.microsoft.com/en-us/azure/architecture/patterns/sidecar)

Deploy những thành phần của ứng dụng sang một process hoặc một container để đảm bảo tính độc lập (isolation) và tính bao đóng (encapsulation). Pattern này cũng cho phép ứng dụng chứa những thành phần hoặc công nghệ không đồng nhất.

Pattern này gọi là `Sidecar` vì nó giống như là một sidecar (như kiểu cái thùng gắn vô bên cạnh xe máy á mn) của một chiếc moto (có thể xem ảnh dưới để dễ hình dung nha)

![https://forchino.com/wp-content/uploads/2018/08/The-Sidecar-Tour-1-3.jpg](https://forchino.com/wp-content/uploads/2018/08/The-Sidecar-Tour-1-3.jpg)

Trong pattern này, sidecar được gắn vào một ứng dụng cha và cung cấp các tính năng hỗ trợ cho ứng dụng. Sidecar cũng cùng vòng đời với ứng dụng cha, được tạo ra và gỡ bỏ cùng với ứng dụng cha.

Vấn đề đặt ra:

- Những ứng dụng và services thường yêu cầu những tính năng liên quan, như là monitoring, logging, configuration, và những dịch vụ network. Những thành phần này có thể được implemented như một thành phần hay service riêng biệt.
- Nếu chúng được tích hợp chặt chẽ vào ứng dụng thì chúng có thể chạy trên cùng một process với ứng dụng, sử dụng hiệu quả tài nguyên hơn. Tuy nhiên, điều này cũng có nghĩa là chúng không được độc lập với nhau, nếu một trong những thành phần bị sự cố thì có thể ảnh hưởng đến những phần khác của ứng dụng. Và chúng thường cũng cần implement cùng ngôn ngữ với ứng dụng cha. Kết quả là những thành phần này và ứng dụng bị phụ thuộc chặt chẽ với nhau.
- Nếu ứng dụng được phân tách thành các services, thì mỗi service có thể được xây dựng bằng những ngôn ngữ và công nghệ khác nhau. Bên cạnh việc đem lại tính linh hoạt thì việc này có nghĩa là mỗi thành phần phải có những dependencies của nó cũng như cá thư viện cần thiết của mỗi ngôn ngữ để truy cập vào nền tảng cơ bản và bất kì những tài nguyên dùng chung nào với ứng dụng cha. Thêm vào đó, triển khai những tính năng như là những services riêng biệt có thể tăng độ trễ cho ứng dụng. Quản lí code và các dependencies cho những ngôn ngữ cụ thể có thể tăng tính phức tạp đặc biệt là hosting, deployment hay management.

**Solution**

- Cùng đặt lại tập các nhiệm vụ trên với ứng dụng chính, nhưng đặt chúng bên trong process hoặc container của riêng chúng, cung cấp giao diện đồng nhất cho các nền service đa ngôn ngữ.

![https://docs.microsoft.com/en-us/azure/architecture/patterns/_images/sidecar.png](https://docs.microsoft.com/en-us/azure/architecture/patterns/_images/sidecar.png)

- Sidecar service không cần thiết phải là một phần của ứng dụng mà chỉ cần kết nối tới ứng dụng. Nó đi đến bất cứ đâu mà ứng dụng cha đến. Sidecar hỗ trợ các processes hoặc services được triển khai với ứng dụng. Trong một chiếc moto, sidecar được gắn vào một chiếc moto, và mỗi moto có sidecar của riêng nó. Bằng cách tương tự sidecar service chia sẻ nhiệm vụ với ứng dụng cha của nó. Với mỗi instance của ứng dụng, một instance của sidecar được deploy và hosted tương ứng.
- Ưu điểm của việc sử dụng sidecar pattern:
    - Sidecar độc lập với ứng dụng chính của nó về môi trường runtime và ngôn ngữ lập trình, do đó bạn không cần phát triển sidecar cho mỗi ngôn ngữ.
    - Sidecar có thể truy cập cùng tài nguyên với ứng dụng chính. Ví dụ, một sidecar có thể monitor tài nguyên hệ thống được dùng bởi cả sidecar và ứng dụng chính.
    - Bởi vì sidecar gần với ứng dụng chính, nên gần như không có độ trễ khi giao tiếp giữa chúng.
    - Ngay cả với những ứng dụng không cung cấp cơ chế mở rộng, bạn vẫn có thể dùng sidecar để mở rộng tính năng bằng việc gắn nó với một process riêng trên cùng host hoặc sub-container như ứng dụng chính.

    Sidecar pattern thường được dùng với những containers và thường được gọi là sidecar container hoặc sidekick container.

    **Những vấn đề và cân nhắc**

    -
