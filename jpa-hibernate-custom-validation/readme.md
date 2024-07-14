# Tự tạo Validator để kiểm tra Model và Entity

**Hibernate và Java** đã cung cấp cho chúng ta nhiều **Annotation** để kiểm tra dữ liệu của model. Nhưng đôi khi, chúng ta cần kiểm tra các điều kiện cụ thể phù hợp với logic kinh doanh của dự án. Để làm điều này, chúng ta có thể tự tạo **Validator** riêng cho mình. Trong bài viết này, chúng tôi sẽ hướng dẫn bạn cách tự tạo Validator bằng Hibernate Validator và Spring Boot.

**Cài đặt**

Trước khi tạo Validator, chúng ta cần chuẩn bị một số thành phần cơ bản.

**Tạo Model**

Chúng ta sẽ sử dụng một model đơn giản là User như sau:

``java
@Data
public class User {
    private String kungfutechId;
}
```

Tạo Controller
Chúng ta cần một Controller để giao tiếp với client:

```java
@RestController
@RequestMapping("api/v1/users")
public class UserController {

    @PostMapping
    public Object createUser(@Valid @RequestBody User user) {
        return user;
    }
}
```

**Cài đặt Hibernate Validator**

Chúng ta cần thêm Hibernate Validator vào Maven pom.xml:

```java
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.1.0.Final</version>
</dependency>
```

# Tạo Annotation

**Định nghĩa Annotation**

Đầu tiên, chúng ta cần định nghĩa một Annotation mới. Trong ví dụ này, chúng ta sẽ tạo Annotation **@KungfutechId** để kiểm tra tính hợp lệ của trường kungfutechId:

```java
@Documented
@Constraint(validatedBy = KungfutechIdValidator.class)
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface KungfutechId {
    String message() default "KungfutechId must start with kungfutech://";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

**Gắn Annotation lên Model**

Bây giờ, chúng ta có thể gắn @KungfutechId lên trường cần kiểm tra tính hợp lệ trong model User:

```java
@Data
public class User {
    @KungfutechId
    private String kungfutechId;
}
```

**Tạo Validator**

Sau khi đã tạo Annotation, chúng ta cần tạo một Validator để kiểm tra tính hợp lệ của trường dựa trên @KungfutechId. Validator này sẽ kiểm tra xem trường kungfutechId có bắt đầu bằng chuỗi "kungfutech://" hay không.

```java
import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

public class KungfutechIdValidator implements ConstraintValidator<KungfutechId, String> {
    private static final String KUNGFUTECH_PREFIX = "kungfutech://";

    @Override
    public boolean isValid(String s, ConstraintValidatorContext constraintValidatorContext) {
        if (s == null || s.isEmpty()) return false;

        return s.startsWith(KUNGFUTECH_PREFIX);
    }
}
```

**Chạy thử**

Bây giờ, chúng ta có thể thực hiện kiểm tra tính hợp lệ của trường kungfutechId trong User thông qua @KungfutechId Annotation. Dưới đây là kết quả của việc chạy thử các request:

Request một User hợp lệ
Request:

```java
{
  "kungfutechId": "kungfutech://user_1"
}
```

Kết quả (200 OK):

```java
{
  "kungfutechId": "kungfutech://user_1"
}
```

Request một User không hợp lệ:

Request:

```java
{
  "kungfutechId": "Laula://user_1"
}
```

Kết quả (400 Bad Request)
