# OneToMany với JPA và Hibernate

**1. Giới thiệu**

Trong khi việc thêm một quan hệ **@OneToMany** là rất dễ dàng với JPA và Hibernate , nhưng việc biết đúng cách để ánh ạ một sự liên kế để nó sinh ra các câu leechj SQL hiệu quả chắc chắn không phải là một điều tầm thường để làm.
Trong một hệ thống cơ sở dữ liệu quan hệ, một liên kết one-to-many liên kết hai bảng dựa trên một cột khóa ngoại để bản ghi bảng con tham chiếu đên skhóa chính của hàng bảng cha.

![om1](https://raw.githubusercontent.com/lean2708/Learn_Spring_Boot/master/docs/image2/om1.webp)

Đây là một mối quan hệ đơn gian trong một cơ sở dữ liệu quan hệ , khi nói trong JPA, liên kết dữ liệu một nhiều có thể được trình bày thông qua chú thích quan hệ **@ManyToOne** hoặc **@OneToMany** vì quan hệ OOP có thể là một chiều hoặc hai chiều.

Chú thích liên kết @ManyToOne cho phép bạn để ánh xạ cột khóa ngoại(foreign key) trong thực thể con kết nối để đối tượng con có thể có một đối tượng thực thể thamg chiếu đến thực thể cha của nó. Đây là một cách tự nhiên nhất để ánh xạ cơ sở dữ liệu liên kêt một nhiều, và thông thường , cũng là cách thay thế hiệu quá nhất.
Cho thuận tiện, để tận dụng các chuyển đổi trạng thái thực thể và cơ chế kiểm tra bẩn , nhiều lập trình viên chon ánh xạ thực thể con như một tập hợp (collection) trong đối tượng cha, vì mục đích nay JPA cung cấp chú thích @OneToMany.

Như giải thích ở trên, nhiều lần, bạn nên thay thế các tập hợp(collections) bằng một câu truy vấn, linh hoạt hơn về hiệu suất tìm nạp. Tuy nhiên , có nhiều lân khi ánh xạ một tập hợp là một điều đúng dắn để thực hiện, khi đó bạn có hai lựa chọn:

**một liên kết một chiều @OneToMany**

**một liên kết hai chiều @OneToMany**

Liên kết hai chiều yên cầu thực thể con ánh xạ cung cấp chú thích @ManyToOne, cái mà chịu trách nhiệu điều khiển liên kết.

Một mặt khác, liên kết một chiều @OneToMany sẽ đơn giản chỉ cần phía bên thực thể cha định nghĩa quan hệ. Trong bài viến này, mình sẽ giải thích những điều cần lưu ý của các liên kết @OneToMany và cách bạn có thể vượt qua chúng.

Có nhiều cách để ánh xạ liên kêt @OneToMany . Chúng ta có thể sử dụng List hoặc Set. Chúng ta cũng có thể xác định một chú thích @JoinColumn. Vì vậy, hãy xem tất cả những điều này hoạt động như thế nào.

**2. @OneToMany một chiều(Unidirectional)**

Hãy xem xét chúng ta có ánh xạ sau:

 ```java
@Entity(name = "PostComment")
@Table(name = "post_comment")
public class PostComment {
 
    @Id
    @GeneratedValue
    private Long id;
 
    private String review;
 
    //Constructors, getters and setters removed for brevity
}
```

Bây giờ, nếu chúng ta gắn một bài đăng (Post) và ba bình luận (PostComment):

```java
Post post = new Post("First post");
 
post.getComments().add(
    new PostComment("My first review")
);
post.getComments().add(
    new PostComment("My second review")
);
post.getComments().add(
    new PostComment("My third review")
);
 
entityManager.persist(post);
```

Hibernate sẽ thực hiện các câu lệch SQL dưới đây :

```sql
insert into post (title, id)
values ('First post', 1)
 
insert into post_comment (review, id)
values ('My first review', 2)
 
insert into post_comment (review, id)
values ('My second review', 3)
 
insert into post_comment (review, id)
values ('My third review', 4)
 
insert into post_post_comment (Post_id, comments_id)
values (1, 2)
 
insert into post_post_comment (Post_id, comments_id)
values (1, 3)
 
insert into post_post_comment (Post_id, comments_id)
values (1, 4)
```

Điều gì vậy ! Tại sao lại có quá nhiều câu truy vấn được thực hiện ? Và việc đưa ra bảng post_post_comment đó là gì ?

Ồ, bởi mặc định, đó là cách liện kết một chiều @OneToMany hoạt động, và điều này là cách nó trông từ góc đọ cơ sở dữ liệu :

![om2](https://raw.githubusercontent.com/lean2708/Learn_Spring_Boot/master/docs/image2/om2.webp)

Với một DBA, điều này trông giống như một liên kết cơ sở dữ liệu nhiều-nhiều hơn là một quan hệ một-nhiều, và nó không hiệu quả lắm. Thay vì hai bảng, giờ chúng ta có ba bảng, vì vậy chúng ta đang sử dụng nhiều bộ nhớ hơn cần thiết. Thay vì chỉ cần một khóa ngoại(foreign key), giờ đây chúng ta có hai trong số đó. Tuy nhiên, vì chúng ta có nhiều khả năng sẽ lập chỉ mục các khóa ngoại này, nên chúng ta sẽ yêu cầu bộ nhớ gấp đôi để lưu vào bộ nhớ đệm chỉ mục cho liên kết này. Không tốt lắm!

**3. Liên kết một chiều @OneToMany với @JoinColumn**

Để khắc phục bảng bổ sung post_post_comment nói trên, chúng ta chỉ cần thêm chú thích @JoinColumn trong đối tượng Post:

```java
@OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
@JoinColumn(name = "post_id")
private List<PostComment> comments = new ArrayList<>();
```

Chú thích @JoinColumn giúp Hibernate tìm ra rằng có một khóa ngoại post_id trong bảng post_comment xác định liên kết.

Với chú thích này đặt ở đây. Khi việc lưu 3 thực thể PostComment, chúng ta sẽ được câu lệch SQL như sau:

```sql
insert into post (title, id)
values ('First post', 1)
 
insert into post_comment (review, id)
values ('My first review', 2)
 
insert into post_comment (review, id)
values ('My second review', 3)
 
insert into post_comment (review, id)
values ('My third review', 4)
 
update post_comment set post_id = 1 where id = 2
 
update post_comment set post_id = 1 where id =  3
 
update post_comment set post_id = 1 where id =  4
```

Đã tốt hơn một chút, nhưng lại có thêm ba câu lệch update ở đây, mục đích có chúng để làm gì ?

Nếu bạn xem thứ tự xử lý của Hibernate, bạn sẽ thấy hành động lưu sẽ được thực hiện trước khi các thành phần của tập hợp (PostComment element) được xử lý. Ở cách này, Hibernate đã chèn các bản ghi của đối tượng con trơpcs mà không có khóa ngoại (Foreign Key) vì thực thể con không lưu trữ thông tin này. Trong giai đoạn xử lý thu thập, cộ khóa ngoại được cập nhật tương ứng.

Logic tương tự cũng được áp dụng cho các sửa đổi trạng thái tập hợp, vì vậy khi xóa mục nhập đầu tiên khỏi tập hợp con :

```java
post.getComments().remove(0);
```

Hibernate thực thi hai câu lệnh thay vì một câu :

```sql
update post_comment set post_id = null where post_id = 1 and id = 2
 
delete from post_comment where id=2
```

Một lần nữa, thay đổi trạng thái thực thể cha thi đầu tiên, điều này sẽ kích hoạt cập nhật thực thể con. Sau đó, khi tập hợp được xử lý, hành động loại bỏ sẽ thực hiện câu lệch xóa hàng con.

**4. Liên kết hai chiều @OneToMany**

Cách tốt nhất để ánh xạ liên kết @OneToMany là dựa vào phía @ManyToOne để tuyên truyền tất cả các thay đổi trạng thái thực thể:

```java
@Entity(name = "Post")
@Table(name = "post")
public class Post {
 
    @Id
    @GeneratedValue
    private Long id;
 
    private String title;
 
    @OneToMany(
        mappedBy = "post",
        cascade = CascadeType.ALL,
        orphanRemoval = true
    )
    private List<PostComment> comments = new ArrayList<>();
 
    //Constructors, getters and setters removed for brevity
 
    public void addComment(PostComment comment) {
        comments.add(comment);
        comment.setPost(this);
    }
 
    public void removeComment(PostComment comment) {
        comments.remove(comment);
        comment.setPost(null);
    }
}
 
@Entity(name = "PostComment")
@Table(name = "post_comment")
public class PostComment {
 
    @Id
    @GeneratedValue
    private Long id;
 
    private String review;
 
    @ManyToOne(fetch = FetchType.LAZY)
    private Post post;
 
    //Constructors, getters and setters removed for brevity
 
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof PostComment )) return false;
        return id != null && id.equals(((PostComment) o).getId());
    }
 
    @Override
    public int hashCode() {
        return getClass().hashCode();
    }
}
```

Có một vài điều cần lưu ý về việc ánh xạ nói trên:
Liên kết @ManyToOne sử dụng FetchType.Lazy, bởi vì nếu không chúng ta sẽ quay lại việc tìm nạp EAGERm điều này không tốt cho hiệu suất.

Thực thể cha Post, có hai phương thức (method) tiện ichs ( ví dụ aaddComment và removeComment) được sử dụng để đồng bộ hóa cả hai phía của kết hợp hai chiều . Bạn phỉa luôn cung cấp các phương pháp này bất cứ khi nào bạn đang làm việc với liên kết hai chiều, nếu không bạn có nguy cơ gặp phải các vấn đề về lan truyền trạng thái rất tinh vi.

Thực thể con PostComment , triển khai các phương pháp equals và hashCode. Vì chúng ta không thể dựa vào mã định danh tự nhiên để kiểm tra tính bình đẳng , nên chúng ta cần sử dụng mã định danh thực thể thay thế cho phương pháp equals. Tuy nhiên, bạn cần phải làm điều đó đúng cách để sự bình đẳng nhất quán trên tất cả các chuyển đổi trạng thái thực thể, đó cũng là lý do tại sao hashCode gía trị phỉa là một giá chị không đổi. Bời vì chúng ta dựa trên sự bình đẳng cho removeComment, nên thực tiễn tốt là ghi đè equals và hashCode cho thực theer con trong một liên kết hai chiều :

Nếu chúng ta lưu lại Post:

```java
Post post = new Post("First post");
 
post.getComments().add(
    new PostComment("My first review")
);
post.getComments().add(
    new PostComment("My second review")
);
post.getComments().add(
    new PostComment("My third review")
);
 
entityManager.persist(post);
```

Hibernate sẽ chỉnh tạo ra một câu lệch cho mỗi PostComment:

```sql
insert into post (title, id)
values ('First post', 1)
 
insert into post_comment (post_id, review, id)
values (1, 'My first review', 2)
 
insert into post_comment (post_id, review, id)
values (1, 'My second review', 3)
 
insert into post_comment (post_id, review, id)
values (1, 'My third review', 4)

Nếu chúng ta xóa một PostComemnt:

Post post = entityManager.find( Post.class, 1L );
PostComment comment1 = post.getComments().get( 0 );
 
post.removeComment(comment1);
```

Sẽ chỉ có một câu lệch SQL delete được thực hiện:

```sql
delete from post_comment where id = 2
```

**5. @ManyToOne có thể là vừa đủ**
Chỉ vì bạn có tùy chọn sử dụng chú thích @OneToMany , điều đó không có nghĩa là đây phải là tùy chọn mặc định cho mọi mối quan hệ cơ sở dữ liệu một-nhiều . Vấn đề với tập hợp là chúng ta chỉ có thể sử dụng chúng khi số lượng bản ghi con là khá hạn chế.
Như được giải thích trong câu trả lời trên StackOverflow, bạn không thể giới bạn kích thước của một tập hợp @OneToMany như trương hợp bạn suwe dụng phân trang cấp truy vấn .

Do đó, hầu hết thời gian, chú thích @ManyToOne ở phía con là mọi thứ bạn cần. Nhưng sau đó, làm thế nào để bạn có được các thực thể con được liên kết với một Post thực thể ?

Tất cả những gì bạn cần chỉ là một truy vấn JPQL:

```java
List<PostComment> comments = entityManager.createQuery(
    "select pc " +
    "from PostComment pc " +
    "where pc.post.id = :postId", PostComment.class)
.setParameter( "postId", 1L )
.getResultList();
```

Điều này chuyển thành một truy vấn SQL đơn giản :

```sql
select pc.id AS id1_1_,
       pc.post_id AS post_id3_1_,
       pc.review AS review2_1_
from   post_comment 
where  pc.post_id = 1
```

Ngay cả khi bộ sưu tập không được quản lý nữa, việc chỉ thêm hoặc xóa các thực thể con bất cứ khi nào cần thiết là điều khá đơn giản. Đối với việc cập nhật các đối tượng con, cơ chế kiểm tra độ bẩn hoạt động tốt ngay cả khi bạn không sử dụng bộ sưu tập được quản lý. Điều thú vị khi sử dụng truy vấn là bạn có thể phân trang nó theo bất kỳ cách nào bạn thích để nếu số lượng thực thể con tăng lên theo thời gian, hiệu suất ứng dụng sẽ không bị ảnh hưởng.

**6. Kết luận**

Như bạn sẽ thấy trong bài viết, tập hợp hai chiều tốt hơn tập hợp một chiều vì chúng dựa vào liên kết @ManyToOne , luôn hiệu quả về mặt câu lệnh SQL được tạo .

Nhưng sau đó, ngay cả khi chúng rất tiện lợi, bạn không phải lúc nào cũng phải sử dụng các bộ sưu tâp(collections). Sự liên kết @ManyToOne là cách tự nhiên nhất và cũng hiệu quả nhất để ánh xạ mỗi quan hệ cơ sở dữ liệu một - nhiều.

Cảm ơn các bạn đã dành thời gian ra đọc bài viết và khi vọng nó sẽ đem lại nhiều điều hữu ích cho các bạn !
