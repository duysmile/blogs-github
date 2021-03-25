# Các biện pháp chống DDOS

Nguồn: Webuild Slack

\#1
Chống DDoS là một bài toán cực khó, như bạn phía trên nói thì cần rất nhiều tiền và phải optimize liên tục nếu bị attacker target vào. Vẫn có những giải pháp có thể áp dụng để giảm/hạn chế mức độ ảnh hưởng hoặc làm attacker nản lòng. Tuy nhiên, nếu attacker nhận ra giá trị, lợi nhuận từ việc attack hệ thống của bạn thì gần như bạn sẽ phải bỏ rất nhiều tiền hoặc .. bó tay chịu trói.
Mình thì combine nhiều giải pháp lại, nói chung cũng hạn chế phần nào thôi chứ cũng nản lòng lắm.
- Mình xài Cloudflare phía trc system của mình, dựa vào bộ lọc sẵn có của Cloudflare để auto detect threat-country/bad-bot giúp mình. Ngoài ra, băng thông của CF đủ lớn để tránh bị bottleneck về network. Ưu điểm lớn nhất là tận dụng dữ liệu detect từ hệ thống của Cloudflare cho một feature giá $0. Khuyên là ko nên xài Rate-Limit của CF, tiền sẽ ko đủ trả cho CF đâu. Ngoài ra nếu hệ thống bị đập dữ quá thì có thể nâng Security Level cho từng endpoint hoặc bật I’m Under Attacker để show 1 static site 5 giây trước khi request của attacker đi vào hệ thống, dạng slowing down giảm tải cho hệ thống.
- Implement một giải pháp rate-limit, cho phép custom số request per second/IP. Ví dụ login/register … thì rule khác, landing page thì rule khác … Nhưng giải pháp này dễ dàng bị by-pass bởi Resident IP, nhưng may mắn 1 xíu là Resident IP ko miễn phí (nhưng có 1 số service thì khá rẻ tầm $100/tháng), nên cách này chủ yếu chống script kidding, tại mục tiêu của script kidding là phá cho vui, nên nó sẽ nản và ko chịu trả tiền chỉ để phá.
- Implement giải pháp detect client IP là từ proxy, hosting, hoặc bad IP (có một số bộ dữ liệu cập nhật liên tục về một IP là bad hay good, có threat score để đánh giá), sau đó dựa trên threat score để block luôn IP đó. Tuy nhiên thường là các service dạng này phải trả tiền, hoặc bộ dữ liệu có sai số -> Dẫn tới dễ block nhầm IP của user thật. Và attacker vẫn có thể by-pass bằng cách dùng Resident IP.
- Gắn CAPTCHA/reCAPTCHA nếu detect request liên tục, nhưng vẫn có những service cung cấp giải pháp by-pass reCAPTCHA, nhưng may mắn là ko có gì miễn phí, attacker phải bỏ tiền nếu muốn by-pass (https://2captcha.com).
Các giải pháp khác không hẳn là về kỹ thuật nhưng tuỳ business như có thể giới hạn số user register trên IP, giới hạn user login dùng nhiều IP trong 1 khoảng thời gian (nhiều thằng nó login bằng resident IP thì phát hiện ra, ban/lock account luôn). Implement tính năng email verification/mobile verification. Nhưng 2 cái này vẫn by-pass đc bằng temp-email hoặc service dạng simthue.
Còn lâu dài thì chắc sẽ phải bỏ nhiều tiền ra để mua giải pháp hoặc tự dựng các giải pháp hỗ trợ. 

\#2
Bạn Minh có thể chia sẻ hiện tại lượng requests mà nghi ngờ là DDOS có độ lớn là bao nhiêu ko? (vài k/chục k requests/second?)
Random path tức là nó cào theo random path của website/system của bạn? nghĩa là nếu path đó tồn tại thì nó hit vào logic? ko tồn tại thì trả về 404 chẳng hạn? nếu vậy thì khi random path kiểu này thì phải có những thông tin login cần thiết như cookie hay session thì mới hit vào sâu bên trong logic đc, nếu ko thì thường bị chặn ở mức auth rồi
Mà thường những url ko cần auth thì có thể cache đc hết
tóm lại là mình vẫn ko hiểu những tấn công mà bạn nói ảnh hưởng tới hệ thống thế nào?
Mình trước cũng gặp vấn đề này, cũng may là lượng req ko lớn, loay hoay một hồi thì ra là có 1 cty họ cào data của mình, dùng nhiều instance khác nhau. Một số khác thì do mấy ông nghịch ngợm dùng bot đi chọc ngoáy vớ vẩn, tóm lại là ko đáng ngại
Như Quang nói ở trên, nếu họ muốn thu đc lợi từ việc ddos mình thì họ cũng phải trả cái giá lớn tương xứng. Để đến tầm bị DDOS theo quy mô lớn thì mình cũng phải là dạng có tầm cỡ rồi, lúc ấy vừa vui, cũng vừa lo :D
