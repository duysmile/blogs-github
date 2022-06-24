# Golang - Day 11

## Hệ thống Pub/sub
Đối với những compute field, để đáp ứng với lượng data lớn thì ta lưu dữ liệu các field này lại trong db luôn, và dùng pub/sub để cập nhật

Nếu update computed field có vấn đề thì có thể chạy 1 con cronjob rồi chạy sql join bảng vs count group by restaurant id, chớ k chạy for qua rồi update là chịu ko nổi

Xài goroutine để offload cái phần update lại field like_count cho restaurant

## Other
Geo fencing -> là gì (dùng trong việc tính toán vị trí trong mấy app giao hàng)

Service mesh -> trong micro service sẽ kết nối các service bằng các proxy tạo thành 1 mạng lưới (mesh) để khi service die thì sẽ còn khả năng điều hướng

Envoy

SRE - site reliability engineer