# Cách Internet hoạt động?

Nguồn dịch: https://medium.com/@User3141592/how-does-the-internet-work-edc2e22e7eb8

## Internet thực ra hoạt động như thế nào?
Inrternet hoạt động thông qua một mạng định tuyến các gói tin theo giao thức Internet (IP - Internet Protocol), Transport Control Protocol (TCP) và những giao thức khác.

### Vậy giao thức là gì?
Một giao thức là một bộ quy tắc (rules) chỉ định cách mà những máy tính giao tiếp với nhau qua mạng. Ví dụ, TCP có một quy tắc là nếu một máy tính gửi dữ liệu tới một máy khác, thì máy đích (destination) phải thông báo cho máy nguồn (source) biết nếu có dữ liệu bị mất để nó có thể gửi lại dữ liệu đó. Hoặc là IP chỉ định cách những máy tính định tuyến thông tin tới máy khác bằng cách đính kèm (attach) địa chỉ trên dữ liệu gửi đi.

### Gói tin (packet) là gì?
Dữ liệu gửi qua Internet được gọi là tin nhắn (message). Trước khi message được gửi, nó được chia thành nhiều mảnh (fragments) gọi là packet. Những packet này được gửi độc lập với nhau. Kích thước tối đa điển hình cho một packet là khoảng từ 1000 đến 3000 kí tự. IP chỉ định cách messages được đóng gói.

### Mạng định tuyến gói tín (packet routing network)?
Đây là một mạng dùng để định tuyến các packets từ một máy nguồn tới một máy đích. Internet được xây dựng từ một mạng lưới lớn các máy tính chuyên dụng gọi là bộ định tuyến (routers). Công việc của mỗi router là biết cách làm thế nào để di chuyển packets từ nguồn đến đích. Mỗi packet sẽ phải di chuyển qua nhiều routers trong suốt hành trình của nó.

Khi một packet di chuyển từ một router đến một router khác, sẽ được gọi là "hop" (nhảy). Bạn có thể dụng lệnh `traceroute` đên xem danh sách các hops mà packet đi qua từ máy của bạn đến một host.
![Kết quả lệnh trace route](https://miro.medium.com/max/1132/1*nFKQkXXUCI41ZNrY8gPk4A.png)

IP chỉ định cách địa chỉ mạng được gắn vào header của packet, một không gian được chỉ định trong packet dùng để chỉ các meta-data của packet. IP còn chỉ định cách mà routers chuyển packets dựa trên địa chỉ ở header.

### Bộ định tuyến Internet có từ đâu? Ai sở hữu chúng?
Bộ định tuyến có nguồn gốc từ những năm 1960s với tên ARPANET, một dự án quân sự với mục tiêu là một mạng máy tính được phân cấp để chính phủ có thể truy cập và phân phối thông tin trong trường hợp xảy ra các sự kiện thảm khốc. Từ đso, một số tập đoàn cung cấp dịch vụ Internet (Internet Service Providers - ISP) đã thêm routers vào các ARPANET routers này.

Không có một chủ sở hữu duy nhất nào cho các bộ định tuyến Internet này, mà chúng thuộc sở hữu của nhiều chủ: Các cơ quan chính phủ và các trường đại học liên kết với ARPANET trong những ngày đầu và các tập đoàn ISP như AT&T và Verizon sau này.

### Có phải các packet sẽ luôn đến theo đúng thứ tự? Nếu không thì làm sao sắp xếp lại chúng?
Những packets có thể đến đích không đúng thứ tự. Việc này xảy ra khi một packet gửi sau tìm thấy một đường đi nhanh đến đích hơn những packet trước đó. Nhưng packet header đã có chứa thông tin về thứ tự packet liên quan đến toàn bộ message. TCP sử dụng thông tin này để khởi tạo lại message ở đích.

### Có phải các packet sẽ luôn luôn đến đích?
IP không thể đảm bảo được packet sẽ luôn đến được đích của nó. Khi điều này xảy ra, nó được gọi là packet loss. Việc này xảy ra điển hình là khi một router nhận nhiều packet hơn là nó có thể xử lí. Nó không có lựa chọn nào khác là phải bỏ bớt một vài packet.

Tuy nhiên, TCP xử lí packet loss bằng cách thực hiện truyền lại (performing re-transmission). Nó làm vậy nhờ có máy tính đích định kì gửi các gói xác nhận trở lại máy nguồn cho biết số lượng message mà nó đã nhận và reconstruct. Nếu máy đích nhận thấy có những gói tin bị mất, nó sẽ gửi request tới máy nguồn để yêu cầu gửi lại những gói tin bị mất đó.

Khi 2 máy tính giao tiếp thông qua TCP, chúng ta gọi là có một TCP connection giữa chúng.

### Địa chỉ Internet trông như thế nào?
Những địa chỉ này được gọi là địa chỉ IP và có 2 tiêu chuẩn.

Tiêu chuẩn đầu tiên của địa chỉ được gọi là IPv4 và nó trông như là này `212.78.1.25`. Nhưng vì IPv4 chỉ hỗ trợ 2^32(khoảng 4 tỉ) đỉa chỉ, nên Internet Task Force đền xuất một chuẩn địa chỉ mới gọi là IPv6, nó sẽ trông như này `3ffe:1893:3452:4:345:f345:f345:42fc`. IPv6 hỗ trợ 2^128 địa chỉ, có thể cung cấp cho nhiều thiết bị mạng hơn, nhiều hơn so với năm 2017 8 tỷ thiết bị được kết nối mạng hiện tại trên Internet.

Như vậy, có một mapping một một (one to one) giữa IPv4 và IPv6. Lưu ý là việc chuyển từ IPv4 tới IPv6 vẫn còn đang trong quá trình và sẽ mất một khoảng thời gian dài. Ở năm 2014 thì Google tiết lộ là traffic trên IPv6 của họ mới chỉ là 3%.

### Làm thế nào có thể có hơn 8 tỉ thiết bị được kết nối mạng trên Internet nếu chỉ có khoảng 4 tỉ địa chỉ IPv4?
Đó là bởi vì có địa chỉ IP public và private. Chỉ cần biết một trong những hàng xóm của nó, gọi là outbound link, thì có thể định tuyến packet đến. Lưu ý rằng địa chỉ IP có thể có thể chia thành 2 phần, một network prefix và một host identifier. Ví dụ, `129.42.13.69` có thể chia thành 2 phần là:
```
Network Prefix: 129.42
Host Identifier: 13.69
```

Tất cả thiết bị kết nối Internet thông qua một kết nối đơn (single connection) (ví dụ như khuôn viên trường, một doanh nghiệp, hoặc ISP trong khu vực tàu điện ngầm) sẽ cùng chia sẻ một network prefix.

Routers sẽ gửi tất cả các packets có dạng 129.42.*.* đến cùng một vị trí. Do đó thay vì phải tracking hàng tỉ địa chỉ IP, routers chỉ cần theo dõi khoảng 1 triệu network prefix.

### Nhưng một router vẫn cần biết nhiều network prefix. Nếu một router mới được thêm vào Internet thì làm sao nó có thể xử lí được những packets cho tất cả những network prefixes?
Một router mới có thể bắt đầu bằng một vài route được cấu hình sẵn. Nhưng nếu gặp một packet mà nó không biết phải định tuyến đi đâu, thì nó sẽ truy vấn đến một trong những router lân cận. Nếu có được thông tin định tuyến của packet thì nó sẽ gửi thông tin lại cho router mới yêu cầu. Và router mới này sẽ lưu thông tin lại cho những lần sử dụng tiếp theo. Bằng cách này, một router mới sẽ xâu dựng bảng định tuyến cho mình, một database về network prefixes cho các outbound links. Nếu mà router lân cận cũng k có thông tin gì thì nó sẽ tiếp tục truy vấn tới những router khác, và cứ như thế đến khi có thì thôi :))

### Làm sao để máy tính nối mạng có thể tìm ra địa chỉ IP dựa vào tên domain?
Chúng ta thường hay gọi việc tìm kiếm địa chỉ IP thông qua một tên miền như `www.google.com` là "phân giải địa chỉ IP" (resolving the IP address). Máy tính phần giải địa chỉ IP thông qua Domain Name System (DNS), một database phi tập trung (decentralized) ánh xạ từ tên miền sang địa chỉ IP.

Để phân giải địa chỉ IP, đầu tiên máy tính sẽ kiểm tra local DNS cache, nơi lưu trữ địa chỉ IP của các website mà nó đã truy cập. Nếu không thể tìm thấy địa chỉ IP ở đây hoặc là bản ghi địa chỉ IP hết hạn, nó sẽ truy vấn những server ISP's DNS chuyên dụng để phân giải địa chỉ IP. Nếu những server ISP's DNS cũng không thể phân giải địa chỉ IP, thì chúng sẽ truy vấn những root name server, nơi có thể phân giải mọi tên miền cho một top-level domain. Top-level domain là những từ ở bên phải ngoài cùng của một domain như là `.com`, `.net`, `.org` là những ví dụ.

### Làm sao để những ứng dụng có thể giao tiếp với nhau qua Internet?
Giống như những dự án kỹ thuật phức tạp khác, Internet được chia thành nhiều phần nhỏ độc lập, chúng hoạt động cùng nhau qua những interface được xác định rõ ràng. Những thành phần này được gọi là Internet Network Layers và chúng bao gồm Link Layer, Internet Layer, Transport Layer, và Application Layer. Chúng được gọi là layer vì chúng được xây dựng trên đỉnh của cái khác, mỗi layer sử dụng các khả năng của layer bên dưới nó mà không phải quan tâm đến chi tiết các bước thực hiện.

[Internet Network Layer](https://miro.medium.com/max/317/1*PxADiXu9n6cFFEl2QOLwdA.jpeg)

Các ứng dụng trên Internet hoạt động ở Application Layer và không cần quan tâm đến chi tiết ở những layer bên dưới. Ví dụ, một ứng dụng kết nối tới một ứng dụng khác trên mạng thông qua TCP sử dụng một kiến trúc gọi là `socket`, nó là tóm tắt các chi tiết của việc định tuyến gói tin, và lắp ráp các gói tin để tạo nên messages.

### Công việc của mỗi Internet Layers là gì?
 Ở tầng thấp nhất là Link Layer được gọi là lớp vật lí (physical layer) của Internet. Link Layer liên quan đến việc truyền data bits thông qua một số phương tiện vật lí như cáp quan hay tín hiệu vô tuyến wifi.

Trên đỉnh của Link Layer là Internet Layer. Internet Layer liên quan dến việc định tuyến gói tin tới đích. Internet Protocol được đề cập ở trên hoạt động trong lớp này (nên chúng có cùng tên vs nhau). Internet Protocol tự động điều chỉnh và định tuyến lại cá gói tin dựa trên tải mảng hoặc trong trường hợp mất điện. Lưu ý rằng nó ko phải khi nào cũng đảm bảo gói tin sẽ đi đến đích, nó chỉ cố gắng tốt nhất có thể thôi.

Phía trên lớp Internet Layer là Transport Layer. Tầng này dùng để bù đắp lại những dữ liệu có thể bị mất ở tầng Internet và Link bên dưới. Transport Control Protocol (TCP) chúng ta đã đề cập ở trên được dùng ở tầng này, và nó họat động chủ yếu để tập hợp các gói tin vào message ban đầu và cũng như truyền lại các thông tin đã mất.

Application Layer là tầng trên cùng. Tầng này dùng tất cả các tầng bên dưới để xử lí những chi tiết phức tạp trong việc chuyển các packets đi qua Internet. Nó giúp các ứng dụng đễ dàng tạo kết nói với các ứng dụng khác trên Internet vói những abstactions đơn giản như sockets. HTTP protocol, giao thức chỉ định cách các web browsers và web servers tương tác, hoạt động ở Application Layer. IMAP Protocol, giao thức chỉ định việc các email clients nhận email, cũng tồn tại ở Application Layer. FTP Protocol, giao thức đảm nhận việc truyền file giữa file-downloading client và file-hosting servers cũng ở lớp này.

### Client và Server là gì?
Client và Server là những ứng dụng có thể giao tiếp thông qua Internet, client thì gần với người dùng hơn,do đó chúng là những ứng dụng người dùng như là web browser, email clients, hay smart phone apps. Server là những ứng dụng chạy trên một máy tính remote mà client có thể giao tiếp thông qua Internet khi cần.

Một định nghĩa chính thống thì là thế này: Một ứng dụng khởi tạo một kết nối TCP là `client`, trong khi một ứng dụng nhận một kết nối TCP là `server`.

### Làm sao mà những dữ liệu nhạy cảm như credit card có thể được truyền một cách bảo mật thông qua Internet?
Trong những ngày đầu của Internet, nó cũng đủ dể đảm bảo rằng các bộ định tuyến và liên kết mạng nằm ở những vị tri an toàn về mặt vật lí. Những khi Internet phát triển nhanh, nhiều routers hơn cũng đi kèm với nhiều rủi ro hơn. Hơn nữa, với sự ra đời cũng các công nghệ không dây như Wifi, hackers có thể đánh chặn các gói tin, dó đó việc đảm bảo các phần cứng mạng an toàn không thôi là chưa đủ. Giải pháp chính là encryption và authentication thông qua SSL/TLS.

### SSL/TLS là gì?
SSL viết tắt của Secured Sockets Layer. TLS viết tắt của Transport Layer Security. SSL lần đầu được phát triển bởi Netscape năm 1994 nhưng một phiên bản bảo mật hơn được nghĩ ra và đổi tên thành TLS. Chúng ta sẽ đề cập đến chúng cùng nhau là SSL/TLS.

SSL/TLS là một tầng tùy chọn nằm giữa Transport Layer và Application Layer. Nó đảm bảo tính bảo mật của những thông tin nhạy cảm thông qua encryption và authentication.

Encryption có nghĩa là client có thể yêu cầu một kết nối TCP tới server được mã hóa. Tất cả các messages được gửi giữa client và server sẽ được mã hóa trước khi chia nhỏ thành các packets. Nếu hacker chặn được những gói tin này, họ cũng ko thể tái tạo lại tin nhắn ban đầu.

Authentication có nghĩa là client có thể tin tưởng server là đúng nơi nó yêu cầu. Điều này bảo vệ khỏi cách tấn công `man in the middle`, điều này là khi một mã độc can thiệp vào kết nối giữa client và server để nghe lén và giả mạo kết nối của họ.

Chúng ta sẽ thấy SSL bất cứ khi nào truy cập vào một site nào có bật SSL trên những browser hiện đại. Khi browser yêu cầu một web site sử dụng `https` protocol thay vì `http`, nó nói với web-server rằng nó muốn một kết nối mã hóa SSL. Nếu web-server hỗ trợ SSL, một kết nối mã hóa bảo mật được tạo và chúng ta sẽ thấy một icon khóa bên cạnh thanh địa chỉ trên browser.

### Làm thế nào mà SSL xác thực định danh của một server và mã hóa giao tiếp của chúng?
Nó sử dụng mã hóa bất đối xứng và chứng chỉ SSL.

Mã hóa bất đối xứng là một cơ chế mã hóa sử dụng public key và private key. Những khóa này căn bản là các số có nguồn gốc từ các số nguyên tố lớn. Private key được dùng để giải mã dữ liệu và sign documents. Còn public key được dùng để mã hóa dữ liệu và xác thực signed documents. Không giống mã hóa đối xứng, mã hóa bất đối xứng có nghĩa là khả năng mã hóa không tự động trao khả năng giải mã. Nó làm được việc này bằng cách sử dụng các nguyên lí trong một nhánh toán học gọi là lý thuyến số (number theory).

Một chứng chỉ SSL là một tài liệu số bao gồm một public key được gán cho một web server. Những chứng chỉ SSL được cấp cho web server thông qua các cơ quan chứng nhận. Hệ điều hành, thiết bị di động, và browser đi kèm với cơ sở dữ liệu của một số cơ quan cấp chứng chỉ để nó có thể xác minh chứng chỉ SSL.

Khi một client yêu cầu một kết nối SSL-encrypted với một server, server gửi lại chứng chỉ SSL của nó. Client kiểm tra chứng chỉ SSL:
- được cấp cho server
- được signed bởi một cơ quan chứng nhận đáng tin cậy
- không hết hạn

Client sau đó dùng public key của chứng chỉ SSL để encrypt một secret key tạm thời ngẫu nhiên và gửi lại cho server. Bởi vì server có một private key tương ứng, nó có thể giải mã secret key tạm thời của client. Bây giờ cả client và server đều biết secret key tạm thời, do đó chúng có thể dùng nó để mã hóa đối xứng các message gửi cho nhau. Chúng sẽ loại bỏ secret key tạm này sau khi hết phiên (session).

### Điều gì xảy ra khi hacker chặn phiên mã hóa SSL?
Giả sử một hacker chặn mỗi message được gửi giữa client và server. Hacker sẽ thấy chứng chỉ SSL mà server gửi và mã hóa secret key tạm của client. Nhưng vì hacker không có private nên nó không thể giải mã secret key tạm được. Và do đó nó không có secret key tạm, nó không thể giải mã bất cứ gì của message gửi client và server.

### Tóm lại
- Internet bắt đầu với ARPANET vào những năm 1960 với mục đích của một mạng máy tính phân tán.
- Về mặt vật lí, Internet là một tập các máy tính chuyển các bits với nhau qua dây, cáp hoặc tín hiệu vô tuyến.
- Giống như nhiều dự án phức tạp, Internet được chia thành nhiều tầng, mỗi tầng dùng để xử lí những vấn đề nhỏ hơn. Mỗi tầng kết nối với nhau qua các interface được định nghĩa sẵn.
- Có nhiều giao thức định nghĩa cách Internet và các ứng dụng của nó hoạt động tại các tầng khác nhau: HTTP, IMAP, SSH, TCP, UDP, IP, vv. Trong trường hợp này, Internet là một tập nhiều quy tắc quy định cách các máy tính và chương trình hoạt động vì nó là một mạng máy tính vật lí.
- Với sự phát triển của Internet, wifi, và những nhu cầu thương mại điện tử, SSL/TLS được phát triển để giải quyết các mối quan tâm v











































































