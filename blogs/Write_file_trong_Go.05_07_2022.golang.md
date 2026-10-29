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

Đó là lí do mà `bufio` ra đời. Nói đơn giản, `bufio` là một lớp bọc (wrapper) quanh `io.Reader`/`io.Writer`, thêm vào giữa một **buffer trong memory** để giảm số lần phải gọi system call.

Vì sao system call lại "đắt"? Mỗi lần `input.Read(...)` hay `output.Write(...)` trên `*os.File`, chương trình phải chuyển từ user space sang kernel space, kernel làm việc rồi trả kết quả về. Bản thân việc copy vài KB thì nhanh, nhưng chi phí chuyển qua lại này lặp đi lặp lại hàng nghìn lần thì cộng dồn lên đáng kể.

`bufio` xử lí như sau:

- **`bufio.Reader`** giữ một buffer nội bộ (mặc định 4096 bytes). Lần `Read` đầu tiên, nó gọi xuống file **một lần** để lấp đầy buffer (tối đa 4096 bytes), rồi trả cho bạn phần bạn cần. Những lần `Read` tiếp theo được lấy thẳng từ buffer trong memory, không cần system call, cho tới khi buffer cạn thì mới đọc tiếp từ file.
- **`bufio.Writer`** thì ngược lại: mỗi lần `Write` chỉ copy dữ liệu vào buffer. Khi buffer đầy (hoặc khi bạn gọi `Flush`), nó mới ghi **một lần** xuống file.

Ví dụ với file 1 MB và `buf` 1024 bytes như ở trên: không có `bufio` thì cần khoảng 1024 lần read + 1024 lần write = ~2048 system calls. Có `bufio` (buffer 4096) thì chỉ còn khoảng 256 + 256 = ~512, giảm 4 lần.

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

### Khi nào `bufio` thật sự đáng dùng?

Ví dụ ở trên chưa phải chỗ `bufio` toả sáng nhất, vì mình vẫn tự đọc từng khối 1024 bytes. `bufio` phát huy tác dụng rõ nhất khi bạn làm **rất nhiều thao tác nhỏ**, ví dụ đọc từng dòng:

```go
scanner := bufio.NewScanner(input)
for scanner.Scan() {
	line := scanner.Text() // mỗi dòng lấy từ buffer, không phải mỗi dòng một system call
	...
}
if err := scanner.Err(); err != nil {
	...
}
```

hoặc ghi rất nhiều dòng ngắn:

```go
w := bufio.NewWriter(output)
for _, r := range records {
	fmt.Fprintf(w, "%s,%d\n", r.Name, r.Value) // gom vào buffer
}
if err := w.Flush(); err != nil { // ghi phần còn lại xuống file
	...
}
```

Nếu không có `bufio`, mỗi dòng có thể là một system call riêng. Với file vài triệu dòng thì khác biệt là rất lớn.

### Vài điều cần nhớ

- **Luôn gọi `Flush()` và kiểm tra lỗi của nó.** Dữ liệu còn nằm trong buffer sẽ bị mất nếu chương trình kết thúc mà chưa flush. Lỗi ghi file (ví dụ hết dung lượng đĩa) cũng thường chỉ lộ ra ở lần `Flush` cuối.
- **Buffer của bạn ≥ buffer của `bufio` thì `bufio` gần như không giúp gì.** Khi bạn `Read` với slice lớn hơn hoặc bằng buffer nội bộ (và buffer đang rỗng), `bufio.Reader` đọc thẳng vào slice của bạn, bỏ qua buffer; `bufio.Writer` cũng vậy với lần ghi lớn. Cần buffer to hơn thì dùng `bufio.NewReaderSize` / `bufio.NewWriterSize`.
- **`bufio.Scanner` mặc định giới hạn mỗi token (mỗi dòng) 64 KB.** Dòng dài hơn sẽ báo lỗi `bufio.ErrTooLong`; khi đó dùng `scanner.Buffer(...)` để tăng giới hạn, hoặc dùng `Reader.ReadString('\n')`.
- **`bufio.Writer` không an toàn khi nhiều goroutine cùng ghi.** Cần thì tự thêm mutex.

### Chỉ copy file thì sao?

Nếu mục đích chỉ là copy nguyên file từ A sang B, cách gọn nhất là:

```go
_, err := io.Copy(output, input)
```

`io.Copy` tự dùng buffer 32 KB, và khi cả hai đầu đều là `*os.File` trên Linux, Go có thể giao luôn việc copy cho kernel (`copy_file_range`/`splice`), dữ liệu không cần đi qua chương trình của bạn. `bufio` vẫn là lựa chọn đúng khi bạn cần **xử lí** dữ liệu: đọc theo dòng, parse, biến đổi rồi mới ghi.

Rồi xong, mình mới chỉ hiểu biết được tới đây và muốn chia sẻ tới mọi người, nếu có gì mới mình sẽ làm bài khác! Byte!

## References:
- https://stackoverflow.com/questions/1821811/how-to-read-write-from-to-a-file-using-go
- https://go101.org/article/memory-block.html
- https://www.educative.io/answers/how-to-read-and-write-with-golang-bufio
- https://pkg.go.dev/bufio
- https://pkg.go.dev/io#Copy
