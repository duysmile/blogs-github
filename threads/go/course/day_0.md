# Golang - Day0

## Mindset cho Backend developer
Mindset: tối ưu, tăng tải

## Tại sao chọn Go?
- Golang - ngôn ngữ native - cận C -> rất mạnh về system
- Golang - ngôn ngữ complie, static typed, nonblocking mạnh, xài được full core full luồng, xử lí binary rất mạnh - music, image.
- Build và compile rất nhanh
- Golang không có OOP
- Hỗ trợ lập trình concurrent

## Cần học gì để sử dụng được Go cho project tiếp theo?
1. Nắm vững Golang, viết API, hệ thống tải cao với Golang
2. Github profile với Golang repo
3. Code structure, system structure với backend Golang

Nội dung:
- Làm được REST API
- Giao tiếp module, service
- PubSub với Channel Golang, Async Job
- Tối ưu DB
- SocketIO vs Golang
- Logging, Tracing

Chuẩn bị:
- https://golang.org
- https://docs.docker.com/get-docker/
- https://edu.200lab.io/blog/tim-hieu-ngon-ngu-lap-trinh-golang

Tài liệu:
- Gopher conferences youtube - workshop về Golang

Khi nào nên dùng REST, khi nào dùng gRPC:
- REST: có thể dùng mọi th
- gRPC: chỉ để giao tiếp server - server, client - server thì ko nên, hỗ trợ cơ chế streaming.

Library:
- Oauth cho Golang - fosite
- Gogin - framework
- Echo - build rest api

Phân loại request:
- Query request: truy vấn - gọi qua và cần dữ liệu về, call and wait
- Transactional request: event, discoupling

Tại sao nên dùng MySQL:
- MySQL rất lợi cho online transactional - Vitess to scale MySQL

Một số tips:
- Capture data change -> nếu data lớn cần luân chuyển
- Token -> nên lưu theo kiểu hash (signature - đuôi của token), hoặc lưu token Id