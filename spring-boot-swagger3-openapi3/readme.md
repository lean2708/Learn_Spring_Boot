

# Sử dụng OpenAPI 3.0 để ghi lại các dịch vụ Spring REST API
**1. Giới thiệu**

REST API là gì?
REST API là một tiêu chuẩn dùng trong việc thiết kế API cho các ứng dụng web (thiết kế Web services) để tiện cho việc quản lý các resource. Nó chú trọng vào tài nguyên hệ thống (tệp văn bản, ảnh, âm thanh, video, hoặc dữ liệu động…), bao gồm các trạng thái tài nguyên được định dạng và được truyền tải qua HTTP.

Vậy tại sao chúng ta cần ghi lại các dịch vụ REST API?
Ghi lại các dịch vụ REST API sẽ giúp chúng ta kiểm tra được tài liệu và dễ dàng bảo trì tài liệu của mình.

Tài liệu là một phần thiết yếu trong việc xây dựng các API REST. Vậy nên trong bài hướng dẫn này, chúng ta sẽ cùng nhau tìm hiểu về SpringDoc - một công cụ đơn giản hóa việc tạo và bảo trì tài liệu API dựa trên đặc điểm kỹ thuật OpenAPI 3 cho các ứng dụng Spring Boot 1.x và 2.x nhé.

**2. Thiết lập springdoc-openapi hoặc springdoc-openapi-starter-webmvc-ui(bao gồm springdoc-openapi)**

Để springdoc-openapi có thể tự động tạo tài liệu đặc tả OpenAPI 3 cho API, chúng ta chỉ cần thêm dependency **springdoc-openapi-ui** hoặc **springdoc-openapi-starter-webmvc-ui (bao gồm cả springdoc-openapi-ui)** vào thư mục pom.xml, **đặc biệt hãy chú ý các phiên bản do sự tương thích giữa các dependencies của Spring Boot** (lấy phiên bản mới nhất tại https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-ui và https://mvnrepository.com/artifact/org.springframework/spring-webmvc ): 

```java
//springdoc-openapi-starter-webmvc-ui
<dependency>
      <groupId>org.springdoc</groupId>
      <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
      <version>2.1.0</version> 
</dependency>
```

**3. Thiết lập springdoc-openapi với giao diện người dùng Swagger**

Bên cạnh việc tự tạo đặc tả OpenAPI 3, chúng ta có thể tích hợp springdoc-openapi với giao diện người dùng Swagger để chúng ta có thể tương tác với đặc tả API của mình và thực hiện các điểm cuối.

**3.1 Thêm Maven Dependency**

Cũng tương tự như trên đầu tiên(phần 2) chúng ta cần thêm dependency **springdoc-openapi-starter-webmvc-ui vì nó bao gồm cả springdoc-openapi-ui** vào thư mục pom.xml của dự án:

```java
//springdoc-openapi-starter-webmvc-ui
<dependency>
      <groupId>org.springdoc</groupId>
      <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
      <version>2.1.0</version> 
</dependency>
```

Bây giờ chúng ta có thể truy cập tài liệu API tại:

```java
http://localhost:8080/swagger-ui.html
```

**3.2 Hỗ Trợ thuộc tính của swagger-ui**

Cũng giống như Sping Boot mọi thuộc tính của swagger-ui cũng tương tự.
Ví dụ, chúng ta cũng có thể tùy chỉnh đường dẫn giống như trên trong **application.properties** là:
```java
springdoc.swagger-ui.path=/swagger-ui-custom.html
```
Vì vậy, bây giờ tài liệu API của chúng ta sẽ có sẵn tại:
```java
http://localhost:8080/swagger-ui-custom.html
```
Một ví dụ khác, để sắp xếp các đường dẫn API theo thứ tự các phương thức HTTP của chúng, chúng ta có thể thêm:
```java
springdoc.swagger-ui.operationsSorter=method
```
**3.3 Ví dụ mẫu về API**

Giả sử ứng dụng của chúng ta có bộ điều khiển để quản lý Sách:

```java
@RestController
@RequestMapping("/api/book")
public class BookController {

    @Autowired
    private BookRepository repository;

    @GetMapping("/{id}")
    public Book findById(@PathVariable long id) {
        return repository.findById(id)
            .orElseThrow(() -> new BookNotFoundException());
    }

    @GetMapping("/")
    public Collection<Book> findBooks() {
        return repository.getBooks();
    }

    @PutMapping("/{id}")
    @ResponseStatus(HttpStatus.OK)
    public Book updateBook(
      @PathVariable("id") final String id, @RequestBody final Book book) {
        return book;
    }
}
```

Sau đó, khi chúng ta chạy ứng dụng, chúng ta có thể xem tài liệu tại đường dẫn:
```java
http://localhost:8080/swagger-ui-custom.html
```
Chúng ta sẽ có được giao diện sau:

![swagger](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/s31.png?raw=true)

Bây giờ chúng ta hãy đi sâu vào điểm cuối /api/book và xem chi tiết về yêu cầu và phản hồi của nó:

![spring-component](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/se2.png?raw=true)

**4. Tích hợp openapi springdoc với Spring WebFlux**

Chúng ta có thể tích hợp springdoc-openapi và Swagger UI trong dự án Spring WebFlux bằng cách thêm springdoc-openapi-webflux-ui (tìm phiên bản mới nhất tại đây https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-webflux-ui):
```java
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-webflux-ui</artifactId>
    <version>1.6.4</version>
</dependency>
```
Giống như trên, tài liệu sẽ có thể truy cập được tại:
```java
http://localhost:8080/swagger-ui.html
```
Để thay đổi đường dẫn chúng ta thực hiện tương tự như phần trên.

**5. Hiển thị thông tin phân trang**

Spring Data JPA tích hợp với Spring MVC khá liền mạch. Một ví dụ về tích hợp như vậy là hỗ trợ Pagable:
```java
@GetMapping("/filter")
public Page<Book> filterBooks(@ParameterObject Pageable pageable) {
     return repository.getBooks(pageable);
}
```
Lúc đầu, chúng ta có thể mong đợi SpringDoc sẽ thêm các tham số trang, kích thước và sắp xếp truy vấn vào tài liệu đã tạo. Tuy nhiên, theo mặc định, SpringDoc không đáp ứng kỳ vọng này. Để mở khóa tính năng này, chúng ta nên thêm thư viện springdoc-openapi-data-rest vào dự án:
```java
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-data-rest</artifactId>
    <version>1.6.4</version>
</dependency>
```
Bây giờ chúng ta có thể thấy nó đã thêm các tham số truy vấn dự kiến vào tài liệu:

![api-swaggerr](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/s33.png?raw=true)

**6. Sử dụng plugin Springdoc-openapi Maven**

Thư viện springdoc-openapi cung cấp một plugin Maven springdoc-openapi-maven-plugin để tạo mô tả OpenAPI ở định dạng json và yaml.

Plugin springdoc-openapi-maven-plugin hoạt động với plugin spring-boot-maven. Maven chạy plugin openapi trong giai đoạn kiểm tra tích hợp.

Đầu tiên chúng ta hãy định cấu hình plugin trong thư mục pom.xml:
```java
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>2.3.3.RELEASE</version>
    <executions>
        <execution>
            <id>pre-integration-test</id>
            <goals>
                <goal>start</goal>
            </goals>
        </execution>
        <execution>
            <id>post-integration-test</id>
            <goals>
                <goal>stop</goal>
            </goals>
        </execution>
    </executions>
</plugin>
<plugin>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-maven-plugin</artifactId>
    <version>0.2</version>
    <executions>
        <execution>
            <phase>integration-test</phase>
            <goals>
                <goal>generate</goal>
            </goals>
        </execution>
    </executions>
</plugin>
Chúng ta cũng có thể định cấu hình plugin để sử dụng các giá trị tùy chỉnh:

<plugin>
    <executions>
        .........
    </executions>
    <configuration> 
        <apiDocsUrl>http://localhost:8080/v3/api-docs</apiDocsUrl> 
        <outputFileName>openapi.json</outputFileName> 
        <outputDir>${project.build.directory}</outputDir> 
    </configuration>
</plugin>
```
Hãy xem xét kỹ hơn các thông số mà chúng ta có thể định cấu hình cho plugin:

apiDocsUrl - URL nơi có thể truy cập tài liệu ở định dạng JSON, với mặc định là http://localhost:8080/v3/api-docs
outputFileName - Tên tệp nơi lưu trữ các định nghĩa, mặc định là openapi.json
outputDir - Đường dẫn tuyệt đối cho thư mục lưu trữ tài liệu, theo mặc định $ {project.build.directory}

**7. Tạo tài liệu tự động bằng cách sử dụng xác thực Bean JSR-303**

Khi mô hình bao gồm các chú thích xác thực bean JSR-303, chẳng hạn như @NotNull, @NotBlank, @Size, @Min và @Max, thư viện springdoc-openapi sử dụng chúng để tạo tài liệu lược đồ bổ sung cho các ràng buộc tương ứng.
Ví dụ:
```java
public class Book {

    private long id;

    @NotBlank
    @Size(min = 0, max = 20)
    private String title;

    @NotBlank
    @Size(min = 0, max = 30)
    private String author;

}
```
Bây giờ, tài liệu được tạo cho Book bean ta thấy có nhiều thông tin hơn một chút:

![swagger-ui](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/s34.png?raw=true)

**8. Tạo tài liệu bằng @ControllerAdvice và @ResponseStatus**

Sử dụng @ResponseStatus trên các phương thức trong lớp @RestControllerAdvice sẽ tự động tạo tài liệu cho các mã phản hồi. Trong lớp 

@RestControllerAdvice này, hai phương thức được chú thích bằng @ResponseStatus:
```java
@RestControllerAdvice
public class GlobalControllerExceptionHandler {

    @ExceptionHandler(ConversionFailedException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ResponseEntity<String> handleConnversion(RuntimeException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.BAD_REQUEST);
    }
    
    @ExceptionHandler(BookNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ResponseEntity<String> handleBookNotFound(RuntimeException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.NOT_FOUND);
    }
}
```

Do đó, bây giờ chúng ta có thể xem tài liệu cho mã phản hồi 400 và 404:

![s](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/s35.png?raw=true)

**9. Tạo tài liệu bằng @Operation và @ApiResponses**

Tiếp theo, hãy xem cách chúng ta có thể thêm một số mô tả vào API của mình bằng cách sử dụng một vài chú thích dành riêng cho OpenAPI. Để làm được điều đó, chúng ta sẽ chú thích điểm cuối /api/book/{id} của bộ điều khiển bằng @Operation và @ApiResponses:
```java
@Operation(summary = "Get a book by its id")
@ApiResponses(value = { 
  @ApiResponse(responseCode = "200", description = "Found the book", 
    content = { @Content(mediaType = "application/json", 
      schema = @Schema(implementation = Book.class)) }),
  @ApiResponse(responseCode = "400", description = "Invalid id supplied", 
    content = @Content), 
  @ApiResponse(responseCode = "404", description = "Book not found", 
    content = @Content) })
@GetMapping("/{id}")
public Book findById(@Parameter(description = "id of book to be searched") 
  @PathVariable long id) {
    return repository.findById(id).orElseThrow(() -> new BookNotFoundException());
}
```
Và đây là hiệu ứng hiển thị:
![sw](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/s36.png?raw=true)

Như chúng ta có thể thấy, văn bản đã thêm vào @Operation được đặt ở cấp hoạt động API. Tương tự, mô tả được thêm vào các phần tử @ApiResponse khác nhau trong chú thích vùng chứa @ApiResponses cũng hiển thị ở đây, bổ sung ý nghĩa cho các phản hồi API.

Rõ ràng là chúng ta không nhận được bất kỳ giản đồ nào cho các phản hồi 400 và 404 ở trên. Như chúng ta đã xác định một @Content trống cho chúng, chỉ các mô tả của chúng được hiển thị.

**10. Hỗ trợ Kotlin**

Vì Spring Boot 2.x có hỗ trợ hạng nhất cho Kotlin, nên SpringDoc hỗ trợ ngôn ngữ JVM này cho các ứng dụng Boot 2.x. Để xem điều này hoạt động, chúng ta sẽ tạo một API Foo đơn giản trong Kotlin.

Sau khi thiết lập ban đầu, chúng ta sẽ thêm một lớp dữ liệu và một bộ điều khiển và thêm chúng vào một gói phụ của ứng dụng khởi động để khi chạy, nó chọn FooController cùng với BookController trước đó:
```java
@Entity
data class Foo(
    @Id
    val id: Long = 0,
    
    @NotBlank
    @Size(min = 0, max = 50)
    val name: String = ""
)

@RestController
@RequestMapping("/")
class FooController() {
    val fooList: List = listOf(Foo(1, "one"), Foo(2, "two"))

    @Operation(summary = "Get all foos")
    @ApiResponses(value = [
	ApiResponse(responseCode = "200", description = "Found Foos", content = [
            (Content(mediaType = "application/json", array = (
                ArraySchema(schema = Schema(implementation = Foo::class)))))]),
	ApiResponse(responseCode = "400", description = "Bad request", content = [Content()]),
	ApiResponse(responseCode = "404", description = "Did not find any Foos", content = [Content()])]
    )
    @GetMapping("/foo")
    fun getAllFoos(): List = fooList
}
```
Bây giờ khi chúng ta nhấn vào URL tài liệu API của mình, chúng ta cũng sẽ thấy API Foo:

![swa](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/s37.png?raw=true)

Để tăng cường hỗ trợ các loại Kotlin, chúng ta có thể thêm phần dependency này (tìm phiên bản mới nhất tại https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-kotlin):
```java
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-kotlin</artifactId
    <version>1.6.4</version>
</dependency>
```
Sau đó, lược đồ Foo của chúng ta sẽ trông có nhiều thông tin hơn, giống như khi chúng ta thêm JSR-303 Bean Validation:

![swag](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/s38.png?raw=true)

**11. Tổng kết**

Vậy trong bài viết này chúng ta đã học được những gì?

Chúng ta đã học được cách thiết lập springdoc-openapi trong các dự án của mình.
Chúng ta đã xem cách tích hợp springdoc-openapi với giao diện người dùng Swagger.
Chúng ta cũng đã biết cách thực hiện điều này với các dự án Spring Webflux.
Chúng ta biết được cách sử dụng Plugin Maven springdoc-openapi để tạo các định nghĩa OpenAPI cho các API của mình và xem cách hiển thị thông tin phân trang và sắp xếp từ Spring Data.
Chúng ta đã xem xét cách springdoc-openapi tạo tài liệu tự động bằng cách sử dụng chú thích xác thực bean JSR 303 và chú thích @ResponseStatus trong lớp @ControllerAdvice.
Chúng ta cũng đã học cách thêm mô tả vào API của mình bằng cách sử dụng một vài chú thích dành riêng cho OpenAPI.
Cuối cùng, chúng ta đã xem được sự hỗ trợ của OpenAPI đối với Kotlin.
Kết luận: Springdoc-openapi tạo tài liệu API theo đặc điểm kỹ thuật của OpenAPI 3. Hơn nữa, nó cũng xử lý cấu hình giao diện người dùng Swagger cho chúng tôi, làm cho việc tạo tài liệu API trở thành một nhiệm vụ khá đơn giản.
