# Golang - Day1

## Notes:
Golang là:
- Ngôn ngữ đơn giản
- Static type
- Compile
- Package
- Không có OOP

Trong một project Go:
- Phải có hàm main trong package main (bắt buộc)
- Hàm init nếu có thì sẽ chạy trước hàm main trong file main
- Import _ thì sẽ import mà k xài code trong file, nếu file có hàm init thì sẽ chạy hàm này, bất cứ hàm init nào của file là một phần của chương trình sẽ chạy trước hàm main được gọi.

## Cơ bản
1. Packages
    - Trong file phải khai báo package
    - Những file chung package thì đều phải khai báo như nhau hết
    - Muốn export một biến phải viết hoa chữ cái đầu tiên.
2. Function
    - Từ khóa là func
    - Tham số, kết quả trả về phải khai báo kiểu, khai báo kiểu đều nằm sau hết
    - vd: func add (x, y int, a, b string) {}
    - Hàm có thể trả về nhiều kết quả, dùng dấu ngoặc tròn khi khai báo kiểu dữ liệu trả về. Vd: func swap() (string, string)
    - Lấy kết quả trả về có thể dùng như spread trong JS
3. Variables
    - Kiểu dữ liệu đặt sau tên biến
4. Basic types
    - Bool
    - String: dùng nháy kép
    - Int, int8 - 8bit, int16 - 16bit, ..
    - Uint, unit8, …
    - Byte = uint8
    - Rune - khi cần lặp qua từng kí tự trong chuỗi, dùng để thể hiện kí tự Unicode
    - Float32, float64
    - Zero values: khi chưa gán giá trị, int -> 0, bool -> false, …
5. Ép kiểu (type conversions)
    - Từ nhỏ ép Len to (int8 -> int16)
    - Còn nếu từ lớn về nhỏ thì có thể có vấn đề -> sai số.
6. Type inference
    - %T: type
    - %f: float
    - %s: string
7. Const - hằng số, không thể được khai báo bằng :=
8. Dịch trái 1 bit -> tăng gấp đôi (<<)
9. Dịch phải 1 bit (>>)

Khi khai báo tường minh -> biết kiểu rồi -> đưa biến vào vùng nhớ cố định
Nếu khai báo := -> thì cần dữ liệu khởi tạo

Package phone cấp theo như thư mục luôn, những file chung package thì có thể nhìn thấy những biến trong file khác luôn.

Lưu ý: Không xài dấu ; để kết thúc lệnh

Các lệnh
1. For
    - Cú pháp: Khởi tạo; điều kiện; thay đổi
    - Ko có while -> dùng for như while luôn
2. If
    - Không cần dấu ngoặc
    - Dấu { nằm trên cùng hàng với if mới chạy đc
    - Có thể kết hợp khai báo biến, gán và điều kiện trong lệnh if
3. Switch case
    - Ko có dấu ngoặc chỗ điều kiện (giống như if)
    - Không cần break, nó chỉ chạy case đúng thôi
    - Có thể Switch ko cần điều kiện
4. Defer
    - Thực thi câu lệnh ngay trước khi hàm kết thúc
    - Mang tính stacking, first in last out


Structs, slice, map
1. Pointers
    - Một biến được khai báo -> có địa chỉ -> biết ô nhớ đó nằm ở đâu -> xài & để lấy địa chỉ
    - vd: i := 1 -> i được chứa ở một ô nhớ có giá trị 1
    - Vd: p := &i -> p chứa địa chỉ của i
    - Dùng *p để lấy giá trị của con trỏ p, giá trị của biến i
    - Thay đổi giá trị con trỏ đang trỏ tới làm thay đổi giá trị biến luôn
    - Còn khi gán 1 biến bằng giá trị biến khác thì nó chỉ copy giá trị thôi
    - Tiết kiệm được memory, có thể thay đổi được biến thông qua con trỏ
    - Go ko có phép toán trực tiếp trên con trỏ
    - Nếu có dấu * thì kiểu dữ liệu dạng con trỏ
    - Khi in ra thì có dấu & nghĩa là con trỏ
    - Zero values là nil
2. Structs
    - Đối với struct thì khi gán một biến bằng một biến struct khác thì chỉ là copy giá trị (tham trị không phải tham chiếu)
    - Go không có object
    - Bộ nhớ cấp phát cho struct thì bằng bộ nhớ cấp phát cho từng kiểu rồi cộng lại, và các ô nhớ nằm kề nhau, quản lí bộ nhớ tốt hơn, tiết kiệm hơn so với Object, với Object thì mỗi properties nó là con trỏ trỏ đến 1 vùng nhớ nên có thể việc cấp phát ko phải liền kề nhau -> việc truy cập có thời gian ko thể bằng struct được.
    - Có thể xài pointer để truy cập và thay đổi các properties trong struct.
3. Arrays
    - Vd: var a [6]int
    - Hoặc: a := [1]int{1}
    - Mảng cố định, không thể thêm bớt
    - Bản chất khi thêm bớt vào mảng là nó copy sang một vùng nhớ mới có len lớn hơn rồi copy giá trị sang
4. Slice
    - Dynamic size
    - Vd: var s []int = a[:4]
    - Cú pháp: a[low:high]
    - Bản chất slice trỏ đến mảng ban đầu
    - Thay đổi slice cũng thay đổi mảng mà nó trỏ đến
    - Bản chất nó cũng là con trỏ trỏ tới mảng thôi
    - Slice length, capacity
    -  Slice length là length của mảng hiện tại mà slice đang chứa len
    - Capacity cap lấy độ chứa tối đa của slice
    - Lưu ý: arr := make([]int, 10, 20) -> len = 10, cap = 20
    - Nếu dùng append mà slice còn sức chứa (cap > len) thì nó cứ thêm vào, còn nếu lớn hơn thì phải cấp phát thêm bộ nhớ, cap sẽ tăng gấp đôi -> thì performance thì tốn hơn.
    - Copy slice thì cách duy nhất là dùng make với for để copy từng giá trị
5. Range
    - Để lấy giá trị lặp qua mảng index, value
6. Map
    - Vd: map[string]int -> map[key]value
    - Key - value
    - Delete -> Xóa key
    - Get giá trị của map, vd: v, ok := map thì giá trị ok sẽ biết là key đó có tồn tại ko
7. Func values
    - Xài func như một tham số của hàm
    - Xài func như một giá trị trả về
8. Methods
    - Là phương thức của một type nào đó
    - Chỉ xài method được trên những type tự định nghĩa, nếu muốn thêm vào những kiểu dữ liệu có sẵn thì phải tạo một type alias cho nó
9. Pointer receivers
    - Vd: func (v *Vertex) Abs() float64 {}
    - Khiến method có thể thay đổi giá trị của biến đó
    - Nếu ko xài pointer receiver thì method chỉ chạy trên một bản copy của biến đó
    - Receiver -> con trỏ -> thì có thể thay đổi giá trị biến
    - Receiver -> biến -> thì chạy method trên bản copy của biến
10. Interface
    - Khai báo signature
    - Vd: type Abser interface { Abs() float64 }
    - Tất cả những kiểu nào có method Abs thì đều có thể gọi là Abser, ko cần implement gì cả.
    - Interface được implement ngầm định
    - Empty interface -> any

Để biết biến i có phải bên dưới là string ko thì xài như sau
```go
var I interface() = “hello”
s := i.(string)
-> s = “hello”
```

Stringers -> là một interface của hệ thống, khi in ra một value struct vào hàm print thì cần hàm String() để tự định nghĩa kiểu nó in ra

error -> bản chất là interface có method Error()

Readers -> có method Read để đọc từng byte giá trị

i.(type) -> lấy được type của i

Xài gin golang để viết restAPI: https://github.com/gin-gonic/gin
Để tương tác vs DB thì xài gorm: https://gorm.io/docs

Ko có OOP nhưng có method và interfaces

Slices - Arrays:
- Bản chất slice trỏ tới một mảng cho trước, do đó slice luôn luôn share storage với array gốc của nó và những slice khác cũng trỏ tới array này

 Maps:
- Số lượng key-value trong map được gọi là length của mập đó, dùng hàm len để get giá trị length này
- Dùng delete để xóa một key trong mập
