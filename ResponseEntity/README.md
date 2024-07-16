# Sử dụng Spring ResponseEntity để thao tác phản hồi HTTP

**1. Giới thiệu**

Khi sử dụng Spring, chúng ta thường có nhiều cách để đạt được cùng một mục tiêu, bao gồm cả việc tinh chỉnh phản hồi HTTP.

Trong hướng dẫn ngắn này, chúng ta sẽ xem cách thiết lập, cấu hình phần nội dung, trạng thái và tiêu đề của phản hồi HTTP bằng cách sử dụng ResponseEntity .

**2. ResponseEntity**

Đây là cách sử dụng repsonse tốt nhất và mình khuyến khích sử dụng

**ResponseEntity** biểu diễn toàn bộ phản hồi HTTP: mã trạng thái, tiêu đề và nội dung . Do đó, chúng ta có thể sử dụng nó để cấu hình đầy đủ phản hồi HTTP.

Nếu chúng ta muốn sử dụng nó, chúng ta phải trả về nó từ điểm cuối; Spring sẽ xử lý phần còn lại.

**ResponseEntity** là một kiểu chung. Do đó, chúng ta có thể sử dụng bất kỳ kiểu nào làm phần thân phản hồi:

```java
@GetMapping("/hello")
ResponseEntity<String> hello() {
    return new ResponseEntity<>("Hello World!", HttpStatus.OK);
}
```

Vì chúng ta chỉ định trạng thái phản hồi theo chương trình nên chúng ta có thể trả về với các mã trạng thái khác nhau cho các tình huống khác nhau:

```java
@GetMapping("/age")
ResponseEntity<String> age(
  @RequestParam("yearOfBirth") int yearOfBirth) {
 
    if (isInFuture(yearOfBirth)) {
        return new ResponseEntity<>(
          "Year of birth cannot be in the future", 
          HttpStatus.BAD_REQUEST);
    }

    return new ResponseEntity<>(
      "Your age is " + calculateAge(yearOfBirth), 
      HttpStatus.OK);
}
```

Ngoài ra, chúng ta có thể thiết lập tiêu đề HTTP:

```java
@GetMapping("/customHeader")
ResponseEntity<String> customHeader() {
    HttpHeaders headers = new HttpHeaders();
    headers.add("Custom-Header", "foo");
        
    return new ResponseEntity<>(
      "Custom header set", headers, HttpStatus.OK);
}
```

Hơn nữa, **ResponseEntity** cung cấp hai giao diện xây dựng lồng nhau : HeadersBuilder và giao diện con của nó, BodyBuilder . Do đó, chúng ta có thể truy cập các khả năng của chúng thông qua các phương thức tĩnh của **ResponseEntity**

Trường hợp đơn giản nhất là phản hồi có nội dung và mã phản hồi **HTTP 200**:

```java
@GetMapping("/hello")
ResponseEntity<String> hello() {
    return ResponseEntity.ok("Hello World!");
}
```

Đối với các mã trạng thái HTTP phổ biến nhất, chúng ta có các phương thức tĩnh:

```java
BodyBuilder accepted();
BodyBuilder badRequest();
BodyBuilder created(java.net.URI location);
HeadersBuilder<?> noContent();
HeadersBuilder<?> notFound();
BodyBuilder ok();
```

Ngoài ra, chúng ta có thể sử dụng phương thức **BodyBuilder status(HttpStatus status)** và **BodyBuilder status(int status)** để thiết lập bất kỳ trạng thái HTTP nào.

Cuối cùng, với **ResponseEntity<T> BodyBuilder.body(T body)** chúng ta có thể thiết lập nội dung phản hồi HTTP:

```java
@GetMapping("/age")
ResponseEntity<String> age(@RequestParam("yearOfBirth") int yearOfBirth) {
    if (isInFuture(yearOfBirth)) {
        return ResponseEntity.badRequest()
            .body("Year of birth cannot be in the future");
    }

    return ResponseEntity.status(HttpStatus.OK)
        .body("Your age is " + calculateAge(yearOfBirth));
}
```

Chúng ta cũng có thể thiết lập tiêu đề tùy chỉnh:

```java
@GetMapping("/customHeader")
ResponseEntity<String> customHeader() {
    return ResponseEntity.ok()
        .header("Custom-Header", "foo")
        .body("Custom header set");
}
```

Vì **BodyBuilder.body()** trả về **ResponseEntity** thay vì BodyBuilder nên đây sẽ là lệnh gọi cuối cùng.

Lưu ý rằng với **HeaderBuilder** chúng ta không thể thiết lập bất kỳ thuộc tính nào cho phần nội dung phản hồi.

Khi trả về đối tượng **ResponseEntity<T>** từ bộ điều khiển, chúng ta có thể gặp ngoại lệ hoặc lỗi trong khi xử lý yêu cầu và muốn trả về thông tin liên quan đến lỗi cho người dùng được biểu diễn dưới dạng một kiểu khác, chẳng hạn như E.

Spring 3.2 mang đến sự hỗ trợ cho **@ExceptionHandler**  toàn cục  với chú thích **@RestControllerAdvice**  mới  , xử lý các loại tình huống này. Đ

Mặc dù **ResponseEntity** rất mạnh, chúng ta không nên lạm dụng nó. Trong những trường hợp đơn giản, có những tùy chọn khác đáp ứng được nhu cầu của chúng ta và chúng tạo ra mã sạch hơn nhiều.

**3. Các lựa chọn thay thế:**

**3.1. @ResponseBody**

Trong các ứng dụng Spring MVC cổ điển, các điểm cuối thường trả về các trang HTML đã được hiển thị. Đôi khi chúng ta chỉ cần trả về dữ liệu thực tế; ví dụ, khi chúng ta sử dụng điểm cuối với AJAX.

Trong những trường hợp như vậy, chúng ta có thể đánh dấu phương thức xử lý yêu cầu bằng @ResponseBody và Spring sẽ xử lý giá trị kết quả của phương thức này như chính nội dung phản hồi HTTP.

**3.2. @ResponseStatus**

Khi điểm cuối trả về thành công, Spring cung cấp phản hồi HTTP 200 (OK). Nếu điểm cuối ném ra ngoại lệ, Spring sẽ tìm trình xử lý ngoại lệ cho biết trạng thái HTTP nào cần sử dụng.

Chúng ta có thể đánh dấu các phương thức này bằng @ResponseStatus và do đó, Spring trả về trạng thái HTTP tùy chỉnh .

**3.3. Điều khiển phản hồi trực tiếp**

Spring cũng cho phép chúng ta truy cập trực tiếp vào đối tượng javax.servlet.http.HttpServletResponse ; chúng ta chỉ cần khai báo nó như một đối số phương thức:

```java
@GetMapping("/manual")
void manual(HttpServletResponse response) throws IOException {
    response.setHeader("Custom-Header", "foo");
    response.setStatus(200);
    response.getWriter().println("Hello World!");
}
```

Vì Spring cung cấp các khả năng trừu tượng và bổ sung trên triển khai cơ bản nên chúng ta không nên thao tác phản hồi theo cách này .

**4. Kết luận**

Trong bài viết này, chúng tôi thảo luận về nhiều cách để thao tác phản hồi HTTP trong Spring và xem xét những lợi ích cũng như hạn chế của chúng.
