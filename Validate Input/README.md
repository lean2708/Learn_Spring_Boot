# Validate Input

**1. Tại Sao Cần Validate Data**

Validate là bước đầu tiên và cực kỳ quan trọng bởi vì nó quyết định tiêu chuẩn dữ liệu đầu vào đối với 1 API. Đối với Backend thì việc validate data là bước bắt buộc bất kể phía Frontend có validate data trước khi gửi request đến Backend hay không. Bởi vì Backend là phần làm việc trực tiếp với database nếu cho phép nhập dữ liệu không hợp lệ sẽ dẫn hàng loạt các vẫn đề phát sinh về sau. Nguy hiểm hơn nữa là nó cũng dễ dàng bị hacker tấn công bằng việc gửi mã độc vào database.

**2. Add dependency**

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

**3. Các Annotation Thường Dùng**

**@NotNull**: để nói rằng một trường giá trị không được rỗng.

**@NotEmpty**: để nói rằng trường danh sách không được để trống.

**@NotBlank**: để nói rằng trường giá trị chuỗi không được là chuỗi trống (tức là nó phải có ít nhất một ký tự).

**@Min** và **@Max**: để nói rằng một trường giá trị số chỉ hợp lệ khi giá trị của nó cao hơn hoặc thấp hơn một giá trị nhất định.

**@Pattern**: để nói rằng trường giá trị chuỗi chỉ hợp lệ khi nó khớp với một biểu thức chính quy nhất định.

**@Email**: để nói rằng trường giá trị chuỗi phải là địa chỉ email hợp lệ.

**4. Validate Request Body/Payload**

– **UserRequestDTO.classs**

```java
public class UserRequestDTO implements Serializable {

    @NotBlank(message = "firstName must be not blank") // Khong cho phep gia tri blank
    private String firstName;

    @NotNull(message = "lastName must be not null") // Khong cho phep gia tri null
    private String lastName;

    @Email(message = "email invalid format") // Chi chap nhan nhung gia tri dung dinh dang email
    private String email;

    @Pattern(regexp = "^\\d{10}$", message = "phone invalid format")
    private String phone;

    @NotNull(message = "dateOfBirth must be not null")
    @DateTimeFormat(iso = DateTimeFormat.ISO.DATE)
    @JsonFormat(pattern = "MM/dd/yyyy")
    private Date dateOfBirth;

    @NotNull(message = "username must be not null")
    private String username;

    @NotNull(message = "password must be not null")
    private String password;

    @NotEmpty(message = "addresses can not empty")
    private Set<Address> addresses;
   
    // setter and setter
    // constructor
```

– Thêm annotation **@Valid** trước **@RequestBody** tại UserController.class

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @PostMapping("/")
    public String addUser(@Valid @RequestBody UserRequestDTO user) {
        return "User added";
    }
}
```

**5. Validate Parameter và PathVariable**

**5.1 Validate Pathvariable**

– Add **@Validated** vào UserController

```java
@RestController
@RequestMapping("/user")
@Validated
public class UserController {
```

– Validate userId với giá trị tối thiểu là 1 và sử dụng message mặc định của Spring Boot {functionName}.{PathVariableName}: must be greater than or equal to {Value}

```java
@PatchMapping("/{userId}")
public String updateStatus(@Min(1) @PathVariable int userId, @RequestParam boolean status) {
    return "User Status changed";
}
```

– Validate userId và định nghĩa message lỗi trả về là userId must be greater than 0

```java
@DeleteMapping("/{userId}")
public String deleteUser(@PathVariable @Min(value = 1, message = "userId must be greater than 0") int userId) {
    return "User deleted";
}
```

**5.2 Validate RequestParam**

– Áp dụng tương tự như validate PathVariable

```java
@GetMapping("/list")
    public List getAllUser(@RequestParam(defaultValue = "0", required = false) int pageNo, @Min(10) @RequestParam(defaultValue = "20", required = false) int pageSize) {
        return List.of(new UserRequestDTO("Tay", "Java", "admin@tayjava.vn", "0123456789"),
                new UserRequestDTO("Leo", "Messi", "leomessi@email.com", "0123456456"));
    }
```

**6. Validator Annotation**

Ngoài các annotation của Spring Boot các bạn cũng có thể tự viết annotation để sử dụng. Dưới đây là ví dụ 1 annotation để validate số phone.

– **PhoneValidator.class**

```java
public class PhoneValidator implements ConstraintValidator<PhoneNumber, String> {

    @Override
    public void initialize(PhoneNumber phoneNumberNo) {
    }

    @Override
    public boolean isValid(String phoneNo, ConstraintValidatorContext cxt) {
        if(phoneNo == null){
            return false;
        }
        //validate phone numbers of format "0902345345"
        if (phoneNo.matches("\\d{10}")) return true;
            //validating phone number with -, . or spaces: 090-234-4567
        else if(phoneNo.matches("\\d{3}[-\\.\\s]\\d{3}[-\\.\\s]\\d{4}")) return true;
            //validating phone number with extension length from 3 to 5
        else //return false if nothing matches the input
            if(phoneNo.matches("\\d{3}-\\d{3}-\\d{4}\\s(x|(ext))\\d{3,5}")) return true;
            //validating phone number where area code is in braces ()
        else return phoneNo.matches("\\(\\d{3}\\)-\\d{3}-\\d{4}");
    }

}
```

– **PhoneNumber.class**

```java
@Documented
@Constraint(validatedBy = PhoneValidator.class)
@Target( { ElementType.METHOD, ElementType.FIELD })
@Retention(RetentionPolicy.RUNTIME)
public @interface PhoneNumber {
    String message() default "Invalid phone number";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

– **UserRequestDTO.class**

```java
//@Pattern(regexp = "^\\d{10}$", message = "phone invalid format")
@PhoneNumber(message = "phone invalid format")
private String phone;
```

**7. Test API với Postman**

– Test validate RequestBody/Payload

```java
curl --location 'http://localhost:8080/user/' \
--header 'accept: */*' \
--header 'Content-Type: application/json' \
--data-raw '{
    "firstName": "Tay", // nhap gia tri "" hoac null de test
    "lastName": "Java", // nhap gia tri null de test
    "email": "admin@tayjava.vn", // nhap gia tri admin@tayjava de test
    "phone": "090-123-4567", // nhap gia tri 118228 de test
    "dateOfBirth": "01/01/2020", // nhap gia tri 01-01-2020 de test
    "username": "admin", // nhap gia tri null de test
    "password": "password", // nhap gia tri null de test
    "addresses": [ // nhap gia tri [] de test
        {
            "apartmentNumber": "10",
            "floor": "10",
            "building": "A",
            "streetNumber": "10",
            "street": "Vo Nguyen Giap",
            "city": "Hanoi",
            "country": "Vietnam",
            "addressType": 1
        }
    ]
}'
```

Response:

```java
{
    "timestamp": "2024-03-28T21:10:38.397+00:00",
    "status": 400,
    "error": "Bad Request",
    "path": "/user/"
}

hoặc

User added

// xem message lỗi ở trong log nhé
```

– Test PathVariable

```java
// Thay doi /user/0 de test

curl --location --request DELETE 'http://localhost:8080/user/1' \
--header 'accept: */*'

Response:
{
    "timestamp": "2024-03-28T21:16:37.555+00:00",
    "status": 500,
    "error": "Internal Server Error",
    "path": "/user/0"
}

hoặc
User deleted

// xem message lỗi ở trong log nhé
```

– Test validate RequestParam

```java
// thay doi pageSize=9 de test

curl -X 'GET' \
  'http://localhost:8080/user/list?pageNo=0&pageSize=10' \
  -H 'accept: */*'

Response:
{
    "timestamp": "2024-03-28T21:17:41.080+00:00",
    "status": 500,
    "error": "Internal Server Error",
    "path": "/user/list"
}

hoặc

[
    {
        "firstName": "Tay",
        "lastName": "Java",
        "email": "admin@tayjava.vn",
        "phone": "0123456789",
        "dateOfBirth": null,
        "username": null,
        "password": null,
        "addresses": null
    },
    {
        "firstName": "Leo",
        "lastName": "Messi",
        "email": "leomessi@email.com",
        "phone": "0123456456",
        "dateOfBirth": null,
        "username": null,
        "password": null,
        "addresses": null
    }
]

// xem message lỗi ở trong log nhé
```

# Hết
