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

- Xem xét định dạng deploy và đóng gói bạn sẽ dùng để deploy các services, processes, hoặc containers. Trong đó containers đặc biệt phù hợp với sidecar pattern.
- Khi thiết kế sidecar services, cẩn trọng trong việc quyết định cơ chế giao tiếp giữa các process. Cố gắng sử dụng các ngôn ngữ hay framework trừ khi những yêu cầu về hiệu suất làm cho điều này bất khả thi.
- Trước khi đặt những tính năng vào sidecar, xem xét xem nó sẽ hoạt động tốt như là một service riêng hoặc một daemon gắn thêm.
- Chúng ta cũng xem xét tính năng có thể được implement như một thư viện hoặc sử dụng như một cơ chế extension truyền thống. Những thư viện chuyên biết cho từng ngôn ngữ có thể được tích hợp ở tầng sâu hơn và ít chi phí network hơn.

**Khi nào nên sử dụng pattern này?**

- Khi ứng dụng của bạn sử dụng một tập hợp các ngôn ngữ hoặc framework không đồng nhất. Một thành phần nằm ở sidecar có thể được dùng bởi những ứng dụng viết bằng những ngôn ngữ hay framework khác.
- Một thành phần thuộc sở hữu của một remote team hoặc một tổ chức khác.
- Một thành phần hoặc chức năng phải cùng được đặt trên cùng host với ứng dụng.
- Bạn cần một service có cùng vòng đời với ứng dụng chính, những có thể được cập nhật độc lập.
- Bạn cần kiểm soát chi tiết nhưng giới hạn tài nguyên cho một tài nguyên hoặc thành phần cụ thể. Ví dụ bạn muốn chỉ định dung lượng memory mà một thành phần cụ thể được sử dụng. Bạn cần deploy một thành phần như một sidecar và quản lí việc sử dụng bộ nhớ độc lập với ứng dụng chính.

**Khi nào không nên sử dụng pattern này?**

- Khi việc giao tiếp giữa các process cần được tối ưu. Giao tiếp giữa ứng dụng cha và những service sidecar sẽ có thêm chi phí, đặc biệt là độ trễ khi gọi qua lại. Điều này có thể là một trade-off không thể chấp nhận cho những ứng dụng chat.
- Đối với những ứng dụng nhỏ thì những chi phí tài nguyên cho việc deploy sidecar service trên mỗi instance không có nhiều quá trị về tính cô lập (isolation).
- Khi service cần scale khác hoặc độc lập với ứng dụng chính. Nếu vậy thì có thể deploy thành một service riêng biệt.

**Ví dụ**

- Infrastructure API. Infrastructure development team tạo ra một service được deploy cùng với mỗi ứng dụng, thay vì là một thư viện client của ngôn ngữ đó để truy cập vào cơ sở hạ tầng (infrastructure). Service được tải lên như một sidecar và cung cấp một common layer cho những infrastructure service, bao gồm logging, dữ liệu environment, lưu trữ cấu hình, discovery, health checks, và những service watchdog. Sidecar còn monitor môi trường host và process (hoặc container) của ứng dụng cha và log thông tin đến một service trung tâm.
- Quản lí NGINX/HAProxy. Deploy NGINX với một sidecar service để monitor trạng thái môi trường, sau đó cập nhật file cấu hình NGINX và tái tạo lại process khi cần một thay đổi trong trạng thái.
- Ambassador (đại sứ) sidecar. Deploy một ambassador service như một sidecar. Ứng dụng gọi qua ambassador, nơi xử lí request logging, routing, circuit breaking, và những tính năng liên quan đến kết nối khác.
- Offload proxy. Đặt NGINX proxy trước instance của nodejs service, để xử lí các file static cho service.
