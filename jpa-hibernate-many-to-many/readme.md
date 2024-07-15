# ManyToMany

Quan hệ N-N là quan hệ giữa hai tập thực thể trong đó một thực thể của tập này có thể liên kết với 0, 1 hoặc nhiều thực thể của tập kia, và ngược lại. Thường quan hệ N-N có thêm phần dữ liệu giao nhau để thêm thông tin cụ thể cho mối quan hệ.

**1. Ví dụ**

Ta có mối quan hệ giữa bảng bài báo và bảng thẻ, một bài báo có thể có nhiều thẻ, 1 thẻ có thể xuất hiện trong nhiều bài báo.

![mm](https://github.com/lean2708/Learn_Spring_Boot/blob/master/docs/image3/mm1.jpg?raw=true)

**2. Mapping các entity**

**Cách 1: Không tạo entity thứ 3**

Ta có quan hệ giữa Article và Tag như sau
**Article Entity**

```java
@Entity(name = "article")
public class Article {
  // ...
  @ManyToMany
  @JoinTable(
      name = "article_tag",
      joinColumns = @JoinColumn(name = "article_id"),
      inverseJoinColumns = @JoinColumn(name = "tag_id")
  )
  private Set<Tag> tags = new HashSet<>();
}
```

**Tag Entity**

```java
@Entity(name = "tag")
@Table(name = "tag")
public class Tag {

@ManyToMany(mappedBy = "tags")
  Set<Article> articles = new HashSet<>();
}
```

Trong Article entity, ta sử dụng annotation **@ManyToMany** và **@JoinTable**, **@JoinTable** được đặt ở entity là chủ sở hữu của mối quan hệ.
Trong **@JoinTable** ta sử dụng các thuộc tính sau:
thuộc tính **name** là tên bảng mới được sinh ra trong CSDL
thuộc tính **JoinColumns** chỉ ra khóa ngoại - ứng với khóa chính của entity là chủ sở hữu của mối quan hệ (Article entity)
thuộc tính **inverseJoinColumns** chỉ ra khóa ngoại - ứng với khóa chính của entity còn lại không phải chủ sở hữu của mối quan hệ
Trong annotation **@ManyToMany** của Tag Entity ta sử dụng thuộc tính mappedBy = “tags”, thuộc tính này chỉ ra tên của thuộc tính ở entity đối diện dùng để mapping cho mối quan hệ (thuộc tính của entity đối diện có tên là tags)
Ta thực thi ứng dụng như sau

```java
@Transactional
    public void generateArticleTag() {
        Tag sport = new Tag("Sport");
        Tag fashion = new Tag("Fashion");
        Tag technology = new Tag("Technology");

        Article at1 = new Article("Ronaldo sang Việt nam thi đấu");
        Article at2 = new Article("Pep Guardiola học lập trình Java");

        at1.addTag(sport);
        at2.addTag(sport);
        at2.addTag(technology);

        em.persist(sport);
        em.persist(fashion);
        em.persist(technology);

        em.persist(at1);
        em.persist(at2);
    }
```

Sau khi thực thi ta thu được Log query như sau

```sql
insert into tag (name, id) values (?, ?)
insert into tag (name, id) values (?, ?)
insert into tag (name, id) values (?, ?)

insert into article (title, id) values (?, ?)
insert into article (title, id) values (?, ?)

insert into article_tag (article_id, tag_id) values (?, ?)
insert into article_tag (article_id, tag_id) values (?, ?)
insert into article_tag (article_id, tag_id) values (?, ?)
```

Cách làm này code sẽ ngắn gọn khi thao tác thêm mới, xoá. Đối với thao tác xoá sử dụng Set thay cho List sẽ giảm thiểu được số lượng câu sql sinh ra , tuy nhiên nhược điểm là cần dùng Set thay vì List.

**Cách 2: Tạo ra thêm entity trung gian**

Ta có 3 entity  có quan hệ với nhau như sau

**Student Entity**

```java
@Entity(name = "student")
public class Student {
   // ...
  @OneToMany(mappedBy = "student")
  private List<StudentSubject> studentSubjects = new ArrayList<>();
}
```

**Subject Entity**

```java
@Entity(name = "subject")
public class Subject {
  //...
  @OneToMany(mappedBy = "subject")
  private List<StudentSubject> studentSubjects = new ArrayList<>();

}
```

**StudentSubject Entity**

```java
@Entity(name = "student_subject")
public class StudentSubject {
  //...
  @ManyToOne
  @JoinColumn(name = "student_id")
  private Student student;

  @ManyToOne
  @JoinColumn(name = "subject_id")
  private Subject subject;

  private float score; //extra column
}
```

Với cách trên ta sinh ra thêm StudentSubject entity, entity thứ ba này sẽ có quan hệ ManyToOne với hai entity còn lại, ngoài ra trong entity này còn bổ sung thêm cột score.
Khác với cách đầu tiên, bảng mới được sinh ra có khóa chính chỉ chứa một thuộc tính, khóa ngoại của bảng này là khóa chính của hai bảng còn lại.
Ta thực thi ứng dụng như sau:

```java
@Transactional
  public void generateStudentSubject() {
    Student tom = new Student("Tom");
    Student bob = new Student("Bob");
    Student jane = new Student("Jane");

    Subject math = new Subject("Math");
    Subject music = new Subject("Music");
    Subject computer = new Subject("Computer");

    StudentSubject tomMath = new StudentSubject(tom, math, 10f);
    StudentSubject tomMusic = new StudentSubject(tom, music, 9f);

    StudentSubject janeMath = new StudentSubject(jane, math, 9f);

    em.persist(tom);
    em.persist(bob);
    em.persist(jane);

    em.persist(math);
    em.persist(music);
    em.persist(computer);

    em.persist(tomMath);
    em.persist(tomMusic);
    em.persist(janeMath);

    em.flush();
  }
```

# Nhận xét :

Với cách làm này ta sẽ phải viết nhiều code hơn vì cần phải thao tác thêm/sửa/xóa với entity thứ 3 sinh ra.
Tuy nhiên cách này có thể dùng List thay cho Set mà vẫn đảm bảo số lượng câu sql sinh ra ít nhất có thể với điều kiện mapping theo kiểu bidirectional.
Sau khi thực thi ta thu được Log query như sau

```sql
insert into student (name, id) values (?, ?)
insert into student (name, id) values (?, ?)
insert into student (name, id) values (?, ?)

insert into subject (name, id) values (?, ?)
insert into subject (name, id) values (?, ?)
insert into subject (name, id) values (?, ?)

insert into student_subject (score, student_id, subject_id, id) values (?, ?, ?, ?)
insert into student_subject (score, student_id, subject_id, id) values (?, ?, ?, ?)
insert into student_subject (score, student_id, subject_id, id) values (?, ?, ?, ?)
```

# Kết
