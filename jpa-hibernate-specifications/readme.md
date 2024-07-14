# Tìm hiểu về Spring Data Specifications

Nếu bạn đang tìm kiếm một cách tốt hơn để quản lý các truy vấn của mình hoặc muốn tạo các truy vấn động và an toàn kiểu chữ thì bạn có thể tìm thấy giải pháp của mình trong Thông số kỹ thuật JPA của Spring Data.

**1. Vậy thì Specifications là gì?**

Spring Data JPA Specifications là một công cụ khác mà chúng tôi sử dụng để thực hiện các truy vấn cơ sở dữ liệu với Spring hoặc Spring Boot.

Specifications được xây dựng dựa trên Criteria API.

Khi tạo một truy vấn Criteria chúng ta bắt buộc phải tụ xây dựng và quản lý các đối tượng Root, CriteraQuery, và CriteriaBuilder:

```java
EntityManager entityManagr = getEntityManager();

CriteriaBuilder builder = entityManager.getCriteriaBuilder();

CriteriaQuery<Product> productQuery = builder.createQuery(Product.class);

Root<Person> personRoot = productQuery.from(Product.class);
```

Specifications được xây dựng dựa trên Criteria API để đơn giản hóa các thao tác của lập trình viên. Đơn giản là chúng ta chỉ cần triển khai Specification interface:

```java
interface Specification<T>{

  Predicate toPredicate(Root<T> root,
            CriteriaQuery<?> query,
            CriteriaBuilder criteriaBuilder);

}
```

**2. Tại sao chúng ta cần Specifications???**

Một trong những cách thức phổ biến nhất để thực hiện các truy vấn trong Spring boot là sử dụng Query Methods như dưới đây:

```java
interface ProductRepository extends JpaRepository<Product, String>,
                  JpaSpecificationExecutor<Product> {

  List<Product> findAllByNameLike(String name);

  List<Product> findAllByNameLikeAndPriceLessThanEqual(
                  String name,
                  Double price
                  );

  List<Product> findAllByCategoryInAndPriceLessThanEqual(
                  List<Category> categories,
                  Double price
                  );

  List<Product> findAllByCategoryInAndPriceBetween(
                  List<Category> categories,
                  Double bottom,
                  Double top
                  );

  List<Product> findAllByNameLikeAndCategoryIn(
                  String name,
                  List<Category> categories
                  );

  List<Product> findAllByNameLikeAndCategoryInAndPriceBetween(
                  String name,
                  List<Category> categories,
                  Double bottom,
                  Double top
                  );
}
```

Vấn đề với các query methods là chúng ta chỉ có thể chỉ định một số tiêu chí cố định. Ngoài ra, số lượng các query methods tăng lên nhanh chóng khi các trường hợp sử dụng tăng lên.

Tại một số thời điểm, có nhiều tiêu chí chồng chéo trên các query methods và nếu có sự thay đổi trong bất kỳ tiêu chí nào trong số đó, chúng ta sẽ phải thực hiện các thay đổi trong nhiều query methods.

Ngoài ra, độ dài của query methods có thể tăng đáng kể khi chúng ta có tên trường dài và nhiều tiêu chí trong truy vấn của mình. Ngoài ra, có thể mất một lúc để ai đó hiểu được một truy vấn dài dòng như vậy và mục đích của nó:

```java
List<Product> findAllByNameLikeAndCategoryInAndPriceBetweenAndManufacturingPlace_State(String name,
                                             List<Category> categories,
                                             Double bottom, Double top,
                                             STATE state);
```

Với Specifications, chúng ta có thể giải quyết những vấn đề này bằng cách tạo atomic predicates. Và bằng cách đặt cho predicates đó một cái tên có ý nghĩa, chúng ta có thể xác định rõ mục đích của chúng. Chúng ta sẽ có thể chuyển đổi những điều trên thành một truy vấn có ý nghĩa hơn nhiều trong phần viết các truy vấn với Specifications.

Specifications cho phép chúng ta viết các truy vấn linh hoạt theo yêu cầu. Do đó, chúng ta có thể xây dựng các truy vấn động dựa trên đầu vào của người dùng. Chúng ta sẽ xem điều này chi tiết hơn trong phần truy vấn động với Specifications.

**3. Cài đặt**

Đầu tiên, chúng ta cần có dependency Spring Data Jpa trong file pom của chúng ta:

```java
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-jpamodelgen</artifactId>
    <version>6.0.0.Alpha7</version>
    <type>pom</type>
    <scope>provided</scope>
</dependency>
```

Chúng ta thêm dependency hibernate-jpamodelgen annotation processor sẽ tạo ra các lớp siêu mô hình tĩnh của các thực thể của chúng ta.

**4. Generated Metamodel**

Các lớp được sinh bởi trình tạo mô hình Hibernate JPA sẽ cho phép chúng ta viết các truy vấn theo cách strongly-typed.

Ví dụ, hãy xem thực thể JPA Distributor:

```java
@Entity
public class Distributor {
  @Id
  private String id;

  private String name;

  @OneToOne
  private Address address;

}
```

Lớp metamodel của thực thể Distributor sẽ trông giống như sau:

```java
@Generated(value = "org.hibernate.jpamodelgen.JPAMetaModelEntityProcessor")
@StaticMetamodel(Distributor.class)
public abstract class Distributor_ {

  public static volatile SingularAttribute<Distributor, Address> address;
  public static volatile SingularAttribute<Distributor, String> name;
  public static volatile SingularAttribute<Distributor, String> id;
  public static final String ADDRESS = "address";
  public static final String NAME = "name";
  public static final String ID = "id";

}
```

Bây giờ, chúng ta có thể sử dụng Distributor_.name trong các truy vấn của mình thay vì sử dụng trực tiếp tên trường của các thực thể. Lợi ích chính của việc này là các truy vấn sử dụng metamodel sẽ phát triển với các thực thể và dễ dàng cấu trúc lại hơn nhiều so với các truy vấn chuỗi.

**5. Viết các truy vấn với Specifications**

Hãy chuyển truy vấn findAllByNameLike() được đề cập ở trên thành Specification:

```java
List<Product> findAllByNameLike(String name);
Specification tương đương của phương thức truy vấn này là:

private Specification<Product> nameLike(String name){
  return new Specification<Product>() {
   @Override
   public Predicate toPredicate(Root<Product> root,
                  CriteriaQuery<?> query,
                  CriteriaBuilder criteriaBuilder) {
     return criteriaBuilder.like(root.get(Product_.NAME), "%"+name+"%");
   }
  };
}
```

Với Java 8 Lambda, chúng ta có thể đơn giản hóa những điều trên thành như sau:

```java
private Specification<Product> nameLike(String name){
  return (root, query, criteriaBuilder)
      -> criteriaBuilder.like(root.get(Product_.NAME), "%"+name+"%");
}
```
Chúng ta cũng có thể viết nó trên cùng một dòng tại vị trí trong code mà chúng ta cần dùng nó:

```java
Specification<Product> nameLike =
      (root, query, criteriaBuilder) ->
         criteriaBuilder.like(root.get(Product_.NAME), "%"+name+"%");
```

Nhưng điều này làm mất đi mục đích tái sử dụng code, vì vậy hãy tránh điều này trừ khi được yêu cầu sử dụng.

Để thực thi Specifications, chúng ta cần mở rộng giao diện JpaSpecificationExecutor trong kho lưu trữ Spring Data JPA:

```java
interface ProductRepository extends JpaRepository<Product, String>,
                  JpaSpecificationExecutor<Product> {
}
```
Giao diện JpaSpecificationExecutor thêm các phương thức sẽ cho phép chúng ta thực thi các Specifications, ví dụ:
```java
List<T> findAll(Specification<T> spec);

Page<T> findAll(Specification<T> spec, Pageable pageable);

List<T> findAll(Specification<T> spec, Sort sort);
```

Cuối cùng, để thực hiện truy vấn, chúng ta có thể chỉ cần gọi:

```java
List<Product> products = productRepository.findAll(namelike("reflectoring"));
```

Chúng ta cũng có thể tận dụng các hàm findAll() overloaded với Pagable và Sort trong trường hợp chúng ta đang mong đợi một số lượng lớn các bản ghi trong kết quả hoặc muốn các bản ghi theo thứ tự được sắp xếp.

Giao diện Specification cũng có các phương thức static công khai and(), or(), và where() cho phép chúng ta kết hợp nhiều specifications. Nó cũng cung cấp một phương thức not() cho phép chúng ta phủ định một Specification.

Cùng xem một ví dụ:

```java
public List<Product> getPremiumProducts(String name,
                    List<Category> categories) {
  return productRepository.findAll(
      where(belongsToCategory(categories))
          .and(nameLike(name))
          .and(isPremium()));
}

private Specification<Product> belongsToCategory(List<Category> categories){
  return (root, query, criteriaBuilder)->
      criteriaBuilder.in(root.get(Product_.CATEGORY)).value(categories);
}

private Specification<Product> isPremium() {
  return (root, query, criteriaBuilder) ->
      criteriaBuilder.and(
          criteriaBuilder.equal(
              root.get(Product_.MANUFACTURING_PLACE)
                        .get(Address_.STATE),
              STATE.CALIFORNIA),
          criteriaBuilder.greaterThanOrEqualTo(
              root.get(Product_.PRICE), PREMIUM_PRICE));
}
```

Ở đây, chúng ta đã kết hợp **specifications belongsToCategory(), nameLike() và isPremium()** thành một bằng cách sử dụng các hàm **where()** và **and()**. Ngoài ra, hãy chú ý cách **isPremium()** mang lại nhiều ý nghĩa hơn cho truy vấn.

Hiện tại, **isPremium()** đang kết hợp hai vị từ, nhưng nếu muốn, chúng ta có thể tạo specifications riêng cho từng vị từ đó và kết hợp lại với and(). Hiện tại, chúng ta sẽ giữ nguyên như vậy, vì các vị từ được sử dụng trong **isPremium()** rất cụ thể cho truy vấn đó và nếu trong tương lai, chúng ta cũng cần sử dụng chúng trong các truy vấn khác thì chúng ta luôn có thể chia nhỏ chúng ra mà không ảnh hưởng đến nơi khác sử dụng hàm isPremium().

**6. Truy vấn động với Specifications**

Giả sử chúng ta muốn tạo một **API** cho phép khách hàng của chúng ta tìm nạp tất cả các sản phẩm và cũng lọc chúng dựa trên một số thuộc tính như danh mục, giá cả, màu sắc, v.v. Ở đây, chúng ta không biết trước sự kết hợp của các thuộc tính khách hàng sẽ sử dụng để lọc các sản phẩm.
Một cách để xử lý điều này là viết các phương thức truy vấn cho tất cả các kết hợp có thể có nhưng điều đó sẽ yêu cầu viết rất nhiều phương thức truy vấn. Và con số đó sẽ tăng lên một cách đáng kể khi chúng ta thêm các trường mới.
Một giải pháp tốt hơn là lấy các vị từ trực tiếp từ clients và chuyển đổi chúng thành các truy vấn cơ sở dữ liệu bằng cách sử dụng specifications. Client chỉ cần cung cấp cho chúng ta danh sách của Filter và backend của chúng ta sẽ lo phần còn lại. Hãy xem cách chúng ta có thể làm điều này.

Đầu tiên, hãy tạo một đối tượng đầu vào để lấy các bộ lọc từ clients:

```java
public class Filter {
  private String field;
  private QueryOperator operator;
  private String value;
  private List<String> values;
}
```
Chúng ta sẽ hiển thị đối tượng này cho clients của chúng ta thông qua REST API.

Thứ hai, chúng ta cần viết một hàm sẽ chuyển đổi Filter thành Specification:

```java
private Specification<Product> createSpecification(Filter input) {
  switch (input.getOperator()){

    case EQUALS:
       return (root, query, criteriaBuilder) ->
          criteriaBuilder.equal(root.get(input.getField()),
           castToRequiredType(root.get(input.getField()).getJavaType(),
                              input.getValue()));

    case NOT_EQUALS:
       return (root, query, criteriaBuilder) ->
          criteriaBuilder.notEqual(root.get(input.getField()),
           castToRequiredType(root.get(input.getField()).getJavaType(),
                              input.getValue()));

    case GREATER_THAN:
       return (root, query, criteriaBuilder) ->
          criteriaBuilder.gt(root.get(input.getField()),
           (Number) castToRequiredType(
                  root.get(input.getField()).getJavaType(),
                              input.getValue()));

    case LESS_THAN:
       return (root, query, criteriaBuilder) ->
          criteriaBuilder.lt(root.get(input.getField()),
           (Number) castToRequiredType(
                  root.get(input.getField()).getJavaType(),
                              input.getValue()));

    case LIKE:
      return (root, query, criteriaBuilder) ->
          criteriaBuilder.like(root.get(input.getField()),
                          "%"+input.getValue()+"%");

    case IN:
      return (root, query, criteriaBuilder) ->
          criteriaBuilder.in(root.get(input.getField()))
          .value(castToRequiredType(
                  root.get(input.getField()).getJavaType(),
                  input.getValues()));

    default:
      throw new RuntimeException("Operation not supported yet");
  }
}
```

Ở đây chúng ta đã hỗ trợ một số hành động như **EQUALS, LESS_THAN, IN, v.v.** Chúng ta cũng có thể thêm nhiều thao tác khác dựa trên yêu cầu.

Bây giờ, như chúng ta đã biết, Criteria API cho phép chúng ta viết các truy vấn an toàn. Vì vậy, các giá trị mà chúng ta cung cấp phải thuộc loại tương thích với loại trường của chúng ta. Filter nhận giá trị là String, có nghĩa là chúng ta sẽ phải truyền các giá trị thành một kiểu bắt buộc trước khi chuyển nó đến CriteriaBuilder:

```java
private Object castToRequiredType(Class fieldType, String value) {
  if(fieldType.isAssignableFrom(Double.class)) {
    return Double.valueOf(value);
  } else if(fieldType.isAssignableFrom(Integer.class)) {
    return Integer.valueOf(value);
  } else if(Enum.class.isAssignableFrom(fieldType)) {
    return Enum.valueOf(fieldType, value);
  }
  return null;
}

private Object castToRequiredType(Class fieldType, List<String> value) {
  List<Object> lists = new ArrayList<>();
  for (String s : value) {
    lists.add(castToRequiredType(fieldType, s));
  }
  return lists;
}
```

Cuối cùng, chúng ta thêm một chức năng sẽ kết hợp nhiều Filters vào một specification:

```java
private Specification<Product> getSpecificationFromFilters(List<Filter> filter){
  Specification<Product> specification =
            where(createSpecification(queryInput.remove(0)));
  for (Filter input : filter) {
    specification = specification.and(createSpecification(input));
  }
  return specification;
}
```

Bây giờ, hãy thử tìm nạp tất cả các sản phẩm thuộc danh mục MOBILE hoặc TV APPLIANCE và có giá dưới 1000 bằng cách sử dụng trình tạo truy vấn specifications động mới của chúng ta.

```java
Filter categories = Filter.builder()
     .field("category")
     .operator(QueryOperator.IN)
     .values(List.of(Category.MOBILE.name(),
             Category.TV_APPLIANCES.name()))
     .build();

Filter lowRange = Filter.builder()
    .field("price")
    .operator(QueryOperator.LESS_THAN)
    .value("1000")
    .build();

List<Filter> filters = new ArrayList<>();
filters.add(lowRange);
filters.add(categories);

productRepository.getQueryResult(filters);
```

Các đoạn mã trên sẽ phù hợp với hầu hết các trường hợp filter nhưng vẫn còn rất nhiều chỗ để cải thiện. Chẳng hạn như cho phép các truy vấn dựa trên các thuộc tính thực thể lồng nhau (manufacturingPlace.state) hoặc giới hạn các trường mà chúng ta muốn cho phép các filters. Hãy coi đây là một bài toán mở.

**7. Khi nào ta nên sử dụng Specifications qua Query Methods?**

Một câu hỏi xuất hiện trong đầu là nếu chúng ta có thể viết bất kỳ truy vấn nào với các specifications thì khi nào chúng ta dùng các query methods? Hay chúng ta có nên dùng chúng nhiều hơn không? Tôi tin rằng có một vài trường hợp mà các query methods có thể hữu ích.

Giả sử thực thể của chúng ta chỉ có một số ít trường và nó chỉ cần được truy vấn theo một cách nhất định thì tại sao lại phải viết Specifications khi chúng ta chỉ có thể viết một query method?

Và nếu các yêu cầu trong tương lai xuất hiện đối với nhiều truy vấn hơn cho thực thể đã cho thì chúng tôi luôn có thể cấu trúc lại nó để sử dụng Specifications. Ngoài ra, Specifications sẽ không hữu ích trong trường hợp chúng ta muốn sử dụng các tính năng dành riêng cho cơ sở dữ liệu trong một truy vấn, chẳng hạn như thực hiện các truy vấn JSON với PostgresSQL.

**Tổng kết**

**Specifications** cung cấp cho chúng ta một cách để viết các truy vấn có thể sử dụng lại và các API mà chúng ta có thể kết hợp và xây dựng các truy vấn phức tạp hơn.
Nói chung, **Spring JPA Specifications** là một công cụ tuyệt vời cho dù chúng ta muốn tạo các vị từ có thể sử dụng lại hay muốn tạo các truy vấn an toàn.
