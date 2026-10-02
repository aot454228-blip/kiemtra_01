# kiemtra_01
Câu 1: Sự khác nhau giữa Value Types và Reference Types trong C# về cơ chế lưu trữ (Stack vs Heap)

Trong C#, hệ thống kiểu (Common Type System) chia các kiểu dữ liệu thành hai nhóm lớn là kiểu giá trị và kiểu tham chiếu. Hai nhóm này khác nhau căn bản ở cách lưu trữ dữ liệu trong bộ nhớ và cách chúng được sao chép, truyền đi.

Kiểu giá trị (Value Types) gồm các kiểu số (int, double, decimal...), bool, char, struct và enum. Biến thuộc kiểu giá trị chứa trực tiếp dữ liệu của nó. Khi được khai báo như một biến cục bộ, dữ liệu được lưu trên vùng nhớ Stack. Stack hoạt động theo nguyên tắc vào sau ra trước, việc cấp phát và giải phóng diễn ra rất nhanh: khi phương thức kết thúc, toàn bộ khung stack (stack frame) bị loại bỏ và bộ nhớ được thu hồi ngay, không cần sự can thiệp của bộ dọn rác. Khi gán b = a, giá trị của a được sao chép sang b, hai biến hoàn toàn độc lập, thay đổi biến này không ảnh hưởng biến kia.

Kiểu tham chiếu (Reference Types) gồm class, interface, delegate, mảng (array) và string. Với nhóm này, dữ liệu thật của đối tượng được cấp phát trên vùng nhớ Heap, còn biến chỉ lưu một tham chiếu (địa chỉ) trỏ tới đối tượng đó, và bản thân tham chiếu này thường nằm trên Stack. Heap được quản lý bởi Garbage Collector (GC): đối tượng chỉ bị thu hồi khi không còn tham chiếu nào trỏ tới và GC chạy, do đó việc cấp phát và giải phóng chậm hơn Stack. Khi gán b = a, chỉ địa chỉ được sao chép, cả hai biến cùng trỏ vào một đối tượng, nên thay đổi qua biến này sẽ thấy ở biến kia. Giá trị mặc định của kiểu tham chiếu là null, nghĩa là chưa trỏ tới đối tượng nào.

Cần lưu ý rằng cách nói "kiểu giá trị nằm trên Stack" chỉ là sự đơn giản hóa. Nếu một kiểu giá trị là trường (field) của một lớp thì nó nằm trên Heap cùng với đối tượng chứa nó. Kiểu giá trị cũng được đưa lên Heap khi bị boxing (ép sang object hoặc interface) hoặc khi được lambda, phương thức async nắm giữ. Vì vậy, điểm bản chất để phân biệt hai nhóm là ngữ nghĩa sao chép giá trị so với sao chép tham chiếu, còn vị trí lưu trữ là hệ quả thường gặp chứ không phải quy tắc tuyệt đối.

Câu 2: Init-only Properties (init) khác gì so với thuộc tính có set thông thường? Trường hợp sử dụng thực tế

Init-only property là tính năng được bổ sung từ C# 9, sử dụng accessor init thay cho set. Điểm khác biệt cốt lõi nằm ở thời điểm cho phép gán giá trị. Với accessor set, thuộc tính có thể được gán lại bất cứ lúc nào, trong suốt vòng đời của đối tượng. Với accessor init, thuộc tính chỉ được gán trong giai đoạn khởi tạo đối tượng, cụ thể là trong constructor, trong object initializer (new T { ... }) hoặc trong biểu thức with. Sau khi đối tượng đã được tạo xong, mọi nỗ lực gán lại đều bị trình biên dịch báo lỗi. Nhờ đó, thuộc tính trở nên bất biến (immutable) sau khi khởi tạo nhưng vẫn giữ được cú pháp object initializer thuận tiện.

Về trường hợp sử dụng thực tế, trước hết là các đối tượng truyền dữ liệu như DTO, request hoặc response model trong Web API: dữ liệu nhận vào chỉ cần gán một lần và không nên bị chỉnh sửa ngầm ở các tầng xử lý phía sau. Thứ hai là các đối tượng cấu hình (Options, Settings) được nạp từ file cấu hình khi ứng dụng khởi động và cần giữ nguyên trong suốt thời gian chạy. Thứ ba là các đối tượng dùng trong môi trường đa luồng: vì bất biến nên có thể chia sẻ giữa các luồng mà không cần cơ chế khóa. Thứ tư là kiểu record, trong đó các thuộc tính khai báo theo cú pháp positional mặc định là init, kết hợp với biểu thức with để tạo bản sao có chỉnh sửa mà không làm thay đổi bản gốc. Cuối cùng, init giúp thay thế những constructor có quá nhiều tham số bằng object initializer dễ đọc hơn mà vẫn đảm bảo tính bất biến; khi cần buộc người dùng phải gán giá trị, có thể kết hợp với từ khóa required (C# 11).

Câu 3: Phân biệt phương thức virtual ở lớp cha và override ở lớp con khi triển khai Đa hình

Đa hình (Polymorphism) là khả năng để cùng một lời gọi phương thức thông qua một tham chiếu kiểu lớp cha nhưng thực thi các hành vi khác nhau tùy theo kiểu thực tế của đối tượng. Trong C#, đa hình lúc chạy được hiện thực bằng cặp từ khóa virtual và override.

Từ khóa virtual được đặt ở lớp cha. Nó khai báo rằng phương thức này cho phép các lớp con viết lại hành vi, đồng thời lớp cha vẫn cung cấp một cài đặt mặc định. Lớp con không bị bắt buộc phải viết lại: nếu không override thì sẽ dùng cài đặt của lớp cha.

Từ khóa override được đặt ở lớp con. Nó cung cấp cài đặt mới, thay thế cài đặt của phương thức virtual (hoặc abstract, hoặc một override khác) ở lớp cha. Phương thức override phải có chữ ký trùng khớp với phương thức gốc về tên, danh sách tham số và kiểu trả về, và chỉ có thể override những phương thức đã được đánh dấu cho phép như trên.

Cơ chế vận hành đằng sau là ràng buộc muộn (late binding, hay dynamic dispatch): khi chạy chương trình, CLR dựa vào kiểu thực tế của đối tượng để chọn phương thức cần gọi, chứ không dựa vào kiểu khai báo của biến. Như vậy, virtual đóng vai trò "mở cửa" cho phép mở rộng ở lớp cha, còn override là hành động "thực hiện việc thay thế" ở lớp con; thiếu một trong hai thì đa hình không xảy ra.

Cần phân biệt thêm với từ khóa new (che giấu phương thức, method hiding): nếu lớp con dùng new thay vì override, phương thức được gọi sẽ phụ thuộc vào kiểu khai báo của biến (ràng buộc sớm), do đó mất đi tính đa hình.

Câu 4: Tại sao thành phần static không thể truy xuất thông qua một instance được tạo bằng new?

Nguyên nhân bắt nguồn từ bản chất của thành phần static. Thành phần static thuộc về chính lớp (kiểu dữ liệu) chứ không thuộc về bất kỳ đối tượng cụ thể nào. Nó chỉ tồn tại một bản duy nhất, được dùng chung cho toàn bộ lớp và được khởi tạo khi kiểu được nạp vào bộ nhớ, hoàn toàn không phụ thuộc vào việc đã có đối tượng nào được tạo bằng toán tử new hay chưa. Ngược lại, thành phần instance gắn với từng đối tượng: mỗi lần new sẽ sinh ra một bản riêng của các trường này.

Vì vậy, cách truy cập phải phản ánh đúng quan hệ sở hữu đó: thành phần static được truy cập qua tên lớp, còn thành phần instance được truy cập qua đối tượng. Nếu truy cập static qua instance, trình biên dịch C# sẽ báo lỗi (CS0176).

Việc C# cấm truy cập qua instance có các lý do thiết kế sau. Một là tránh nhầm lẫn về ngữ nghĩa: viết c.Total dễ khiến người đọc hiểu nhầm Total là dữ liệu riêng của c, trong khi thực chất đó là dữ liệu chung của mọi đối tượng; viết Counter.Total thể hiện rõ đây là dữ liệu cấp lớp. Hai là tính độc lập với instance: thành phần static có thể được sử dụng ngay cả khi chưa tồn tại đối tượng nào, nên cách truy cập của nó không thể phụ thuộc vào đối tượng. Ba là sự khác biệt có chủ đích so với Java và C++: các ngôn ngữ đó cho phép gọi thành phần static qua instance (thường kèm cảnh báo), còn C# chọn cấm hẳn để mã nguồn rõ ràng và giảm thiểu lỗi.

Ngoài ra, quan hệ ngược lại cũng cần nhớ: phương thức static không có con trỏ this (vì không gắn với đối tượng nào) nên không thể truy cập trực tiếp các thành viên instance, trừ khi một instance cụ thể được truyền vào thông qua tham số. Điều này cho thấy hai loại thành phần thuộc hai "thế giới" khác nhau: cấp lớp và cấp đối tượng.
