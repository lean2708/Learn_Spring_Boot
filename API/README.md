Hiện nay API nói chung và Web API nói riêng đang được ứng dụng ngày càng nhiều. Kiến trúc ứng dụng hiện đại ngày nay ngày càng phân tán, không phụ thuộc ngôn ngữ đã thúc đẩy việc ứng dụng API.  
**Vậy API là gì?** Nguồn gốc và ưu điểm của nó là như thế nào?

# API là gì?

API là các phương thức, giao thức kết nối với các thư viện và ứng dụng khác. Nó là viết tắt của Application Programming Interface – giao diện lập trình ứng dụng. API cung cấp khả năng truy xuất đến một tập các hàm hay dùng. Và từ đó có thể trao đổi dữ liệu giữa các ứng dụng.

![api](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/api1.png?raw=true)

**API thường ứng dụng vào đâu?**

Web API: là hệ thống API được sử dụng trong các hệ thống website. Hầu hết các website đều ứng dụng đến Web API cho phép bạn kết nối, lấy dữ liệu hoặc cập nhật cơ sở dữ liệu. Ví dụ: Bạn thiết kế chức nằng login thông Google, Facebook, Twitter, Github… Điều này có nghĩa là bạn đang gọi đến API của. Hoặc như các ứng dụng di động đều lấy dữ liệu thông qua API.
API trên hệ điều hành: Windows hay Linux có rất nhiều API, họ cung cấp các tài liệu API là đặc tả các hàm, phương thức cũng như các giao thức kết nối. Nó giúp lập trình viên có thể tạo ra các phần mềm ứng dụng có thể tương tác trực tiếp với hệ điều hành.
API của thư viện phần mềm hay framework: API mô tả và quy định các hành động mong muốn mà các thư viện cung cấp. Một API có thể có nhiều cách triển khai khác nhau và nó cũng giúp cho một chương trình viết bằng ngôn ngữ này có thể sử dụng thư viện được viết bằng ngôn ngữ khác. Ví dụ bạn có thể dùng Php để yêu cầu một thư viện tạo file PDF được viết bằng C++.

API có thể sử dụng cho computer hardware, or software library, database system, web-based system, operating system.
Bên cạnh đó, API cũng có rất nhiều dạng khác nhau, nhưng nhìn chung bao gồm một số đại diện đặc tả cho: data structures, variables, object classes, routines hoặc remote calls.
Tài liệu cho các API thường được cung cấp để tạo sự thuận lợi cho việc triển khai và sử dụng. Web API hiện đang được các doanh nghiệp lớn sử dụng cho khách hàng hoặc chính nội bộ công ty họ.

**Một số khái niệm khác:**
API hiện nay đều tuân thủ theo tiêu chuẩn REST và HTTP, tạo sự thân thiện dễ sử dụng với nhà phát triển. Giúp người dùng dễ dàng truy cập, dễ hiểu hơn. Web API hiện đại dùng cho các đối tượng cụ thể, chẳng hạn như mobile developer với document, version khác nhau.
API key: Đây là loại code (string) được truyền tải bởi các chương trình máy tính gọi là API để xác định chương trình, nhà phát triển hoặc người dùng nó tới trang web. Các API key được sử dụng với mục đích nhằm giới hạn, kiểm soát sử dụng API. Chẳng hạn như ngăn chặn sự việc lạm dụng API.
**API Key** thường hoạt động như một mã định danh duy nhất và mã thông báo bí mật để xác thực và thường sẽ có một bộ quyền truy cập trên API được liên kết với nó. Các API Key có thể dựa trên hệ thống định danh duy nhất toàn cầu (UUID) để đảm bảo chúng sẽ là duy nhất cho mỗi người dùng.
Web API là gì?
Web API là một phương thức dùng để cho phép các ứng dụng khác nhau có thể giao tiếp, trao đổi dữ liệu qua lại. Dữ liệu được Web API trả lại thường ở dạng JSON hoặc XML thông qua giao thức HTTP hoặc HTTPS.

Những điểm nổi bật của Web API
Web API hỗ trợ restful đầy đủ các phương thức: Get/Post/put/delete dữ liệu. Nó giúp bạn xây dựng các HTTP service một cách rất đơn giản và nhanh chóng. Nó cũng có khả năng hỗ trợ đầy đủ các thành phần HTTP: URI, request/response headers, caching, versioning, content format.

Tự động hóa sản phẩm
Với web API, chúng ta sẽ tự động hóa quản lý công việc, cập nhật luồng công việc, giúp tăng năng suất và tạo hiệu quả công việc cao hơn.

Khả năng tích hợp linh động
API cho phép lấy nội dung từ bất kỳ website hoặc ứng dụng nào một cách dễ dàng nếu được cho phép, tăng trải nghiệm người dùng. API hoạt động như một chiếc cổng, cho phép các công ty chia sẻ thông tin được chọn nhưng vẫn tránh được những yêu cầu không mong muốn.

Cập nhật thông tin thời gian thực
API có chức năng thay đổi và cập nhật thay đổi theo thời gian thực. Với công nghệ này, dữ liệu sẽ được truyền đi tốt hơn, thông tin chính xác hơn, dịch vụ cung cấp linh hoạt hơn.

Có tiêu chuẩn chung dễ sử dụng
Bất kỳ người dùng, công ty nào sử dụng cũng có thể điều chỉnh nội dung, dịch vụ mà họ sử dụng.

Hỗ trợ đầy đủ các thành phần MVC như: routing, controller, action result, filter, model binder, IoC container, dependency injection, unit test.

**Web API hoạt động như thế nào?**

Đầu tiên là xây dựng URL API để bên thứ ba có thể gửi request dữ liệu đến máy chủ cung cấp nội dung, dịch vụ thông qua giao thức HTTP hoặc HTTPS.
Tại web server cung cấp nội dung, các ứng dụng nguồn sẽ thực hiện kiểm tra xác thực nếu có và tìm đến tài nguyên thích hợp để tạo nội dung trả về kết quả.
Server trả về kết quả theo định dạng JSON hoặc XML thông qua giao thức HTTP/HTTPS.
Tại nơi yêu cầu ban đầu là ứng dụng web hoặc ứng dụng di động , dữ liệu JSON/XML sẽ được parse để lấy data. Sau khi có được data thì thực hiện tiếp các hoạt động như lưu dữ liệu xuống Cơ sở dữ liệu, hiển thị dữ liệu…
Nói thêm về Web API, hiện nay các lập trình viên website thường tự viết API riêng cho dự án của mình. Chủ yếu để thao tác với Database được xây dựng sẵn từ sản phẩm trước đây, nhằm phục vụ cho những tính năng tương đồng ở các dự án khác trong tương lai (Ví dụ Api để lấy thông tin toàn bộ bài viết theo ngày). Nó còn gọi là tái sử dụng những tài nguyên mà công ty đã xây dựng sẵn hoặc sử dụng API từ các nhà cung cấp như Microsoft hay Google, điển hình và phổ biến nhất có thể kể đến là Google Maps API hoặc Facebook Api…

# Ưu và nhược điểm của Web API

Mỗi một ứng dụng bất kỳ đều có những ưu nhược điểm riêng, hỗ trợ tốt cho các ứng dụng. Vì vậy mà web API cũng không ngoại lệ:

**1. Ưu điểm**

Web API được sử dụng hầu hết trên các ứng dụng desktop, ứng dụng mobile và ứng dụng website.
Linh hoạt với các định dạng dữ liệu khi trả về client: Json, XML hay định dạng khác.
Nhanh chóng xây dựng HTTP service: URI, request/response headers, caching, versioning, content formats và có thể host trong ứng dụng hoặc trên IIS.
Mã nguồn mở, hỗ trợ chức năng RESTful đầy đủ, sử dụng bởi bất kì client nào hỗ trợ XML, Json.
Hỗ trợ đầy đủ các thành phần MVC như: routing, controller, action result, filter, model binder, IoC container, dependency injection, unit test.
Giao tiếp hai chiều được xác nhận trong các giao dịch, đảm bảo độ tin cậy cao.
**2. Nhược điểm**

Do web API còn khá mới nên chưa thể đánh giá nhiều về nhược điểm của mô hình nay. Tuy nhiên, có hai nhược điểm dễ dàng nhận thấy:

Web API chưa hoàn toàn phải là RESTful service, mới chỉ hỗ trợ mặc định GET, POST
Để sử dụng hiệu quả cần có kiến thức chuyên sâu, có kinh nghiệm backend tốt
Tốn thời gian và chi phí cho việc phát triển, nâng cấp và vận hành
Có thể gặp vấn đề về bảo mật khi hệ thống bị tấn công nếu không giới hạn điều kiện kỹ.



