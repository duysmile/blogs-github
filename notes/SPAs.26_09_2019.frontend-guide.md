# Front-end Guide from Grab

## Single-page Apps (SPAs)
Ngaỳ nay khi phát triển sản phẩm thì các web developers thường hướng tới việc xây dựng web apps thay vì websites. Thực ra thì không có sự khác biết quá nhiều trong hai khái niệm này, chủ yếu là web app thì có tính tương tác và linh hoạt hơn, cho phép người dùng tương tác và nhận phản hồi. Còn như các websites truyền thống thì browser sẽ nhận HTML từ server và render nó. Khi người dùng điều hướng sang URL khác thì cả trang sẽ tải lại server phải trả lại HTML mới tương ứng. Cái này gọi là server-side rendering.

Tuy nhiên đối vs SPAs, thì client-side rendering được sử dụng. Browser tải trang khởi tạo từ server (initial page), kèm theo là các scripts (framework, libraries, app code) và stylesheets của app. Khi người dùng điều hướng sang page khác thì page sẽ không tải lại. URL của page được cập nhật lại thông qua HTML5 History API. Data cuả page mới được tải sẽ được lấy thông qua ẠJAX thường là dưới dạng JSON từ server. SPA cập nhật động page với dữ liệu thông qua Javascript đã được load trong trang khởi tạo (initial page). Và mô hình này hoạt động tương tự với nhưng native mobile app.

Lợi ích: 
- App sẽ mượt hơn và người dùng sẽ không thấy việc chuyển trang của việc tải lại toàn bộ trang.
- Số lượng HTTP request tới server sẽ ít hơn, và những assets giống nhau sẽ không cần phải tải lại khi load page.
- Làm cho client và server phân định rõ ràng hơn; bạn có thể dễ dàng xây dựng những clients mới cho những nền tảng khác nhau như mobile, chatbots, smart watches mà không cần thay đổi gì ở server. Bạn cũng có thể thay đổi technology stack ở client và server hoàn toàn độc lập, miễn là các liên kết APIs không bị phá vỡ.

Nhược điểm:
- Làm cho việc tải trang khởi tạo nặng hơn vì phải load nhiều thứ hơn, nào là framework, app code và những assets liên quan để phục vụ cho những page khác nữa.
- Có thêm một bước nữa cần phải thực hiện ở server là phải cấu hình tất cả requests tới tới một entry point duy nhất và cho phép client-side routing tự xử phần còn lại.
- SPAs dựa vào Javascript để render nội dung, nhưng không phải tất cả các search engine đều thực thi Javascript trong quá trình crawl, và chúng có thể thâý trang web của bạn trống không. Điêù này làm cho việc SEO(Search Engine Optimization) kém hơn trên app của bạn. Tuy nhiên, hầu hết thời gian khi bạn xây dựng apps, SEO không phải là yếu tố quan trọng nhất, bởi vì không phải tất cả nội dung của trang đều cần được đánh index bởi search engine. Để giải quyết điều này thì bạn có thể server-side render app hoặc là sử dụng những service như `Prerender` để "render javascript trên browser, lưu lại file HTML tĩnh và trả lại cho những con bọ (crawlers)".

Trong khi những app server-side render vẫn còn tồn tại, thì việc tách biệt client-server sẽ mở rộng dễ hơn cho những team lớn hơn, vì client và server code có thể phát triển và triển khai độc lập. Điều này đặc biệt đúng với Grab khi mà họ có nhiều ứng dụng cùng truy cập tới một máy chủ.

Vì những web developer đang xây dựng apps thay vì pages, việc tổ chức Javascript ở client-side ngày càng trở nên quan trọng. Ở những trang được render server-side, người ta thường sử dụng những đoạn code jQuery để tăng tính tương tác của mỗi page. Tuy nhiên, khi xây dựng những app lớn, thì chỉ jQuery thôi thì không đủ. Sau tất cả thì jQuery là một thư viện chủ yếu để thao tác với DOM và nó không phải là một framework, nó ko định nghĩa một cấu trúc rõ ràng, tổ chức app của bạn.

Những Javascript framework được tạo ra nhằm cung cấp higher-level abstractions hơn DOM, nó cho phép bạn giữ state trong memory, nằm ngoài DOM. Sử dụng những framework đem lại những lợi ích trong việc tái sử dụng và áp dụng các best practices để xây dựng app. Một member mới dù không thân thuộc với code base nhưng có hiểu biết về framework thì cùng có thể dễ dàng hiểu code bởi vì code được tổ chức theo cấu trúc đã quen thuộc với họ. Những framework phổ biến thì có nhiều hướng dẫn, và điều này làm cho việc tiếp cận của những người mới sẽ nhanh hơn.

