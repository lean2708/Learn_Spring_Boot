# Validate Enum Trong Spring Boot

**1. Giới Thiệu**

Làm thế nào để validate enum và trả về message mong muốn? Đây là một câu hỏi không dễ chút nào khi mà tất cả các annotation hiện có đều không hỗ trợ validate enum. Ở bài này TayJava sẽ hướng dẫn các bạn viết annotation theo những cách khác nhau để validate các enum theo mong muốn của các bạn.

**2. Validate Enum với @Pattern**

Thật không may là hầu hết các annotation hiện có đều không thể sử dụng để validate cho enum. Chỉ 2 annotation có thể dùng là @NotNull và @Null.

Nếu chúng ta sử dụng @Pattern để validate enum

```java
@Pattern(regexp = "^ACTIVE|INACTIVE|NONE$", message = "status must be one in {ACTIVE, INACTIVE, NONE}")
private UserStatus status;
```

Thì chúng ta sẽ bị lỗi như sau:

```java
jakarta.validation.UnexpectedTypeException: HV000030: No validator could be found for constraint 'jakarta.validation.constraints.Pattern' validating type 'vn.tayjava.util.Gender'. Check configuration for 'gender'
```
– Nếu bạn thực sự muốn áp dụng @Pattern thì chỉ có thể dùng như sau:

```java
@Pattern(regexp = "^ACTIVE|INACTIVE|NONE$", message = "status must be one in {ACTIVE, INACTIVE, NONE}")
private String status;
```
**3. Validate Enum Sử Dụng Regex**

– Tạo enum UserStatus

```java
public enum UserStatus {
    @JsonProperty("active")
    ACTIVE,
    @JsonProperty("inactive")
    INACTIVE,
    @JsonProperty("none")
    NONE
}
```

– Tạo annotation @EnumPattern

```java
@Documented
@Retention(RUNTIME)
@Target({METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE})
@Constraint(validatedBy = EnumPatternValidator.class)
public @interface EnumPattern {
    String name();
    String regexp();
    String message() default "{name} must match {regexp}";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
}
```

– Tạo validator EnumPatternValidator

```java
public class EnumPatternValidator implements ConstraintValidator<EnumPattern, Enum<?>> {
    private Pattern pattern;

    @Override
    public void initialize(EnumPattern enumPattern) {
        try {
            pattern = Pattern.compile(enumPattern.regexp());
        } catch (PatternSyntaxException e) {
            throw new IllegalArgumentException("Given regex is invalid", e);
        }
    }

    @Override
    public boolean isValid(Enum<?> value, ConstraintValidatorContext context) {
        if (value == null) {
            return true;
        }

        Matcher m = pattern.matcher(value.name());
        return m.matches();
    }
}
```

– Áp dụng @EnumPattern

```java
@EnumPattern(name = "status", regexp = "ACTIVE|INACTIVE|NONE")
private UserStatus status;
```

– Ưu điểm của phương pháp này là chúng ta có thể áp dụng chung cho tất cả các enum khác nhau:

```java
@EnumPattern(name = "gender", regexp = "MALE|FEMALE|OTHER")
private Gender status;
```

**4. Validate Enum Với Một Số Giá Trị**

Phương pháp validate dựa vào so sánh giá trị tham số với các giá trị của enum.
– Tạo enum Gender

```java
public enum Gender {
    @JsonProperty("male")
    MALE,
    @JsonProperty("female")
    FEMALE,
    @JsonProperty("other")
    OTHER;
}
```

– Tạo annotation @GenderSubset

```java
@Documented
@Target({METHOD, FIELD})
@Retention(RUNTIME)
@Constraint(validatedBy = GenderSubSetValidator.class)
public @interface GenderSubset {
    Gender[] anyOf();
    String message() default "must be any of {anyOf}";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

– Tạo class GenderSubSetValidator

```java
public class GenderSubSetValidator implements ConstraintValidator<GenderSubset, Gender> {
    private Gender[] genders;

    @Override
    public void initialize(GenderSubset constraint) {
        this.genders = constraint.anyOf();
    }

    @Override
    public boolean isValid(Gender value, ConstraintValidatorContext context) {
        return value == null || Arrays.asList(genders).contains(value);
    }
}
```

– Áp dụng @GenderSubset

```java
@GenderSubset(anyOf = {MALE, FEMALE, OTHER})
private Gender gender;
```

– Ưu điểm của phương pháp này là chúng ta có thể chỉ định cụ thể một vài giá trị cần validate trong enum thay vì tất cả.

```java
@GenderSubset(anyOf = {MALE, FEMALE})
private Gender gender;
```

**5. Validate Giá Trị Của Enum**

Chúng ta sẽ so sánh giá trị của String với các giá trị trong enum trong 1 list
– Tạo annotation @EnumValue

```java
@Target({METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = EnumValueValidator.class)
public @interface EnumValue {
    String name();
    String message() default "{name} must be any of enum {enumClass}";
    Class<? extends Enum<?>> enumClass();
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

– Tạo class EnumValueValidator

```java
public class EnumValueValidator implements ConstraintValidator<EnumValue, CharSequence> {
    private List acceptedValues;

    @Override
    public void initialize(EnumValue enumValue) {
        acceptedValues = Stream.of(enumValue.enumClass().getEnumConstants())
                .map(Enum::name)
                .toList();
    }

    @Override
    public boolean isValid(CharSequence value, ConstraintValidatorContext context) {
        if (value == null) {
            return true;
        }

        return acceptedValues.contains(value.toString().toUpperCase());
    }
}
```

– Áp dụng @EnumValue

```java
@NotNull(message = "type must be not null")
@EnumValue(name = "type", enumClass = UserType.class)
private String type;
```

– Ưu điểm của phương pháp này là có thể áp dụng chung cho tất cả enum và dễ dàng handle exception.

**6. Test Validate Enum**

– cURL để test trên Postman

```java
curl --location 'http://localhost:8080/user/' \
--header 'accept: */*' \
--header 'Content-Type: application/json' \
--data-raw '{
  "firstName": "Tay",
  "lastName": "Java",
  "email": "someone@email.com",
  "phone": "0123456789",
  "dateOfBirth": "06/05/2003",
  "gender": "other",
  "username": "tayjava",
  "password": "password",
  "type": "user",
  "status": "active",
  "addresses": [
    {
      "apartmentNumber": "10",
      "floor": "10",
      "building": "A",
      "streetNumber": "10",
      "street": "Wall street",
      "city": "Hanoi",
      "country": "Vietnam",
      "addressType": 1
    }
  ]
}'
```

# Hết
