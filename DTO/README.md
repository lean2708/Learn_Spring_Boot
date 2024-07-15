# 1. DTO là gì?
DTO là viết tắt của Data Transfer Object, được sử dụng trong lập trình để chuyển dữ liệu giữa các thành phần khác nhau của một ứng dụng. Thông thường, một DTO là một đối tượng đơn giản, không có logic xử lý phức tạp, chỉ chứa các thuộc tính và phương thức getter và setter để truy cập và cập nhật các thuộc tính đó.

**Mục đích chính của DTO là giảm thiểu việc truyền dữ liệu liên tục giữa các thành phần khác nhau của ứng dụng, đồng thời tăng tính rõ ràng và dễ bảo trì của mã nguồn. DTO cũng có thể được sử dụng để định nghĩa các giao diện giữa các ứng dụng hoặc giữa các tầng của một ứng dụng, chẳng hạn như giữa tầng lưu trữ và tầng giao diện người dùng.**

# 2. Tại sao cần có DTO?
Tách biệt các thành phần: Khi một ứng dụng được chia thành nhiều thành phần khác nhau (ví dụ: tầng giao diện người dùng, tầng xử lý logic, tầng lưu trữ dữ liệu), các thành phần này cần trao đổi dữ liệu với nhau. Sử dụng DTO giúp tách biệt các thành phần và giảm thiểu sự phụ thuộc giữa chúng.

Hiệu suất tốt hơn: Khi dữ liệu được truyền giữa các thành phần, việc truyền toàn bộ đối tượng có thể tốn kém về hiệu suất. Sử dụng DTO giúp chỉ truyền các thuộc tính cần thiết, giảm thiểu kích thước dữ liệu được truyền qua lại giữa các thành phần, giúp tăng hiệu suất của ứng dụng.

Bảo mật: Sử dụng DTO có thể giúp bảo mật thông tin nhạy cảm. Ví dụ, nếu một đối tượng chứa thông tin người dùng được truyền giữa các thành phần, một số thông tin có thể không cần thiết và phải được loại bỏ để tránh rò rỉ thông tin.

Khả năng mở rộng: Sử dụng DTO giúp tăng tính mở rộng của ứng dụng bằng cách giảm thiểu sự phụ thuộc giữa các thành phần. Khi các thành phần của ứng dụng thay đổi, việc thay đổi các DTO có thể dễ dàng hơn và không ảnh hưởng đến các thành phần khác.

Dễ bảo trì: Sử dụng DTO giúp mã nguồn trở nên rõ ràng hơn và dễ bảo trì hơn, bởi vì các đối tượng DTO chỉ chứa dữ liệu và không có logic phức tạp.

# 3. Cách sử dụng DTO
DTO là một cấu trúc dữ liệu phẳng và không chứa logic, dữ liệu được ánh xạ từ domain model sang DTO và ngược lại thông qua một thành phần gọi là Mapper.

![dto1](https://raw.githubusercontent.com/lean2708/Learn_Spring_Boot/2d369a6ee7aa8f6378140825fd6362eb80dc1523/docs/images/dto1.svg)

Bây giờ ta có Class User để lưu trữ các thông tin của một User

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private String email;
    private String phone;
    private String address;
    private String avatar;
    private String password;
}
```

Tương ứng ta có Class UserDTO để trả dữ liệu về cho người dùng chứa đầy đủ thông tin như Class User nhưng không có password

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class UserDTO {
    private int id;
    private String name;
    private String email;
    private String phone;
    private String address;
    private String avatar;
}
```

Bây giờ để chuyển đổi ta sẽ tạo thêm 1 class có tên là UserMapper

```java
public class UserMapper {
    public UserDTO toUserDTO(User user) {
        UserDTO userDTO = new UserDTO();
        userDTO.setId(user.getId());
        userDTO.setName(user.getName());
        userDTO.setEmail(user.getEmail());
        userDTO.setPhone(user.getPhone());
        userDTO.setAddress(user.getAddress());
        userDTO.setAvatar(user.getAvatar());

        return userDTO;
    }
}
```

Vậy là đã chuyển đổi xong, giờ ta sẽ định nghĩa lại dữ liệu trả về trong Service và Controller

```java
//Service
@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;

    // Lấy thông tin của user theo id
    public UserDTO getUserById(int id) {
        User user = userRepository.findById(id).orElseThrow(() -> {
            throw new NotFoundException("Not found user with id = " + id);
        });

        return UserMapper.toUserDTO(user);
    }

    // Lấy danh sách user ở dạng DTO
    public List<UserDto> getUsers() {
        return userRepository.findAll()
                .stream()
                .map(UserMapper::toUserDTO)
                .toList();
    }
}

//Controller
@RestController
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;

    // Lấy chi tiết user theo id
    @GetMapping("/users/{id}")
    public ResponseEntity<?> getUserById(@PathVariable int id) {
        UserDTO userDTO = userService.getUserById(id);
        return ResponseEntity.ok(userDTO);
    }

    // Lấy danh sách user
    @GetMapping("/users")
    public ResponseEntity<?> getUsers() {
        List<UserDTO> userDTOs = userService.getUsers();
        return ResponseEntity.ok(userDTOs);
    }
}
```

OK, vậy là xong rồi, giờ ta test với postman xem sao nhé

**Database giả định**
```java
public class UserDatabase {
    public static List<User> users = new ArrayList<>(List.of(
            new User(1, "Xuân Vũ", "xuanvu@gmail.com", "0123456789", "Tỉnh Thái Bình", null, "111"),
            new User(2, "Nguyễn Thu Hằng", "thuhangvnua@gmail.com", "0123456789", "Tỉnh Nam Định", null, "222"),
            new User(3, "Bùi Phương Loan", "loan@techmaster.vn", "0123456789", "Tỉnh Hưng Yên", null, "333")
    ));
}
```

**Trả về User theo Id**

![dto2](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/dto2.png?raw=true)

**Trả về List<User>**

![dto3](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/images/dto3.png?raw=true)

# Tạm kết
Trong bài viết này, chúng ta đã thấy định nghĩa của Mẫu DTO , tại sao nó tồn tại và cách triển khai nó.
