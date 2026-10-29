# Read/Write file in Golang

Lí do bài viết này ra đời là do mình có dịp làm một OSS liên quan đến việc xử lí I/O, ở đây cụ thể là đọc/ghi file.

Có rất nhiều cách tiếp cận từ đơn giản đến ít đơn giản hơn.

Đầu tiên là cách mà mọi người hay dùng để "cho nhanh". Đó là load hết nội dung file lên memory rồi xử lí trên đó. Việc này rất đơn giản, vì gần như ngôn ngữ server nào cũng cần phải có những standard lib để xử lí I/O.
```go
func main() {
	content, _ := os.ReadFile("input.txt")
	// Xử lí nội dung file nằm trong biến `content`
	...
	os.WriteFile("output.txt", content, 0644)
}
```

Ok, done!!! Vậy là xong, vấn đề đã được giải quyết quá gọn gàng 🌱

Ủa alo, vậy thì bài viết này có mục đích gì? À thật ra không phải cách nào nhanh cũng tốt, nên hãy bình tĩnh xem lại cách trên có vấn đề gì nào.

...

Nghĩ ra chưa? Đó chính là việc load hết nội dung file vào memory, vậy nếu memory của bạn không đủ thì sao?

Nếu như nội dung file lớn hơn lượng memory còn trống thì Go process của bạn sẽ bị crash (out of memory) khi mà bạn còn chưa kịp làm gì cả.

Rồi giờ làm sao đây, không đọc được thì sao ghi vào được?

Và đương nhiên, chuyện đâu còn có đó, ông cha ta đã gặp những vấn đề này rồi và cũng đã có cách giải quyết từ lâu rồi, chúng ta chỉ cần học theo thôi.

Bây giờ, thay vì đọc hết tất cả nội dung, thì chúng ta sẽ đọc từ từ, mỗi lần một ít, rồi ghi vào output, cho đến khi đọc hết input. Và standard lib cũng đã cung cấp những thứ cần thiết để chúng ta làm việc này
```go
func main() {
	input, err := os.Open("input.txt")
	...
	output, err := os.Create("output.txt")
	...

	// đọc nhiều nhất 1024 bytes một lần
	buf := make([]byte, 1024)
	for {
		// n: số byte đọc được
		n, err := input.Read(buf)
		...
		if err == io.EOF {
			break
		}
		...
		err := output.Write(buf[:n])
		...
	}
}
```

Yah, bây giờ thì chương trình của chúng ta có thể chạy trơn tru ngay cả với file "lớn" nhưng ...

vẫn còn một vấn đề ẩn giấu trong những dòng code trên, đó là việc chúng ta phải gọi rất nhiều system calls (I/O) để tương tác với file (read/write)

Đó là lí do mà `bufio` ra đời, nói một cách đơn giản thì bufio là một wrapper với mục đích đưa buffer I/O để tăng performance. Ví dụ như thay vì phải đọc nhiều lần, mỗi lần thì phải gọi 1 system call, thì giờ chúng ta sẽ đọc batches một lần, rồi lưu vào buffer sau đó xử lí với những gì đã được ghi trong buffer, tương tự với việc write, thay vì ghi nhiều lần vào file thì chúng ta sẽ ghi vào buffer, đến khi buffer đầy thì đẩy vào file. Với cách này thì số lần gọi system call đã giảm đi rất nhiều và performance của chương trình cũng sẽ được cải thiện đáng kể.
```go
func main() {
	input, err := os.Open("input.txt")
	bInput := bufio.NewReader(input)
	...
	output, err := os.Create("output.txt")
	bOutput := bufio.NewWriter(output)
	...

	// đọc nhiều nhất 1024 bytes một lần
	buf := make([]byte, 1024)
	for {
		// n: số byte đọc được
		n, err := bInput.Read(buf)
		...
		if err == io.EOF {
			break
		}
		...
		err := bOutput.Write(buf[:n])
		...
	}

	// đoạn này dùng để đẩy những gì còn lại trong buffer vào file
	err := bOutput.Flush()
	...
}
```

Rồi xong, mình mới chỉ hiểu biết được tới đây và muốn chia sẻ tới mọi người, nếu có gì mới mình sẽ làm bài khác! Byte!

## References:
- https://stackoverflow.com/questions/1821811/how-to-read-write-from-to-a-file-using-go
- https://go101.org/article/memory-block.html
- https://www.educative.io/answers/how-to-read-and-write-with-golang-bufio
