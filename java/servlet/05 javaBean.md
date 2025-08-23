## 1. JavaBean
### 1. JavaBean
 - Sun(Java)에서 발표한 “재사용 가능한 컴포넌트” 규약
 - 프레임워크가 자동으로 객체를 만들고 다루기 쉽도록 “클래스를 작성할 때 지켜야 하는 약속
 
### 2. JavaBean 규칙
| 조건                   | 이유 / 목적                        |
| -------------------- | ------------------------------ |
| public 기본 생성자        | 외부에서 new Instance 시 리플렉션 가능하도록 |
| private 멤버 변수        | 캡슐화                            |
| public getter/setter | 표준화된 방법으로 값 접근 (읽기/쓰기)         |
| Serializable 구현      | 상태 저장, 네트워크 전송, JSP 세션 등 사용 가능 |


### 3. JavaBean 속성(Property)
 - JavaBean에 들어갈 멤버 변수는 자동으로 “프로퍼티”라고 호칭함.
 ```
  private String name;
  // getName() setName() 이 있으면 name이라는 Property를 가진 JavaBean이라고 판단함
 ```

### 4. javaBean 예시
    ```
    public class User {
        // private 필드 (캡슐화)
        private String name;
        private int age;
    
        // public 기본 생성자
        public User() {
        }
    
        // public getter / setter 메서드
        public String getName() {
            return name;
        }
        public void setName(String name) {
            this.name = name;
        }
    
        public int getAge() {
            return age;
        }
        public void setAge(int age) {
            this.age = age;
        }
    }    
    ```

### 5. JavaBean 사용
 - JSP: <jsp:useBean> 태그로 사용 → 자동 생성 & setter 호출
 - EL ( ${user.name} ) → getter 자동 호출
 - Spring MVC: Form에 입력값 바인딩 시 → setter 기반으로 값 넣음
 - Jackson(ObjectMapper): JSON → 객체 변환 시 → setter 기반으로 필드 채움
 - MyBatis: SQL 결과를 JavaBean에 자동 매핑 → setter 호출

## 2. Spring Bean
### 1. Spring Bean 
 - Spring IoC Container에 등록되어 컨테이너에 의해 관리되는 객체

### 2. SpringBean의 대상
| 방법         | 예시                                                     | 설명                                |
| ---------- | ------------------------------------------------------ | --------------------------------- |
| 어노테이션 기반   | `@Component`, `@Service`, `@Repository`, `@Controller` | 클래스에 붙이면 자동 등록 (`Component Scan`) |
| 설정 파일(xml) | `<bean id="..." class="..."/>`                         | XML 기반 설정 방식                      |
| 자바 설정 클래스  | `@Bean`                                                | `@Configuration` 안에서 메소드로 등록      |

### 3. SpringBean 예시
    ```
    @Service // “MemberService” 객체를 Spring Bean으로 등록
    public class MemberService {
    private final MemberRepository repository;
    
        @Autowired  // Spring이 다른 Bean(MemberRepository)을 주입함.
        public MemberService(MemberRepository repository) { 
            this.repository = repository;  // DI (Dependency Injection)
        }
    
        public void join() {
            repository.save();
        }
    }
    ```

## 3. JavaBean과 SpringBean
| 구분                | JavaBean                                                                   | Spring Bean                                  |
| ----------------- | -------------------------------------------------------------------------- | -------------------------------------------- |
| 정의            | 값(data)을 저장하고 전달하는 재사용 가능한 자바 객체                                       | Spring 컨테이너가 생성·관리하는 애플리케이션 구성 객체        |
| 주요 목적         | 값 저장, 계층 간 데이터 전달, 폼 바인딩                                                   | IoC/DI 적용, 생명주기 관리, AOP 적용                   |
| 필수 규약         | - public 기본 생성자<br>- private 필드<br>- public getter/setter | 없음 (Bean 등록 방법만 있으면 됨)                       |
| 누가 생성하나       | 개발자가 `new`                                                                 | Spring IoC 컨테이너                              |
| 주 사용 대상       | DTO, VO, Form 객체, Model 객체                                                 | Controller, Service, Repository, Component 등 |
| Life-cycle 관리 | 없음                                                                         | 초기화, 소멸, Proxy 등 Spring이 관리                  |
| 자동 주입(DI)     | 없음                                                                         | 가능 (`@Autowired`, 생성자 주입 등)                  |

