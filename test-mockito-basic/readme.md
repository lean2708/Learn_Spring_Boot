# Mockito

Hầu hết các class mà chúng ta sử dụng đều có dependency, và đôi khi, các method ủy thác một số công việc cho các method khác trong các class khác. Các class này là sự phụ thuộc của chúng ta.
Khi viết unit test cho các method như vậy, nếu chúng ta chỉ sử dụng JUnit, unit test của chúng ta cũng sẽ phụ thuộc vào các method đó. Chúng ta muốn các unit test của mình được độc lập với tất cả các phụ thuộc khác.

Ví dụ chúng ta muốn test method createCoinDetail trong class CoinDetailService và trong method createCoinDetail này, method lưu trữ của class coinDetailDAO được gọi ra. Chúng ta không muốn gọi thực hiện thực sự của method lưu trữ coinDetailDAO.addCoinDetail()( vì một vài lý do như sau :

Chúng ta chỉ muốn kiểm tra logic bên trong addCoinDetail() của service trong sự cô lập, ko có dependency.
Chúng ta có thể chưa implement nó.
Chúng ta không muốn việc test method addCoinDetail() của service không thành công nếu có một lỗi trong phương thức addCoinDetail() trong CoinDetailDao.
Vì vậy, chúng ta nên giả lập hành vi của các dependency. **Mockito** là những gì tôi sử dụng cho việc này, và trong bài này, chúng ta sẽ thấy làm thế nào để sử dụng hiệu quả Mockito để mock những dependency.

**Mokito là gì?**

Mockito là một framework mock mà thị hiếu thực sự tốt. Nó cho phép bạn viết các test đẹp với API sạch sẽ và đơn giản. Mockito không làm cho bạn khó hiểu hay nao núng bởi vì các test là rất dễ đọc và nó cung cấp các lỗi xác minh rõ ràng và dễ hiểu.

**Inject mock với Mockito**

Quay lại ví dụ trên, làm thế nào để chúng ta mô phỏng sự phụ thuộc bằng cách sử dụng Mockito? Vâng, chúng ta có thể inject một mock đến các class đang được test thay vì implement thật trong khi chúng ta đang chạy test của mình!

Hãy xem xét một ví dụ về một class đang được test có depend vào CoinDetailDAO:

```java
@Component
@Transactional
public class CoinDetailServiceImpl implements CoinDetailService {

    @Autowired
    private CoinDetailDAO coinDetailDAO;

    @Override
    public CoinDetail addCoinDetail(CoinDetail coinDetail) {
        return coinDetailDAO.addCoinDetail(coinDetail);
    }

    @Override
    public void createCoinDetail(CoinDetail coinDetail) {
        coinDetail.setCreatedDate(new Date());
        coinDetailDAO.createCoinDetail(coinDetail);
    }

    @Override
    public CoinDetail updateCoinDetail(CoinDetail coinDetail) {
        return coinDetailDAO.updateCoinDetail(coinDetail);
    }
}
```

Sau đây là test mocks dependency bằng cách sử dụng Mockito:

```java
@RunWith(MockitoJUnitRunner.class)
public class CoinDetailServiceTest {

    @InjectMocks
    private CoinDetailServiceImpl coinDetailService;

    @Mock
    private CoinDetailDAO coinDetailDAO;

    @Before
    public void setUp() throws Exception {
         MockitoAnnotations.initMocks(this);
    }
    @Test
    public void test() {
         //assertion here
    }
}
```

Chúng ta hãy nhìn vào vai trò của các annotation trong ví dụ trên.

**@Mock** sẽ tạo ra mock implementation cho dependency CoinAccountDAO
**@InjectMocks** là dành đối tượng đc test, ở đây chính là CoinAccountService
**Đừng bỏ qua MockitoAnnotations.initMocks(this) ở setUp()** nhé 😃

**Mocking Methods với Mockito**
Sau khi chúng ta đã thành công trong việc tạo ra và inject mock, thì bây giờ chúng ta xem thử chúng mock hoạt động như thế nào trong method. Dưới đây là ví dụ sử dụng Mockito.when cho method cần mock

```java
@Test
    public void testAddCoinDetail_returnsNewCoinDetail() {

        when(coinDetailDAO.create(any(CoinDetail.class))).thenReturn(new CoinDetail());

        CoinDetail customer = new CoinDetail();

        assertThat(coinDetailService.create(customer), is(notNullValue()));

    }

    @Test
    public void testAddCoinDetail_returnsNewCoinDetailWithId() {

        when(coinDetailDAO.create(any(CoinDetail.class))).thenAnswer(new Answer<CoinDetail>() {

            @Override
            public CoinDetail answer(InvocationOnMock invocationOnMock) throws Throwable {

                Object[] arguments = invocationOnMock.getArguments();
                if (arguments != null && arguments.length > 0 && arguments[0] != null){
                    CoinDetail coinDetail = (CoinDetail) arguments[0];
                    coinDetail.setCoinDetailId(1);
                    return coinDetail;
                }

                return null;
            }
        });

        CoinDetail coinDetail = new CoinDetail();

        coinDetailService.create(coinDetail);

        Assert.assertEquals(coinDetail.getCoinDetailId(), 1);
    }

    //Throwing an exception from the mocked method
    @Test(expected = StaleObjectStateException.class)
    public void testAddCoinDetail_throwsException() {
        when(coinDetailDAO.create(any(CoinDetail.class))).thenThrow(StaleObjectStateException.class);
        CoinDetail coinDetail = new CoinDetail();
        coinDetailService.create(coinDetail);
    }
```

**Mocking Void Methods với Mockito**

Dùng doAnswer nếu chúng ta muốn mock method của dependency Ví dụ , nếu chúng ta muốn test method updateCoinDetail ở tầng service nhưng ở tầng DAO method updateCoinDetail lại là void method

```java
@Test
    public void testUpdate() {

        doAnswer(new Answer<Void>() {
            public Void answer(InvocationOnMock invocation) {
                Object[] arguments = invocation.getArguments();
                if (arguments != null && arguments.length > 0 && arguments[0] != null){
                    CoinDetail coinDetail = (CoinDetail) arguments[0];
                    coinDetail.setCoinBalance(BigDecimal.TEN);
                }

                return null;
            }
        }).when(coinDetailDAO).updateCoinDetail(any(CoinDetail.class));

        // calling the method under test
        CoinDetail coinDetail = coinDetailService.updateCoinDetail(new CoinDetail(), BigDecimal.TEN);

        assertThat(coinDetail, is(notNullValue()));
        assertEquals(coinDetail.getCoinBalance(), BigDecimal.TEN);

    }
```

Nếu muốn mock method của tầng DAO throw Exception

```java
@Test(expected = StaleObjectStateException.class)
    public void testUpdate_throwsException() {

        doThrow(StaleObjectStateException.class).when(coinDetailDAO).updateCoinDetail(any(CoinDetail.class));

        // calling the method under test
        CoinDetail coinDetail = coinDetailService.updateCoinDetail(new CoinDetail(), BigDecimal.ONE);

    }
```

**Testing Void Methods với Mockito**

Có 2 cách để test void method

**Cách 1:: Sử dụng verify**

```java
@Test
    public void testCreateCoinAccountByVerify() {

        CoinAccount coinAccount = new CoinAccount();
        coinAccountService.createCoinAccount(coinAccount);

        //verify that the createCoinAccount method has been invoked
        verify(coinAccountDAO).createCoinAccount(any(CoinAccount.class));
        //the above is similar to : verify(coinAccountDAO, times(1)).createCoinAccount(any(CoinAccount.class));
        verify(coinAccountDAO, times(1)).createCoinAccount(any(CoinAccount.class));

        //verify that the updateCoinAccount method has never been  invoked
        verify(coinAccountDAO, never()).updateCoinAccount(any(CoinAccount.class));
    }
```

**Giải thích:**
veify của mockito là để kiểm tra xem method của dependency có được gọi hay ko Cụ thể hay đây khi test createCoinAccount của service thì mình cần verify xem có đúng là khi gọi method này của service thì createCoinAccount của DAO được gọi 1 lần hay ko? và tất nhiên là method updateCoinAccount của DAO không bao giờ được gọi rồi 😃

**Cách 2: Ngoài ra ta có thể dùng ArgumentCaptor để test void method**

```java
@Captor
    private ArgumentCaptor<CoinAccount> coinAccountArgumentCaptor;

@Test
    public void testCreateCoinAccountByArgumentCaptor() {

        // Requirement: we want to register a new CoinAccount. Every new CoinAccount
        // should be set createdDate before saving in the database.
        coinAccountService.createCoinAccount(new CoinAccount());

        // captures the argument which was passed in to save method.
        verify(coinAccountDAO).createCoinAccount(coinAccountArgumentCaptor.capture());

        // make sure createdDate is assigned by the createCoinAccount method before saving.
        assertThat(coinAccountArgumentCaptor.getValue().getCreatedDate(), is(notNullValue()));

    }
```

Như ví dụ trên thì chúng ta captor coinAccount để xem sau khi gọi createCoinAccount của service thì object này thay đổi như thế nào. Cụ thể là ở đây khi ta ta gọi **createCoinAccount** ở service thì createdDate được set value:

```java
@Override
    public void createCoinAccount(CoinAccount coinAccount) {
        coinAccount.setCreatedDate(new Date());
        coinAccountDAO.createCoinAccount(coinAccount);
    }
```

Vậy khi captor đc object sau khi gọi service ta chỉ cần verify xem các thuộc tính của object này có thay đổi đúng như ta mong muốn không hay thôi.

```java
        assertThat(coinAccountArgumentCaptor.getValue().getCreatedDate(), is(notNullValue()));
```

**Spying With Mockito**

Đôi khi chúng ta cần phải gọi method thực sự của dependency nhưng vẫn muốn xác minh hoặc theo dõi các tương tác với dependency đó. Đây là lý do chúng ta sẽ cần đến @Spy. Spy nó là trường hợp đặc biệt của mock, nó gọi thực sự method của dependency. Một số behavior của Spy có thể được mock nếu cần, vì cuối cùng Spy vẫn là mock mà. Ví dụ:

```java
@Spy
    private CoinAccountDAOImpl coinAccountDAOSpy;

    @Before
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testAddCoinAccountBySpy() {

        coinAccountService.addCoinAccount(new CoinAccount());

        //verify that the createCoinAccount method has been invoked
        verify(coinAccountDAOSpy).addCoinAccount(any(CoinAccount.class));
        //the above is similar to : verify(coinAccountDAOSpy, times(1)).addCoinAccount(any(CoinAccount.class));
        verify(coinAccountDAOSpy, times(1)).addCoinAccount(any(CoinAccount.class));

        //verify that the updateCoinAccount method has never been  invoked
        verify(coinAccountDAOSpy, never()).updateCoinAccount(any(CoinAccount.class));
    }
```
