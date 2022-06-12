# Load test

Viet Tran, [12 May 2021 at 11:50:58]:
các ae thử chia sẻ công cụ stress test thường dùng nhé:

wrk hoặc wrk2: cli đơn giản gọn nhẹ, test nhanh mấy cái API đơn giản.
tsung: là erlang nên thấy nó chạy ổn định kinh khủng, này cty cũ setup sẵn nên mình vào bấm chạy rồi coi số thôi.
locust: có dashboard nên rất okie, là python nên cũng dễ hiểu hơn (vì mình biết python).

Ai đã từng stress test mà quên báo trước AWS không, nó detect DDOS xong ban luôn cái đầu IP gọi stress test =)))

Nguyen Thanh Dong, [12 May 2021 at 11:53:18]:
tsenart/vegeta
HTTP load testing tool and library. It's over 9000! - tsenart/vegeta

Nguyen Thanh Dong, [12 May 2021 at 11:53:18]:
https://github.com/tsenart/vegeta

thường e hay xài tool này. Mấy tool kia chạy nóng máy quá xài k nổi

tool này nó gen ra cho mình cả chart luôn nên xài tiện

mà load test khác gì stress test a @viettx nhỉ

Viet Tran, [12 May 2021 at 11:55:37]:
"Load testing is performed to find out the upper limit of the system or application. Stress testing is performed to find the behavior of the system under pressure. The factor tested during load testing is performance. The factor tested during stress testing is robustness and stability"

VD mọi người đang cân nhắc DB nào cho tác vụ write nhiều với 1 structure cụ thể, khi ấy mình cần load test => biết được giới hạn ở đâu, tốc độ ra sao

còn stress test là ví dụ sắp launching black friday, mình cần đẩy 1 lượng traffic rất lớn vào hệ thống để kiểm tra xem tính ổn định và các vấn đề sẽ gặp phải dưới áp lực như thế

2 cái này giống như khác nhau nhé, thấy nhiều ae hay nhập làm 1 là ko đúng lắm

các tool load test vẫn dùng để stress được, nhưng thường stress test sẽ hay đi thành 1 bộ request có kịch bản hơn là cái API riêng lẻ

Nguyen Thanh Dong, [12 May 2021 at 12:00:45]:
bộ request nghĩa là cùng 1 lúc có thể đẩy request ở các API khác nhau vào hệ thống đúng k a?. 

2 khái niệm này dễ nhầm lẫn thật.

Viet Tran, [12 May 2021 at 14:28:52]:
VD flow: list sản phẩm flash sale, chọn vào giỏ hàng (check số lượng, lấy giá deal), đặt hàng và các side effect khi tải cao

thường load test hay dùng để tính toán và confirm giải pháp, est cost hệ thống (khi vận hành production bình thường)

còn stress test là cho các thời điểm peak traffic, để tìm thấy những vấn đề từ trước, củng cố hoặc caching hợp lý hơn

mấy ae cứ share các tool bt hay dùng và thảo luận với nhau nhé, mấy nay dịch nên WFH chắc cũng nhiều rồi ha 😄

Thanh Nguyen, [12 May 2021 at 14:32:06]:
K6.io

M thấy mấy bạn bên m hay dùng

Viet Tran, [12 May 2021 at 14:34:30]:
uhm k6 rất okie, bt mình cũng dùng để interaction testing, nhưng hồi đó mình xài ở mức simple thôi, ko biết nó giả lập high traffic tầm trên >= 100K CCU thì ổn ko
