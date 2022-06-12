# The Twelve-Factor App - Codebase

- Codebase (mã nguồn) được kiểm soát trong hệ thống kiểm soát phiên bản (revision control), qua nhiều lần deploy
- Một ứng dụng 12-factor luôn được kiểm soát trong hệ thống quản lí phiên bản như Git, Mercurial, hoặc Subversion. Một bản sao của cơ sơ dữ liệu theo dõi phiên bản được gọi là code repository hay gọi tắt là repo.
- Một codebase là một repo bất kì (trong hệ thống tập trung như Subversion), hay một tập các repos chia sẻ chung root commit (trong hệ thống phân quyền như Git).
- Luôn có một mối tương quan 1-1 giữa codebase và ứng dụng:
    - Nếu có nhiều codebase, thì đó ko phải là 1 ứng dụng - nó là một hệ thống phân tán (distributed system). Mỗi thành phần trong hệ thống phân tán chính là 1 ứng dụng, và mỗi ứng dụng đều nên tuân thủ 12 yếu tố.
    - Nhiều ứng dụng dùng chung code chính là không tuân thủ 12 yếu tố.  Giải pháp ở đây chính là đưa những phần code dùng chung thành những thư viện có thể được sử dụng dễ dàng trong ứng dụng và quản lí thông qua dependency manager.

- Chỉ có một code cho một ứng dụng, những sẽ có nhiều phiên bản deploy của ứng dụng. Một bản deploy là một instance thực thi của ứng dụng. Có thể là phiên bản production hoặc là staging. Hơn nữa, còn có những phiên bản chạy ở môi trường development ở nội bộ trên máy của developer, tất cả chúng đều là những phiên bản deploy.
- Codebase giống nhau trong tất cả các phiên bản deploy, mặc dù version khác nhau có thể hoạt động trong mỗi lần deploy. Ví dụ, một developer có vài commit chưa được deploy lên staging, staging có một vài commit chưa deploy lên production. Nhưng tất cả đều dùng chung một codebase, do đó có thể xem là những lần deploy khác nhau của cùng một ứng dụng.
