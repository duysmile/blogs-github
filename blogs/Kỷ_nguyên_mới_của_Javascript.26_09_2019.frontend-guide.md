# Front-end Guide from Grab

## Kỷ nguyên mới của Javascript

Trước khi đào sâu vào các khía cạnh nhau của việc xây dựng một web app, thì điều quan trọng đầu tiên là làm quen với Javascript, hoặc ECMAScript. JS là một ngôn ngữ vô cùng linh hoạt, bạn có thể dùng nó để xây dựng web servers, native mobile apps và desktop apps.

Trước năm 2015, bản cập nhật lớn cuối cùng được cập nhật là ECMAScript 5.1, ở năm 2011. Tuy nhiên, trong những năm gần đây, JS đã có những bước chuyển lớn trong một khoảng thời gian ngắn. Năm 2015, ECMAScript 2015(còn gọi là ES6) được released và hàng tá những cú pháp mới được giới thiệu để việc viết code dễ dàng hơn. Nếu bạn tò mò về nó, thì `Auth0` có một bài viết hay về lịch sử của JS. Đến ngày nay, không phải tất cả các browsers đều hỗ trợ đầy đủ ES6. Những công cụ như `Babel` cho phép developers viết ES6 trong app của họ và dùng Babel để chuyển chúng sang ES5 để tương thích với browsers.

Làm quen với cả ES5 và ES6 là rất quan trọng. ES6 vẫn còn khá ới và nhiều open-source và cả NodeJS app vẫn viết bằng ES5. Nếu bạn đang debug trên browser console, bạn có thể k sử dụng được cú pháp ES6. Mặt khác, tài liệu và example code cho những thư viện hiện đại vẫn được viết bằng ES6. Tại Grab, họ dùng babel-preset-env để cảm nhận việc tăng năng suất từ các cú pháp đã được cải tiến mà JS cung cấp. `babel-preset-env` xác định rất thông minh những plugin babel nào là cần thiết (những tính năng mới mà chưa được hỗ trợ và cần được dịch). Nếu bạn tích sử dụng các tính năng đã ổn định (stable), bạn có thể tìm package `babel-preset-stage-3`.

Dành một hoặc 2 ngày để xem lại ES5 và ES6. Những tính năng chính trong ES6 bao gồm `Arrows and Lexical This`, `Classes`, `Template strings`, `Destructuring`, `Default/Rest/Spread operators`, và `Importing/Exporting Modules`.

Thời gian dự kiến 3-4 ngày. Bạn có thể học, tìm những cú pháp khi học các thư viện khác và thử xây dựng ứng dụng riêng của bạn.











