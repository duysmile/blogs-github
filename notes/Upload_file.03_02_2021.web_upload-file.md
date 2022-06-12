# Upload file

Source: https://www.facebook.com/edu.200lab/posts/175306537706296

Upload File từ Front-End đến Back-End - Rất nhiều bạn vẫn đang làm sai!!
Upload file là một API (đối với back-end), một tính năng (với front-end) mà gần như app nào cũng có. Đây cũng là một tính năng nhạy cảm rất dễ làm sai đối với các anh em mới.
1. Client encode file (base64) rồi gởi về backend:
200Lab đã từng thấy nhiều bạn backend developer yêu cầu phía frontend làm như vậy. Cách này nhược điểm là xử lý rất nặng ở cả 2 phía. Vì frontend phải encode mà backend cũng phải decode.
Trong cách này nếu backend không decode để tái tạo lại file mà lưu hết base64 string vào DB sẽ là một sai lầm rất nghiêm trọng. Khi ấy DB chúng ta rất nặng vì chứa cả file vật lý. Khi truy xuất tốn CPU và băng thông rất lớn.
Rõ ràng đây không phải là một giải pháp tốt.
2. Client dùng API + Secret Key để thực hiện upload trực tiếp lên các Cloud Storage
Đây là một giải pháp nhanh gọn, đỡ phải qua backend, NHƯNG cực kì tệ. Lý do rất đơn giản là vì secret key dễ dàng bị leak chỉ với một thủ pháp đơn giản là… capture/debug request upload file.
Một khi secret key bị lộ, người tấn công có thể toàn quyền với key ấy. Nhẹ thì storage của chúng ta bị xài free. Nặng thì bị kéo toàn bộ file hoặc xoá toàn bộ file trên storage (nếu key có quyền xoá).
Nếu vậy thì làm sao cho ĐÚNG? Có 2 cách các bạn có thể tham khảo:
1. Upload trung gian qua backend:
Client upload file lên backend nhưng không cần thực hiện base64 encode. Chúng ta chỉ cần sử dụng multipart form data là ổn. Khi ấy backend nhận file xong có thể lựa chọn upload tiếp lên Cloud Storage hoặc một nơi nào đó bảo mật mà chỉ có backend biết. 
Như vậy client không cần secret key nên không sợ lộ nữa. Backend khi cần có thể chuyển đổi được dịch vụ Cloud nhanh chóng.
Tuy nhiên cách này có nhược điểm: 
- Client sẽ đợi lâu hơn đáng kể để nhận phản hồi từ backend. Vì backend phải upload lại qua Cloud.
- Băng thông tiêu thụ ở backend sẽ gấp đôi. VD nhận file 10MB, upload đi 10MB là 20MB băng thông I/O.
2. Client upload file lên Cloud Storage NHƯNG dùng Presigned URL từ backend.
Để giải quyết tốt hơn cho bài toán trên, các nhà điều hành Cloud Storage đã nghĩ ra một giải pháp để vừa có thể đảm bảo tính bảo mật và backend cũng không cần tốn băng thông. Đó là Presigned URL.
Presigned URL đại khái là một URL với các thông số bảo mật, cung cấp một permission tạm thời để đọc và tạo file trên Cloud. Để tạo thành công Presigned URL, chúng ta cần backend làm điều này. Một lần nữa, 200Lab nhấn mạnh và khuyên rằng chúng ta không nên làm việc này ở Client/Frontend.
Client có thể dùng URL này để upload file trực tiếp vào, như vậy backend không cần phải "trung chuyển" file nữa. Từ đó vấn đề băng thông được giải quyết. Resource được giải phóng. Hoá đơn tính tiền cũng nhẹ gánh rất đáng kể.
Hy vọng rằng với chia sẻ trên, các bạn backend sẽ làm API Upload file chuẩn chỉnh hơn.
Đối với các bạn frontend, nếu backend yêu cầu encode64 hoặc đưa mình secret key để tự đi upload file. Các bạn hãy chia sẻ bài này cho bạn ấy để chúng ta code vừa đúng vừa nhẹ đầu nhé ^^.

![Image](https://scontent.fdad1-1.fna.fbcdn.net/v/t1.0-9/143241313_175306007706349_814646044867664083_n.png?_nc_cat=105&ccb=2&_nc_sid=730e14&_nc_ohc=j697QCCm-68AX_ZtVrz&_nc_ht=scontent.fdad1-1.fna&oh=0bdfeef31475c0b84fc3a112774ba266&oe=60429C86)
