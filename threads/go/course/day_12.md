# Golang - Day 12

## Pub/sub
Là một messaging pattern -> sender gửi (publish) message và receiver sẽ nghe (subscribe)

Thay vì gửi trực tiếp thì sender sẽ gửi vào một message broker

Trong Golang có thể tạo một message queue với buffer channel

Pub/sub vs Queue Producer/Consumer

Publisher gửi message -> thì tất cả các subscriber của nó đều nhận

Con producer gửi job -> thì consumer sẽ lấy 1 job bất kì để xử lí, và job đó chỉ có 1 consumer lấy thôi

Trong hệ thống message broker hiện đại thường có thể mix cả hai thông qua message exchange
