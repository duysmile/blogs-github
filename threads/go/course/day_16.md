# Golang - Day 16

## Notes
- Khi tạo order, thì thêm vào nhiều bảng:
    - Cần cùng insert một lượt
    - Dùng 2 bảng nhưng cùng service nên có thể dùng 1 storage
    - Dùng associate của GORM hoặc tự xài transaction để insert
    - Bảng tracking -> pubsub, hoặc dùng chung con trỏ db
    - Research 2 phase commit

## Tools
Một số tools monitor: ELK, Grafana
1. Netdata
    - Theo dõi metric của hệ thống
    - Nhanh gọn lẹ
2. WRK GitHub:
    - Dùng để benchmark http
    - Cần xem request/s, cố gắng 300-500 req/s là rất đỉnh rồi
    - Một số tool khác để benchmark:
        - Tsung viết bằng Erlang
        - locust.io viết bằng Python
        - k6 dùng js
    - Một số trường hợp khi test, sau đó xem tracing, rồi xem monitor để kiểm tra và đối soát lại
3. Unit test trong golang
    - https://medium.com/rungo/unit-testing-made-easy-in-go-25077669318
    - TDD: nên viết tét trước rồi mới viết feature
4. Logging
    - Logrus: https://github.com/sirupsen/logrus
    - Nếu muốn log mang đi dùng nhiều nơi thì đặt trong app context
    - -> log ra json string -> hỗ trợ các tool thu log như log stash
    - Trong hệ thống thường có 7 level log: info, error, …
    - Always show -> xài log mặc định để Luan show ra biết trạng thái hệ thống
5. Papertrail -> Logging
    - Stream log
    - Có thể chạy docker
6. Microservices
    - Mesh -> service gọi nhau thông qua proxy, mỗi service có 1 proxy
    - Control plane
    - Data plane
