# Swagger 2 với Spring REST API

**1. Tổng quan**
Ngày nay, các components của FE và BE thường tách biệt hoàn toàn trên một ứng dụng web. Thông thường, chúng ta sẽ để API như một BE components để cho FE components hoặc các bên thứ 3 có thể tương tác.

Trong trường hợp như thế, chúng ta sẽ cần phải có các thông số kỹ thuật phù hợp cho các API, đồng thoi tài liệu API phải đầy đủ, dễ đọc và dễ theo dõi. Hơn nữa, tài liệu phải mô tả đựoc hết tất cả những thay đổi mới nhất của API. Thực hiện việc này một cách thủ công sẽ vô cùng tẻ nhạt vì vậy việc tự động quá quy trình là điều rất cần thiết.

Trong bài viết này, chúng ta sẽ xem xét đến Swagger 2 và sử dụng Springfox để triển khai đặc tả của Swagger 2.

**2. Project**
Phần tạo REST service sẽ không được đề cập đến trong bài viết này. Nếu bạn đã có một project thích hợp thì hãy sử dụng nó. Còn không thì project trong các link sau sẽ là sự lựa chọn tốt để bắt đầu:

REST API với Spring 4 và Java config
RESTful web service
**3. Thêm depndency Maven**
Như đã đề cập ở trên, chúng ta sẽ sử dụng Springfox để triển khai Swagger. Phiên bản mới nhất có thê rtimf thấy ở đây

Để thêm nó vào Maven project của chúng ta. ta cần thêm depndency vào file pom.xml:

```java
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>3.0.0</version>
</dependency>
```

**3.1. Spring boot depndency**
Đối với project loại Spring Boot ta chỉ cần thêm duy nhất depndency springfox-boot-starter là đủ:

```java
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

Chúng ta có thể thêm các starter khác nếu cần.

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.0</version>
</dependency>
```

**4. Tích hợp Swagger 2 vào project**

**4.1. Configuration Java**
Phần configuration của Swagger chủ yếu nằm ở xung quanh Docket bean:

```java
@Configuration
public class SpringFoxConfig {                                    
    @Bean
    public Docket api() { 
        return new Docket(DocumentationType.SWAGGER_2)  
          .select()                                  
          .apis(RequestHandlerSelectors.any())              
          .paths(PathSelectors.any())                          
          .build();                                           
    }
}
```

Sau khi xác định Docket bean. Hàm select() của nó sẽ trả về một instance của ApiSelectorBuilder thứ sẽ cung cấp cách để kiểm soát các endpoint mà Swagger tương tác.

Chúng ta có thể cài đặt để chọn RequestHandlers với sự giúp đỡ từ RequestHandlerSelectors và PathSelectors. Sử dụng any() cho cả 2 sẽ tạo documentation cho toàn bộ API khả dụng thông qua Swagger.

**4.2 Configuration khi không có Spring Boot**
Trong các project Spring đơn giản, ta cần kích hoạt Swagger 2 một các rõ ràng hơn. Để làm thế, ta phải dùng @EnableSwagger2WebMvc trong class configuration của chúng ta

```java
@Configuration
@EnableSwagger2WebMvc
public class SpringFoxConfig {                                    
}
```

Ngoài ra, khi không có Spring Boot, ta sẽ không có những phần auto-configuration cho các trình sử lý resource. Swagger UI thêm vào một bộ resources mà chúng ta phải configure như một phần của class mở rộng WebMvcConfigurerAdapter và được chú thích với @EnableWebMvc:

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("swagger-ui.html")
      .addResourceLocations("classpath:/META-INF/resources/");

    registry.addResourceHandler("/webjars/**")
      .addResourceLocations("classpath:/META-INF/resources/webjars/");
}
```

**4.3. Xác minh**
Để xác minh xem Springfox có hoạt động hay không, ta cần truy cập vào URL sau: 

```java
http://localhost:8080/spring-security-rest/api/v2/api-docs
```

Kết quả sẽ là một JSON response với một số lượng lớn các cặp key-value, thứ mà không hề dễ dàng để đọc hiểu. May thay chúng ta có Swagger UI để sử lý vấn đề này.

**5. Swagger UI**
Swagger UI là một giải pháp giúp sự tuơng tác của user với tài liệu của Swagger-generated API một cách dễ dàng hơn.

**5.1. Kích hoạt Springfox's Swagger UI**

Để sử dụng Swagger UI, ta cần thêm phần dependency Maven:

```java
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

Bây giờ ta có thể test bằng cách truy cập URL sau: 

```java
http://localhost:8080/your-app-root/swagger-ui/
```

Trong trường hợp của chúng ta, URL chính xác sẽ là: 

```java
http://localhost:8080/spring-security-rest/api/swagger-ui/
```

Kết quả nhận được sẽ tuơng tự như sau:
