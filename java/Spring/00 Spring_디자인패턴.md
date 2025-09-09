## 1. 전체 
### 1. 전체
| 패턴              | 개념 / 목적                              | 구조 특징                                       | 사용 예시 (Java)                          | 스프링 사용 사례                                                           |
| --------------- | ------------------------------------ | ------------------------------------------- | ------------------------------------- | ------------------------------------------------------------------- |
| 어댑터 (Adapter)   | **인터페이스 불일치**를 맞춰서 기존 코드와 새 코드를 연결   | Target 인터페이스 + Adaptee + Adapter            | MediaAdapter, InputStreamReader       | HandlerAdapter, RestTemplate Adapter, I/O 변환                        |
| 프록시 (Proxy)     | 실제 객체 호출 전후에 **부가 기능 수행**            | Subject 인터페이스 + RealSubject + Proxy         | ProxyImage, RealImage                 | Spring AOP, @Transactional, Hibernate Lazy Loading, Security Proxy  |
| 싱글톤 (Singleton) | 시스템 전체에서 **단 하나의 객체를 공유**함           | private 생성자 + static 인스턴스 + getInstance()   | Logger, Config 클래스                    | Bean 기본 스코프, 트랜잭션 매니저, 공통 유틸                                        |
| 템플릿 메서드         | **알고리즘 골격은 고정**하고, 일부 단계만 서브클래스에서 구현 | AbstractClass(템플릿 메서드) + ConcreteClass      | DataProcessor, CSV/JSON 처리            | JdbcTemplate, TransactionTemplate, AbstractController               |
| 팩토리 (Factory)   | **객체 생성 과정을 캡슐화**하여 클라이언트 코드 분리      | Product + ConcreteProduct + Factory         | ShapeFactory, Circle/Rectangle 객체 생성  | BeanFactory, ApplicationContext#getBean, DataSource                 |
| 전략 (Strategy)   | **동일한 인터페이스**로 다양한 알고리즘을 교체 가능       | Strategy 인터페이스 + ConcreteStrategy + Context | PaymentStrategy, CreditCard/Paypal 결제 | Payment 서비스, MessageSender, AuthenticationProvider                  |
| 템플릿 콜백          | 공통 로직은 템플릿에서 처리, 변하는 동작은 **콜백으로 전달** | Template + Callback 인터페이스                   | TaskTemplate + Task 콜백                | JdbcTemplate#query, TransactionTemplate#execute, ResponseBodyAdvice |

### 2. 어댑터/팩토리/전략
| 구분    | 어댑터 패턴                       | 팩토리 패턴             | 전략 패턴                       |
| ----- | ---------------------------- | ------------------ | --------------------------- |
| 객체 생성 | Adapter 내부에서 Adaptee 객체 호출   | Factory가 생성해서 반환   | Context가 이미 가지고 있는 객체 사용 가능 |
| 실행 책임 | Adapter가 Adaptee 호출 및 변환 처리  | 클라이언트가 직접 실행       | Context가 실행                 |
| 목적    | 인터페이스 불일치 해결, 기존 코드와 새 코드 연결 | 생성 로직 캡슐화          | 알고리즘/동작 교체 가능               |
| 사용 시점 | 기존 인터페이스와 새로운 구현체 연결 시       | 객체가 필요할 때마다 생성     | 동일 타입의 객체를 런타임에 바꿔서 사용      |


## 2. 어댑터 패턴(Adapter Pattern)
``서로 다른 인터페이스를 가진 클래스들을 함께 동작할 수 있도록 중간에 맞춰주는(변환해주는) 패턴``

### 1. 어댑터 패턴
 - 기존 로직을 수정하지 않고 새로운 기능이나 인터페이스를 연결할 수 있음
 - 기존 코드와 새로운 코드 간 결합도를 낮춤

### 2. 구조
 - Target (인터페이스): 클라이언트가 기대하는 인터페이스 정의
 - Adaptee (기존 클래스): 기존에 구현된 클래스
 - Adapter: Target 인터페이스를 구현하고, 내부에서 Adaptee를 호출

### 3. 코드 예시
    ```
    // Target: 클라이언트가 기대하는 인터페이스
    interface MediaPlayer {
       void play(String fileName);
    }
    
    // Adaptee: 기존에 있던 규격 (호환 안 됨)
    class AdvancedMediaPlayer {
       void playMp3(String fileName) {
       System.out.println("Playing mp3 file: " + fileName);
       }
    
       void playMp4(String fileName) {
            System.out.println("Playing mp4 file: " + fileName);
       }
    }
    
    // Adapter: Target을 구현하면서 내부적으로 Adaptee를 호출
    class MediaAdapter implements MediaPlayer {
        private AdvancedMediaPlayer advancedPlayer = new AdvancedMediaPlayer();
    
        @Override
        public void play(String fileName) {
            if (fileName.endsWith(".mp3")) {
                advancedPlayer.playMp3(fileName);
            } else if (fileName.endsWith(".mp4")) {
                advancedPlayer.playMp4(fileName);
            } else {
                System.out.println("Unsupported format: " + fileName);
            }
        }
    }
    
    // Client
    public class AdapterPatternDemo {
    public static void main(String[] args) {
            MediaPlayer player = new MediaAdapter();
            player.play("song.mp3");  // 내부적으로 playMp3 호출
            player.play("movie.mp4"); // 내부적으로 playMp4 호출
        }
    }
    ```
  - 어댑터 패턴은 기존에 사용하던 인터페이스(규격) 가 이미 클라이언트 코드에 깊게 연결되어 있는데,
  - 새로운 클래스나 라이브러리가 들어왔는데 그 인터페이스와 호환되지 않을 때,
  - 기존 로직은 그대로 두고, 어댑터 클래스를 만들어서 새 클래스를 기존 인터페이스 규격에 맞게 변환해주는 방식

### 4. Spring에서 대표적인 예시
| 사용 영역       | 역할/설명                                   | 예시                   |
| ----------- | --------------------------------------- | -------------------- |
| Spring MVC  | 다양한 컨트롤러 타입을 공통 방식으로 호출 가능하게 함          | `HandlerAdapter`     |
| I/O 변환      | byte → char, legacy API → modern API 호환 | `InputStreamReader`  |
| 외부 라이브러리 연동 | 기존 인터페이스와 다른 외부 API를 어댑터로 감싸서 사용        | RestTemplate Adapter |


## 3. 프록시 패턴(Proxy Pattern)
``프록시 패턴은 실제 객체에 접근할 때 제어/부가 기능을 수행하도록 중간 객체를 두는 패턴``

### 1. 프록시 패턴
 - 실제 객체 호출 전후에 로그, 캐싱, 권한 체크 등을 수행할 수 있음
 - 클라이언트는 프록시를 실제 객체처럼 사용함

### 2. 구조
 - Subject (인터페이스): 실제 객체와 프록시가 구현하는 공통 인터페이스
 - RealSubject: 실제 객체
 - Proxy: Subject를 구현하고, 내부에서 RealSubject를 호출하며 부가 기능 수행

### 3. 코드예시
    ```
    // Subject
    interface Database {
        void query(String sql);
    }
    
    // RealSubject
    class RealDatabase implements Database {
        public RealDatabase() {
            // 실제 연결은 비용이 큼
            System.out.println("Connecting to the real database...");
        }
    
        @Override
        public void query(String sql) {
            System.out.println("Executing query on real DB: " + sql);
        }
    }
    
    // Proxy
    class DatabaseProxy implements Database {
        private RealDatabase realDatabase;
    
        @Override
        public void query(String sql) {
            if (realDatabase == null) {
                // 실제 객체는 필요할 때 생성
                realDatabase = new RealDatabase();
            }
            System.out.println("Proxy: Pre-processing before query");
            realDatabase.query(sql);
            System.out.println("Proxy: Post-processing after query");
        }
    }
    
    // Client
    public class ProxyPatternDemo {
        public static void main(String[] args) {
            Database db = new DatabaseProxy();
    
            // 실제 DB 연결은 아직 안 됨
            System.out.println("Client created proxy.");
    
            // 여기서 처음 query 호출 시 실제 DB 연결 발생
            db.query("SELECT * FROM users");
            db.query("SELECT * FROM orders");
        }
    }
    ```
 - 프록시는 기존에 사용하던 객체가 있는데,
 - 그 객체를 직접 호출하지 않고 대신 호출해주는 대리 객체(프록시)를 만들어서,
 - 프록시 내부에서 실제 객체 호출 전후로 부가 기능(접근제어, 캐싱, 로깅, 트랜잭션 등)을 끼워 넣고,
 - 클라이언트는 실제 객체와 똑같이 사용하지만 실제 동작은 프록시가 대신 처리하는 방법 

### 4. Spring에서 대표적인 예시
| 사용 영역        | 역할/설명                  | 예시                                                  |
| ------------ | ---------------------- | --------------------------------------------------- |
| AOP          | 메서드 호출 전후 부가 기능 수행     | `@Transactional`, `@Cacheable`, Spring Proxy 기반 AOP |
| Lazy Loading | 실제 객체 생성 지연 및 필요 시 초기화 | Hibernate Lazy Loading Proxy                        |
| 보안/권한        | 접근 권한 체크 및 검증 수행       | Spring Security Method Security Proxy               |

## 4. 데코레이터 패턴(Decorator Pattern)
``기존 객체의 기능은 그대로 두고, 추가 기능을 덧붙이기 위해 객체를 감싸는(wrapper) 구조를 만드는 패턴``
### 1. 데코레이터 패턴
 - 원본 객체를 수정하지 않고 기능 확장 가능
 - 런타임에 동적으로 기능 조합 가능

### 2. 구조
 - Component (공통 인터페이스): 원본 객체와 데코레이터가 모두 구현하는 인터페이스
 - ConcreteComponent (원본 객체): 실제 기능을 수행하는 기본 객체
 - Decorator (추상 데코레이터): Component 인터페이스를 구현하고, 내부에 Component를 참조
 - ConcreteDecorator (구체 데코레이터): 추가 기능을 구현하고, 내부 Component 호출 전후에 부가기능 삽입

### 3. 코드예시
    ```
    // Component
    interface Coffee {
        String getDescription();
    }
    
    // ConcreteComponent
    class SimpleCoffee implements Coffee {
        @Override
        public String getDescription() {
            return "Plain Coffee";
        }
    }
    
    // Decorator
    abstract class CoffeeDecorator implements Coffee {
        protected Coffee coffee;
    
        public CoffeeDecorator(Coffee coffee) {
            this.coffee = coffee;
        }
    }
    
    // ConcreteDecorator
    class MilkDecorator extends CoffeeDecorator {
        public MilkDecorator(Coffee coffee) {
            super(coffee);
        }
    
        @Override
        public String getDescription() {
            return coffee.getDescription() + " + Milk";
        }
    }
    
    // Client
    public class DecoratorDemo {
        public static void main(String[] args) {
            Coffee coffee = new SimpleCoffee();
            System.out.println(coffee.getDescription()); // Plain Coffee
    
            Coffee milkCoffee = new MilkDecorator(coffee);
            System.out.println(milkCoffee.getDescription()); // Plain Coffee + Milk
        }
    }
    ```
  - 데코레이터는 기존에 사용하던 객체(구현체)가 있는데,
  - 그 객체를 직접 수정하지 않고, 기능을 확장하거나 조합해서 새로운 기능을 덧붙이고 싶을 때,
  - 원본 객체와 같은 인터페이스를 구현하는 데코레이터 객체를 만들어서,
  - 데코레이터 내부에서 원본 객체를 호출하면서 추가 동작(로그, 데이터 변환, 포맷팅 등)을 앞뒤로 넣고,
  - 클라이언트는 원본과 똑같이 사용하지만 결과적으로는 확장된 기능을 얻게 되는 방법

### 4. Spring에서 대표적인 예시
| 사용 영역                      | 역할/설명                                           | 예시 클래스/기능                                                 |
| -------------------------- | ----------------------------------------------- | --------------------------------------------------------- |
| Spring MVC                 | ResponseBody, View 등에 추가 기능(포맷팅, 변환, 필터링 등)을 적용 | `ResponseBodyAdvice`, `Filter`                            |
| Spring WebSocket / Servlet | 요청/응답 스트림 감싸기, 추가 기능 처리                         | `HttpServletResponseWrapper`, `ServletOutputStreamWrapper` |
| Spring Security            | 기존 인증/인가 객체를 감싸서 부가기능 추가                        | `AuthenticationDecorator` (권한 체크, 로깅 등)                   |
| Spring I/O 관련 기능           | Stream 객체 감싸서 부가기능 추가 (버퍼링, 암호화, 압축 등)          | `BufferedInputStream`                                     |


## 5. 싱글톤 패턴(Singleton Pattern)
``싱글톤 패턴은 시스템 전체에서 클래스의 객체를 단 하나만 만들어서 공유하고 싶을 때 사용하는 패턴``
### 1. 싱글톤 패턴
 - 어디서든 동일한 인스턴스를 참조할 수 있음
 - 객체 생성을 제한하여 전역 상태를 관리할 때 유용함.

### 2. 구조
 - 클래스 내부에 private 생성자를 두어 외부에서 객체 생성 불가함
 - 클래스 내부에 static 필드로 유일한 인스턴스를 저장함
 - public static getInstance() 메서드를 통해 외부에서 객체를 가져올 수 있음

### 3. 코드예시
    ```
    class Singleton {
        private static Singleton instance = new Singleton();
        private Singleton() {}

        public static Singleton getInstance() {
            return instance;
        }
        public void showMessage() {
            System.out.println("Hello Singleton");
        }
    }
    
    public class Main {
        public static void main(String[] args) {
            Singleton s1 = Singleton.getInstance();
            Singleton s2 = Singleton.getInstance();
            System.out.println(s1 == s2); // true
            s1.showMessage();
        }
    }
    ```
  - 빈생성하는 패턴, 생성할때 prviate static로 생성하면, 외부에서 생성불가함.

### 4. Spring에서 대표적인 예시
| 사용 영역    | 역할/설명                            | 예시                             |
| -------- | -------------------------------- | ------------------------------ |
| 스프링 Bean | 컨테이너에 등록된 Bean을 하나만 생성하고 공유함     | `@Service`, `@Repository` Bean |
| 트랜잭션 관리  | 트랜잭션 매니저 객체를 하나만 생성하여 전체에서 재사용함  | `PlatformTransactionManager`   |
| 공통 유틸 객체 | 설정값, 캐시, 공통 기능 객체를 하나만 생성하여 재사용함 | 환경설정, Logger, Cache 객체         |


## 6. 템플릿 메서드 패턴(Template Method Pattern)
``템플릿 메서드 패턴은 변하지 않는 공통 알고리즘의 흐름을 정의하고, 일부 단계만 서브클래스에서 구현하게 함``
### 1. 템플릿 패턴
 - 알고리즘의 구조는 그대로 두고, 세부 동작만 다양하게 확장 가능함
 - 코드 중복을 줄이고 유지보수를 용이하게 함

### 2. 구조
 - AbstractClass: 알고리즘의 골격(템플릿 메서드)을 정의하고, 일부 단계는 추상 메서드로 선언함
 - ConcreteClass: 추상 메서드를 구현하여 세부 동작을 정의함

### 3. 코드예시
    ```
    abstract class DataProcessor {
        // 알고리즘 골격
        public void process() {
            readData();
            processData();
            writeData();
        }
    
        abstract void readData();
        abstract void processData();
        abstract void writeData();
    }
    
    class CSVProcessor extends DataProcessor {
        //Ovveride하는 부분이 내부 구현 부분
        @Override
        void readData() {
            System.out.println("Read CSV Data");
        }
        @Override
        void processData() {
            System.out.println("Process CSV Data");
        }
        @Override
        void writeData() {
            System.out.println("Write CSV Data");
        }
    }
    
    public class TemplateDemo {
        public static void main(String[] args) {
            //abstarct 타입에, CSVProcessor타입으로 구현
            DataProcessor processor = new CSVProcessor();
            processor.process();
        }
    }
    ```
   - abstarct 클래스 활용,

### 4. Spring에서 대표적인 예시
| 사용 영역              | 역할/설명                                                      | 예시                              |
| ------------------ | ---------------------------------------------------------- | ------------------------------- |
| JDBC 템플릿           | 반복되는 JDBC 로직(커넥션 관리, 예외 처리 등)을 공통으로 처리하고, SQL만 구현하도록 함     | `JdbcTemplate#execute`, `query` |
| Spring MVC Handler | 요청 처리의 골격은 공통으로 정의하고, 구체적인 핸들러 구현체에서 실제 처리 구현함             | `AbstractController`            |
| 트랜잭션 처리            | 트랜잭션 시작/커밋/롤백 등 공통 흐름은 템플릿에서 처리하고, 실제 비즈니스 로직은 서브 클래스에서 실행 | `TransactionTemplate#execute`   |


## 7. 팩토리 패턴(Factory Pattern)
``팩토리 패턴은 객체 생성 과정을 별도의 Factory 클래스에 위임하여, 클라이언트 코드가 구체적인 생성 과정을 알 필요 없이 객체를 사용할 수 있도록 함``
### 1. 팩토리 패턴
 - 객체 생성 로직을 캡슐화함
 - 생성 방식이 바뀌더라도 클라이언트 코드를 수정하지 않아도 됨

### 2. 구조
 - Product (인터페이스/추상 클래스): 생성할 객체의 공통 타입 정의
 - ConcreteProduct (구체 클래스): 실제 생성될 객체 정의
 - Creator / Factory: 객체 생성 책임을 담당, Product 타입 반환

### 3. 코드예시
    ``` 
    // Product
       interface Shape {
       void draw();
       }
    
    // ConcreteProduct
    class Circle implements Shape {
        public void draw() { System.out.println("Draw Circle"); }
    }
    class Rectangle implements Shape {
        public void draw() { System.out.println("Draw Rectangle"); }
    }
    
    // Factory
    class ShapeFactory {
        // Factory에서 케이스에 따라서 원하는 개체를 리턴하게 만듬.
        public static Shape createShape(String type) {
            if ("circle".equalsIgnoreCase(type)) return new Circle();
            else if ("rectangle".equalsIgnoreCase(type)) return new Rectangle();
            return null;
        }
    }
    
    // Client
    public class FactoryDemo {
        public static void main(String[] args) {
            //원하는 createShape에 맞게 객체를 리턴해줌
            Shape shape1 = ShapeFactory.createShape("circle");
            shape1.draw(); // Draw Circle
            
            Shape shape2 = ShapeFactory.createShape("rectangle");
            shape2.draw(); // Draw Rectangle
        }
    }
    ```
### 4. Spring에서 대표적인 예시
| 사용 영역           | 역할/설명                                             | 예시                                                   |
| --------------- | ------------------------------------------------- | ---------------------------------------------------- |
| Bean 생성         | 다양한 Bean 타입을 생성할 때 Factory를 통해 캡슐화함               | `BeanFactory`, `ApplicationContext#getBean`          |
| JDBC Template   | Connection, Statement, ResultSet 등 객체 생성 과정을 캡슐화함 | `DataSource`, `JdbcTemplate`                         |
| Spring Security | 인증/인가 객체 생성 책임을 Factory에 위임                       | `AuthenticationManagerBuilder`, `UserDetailsService` |


## 8. 전략 패턴(Strategy Pattern)
``전략 패턴은 동일한 문제를 해결하는 다양한 알고리즘이나 동작을 캡슐화하여, 런타임에 동적으로 교체 가능하도록 함``
### 1. 전략 패턴
 - 클라이언트는 알고리즘의 구체적 구현을 알 필요 없음
 - 코드 중복을 줄이고 유연성을 높임

### 2. 구조
 - Strategy (인터페이스/추상 클래스): 수행할 알고리즘 정의
 - ConcreteStrategy (구체 클래스): 실제 알고리즘 구현
 - Context: Strategy 객체를 보유하고, 알고리즘 호출 시 위임

### 3. 코드예시
    ```
    // Strategy
    interface PaymentStrategy {
        void pay(int amount);
    }
    
    // ConcreteStrategy
    // 이거는 인터페이스에 따라서 다구현을 진행해야함.
    class CreditCardPayment implements PaymentStrategy {
        public void pay(int amount) { System.out.println("Pay " + amount + " with Credit Card"); }
    }

    class PaypalPayment implements PaymentStrategy {
        public void pay(int amount) { System.out.println("Pay " + amount + " with PayPal"); }
    }
    
    // Context
    
    class PaymentContext {
        private PaymentStrategy strategy;
        // 인터페이스로 구현한 클래스 중에서 어떤걸로 사용할지 설정함.
        public void setStrategy(PaymentStrategy strategy) { this.strategy = strategy; }
        public void pay(int amount) { strategy.pay(amount); }
    }
    
    // Client
    public class StrategyDemo {
        public static void main(String[] args) {
            PaymentContext context = new PaymentContext();
            //클래스 결정(new CreditCardPayment())후 메소드(pay(1000)) 호출
            context.setStrategy(new CreditCardPayment());
            context.pay(1000); // Pay 1000 with Credit Card
            
            //클래스 결정(new PaypalPayment())후 메소드(pay(500)) 호출
            context.setStrategy(new PaypalPayment());
            context.pay(500); // Pay 500 with PayPal
        }
    }
    ```
  - 전략 패턴은 인터페이스가 동일하지만, 구현체마다 내부 로직이 다르고,
  - 그래서 Context(전략을 쓰는 클래스) 안에서 Strategy 인터페이스 타입으로 구현체를 교체하며 사용 가능함
  - 클라이언트는 어떤 전략이 선택되었는지 몰라도 동일한 방식으로 호출함
    
### 4. Spring에서 대표적인 예시
| 사용 영역        | 역할/설명                                     | 예시                                |
| ------------ | ----------------------------------------- | --------------------------------- |
| 결제 처리        | 다양한 결제 방식(카드, 페이팔, 계좌이체 등)을 Strategy로 캡슐화 | 결제 서비스에서 PaymentStrategy 인터페이스 사용 |
| 메시지 발송       | 이메일, SMS, 푸시 등 발송 방식 교체 가능                | MessageSender 전략 인터페이스와 구현체       |
| 스프링 Security | 인증 방식(폼 로그인, OAuth, JWT 등)을 전략으로 처리       | AuthenticationProvider 구현체 여러 개   |

## 9. 템플릿 콜백 패턴(Template Callback Pattern)
``템플릿 콜백 패턴은 반복되는 공통 로직을 템플릿 메서드에서 처리하고, 변하는 부분만 콜백으로 전달받아 실행하도록 함``

### 1. 템플릿 콜백
 - 알고리즘의 골격은 템플릿에서 처리하고, 세부 동작은 런타임에 전달된 콜백으로 처리함
 - 코드 중복을 줄이고, 유연하게 동작을 바꿀 수 있음

### 2. 구조
 - Template: 공통 로직(템플릿 메서드)을 구현
 - Callback (인터페이스/람다): 변하는 로직을 실행할 메서드 정의
 - Client: Callback 구현체를 생성하여 Template에 전달

### 3. 코드예시
    ```
    interface Task {
        void execute();
    }
    
    class TaskTemplate {
        public void run(Task task) {
            System.out.println("Start Task");
            task.execute();  // 콜백 실행
            System.out.println("End Task");
        }
    }
    
    public class TemplateCallbackDemo {
        public static void main(String[] args) {
            TaskTemplate template = new TaskTemplate();
    
            template.run(new Task() {
                public void execute() {
                    System.out.println("Task 1 실행");
                }
            });
    
            template.run(new Task() {
                public void execute() {
                    System.out.println("Task 2 실행");
                }
            });
        }
    }
    ```

### 4. Spring에서 대표적인 예시
| 사용 영역       | 역할/설명                                              | 예시                                                        |
| ----------- | -------------------------------------------------- | --------------------------------------------------------- |
| JDBC 템플릿    | 반복되는 커넥션 관리, 예외 처리, 결과 처리 공통 로직을 처리하고 SQL만 콜백으로 전달 | `JdbcTemplate#query(PreparedStatementCreator, RowMapper)` |
| 트랜잭션 처리     | 공통 트랜잭션 시작/커밋/롤백 로직 처리 후, 실제 비즈니스 로직만 콜백으로 실행      | `TransactionTemplate#execute(TransactionCallback)`        |
| 스프링 MVC 후처리 | 공통 응답 처리 로직 후, 개별 로직을 콜백으로 실행                      | `ResponseBodyAdvice` 구현                                   |
