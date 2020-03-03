# Hệ điều hành hoạt động như thế nào?

Reference: https://medium.com/cracking-the-data-science-interview/how-operating-systems-work-10-concepts-you-should-know-as-a-developer-8d63bb38331f

Bạn có nói được nhị phân không nhỉ? Bạn có hiểu được mã máy không? Nếu đưa bạn một bảng toàn là 0, 1 bạn có thể hiểu được ý nghĩa của nó không? Nếu bạn đi đến một đất nước mà bạn chưa từng được nghe ngôn ngữ của họ, hoặc là có nghe đến nhưng bạn không thực sự nói được, vậy bạn sẽ cần gì để giúp bạn giao tiếp với người bản địa đây?

Bạn sẽ cần một thông dịch viên. Chức năng của hệ điều hành giống như một thông dịch viên trong máy tính vậy. Nó chuyển những chuỗi giá trị 0/1, yes/no, và on/off sang ngôn ngữ có thể đọc để bạn hiểu được. Nó là tất cả việc này trong một luồng giao diện người dùng, hay GUI, mà bạn có thể di chuyển với chuột, click, và nhìn mọi thứ diễn ra ngay trước mắt.

Tùy vào phạm vị và độ sâu kiến thức, biết càng nhiều về các nguyên tắc cơ bản sẽ rất quan trọng trong việc làm cho chương trình, cấu trúc và flow của họ hoạt động tốt.

Tại sao? Khi bạn viết một chương trình và nó chạy chậm, bạn thấy code không có gì sai, vậy bạn tìm giải pháp ở đâu đây? Làm sao bạn debug được vấn đề nếu bạn không biết được cách mà hệ điều hành hoạt động? Bạn có đang truy cập quá nhiều file? Bộ nhớ có bị cạn kiệt và swap có đang trong trạng thái high usage? Nhưng bạn thậm chí còn không biết swap là gì? Hay là I/O bị blocking?

Và bạn muốn giao tiếp với máy khác. Làm sao bạn làm được qua local hay qua Internet? Sự khác nhau là gì? Tại sao những lập trình viên lại thích OS này hơn cái còn lại?

Trong quá trình trở thành một developer xịn sò, tôi đã biết đến khóa học của Georgia Tech [Introduction to Operating Systems](https://www.udacity.com/course/introduction-to-operating-systems--ud923). Khóa này dạy những abstractions, cơ chế, và những implementations căn bản của OS. Cốt lõi của khóa học này là phần lập trình đồng thời (concurrent programming) (threads và synchronization), giao tiếp inter-process, và một phần giới thiệu về distributed OSs.

Tôi muốn dùng bài viết này để chia sẻ những bài học từ khóa học này, đây là 10 khái niệm quan trọng trong hệ điều hành bạn cần học nếu muốn phát triển phần mềm tốt hơn.

## Hệ điều hành là gì?

Đầu tiên, hãy xác định hệ điều hành là gì đã. Một Hệ điều hành (Operating System) là một tập các phần mềm để quản lí phần cứng máy tính và cung cấp những services cho chương trình. Đặc biệt, nó làm ẩn đi những sự phức tạp của phần cứng, quản lí tài nguyên tính toán (computational resources) và cung cấp sự cô lập(isolation) và bảo vệ(proctection). Quan trọng nhất, nó có quyền truy cập vào tầng dưới phần cứng trực tiếp.

Những thành phần chính của một hệ điều hành là file system, scheduler, và device driver. Bạn có thể là đã dùng cả hệ điều hành Desktop (Windows, Mac, Linux) và Embedded (Android, iOS) trước đây.

































