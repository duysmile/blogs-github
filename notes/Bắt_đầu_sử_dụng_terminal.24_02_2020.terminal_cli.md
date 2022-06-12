# Làm thế nào để bắt đầu sử dụng Terminal

Reference: https://www.howtogeek.com/140679/beginner-geek-how-to-start-using-the-linux-terminal/

Nếu bạn là một người mới dùng Linux hay đã dùng biết một chút về Linux, chúng tôi sẽ giúp bạn bắt đầu với terminal. Terminal không phải là một thứ gì đó mà bạn phải lo sợ - nó là một công cụ tuyệt với với rất nhiều công dụng.

Bạn không thể học tất cả mọi thử bạn cần biết về terminal chỉ bằng việc đọc một bài viết được. Đầu tiên là cần phải có kinh nghiệm vọc vạch với terminal đã. Chúng tôi hi vọng chỉ dẫn này giúp bạn nắm được những cơ bản để bạn có thể tiếp tục học được nhiều hơn.

## Sử dụng Terminal căn bản

Mở một terminal từ menu ứng dụng desktop (hoặc với tổ hợp phím Ctrl + Alt + T) và bạn sẽ thấy bash shell. Có những loại shell khác, nhưng hầu hết những bản Linux dùng bash làm mặc định.

Bạn có thể chạy một chương trình bằng cách gỡ tên của nó ở prompt. Mọi thứ bạn chạy ở đây - từ các ứng dụng có giao diện như Firefox đến các công cụ dòng lệnh - là một chương trình.(Bash thực sự có một vài lệnh được xây dựng sẵn cho việc quản lí file cơ bản, nhưng những chức năng đó thì cúng giống như những chương trình). Không giống như trên Windows, bạn không cần phải gõ đường dẫn đầy đủ đến chương trình muốn chạy. Ví dụ, hãy nói bạn muốn mở Firefox. Trên Windows, bạn cần phải gõ đường dẫn tuyệt đối đến file .exe của Firefox. Trên Linux, bạn chỉ cần gõ:
> firefox

![firefox](https://www.howtogeek.com/wp-content/uploads/2013/03/xopen-firefox-from-terminal.png.pagespeed.gp+jp+jw+pj+ws+js+rj+rp+rw+ri+cp+md.ic.TWNsPZLClP.png)

Nhấn Enter sau khi gõ lệnh để chạy nó. Lưu ý rằng bạn không cần phải thêm một đuôi .exe hay bất cứ gì tương tự - chương trình không có file extensions trên Linux.

Những lệnh terminal cũng có thể nhận các đối số. Những loại đối số bạn có thể dùng phụ thuộc vào chương trình. Ví dụ, Firefox chấp nhận những địa chỉ web là đối số. Để chạy Firefox và mở trang How-to-Geek, bạn có thể chạy lệnh sau:
> firefox howtogeek.com

Những lệnh khác bạn cũng có thể sẽ chạy trong terminal function như Firefox, ngoại trừ nhiều lệnh chỉ chạy trên terminal và không mở bất cứ cửa sổ giao diện nào.

## Cài đặt phần mềm

Một trong những việc hiệu quả nhất được làm từ terminal đó chính là cài đặt phần mềm. Những ứng dụng quản lí phần mềm như Ubuntu Software Center có giao diện đẹp với một vài lệnh terminal dùng ở background. Thay vì click xung quanh và chọn những ứng dụng từng cái một, bạn có thể cài chúng với một câu lệnh terminal. Bạn thậm chí có thể cài nhiều ứng dụng chỉ với một câu lệnh duy nhất.

Trên Ubuntu (những bản Linux khác thì có hệ thống quản lí package khác của riêng chúng), lệnh để cài đặt một chương trình mới là:
> sudo apt-get install packagename

Có vẻ phức tạp hơn một chút, nhưng nó hoạt động giống như lệnh Firefox ở trên. Dòng ở trên chạy **sudo**, sẽ hỏi bạn mật khẩu trước khi chạy apt-get với quyền root (administrator). Chương trình apt-get đọc đối số **install packagename** và cài đặt một package tên là **packagename**.

Tuy nhiên, bạn cũng có thể chỉ định nhiều packages làm đối số. Ví dụ, để cài đặt trình duyệt Chromium và Pidgin instant messenger, bạn có thể thực thi lệnh này:
> sudo apt-get install chromium-browser pidgin.

Nếu bạn chỉ cài Ubuntu và muốn cài tất cả các phần mềm ưa thích của mình, bạn có thể làm việc này chỉ với một dòng lệnh như ở trên. Bạn chỉ cần biết tên của package của phần mềm muốn cài, bạn có thể đoán tên chúng khá dễ dàng. Bạn cũng có thể hoàn thành việc đoán bằng cách dùng tab như hướng dẫn ở dưới.

Để tìm hiểu kĩ hơn, có thể đọc thêm ở [đây]https://www.howtogeek.com/63997/how-to-install-programs-in-ubuntu-in-the-command-line/).

## Làm việc với directories và files

Shell sẽ nhìn vào trong thư mục hiện tại của bạn nếu bạn không chỉ định rõ ràng một thư mục khác. Ví dụ, [nano](https://www.howtogeek.com/howto/42980/the-beginners-guide-to-nano-the-linux-command-line-text-editor/) là một text-editor để sử dụng trên terminal. Lệnh **nano document1** sẽ yêu cầu **nano** chạy và mở file tên là **document1** từ thư mục hiện tại của bạn. Nếu bạn muốn mở một thư mục ở thư mục khác, bạn cần phải chỉ định đường dẫn tuyệt đối tới file đó - ví dụ, **nano /home/chris/Documents/document1**.

Nếu bạn chỉ định đường dẫn tới một file không tồn tại, nano (và nhiều phần mềm khác) sẽ tạo mới một file rỗng đến vị trí đó và mở nó.

Để làm việc với file và directories bạn cần phải biết một vài lệnh cơ bản sau:
- **cd** - **~** ở bên trái prompt thể hiện home directory của bạn (đó là /home/you), là thư mục mặc định của terminal. Để thay đổi một thư mục khác, bạn có thể dùng lệnh **cd**. Ví dụ **cd /** sẽ thay đổi sang thư mục root, **cd Downloads** sẽ thay đổi sang thư mục Download bên trong thư mục hiện tại (do đó chỉ mở thư mục Download nếu terminal ở trong thư mục home), **cd /home/you/Downloads** sẽ chuyển sang thư mục Downloads từ bất cứ đây trong hệ thống, **cd ~** sẽ chuyển đến thư mục home, và **cd ..** sẽ đi ra ngoài một thư mục.

- **ls** - lệnh **ls** sẽ liệt kê ra những file trong thư mục hiện tại.

- **mkdir** - lệnh này tạo ra một thư mục mới. Ví dụ **mkdir example** sẽ tạo một thư mục mới trong thư mục hiện tại, trong khi **mkdir /home/you/Downloads/test** sẽ tạo một thư mục mới tên test trong thư mục Downloads của bạn.

- **rm** - lệnh dùng để xóa file. Ví dụ **rm example** sẽ xáo file tên example trong thư mục hiện tại và lệnh **rm /home/you/Downloads/example** xóa file tên example trong thư mục Downloads.

- **cp** - Lệnh cp để copy một file từ vị trí này sang chỗ khác. Ví dụ, **cp example /home/you/Downloads** sẽ copy file tên là example trong thư mục hiện tại sang /home/you/Downloads.

- **mv** - lệnh này dùng để di chuyển một file từ vị trí này sang vị trí khác. Nó hoạt động giống như lệnh copy ở trên, nhưng là di chuyển file luôn chứ không phải tạo một bản sao như trên. mv có thể dùng để đổi tên file. Ví dụ, **mv original renamed** sẽ thay đổi tên file tên original sang renamed.

## Tab Completion

Tab completion là một trick hữu ích. Trong khi gõ vài thứ - một lệnh, tên file, hay một số loại đối số - bạn có thể dùng Tab để autocomplete cái bạn vừa gõ. Ví dụ, nếu bạn gõ *firef* trên terminal và gõ Tab, *firefox* sẽ tự động xuất hiện. Nó giúp bạn thuận tiện và tiết kiệm hơn để gõ chính xác - bạn có thể nhấn Tab và shell sẽ hoàn thành việc gõ cho bạn. Nó cũng hoạt động với folders, file names, và package names. Ví dụ bạn gõ *sudo apt-get install pidg* và nhấn Tab để tự động complete **pidgin**.

Trong nhiều trường hợp, shell sẽ không biết bạn đang cố gõ gì bởi vì có nhiều cái thích hợp quá. Nhấn Tab lần thứ 2 bạn sẽ thấy danh sách những possible matches. Tiếp tuc gõ một vài kí tự nữa và gõ Tab để tiếp tục.

Để biết thêm nhiều tricks nữa thì có thể tham khảo ở [đây](https://www.howtogeek.com/110150/become-a-linux-terminal-power-user-with-these-8-tricks/).

-----------------------
