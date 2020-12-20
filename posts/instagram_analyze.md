Instagram - Thiết Kế Hệ Thống có gì hay? 💻

Thiết Kế Hệ Thống tương tự Instagram có khó? và những điều cần lưu ý!


Một hệ thống rất được các Anh Chị Em chọn làm 1 nơi để lưu giữ những kỉ niểm tưởng chừng đơn giản với một hệ thống chuyên dụng cho việc upload/download(view) Ảnh, nhưng để có được một kiến trúc hầm hố chứa được 1 lượng traffic cực khủng như Instagram là 1 bài toán cực kì hầm hố và phức tạp, hãy cùng mình tìm hiểu về các vấn đề để thiết kế 1 hệ thống tương tự Instagram nhé!


Xin chào, mình là Khanh Ney, 1 Backend Developer chính hiệu sùng bái các vị Vua và mang tư duy của các vị Vua giải quyết vấn đề với DIY (Do it Yourself). Hi vọng với kiến thức của mình có thể giúp cho các bạn chưa tiếp xúc với các vấn đề có thể thiết kế và dự tính, đánh gía các vấn đề từ 1 bài toán thiết kế hệ thống.


Để Design một System hiệu quả, bạn cần nắm được các yêu cầu cốt lõi từ hệ thống, và đưa ra các yếu tố giúp hệ thống có khả năng mở rộng về sau.

1/ Yêu cầu Hệ Thống
1.1/ Yêu cầu bắt buộc
Cho phép người dùng Upload/Download các hình ảnh của chính họ hoặc từ các User khác
User có thể tìm kiếm các thông tin như tên Người dùng, Hình ảnh, Video
Là 1 mạng xã hội, thì tính năng theo dõi, yêu thích là 1 phần không thể thiếu
Hình ảnh, Video hoặc các nội dung khác từ người dùng upload sẽ không mất sau một khoảng time hệ thống cho phép lưu trữ
Hệ thống tự có khả năng generate và hiển thị nội dung trên News Feed từ tất cả người dùng đã theo dõi.
1.2/ Yêu cầu không bắt buộc
Hệ thống đáp ứng với khả năng phản hồi từ các yêu cầu Download/Viewed dưới 200ms
Hệ thống cần có khả năng đáp ứng tính sẵn sàng cao (Highly Available)
1.3/ Note
Số lượng hình ảnh và các nội dung khác sẽ được User sử dụng rất nhiều, nên việc quản lý Storage một cách hiểu quả sẽ đem lại response-time thấp nhất.
Hệ thống phải đảm bảo mức độ an toàn dữ liệu (Storage: hình ảnh, video, …) sẽ ở mức độ 100%
Tỉ lệ Read/Write ước tính ~= 5/1 (tức trung bình User sẽ download(view) 5 ảnh/1 ảnh upload)
2/ Dự tính khả năng lưu trữ
giả sử hệ thống có 10M User, với số lượt truy cập trên ngày DAU là 1M User
200.000 hình ảnh được upload mỗi ngày → ~= 3 photo/s
Trung bình, 1 ảnh sẽ có dung lượng ~= 300KB

Nếu bạn không quan tâm đến việc tính toán và ước chừng về các thành phần dưới đây thì có thể next sang mục tiếp theo nhé!


2.1/ Ước tính Traffic

với tỉ lệ read/write ~= 5/1
trung bình mỗi ngày có 200.000 hình ảnh được upload
5 * 200.000(photo/day) = 1.000.000 (photo/day)
-> 1.000.000 / (1 ngày * 24 giờ * 3000 giây) ~= 12 photo/s (QPS - queries per second)
với tỉ lệ 5:1 Read/Write

→ 5 (5 Read photo/1 Write photo) * 12 photo/s (Query per Second)


=> Request Per Second: 60(rps)


300(KB/photo) * 12 photo/s = 3600KB/s = 3.5MB/s


2.2/ Ước tính Storage

200.000(photo/day) * 300(KB/photo) ~= 60GB/day
vậy với 1 năm → 60GB/day * 365 day = 29TB/year

2.3/ Ước tính Bandwidth

Với Write Request(Upload), với 3 photo/s → tổng dữ liệu Incoming = 3(photo/s)*300(KB/photo) = 900KB/s
Với Read Request(Download/View), với 60(Request per second) → Tổng dữ liệu Outcoming = 60(request_photo/s) * 300(KB/photo) ~= 17.5 MB/s

2.4/ Ước tính Memory

Nếu bạn follow Theo rule 20-80, tức 20% cho các Hot Photo
Với 60(rps) → 1 ngày ta có: 60 * 3600 seconds * 24 hours = 5.184.000 (Request per Day)
Theo rule 20-80, ta cần: 5.184.000 * 0.2 (20% Hot Photo for Cache) * 300 (KB/Photo) ~= 311GB
3/ Database & Storage



Việc thiết kế 1 Database có thể lưu trữ các thông tin với yêu cầu trên sẽ không gây khó khăn nhiều cho các bạn, Nhưng ở góc độ HighLevel bạn cần có 1 cách tiếp cận với việc dữ liệu sẽ bùng nổ, và khả năng Latency (Độ Trễ) là 1 key-design mà bạn cần phải hướng đến.
Với các cở sở dữ liệu hiện nay như SQL vs NoSQL, chúng ta cần có 1 chiến lược tiệp cận phù hợp, chúng ta sẽ có 1 vài lưu ý trước khi việc chọn CSDL cho Hệ Thống này, như:
Số lượng record cần lưu trữ: 200.000 records/day → 10 year = 200.000 * 365days * 10 years = 730M records/10year
Mỗi object giả sữ dưới 1K/record(document) (việc này bạn có thể theo dõi từ các CLI DB, hoặc đánh gía qua các filed, ví dụ với MongoDB: _id = 12bytes, number = 8bytes,…)
Không cần lưu trữ mối quan hệ giữa các bảng ghi
Hệ thống có tỉ lệ READ cao
→ từ các lưu trên, ta có thể đưa ra các LOẠI DB hiệu quả như: key-values(Cassandra), NoSQL (MongoDB)
Về storage, chúng ta có thể dùng các Storage Distributed file như S3, BackBlaze,…
4/ Sharding
Với các yêu cầu từ hệ thống:
Hệ thống đáp ứng với khả năng phản hồi từ các yêu cầu Download/Viewed dưới 200ms
Hệ thống cần có khả năng đáp ứng tính sẵn sàng cao (Highly Available)
Sharding là 1 giải pháp hiệu quả, giúp bạn phân tán dữ liệu (1 PHIÊN BẢN nâng cao INDEX Distributed 😂~ mình nghĩ là vậy). Giúp bạn Scale-Horizontal DB 1 cách hiệu quả.
Việc thực hiện Sharding sẽ giúp các bạn giải quyết vấn đề trên, ngoài ra khi các node triển khai với Sharding, nếu deploy với Kiến Trúc Master-Slave (Replica-set) sẽ giúp bạn có 1 hệ thống có tính Sẵn Sàng Cao (Highly Available)






Lưu ý: Trong quá trình tính toán về Metadata lưu trữ, nếu vẫn đảm bảo dữ liệu phát sinh vẫn nằm trong vùng cho phép, thì bạn vẫn có thể chọn giải pháp Scale-up với việc tăng hạ tầng hệ thống, nhưng nhìn chung, với 1 Hệ Thống Sharding, về sau 5, 10 năm nữa mình cũng cảm thấy an tâm, việc còn lại của mình chỉ là làm thế nào chọn các SHARD-KEY (tương tự mongod indexed vậy), và cần test tùm lum thứ để đảm bảo rằng việc bạn phán quyết nó sẽ là SỰ SỐNG CÒN khi ra môi trường Production 😎 .

5/ NewsFeed - Là sự sự sống cho hầu hết Anh/Chị/Em dùng Fb hoặc Instagram
Với các cách truyền thống (mình hay sử dụng trước đây), quy trình tạo ra 1 NewsFeed sẽ như sau
Lấy danh sách các User đang theo dõi → Query Photo mới nhất của từng User
Sau đó query thêm các thông tin metadata (photo, user, …) → response cho người dùng
Sử dụng 1 thuật toán gì đó, để sắp xếp thứ tự các hình ảnh

Quy trình sẽ như sau Query → Sorting → Merging → Ranking


Problems: Với cách tiếp cận này chúng ta sẽ gặp các vấn đề, như sau:

Lantency cao (DB), đến từ việc Query từ nhiều bảng (từ 1 request)
Latency cao trong việc thực hiện quy trình từ Query → Sorting → Merging → Ranking (Logic).

Solution:

Thực hiện các tác vụ ngầm hoặc trigger → Pre-generating NewsFeed
Chi tiết ví dụ như sau:
Tạo 1 table generateNewsFeed, có liên kết:
Users (những người nhận tin + top Ranking)
Author
…metadata (newsFeed ref, hoặc info NewsFeed)
Mỗi lần User_tạo 1 Post(Photo),
Server thực hiện tìm kiếm → update(tạo record generateNewsFeed chẳng hạn), xử lý các thuật toán Ranking → cập nhật DB.
Hoặc có thể chạy các Schedule-Task Event để xử lý các việc create/update trên table generateNewsFeed
Khi User request NewsFeed → Server Query trên table generateNewsFeed 🤩
Vấn đề tiếp theo mà ta cầm vắn não suy nghĩ là, Vậy NewsFeed sẽ gửi Cho Clients(Users) như thế nào?chúng ta có các cách tiếp cận như sau:
1/ Pull, tức là Clients cần gửi các yêu cầu để nhận được NewsFeed, nhưng vấn đề và từ cách tiếp cận này như sau:
Clients sẽ request nếu muốn có NewsFeed (việc này như khi bạn dùng App Mobile Facebook, bạn cần vuốt xuống từ màn hình để nhận được các nội dung NewsFeed mới) 😔
Server đôi lúc sẽ không có dữ liệu trả về (có thể do đã lấy tất cả NewsFeed, hoặc không có NewsFeed mới từ các User đang theo dõi)
2/ Push, tức khi 1 trong danh sách các User đang theo dõi tạo 1 post mới, Server sẽ tự thực hiện tác vụ gửi cho các Client đang follow User này, cách tiếp cận này cũng có những vấn đề như sau:
Đối với các User có lượng User (mình đang theo dõi) LỚN (ví dụ 100nghìn), thì hệ thống sẽ push NewsFeed liên tục, nếu các Anh Chàng/Cô Nàng đấy đăng ảnh với 1 tầng suất chóng mặt 😕
Nhưng vượt qua các trở ngại đó, giả sử đối với các User có lượng theo dõi <100nghìn User, thì xem như tạm chấp nhận, vậy ta phải chọn Giao Thức nào để implement Push, có rất nhiều loại Giao Thức, nhưng mình chỉ đưa ra 1 vài gợi ý như: HTTP Polling, HTTP Long Polling, WebSocket, … bạn có thể xem thêm thông tin và đánh giá → chọn lựa phù hợp với hệ thống các bạn nhé
3/ Đi với phật mặc áo cà sa, đi với ma mặc áo giấy 👻
Từ việc phân tích ưu/nhược điểm ta có thể chọn 1 cách mà giúp tối ưu hoá trong việc send NewsFeed cho Clients, như sau:
1/ Đối với các User có lượng đang theo dõi ÍT (<100nghìn), ta có thể chọn PUSH để gửi NewsFeed cho các Client đó
2/ Đối với các User có lượng đang theo dõi CAO:
với các NewsFeed có lượng RANKING cao hoặc 1 tiêu chí gì đó mà Server ưu tiên: → có thể dùng PUSH-based để gửi NewsFeed
với các NewsFeed bình thường → sử dùng PULL-based để xem các NewsFeed
6/ Caching

Chúng ta có rất nhiều cấp độ cache: Client Cache, CDN Cache, Webserver Cache, Application Cache, DB Cache, …nhưng chúng ta chỉ sẽ đề cập về Application Cache

Memcache hoặc các Enginee khác như Redis sẽ là 1 Tool giúp bạn tối ưu hoá các câu tìm kiếm (HOTPOST/Photo). dựa vào các cơ chế, như:
LRU(Least Recently Used)
LFU(Least Frequency Used)

→Với hệ thống hiện tại, NewsFeed sẽ ưu tiên Ranking theo tiêu chí Thời Gian (Gần Nhất) → nên chúng ta sẽ chọn LRU cho việc Cache eviction

Lưu ý:
Tuân theo quy luật 20% lượng Traffic
Việc cache sẽ dẫn đến các vấn đề đau đầu như Invalidate Cache, nên hãy làm chủ mọi cuộc chơi để không phải dùng câu ‘này là Feature’ nhé.

Nguồn: https://www.facebook.com/notes/730544164474628/
