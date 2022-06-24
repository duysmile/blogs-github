# Golang -Day10

## Tips
Kafka - Java - distributed broker so vs Rabbit - Erlang
- Kafka: Stable, tốn RAM hơn, scale ngang hỗ trợ tốt hơn, dump message broker
- Kafka có nhiều tính năng hơn, hỗ trợ offset, channel (topic) có thể nằm trên nhiều máy chủ
- Nếu muốn microservice event sourcing, pub/sub system thì xài Kafka sẽ stable hơn.
- Rabbit thiên về queue hơn và nhẹ hơn, smart message broker, nếu muốn setting chỉ queue

Enterprise
- Đánh đổi scale và data consistency, ưu tiên security
- Xài enterprise DB - rất reliable
- DB phải đảm bảo CAP

Elasticsearch
- Fulltext indexes search DB
- Dùng để tìm kiếm đặc biệt về fulltext indexes
- Kết quả phụ thuộc vào cách implement, ko thể xài DB chính

Context trong Go:
- Dùng để cancel một goroutine giúp k bị leak
- Khi gắn vào tracing -> vì context nó đi theo dạng cây, giúp việc trace dễ hơn, lấy đc toàn bộ đường đi.

Load test hệ thống:
- Ví dụ chat - writing speed
- App bán hàng - tốc độ done transaction
- CPU - Disk - RAM - Bandwidth

Nonblocking:
- Dùng để offload, chạy async call để thực hiện một cái side effect, k block main stream

JSON xài convention là snake_case

Sqlx xài để tối ưu hơn, nhanh nhất là xài nó để viết system

## CV:
- Ghi kinh nghiệm từ mới tới cũ
- Chú ý GitHub, blog trong information
- Title vị try apply vào là gì
- Phần kinh nghiệm và summary về skill, ưu điểm đưa lên đầu tiên
- Ghi rõ tech để làm gì, project làm gì
- Ghi tech stack mình xài
- Ghi experience thì ghi rõ từng mục: role, team size
- Ghi định hướng short term, long term
