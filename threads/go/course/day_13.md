# Golang - Day 13

## Websocket:
- Init connection -> Handshake -> Connection opened -> Bi-directional messages
- Stateful
- Phải maintain connection này -> điểm yếu của websocket
- Cần realtime, streaming mới cần keep connection
- Ở dạng khác thì đừng lạm dụng Websocket -> khó scale
- Websocket  paper -> research them
- go-socket.io: https://github.com/googollee/go-socket.io -> v1.4.4
- Namespace: client quyết định
- Room: server quyết định
- go get github.com/googollee/go-socket.io@v1.4.4
- Transports: long polling, websocket
- Ép về transport websocket luôn
- Bản chất socketIO nó cần mượn 1 đường HTTP để upgrade protocol
- Bản chất dữ liệu truyền nhận là mảng byte
- Engine: bộ giao tiếp để liên kết
- Đặt realtime engine ở subscriber
- Xài db chuyên dụng cho việc search lat, lng -> ok hơn cho việc tìm kiếm
- Có thể mượn firebase để xài key realtime để scale realtime, tạo pub, sub free
