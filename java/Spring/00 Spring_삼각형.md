## 1.스프링삼각형
| 요소  | 풀네임                          | 핵심 개념                            | 역할/효과             |
| --- | ---------------------------- | -------------------------------- | ----------------- |
| IoC | Inversion of Control         | 객체 생성 및 의존성 제어를 컨테이너가 담당 (DI 기반) | 코드 결합도 감소, 테스트 용이 |
| AOP | Aspect-Oriented Programming  | 핵심 로직과 횡단 관심사(트랜잭션, 로깅 등) 분리     | 중복 제거, 유지보수성 향상   |
| PSA | Portable Service Abstraction | 다양한 기술(JDBC, JPA, JMS 등) 추상화     |                   |
 
### 1. IOC
#### 1. IOC
 - "제어의 역전"이라는 뜻으로, 객체의 생성과 의존성 관리에 대한 제어권이 개발자가 아닌 프레임워크(컨테이너)로 넘어가는 개념
 - 의존성 주입(DI, Dependency Injection): IoC를 구현하는 대표적인 방식. 생성자 주입, 세터 주입, 필드 주입 등.
 - 느슨한 결합(Loose Coupling): 객체 간 의존성을 코드 레벨이 아닌 설정/컨테이너에 위임 → 재사용성과 확장성 증가.
 - 객체 생명주기 관리: 생성, 초기화, 소멸까지 컨테이너가 관리. 

#### 2. 패턴 변화
  ```
   // 기존 패턴
   public class OrderService {
     private OrderRepository orderRepository = new OrderRepository(); // 직접 new
   }
  ```
   - 개발자가 직접 객체를 생성 (new)
   - 필요한 의존 객체도 직접 주입
  ```
   @Component
   public class OrderService {
      private final OrderRepository orderRepository;

      @Autowired
      public OrderService(OrderRepository orderRepository) { // 컨테이너가 주입
          this.orderRepository = orderRepository;
      }
  }
  ```
   - 스프링 컨테이너가 객체(Bean)를 생성
   - 컨테이너가 의존성을 찾아 자동으로 주입
   - 개발자는 컨테이너에서 객체를 꺼내 쓰기만 함
   
#### 2. IOC 관련 용어
| 용어                         | 정의                                              | IoC와의 관계                                          |
| -------------------------- | ----------------------------------------------- | ------------------------------------------------- |
| IoC (Inversion of Control) | 객체 생성과 제어의 권한을 프레임워크/컨테이너에 위임하는 개념              | 스프링의 근본 설계 원칙                                     |
| DI (Dependency Injection)  | 객체가 의존하는 다른 객체를 외부에서 주입받는 방식                    | IoC를 구현하는 대표적인 메커니즘                               |
| DL (Dependency Lookup)     | 컨테이너에서 필요한 객체를 직접 조회해서 가져오는 방식                  | IoC의 다른 구현 방법 (예: `ApplicationContext.getBean()`) |
| Bean                       | 스프링 IoC 컨테이너가 관리하는 객체                           | IoC의 대상                                           |
| BeanFactory                | 가장 기본적인 IoC 컨테이너 인터페이스                          | Bean의 생성·관리 기능 담당                                 |
| ApplicationContext         | BeanFactory를 확장한 IoC 컨테이너                       | DI, AOP, 이벤트, 메시지 리소스 관리까지 포함                     |
| Container                  | BeanFactory나 ApplicationContext 같은 IoC 구현체      | IoC를 실질적으로 수행하는 주체                                |
| Configuration Metadata     | XML, Java Config, Annotation 등으로 작성된 Bean 정의 정보 | 컨테이너가 객체 생성 규칙을 알 수 있게 함                          |
| Lifecycle (Bean Lifecycle) | Bean의 생성 → 의존성 주입 → 초기화 → 사용 → 소멸 과정            | IoC 컨테이너가 책임지고 관리                                 |

#### 3. 주입 방법 예시
 - XML 파일 사용
    - XML
      ``` 
       <bean id="koreaTire" class="com.example.tire.KoreaTire"/>
       <bean id="americanTire" class="com.example.tire.AmericanTire"/>
      ```
    - JAVA
      ```
      public static void main(String[] args) {
        ApplicationContext context =
                new ClassPathXmlApplicationContext("applicationContext.xml");

        // XML에서 Tire Bean 가져오기
        Tire tire = context.getBean("koreaTire", Tire.class);

        // Car 객체는 일반적으로 생성
        Car car = new Car();

        // 소스 코드에서 직접 주입
        car.setTire(tire);
      ```
   - XML에서 속성 주입
     - XML
     ```
      <!-- Tire 인터페이스 구현체 Bean 등록 -->
      <bean id="koreaTire" class="com.example.tire.KoreaTire"/>
      <bean id="americanTire" class="com.example.tire.AmericanTire"/>

      <!-- Car 객체에 Tire 의존성 주입 (koreaTire 주입 예시) -->
      <bean id="car" class="com.example.car.Car">
          <property name="tire" ref="koreaTire"/>
      </bean>
     ```
 - @Autowried
    - XML에서는 빈만 생성
    - JAVA
      ``` 
      // 자동 주입
      @Autowired
      private Tire tire;

      public void printTireBrand() {
        System.out.println("장착된 타이어: " + tire.getBrand());
      }
      ```
 
 - @Resource
   - XML에서는 빈만생성
   - JAVA
     ```
     @Resource(name="koreaTire")  // Bean 이름으로 매칭
     private Tire tire;

     public void printTireBrand() {
       System.out.println("장착된 타이어: " + tire.getBrand());
     }
     ```
#### 4. note
 - 개발에서의 변화
   - 기존 방식에서는 객체가 의존 대상을 직접 생성해야 하므로, 구체적인 클래스에 대해 정확히 알고 있어야함.
   - 하지만 의존성 주입(DI)을 적용하면 객체는 구체 클래스가 아니라 인터페이스만 의존하게 되고, 실제 구현체는 외부에서 주입되므로 인터페이스 규격만 맞으면 정상적으로 동작
 - bean을 만드는 작업과 주입하는 과정을 분리하고, 상황에 따라서 유동적으로 빈을 주입해서 사용가능함.
 - Resource와 Autowired
   - 스프링 초기
     - 의존성 주입(DI)을 지원했지만, XML 기반이 대부분
     - 자바 코드에서 자동 주입을 지원하는 애노테이션은 없음
     - 개발자는 <property> 등으로 의존성을 수동 연결
   - @Resource 등장
     - 스프링 2.x 이후부터 지원
     - Java EE 표준(JSR-250) 기반
     - 자동 주입 가능, 기본적으로 이름(byName) 기준
     - 이 시점부터 XML Bean 등록만 해두고, 클래스 필드에 @Resource 붙이면 컨테이너가 알아서 주입
   - @Autowired 등장
     - 스프링 전용 어노테이션, 타입(byType) 기준
     - 이름 지정 필요 없이 타입만으로 주입 가능
     - 선택적 의존성(required=false) 가능

### 2. AOP
#### 1. AOP
 - 핵심 로직과 공통 관심사(Cross-Cutting Concern)를 분리하는 프로그래밍 기법
   - 핵심 로직(Core Business Logic): 실제 애플리케이션이 수행하는 기능
   - 횡단 관심사(Cross-Cutting Concern): 로깅, 트랜잭션, 보안, 성능 측정 등 여러 곳에서 반복되는 코드
#### 2. AOP 관련용어
| 용어         | 정의                                                |
| ---------- | ------------------------------------------------- |
| Aspect     | 횡단 관심사를 모아놓은 모듈 (예: 로깅, 트랜잭션)                     |
| Join Point | Aspect가 적용될 수 있는 지점 (메서드 실행, 예외 발생 등)             |
| Advice     | Join Point에 실제로 적용되는 코드 (Before, After, Around 등) |
| Pointcut   | Advice가 적용될 Join Point를 선택하는 조건                   |
| Weaving    | Aspect를 실제 코드에 적용하는 과정 (컴파일, 클래스 로딩, 런타임 시 가능)    |

#### 3. 사용방법
```
@Aspect // 등록
@Component // bean
public class LoggingAspect {
    @Before("execution(* com.example.service.OrderService.placeOrder(..))") // 클래스 작업전에
    public void logBefore() { // 요거를 호출
        System.out.println("주문 처리 전 로깅...");
    }
}

```

### 3. PSA
#### 1. PSA
 - **스프링이 제공하는 기술 추상화 계층**을 의미 / 개발자가 생성한건 해당없음.
 - 스프링이 다양한 기술(JDBC, JMS, JPA, 트랜잭션 등)에 일관된 API를 제공하여,
 - 개발자가 특정 기술에 종속되지 않고 코드를 작성할 수 있도록 해주는 개념

#### 2. 주요 용어
| 특징      | 설명                       |
| ------- | ------------------------ |
| 추상화     | 기술 구현체를 몰라도 공통 API 사용 가능 |
| 일관성     | 여러 기술을 동일한 방식으로 다룸       |
| 이식성     | 기술 교체 시 코드 수정 최소화        |
| 유지보수 용이 | 공통 API 기반으로 관리           |

#### 3. PSA 범위
 - PSA 범위
   - JDBC, JPA, JMS, 트랜잭션, 메시지, 스케줄링 등 스프링이 추상화한 공통 API
   - 예: JdbcTemplate, PlatformTransactionManager, JmsTemplate, JpaTransactionManager 등
 - 포함되지 않는 것
   - Service, Controller, Utility Bean 등 비즈니스 로직 또는 애플리케이션 단위 객체
   - PSA는 “비즈니스 로직이 아니라, 기술 추상화”에 초점
 - PSA의 역할
   - 기술 구현체에 독립적인 API 제공
   - 개발자가 핵심 로직을 작성할 때 구체 기술(JDBC, JPA, JMS 등) 변경에 무관하게 코드를 유지할 수 있도록 지원