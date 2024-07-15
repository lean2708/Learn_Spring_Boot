# Postman là gì? Hướng dẫn sử dụng Postman cơ bản dành cho người mới

**Postman là gì?**

Postman là một nền tảng API để xây dựng và sử dụng các API. Postman đơn giản hóa từng bước của vòng đời API và hợp lý hóa việc cộng tác để bạn có thể tạo các API tốt hơn — nhanh hơn.

![po1](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po1.png?raw=true)

**Công dụng của Postman**

1. Kho API: Dễ dàng lưu trữ, lập danh mục và cộng tác xung quanh tất cả các tạo phẩm API của bạn trên một nền tảng trung tâm. Postman có thể lưu trữ và quản lý thông số kỹ thuật API, tài liệu, công thức quy trình làm việc, trường hợp và kết quả thử nghiệm, chỉ số và mọi thứ khác liên quan đến API.
2. Công cụ: Nền tảng Postman bao gồm một bộ công cụ toàn diện giúp đẩy nhanh vòng đời của API — từ thiết kế, thử nghiệm, tài liệu và mô phỏng đến chia sẻ và khả năng khám phá của các API của bạn.
3. Quản trị: Phương pháp quản trị toàn vòng đời của Postman cho phép những người chấp nhận thay đổi các phương pháp phát triển của họ, dẫn đến các API chất lượng tốt hơn và thúc đẩy sự hợp tác giữa các nhóm nhà phát triển và nhóm thiết kế API.
4. Không gian làm việc: Không gian làm việc của người đưa thư giúp bạn tổ chức công việc API của mình và cộng tác trong tổ chức của bạn hoặc trên toàn thế giới. Có ba loại không gian làm việc Postman khác nhau cho các nhu cầu khác nhau của bạn: không gian làm việc cá nhân, không gian làm việc nhóm và không gian làm việc công cộng.
5. Tích hợp: Postman tích hợp với các công cụ quan trọng nhất trong quy trình phát triển phần mềm của bạn để cho phép thực hành API ưu tiên. Nền tảng Postman cũng có thể mở rộng thông qua API Postman và thông qua các công nghệ mã nguồn mở .

**Cách cài đặt và sử dụng Postman**

# 1.Cài đặt Postman
Bước 1: Truy cập https://www.postman.com/downloads/ và lựa chọn phiên bản phù hợp với máy tính của mình chẳng hạn như MacOs, Windown, Linux. Click vào !

![po2](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po2.png?raw=true)

Bước 2: Sau khi tải xong, ta giải nén file đó ra và tiến hành chạy ứng dụng Postman
Bước 3: Tiến hành lựa chọn tài khoản đăng nhập hoặc đăng ký

![po3](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po3.png?raw=true)

Bước 4: Sau khi cài đặt xong sẽ hiển thị ra trang chủ

![po4](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po4.png?raw=true)

# 2.Sử dụng Postman

Cửa sổ làm việc của Postman

![po5](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po5.png?raw=true)

**New**: Là nơi cho phép bạn tạo request, enviroment mới hoặc collection.

**Import**: Được sử dụng nhằm import collection hoặc environment. Một số tùy chọn còn được dùng để import từ file folder, paste từ text thuần hoặc link

**Open new**: Dùng để mở một tab mới, cửa sổ postman hoặc cửa sổ runner bằng việc kích nút này.

**Runner**: Nhằm kiểm tra tự động hóa giúp thực hiện thông qua Runner cả collection.

**My workspace**: Tạo cửa sổ làm việc riêng hoặc dành cho một nhóm.

**Invite**: Làm việc và cộng tác với nhiều thành viên hơn bằng cách mời các thành viên.

**History**: Những request mà bạn đã thực hiện sẽ hiển thị ở mục history. Từ đó, bạn hoàn toàn có thể lần theo hành động đã làm từ trước.

**Collections**: Nhằm tổ chức các thử nghiệm bằng cách tạo collection. Mỗi một collection sẽ có thư mục con với nhiều yêu cầu có thể là request hoặc thư mục trùng lặp.

**Tab request**: giúp hiển thị tiêu đề request mà bạn làm việc. Nó sẽ mặc định “untitled Request và hiển thị cho những request không có tiêu đề khác.

**Request URL**: hay còn gọi là điểm cuối, nó là nơi giúp bạn xác định liên kết đến nơi mà API sẽ giao tiếp.

**HTTP Request**: Khi click vào đây thì danh sách hiển thị sẽ được thả xuống với những request khác. Chúng có thể là: post, copy, get, delete,…

**Save**: Nếu như thay đổi với request bạn chỉ cần nhấp vào save để các thay đổi được lưu và không bị ghi đè.

**Params**: Giúp bạn vẽ những tham số cần thiết cho một request.

**Headers**: Bạn có thể tiến hành thiết lập các header như nội dung JSON tùy theo cách tổ chức của bản thân.

**Body**: Cho phép chúng ta tùy chỉnh các chi tiết trong request và thường được dùng nhiều trong request Post.

**Tests**: Là những script được thực hiện khi thực hiện request. Tuy nhiên, cần phải có các thử nghiệm như thiết lập điểm checkpoint nhằm kiểm tra trạng thái. Khi đó, dữ liệu nhận được sẽ như mong đợi và sở hữu các thử nghiệm khác.

**Pre-request script**: Các tập lệnh sẽ được thực thi trước request. Đa phần chúng sẽ cho môi trường được sử dụng để đảm bảo cho các kiểm tra và được chạy ở trong môi trường chính xác nhất.

# Làm việc với Postman

**3. Method GET**

Ta sẽ xác định api trước để khi vào Postman thì chúng ta có thể chạy cho đúng nhé:

Tên api sẽ là http://localhost:3000/products!
Sau khi đã xác định được tên và method thì chúng ta sẽ gắn các giá trị kia vào Input một cách dễ dàng mà không bị nhầm lẫn. Trước khi điền giá trị trong Postman thì ta cần phải chạy server của mình trước. Trong Postman bạn sẽ điền như sau và ấn SEND:

![po6](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po6.png?raw=true)

Kết quả ta nhận được đó là:

Bạn sẽ nhìn thấy message 200 OK
Hiển thị kết quả người dùng ở body

**4. Method POST**

POST yêu cầu khác với GET vì có thao tác dữ liệu với người dùng thêm dữ liệu vào điểm cuối. Sử dụng cùng dữ liệu từ hướng dẫn trước trong Nhận yêu cầu, bây giờ chúng ta hãy thêm người dùng của riêng chúng tôi

Bước 1: Tạo 1 request mới

![po7](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po7.png?raw=true)

Bước 2: Tương tự như GET thì ta cũng cần nhập url của api vào như trên hình sẽ là http://localhost:3000/products

Bước 3: Tạo tương tự như các product khác. Đảm bảo mã chính xác với các dấu đóng mở hoặc dấu phẩy

![po8](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po8.png?raw=true)

Bước 4: Bấm SEND thì dữ liệu POST sẽ được hiển thị ở phần body

![po9](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image2/po9.png?raw=true)

# Kết

Như vậy, bài viết đã giúp bạn hiểu được Postman là gì, các chức năng chính, cách cài đặt và sử dụng của ứng dụng. Có thể nói, đây là một trong những công cụ làm việc và thử nghiệm với API tốt nhất hiện nay. Nếu bạn đang tìm kiếm một công cụ giúp tối ưu hoá hoạt động phát triển phần mềm cùng nhiều lợi ích khác, hãy thử ngay Postman nhé!
