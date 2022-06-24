# Golang - Day9

Link user vào restaurant:
- Xài Preload của gorm
- Mỗi lần preload thì gọi thêm 1 query
- Xài db.Debug() để bật debug mode cho gorm
- Link data micro services -> Tạo repository
- Nhiệm vụ của repo là đi tổng hợp dữ liệu
- Dùng go `resty` lib để call API hoặc xài native
- Đặt transport ở nơi lấy dữ liệu gốc
- Format date lại theo RFC standard khi query trong sql
