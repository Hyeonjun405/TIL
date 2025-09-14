## 1. AOP
### 1. AOP
- 핵심 로직과 공통 관심사(Cross-Cutting Concern)를 분리하는 프로그래밍 기법
   - 핵심 로직(Core Business Logic): 실제 애플리케이션이 수행하는 기능
   - 횡단 관심사(Cross-Cutting Concern): 로깅, 트랜잭션, 보안, 성능 측정 등 여러 곳에서 반복되는 코드
    
### 2. 구성요소
| 구성 요소      | 설명                                                                   |
| ---------- |----------------------------------------------------------------------|
| Aspect     | 공통 관심 사항을 모듈화한 단위 (ex: 로그, 트랜잭션)                                     |
| Advice     | Aspect 내에서 실행될 코드 (Before, After, Around 등)<br> 실행 되는 코드와 타이밍        |
| Join point | Advice가 적용될 수 있는 지점 (메서드 실행, 예외 발생 등)<br> 어떤 메소드에서 advice를 실행할 것 인가? |
| Pointcut   | Advice를 적용할 Join point를 지정하는 표현식                                     |
| Target     | 핵심 비즈니스 로직이 있는 객체                                                    |
| Weaving    | Aspect와 Target을 연결하는 과정 (컴파일, 클래스 로딩, 런타임)                           |

## 2. AOP 사용!
### 1. 어드바이스 종류
| Advice 종류       | 실행 시점       | 내용                                            | 예시 코드                                                                                                                                                                                                                 |
| --------------- | ----------- | --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Before          | 메서드 실행 전    | 메서드 실행 전에 수행, 주로 로깅, 권한 체크, 입력 검증             | `@Before("execution(* com.example.service.*.*(..))") public void logBefore(JoinPoint jp) { }`                                                                                                                         |
| After Returning | 메서드 정상 종료 후 | 메서드 결과 값 사용 가능, 성공 후 처리                       | `@AfterReturning(pointcut="execution(* com.example.service.*.*(..))", returning="result") public void logAfterReturn(Object result) { }`                                                                              |
| After Throwing  | 메서드 예외 발생 시 | 예외 처리, 로깅 등에 사용                               | `@AfterThrowing(pointcut="execution(* com.example.service.*.*(..))", throwing="ex") public void logException(Exception ex) { }`                                                                                       |
| After (Finally) | 메서드 종료 후 항상 | 정상/예외 관계없이 항상 실행                              | `@After("execution(* com.example.service.*.*(..))") public void logAfter() { }`                                                                                                                                       |
| Around          | 메서드 실행 전/후  | 메서드 실행 전후 모두 처리 가능, 결과 조작 가능, proceed() 호출 필수 | `@Around("execution(* com.example.service.*.*(..))") public Object logAround(ProceedingJoinPoint pjp) throws Throwable { System.out.println("전"); Object res = pjp.proceed(); System.out.println("후"); return res; }` |
 - Before → Around → AfterReturning/AfterThrowing → After(Finally)

### 1. execution
#### 1. execution
- execution = 메서드 실행(join point)을 패턴으로 선택하는 지시자
- execution(접근제어자 반환타입 패키지.클래스.메서드(매개변수))

#### 2. execution 사용
| 위치      | 의미                        | 예시                               |
| ------- | ------------------------- | -------------------------------- |
| 접근제어자   | public, private 등 (생략 가능) | `public`                         |
| 반환타입    | 리턴 타입, `*` 가능             | `void`, `*`                      |
| 클래스/패키지 | 적용 대상 클래스, 패키지            | `com.example.service.CarService` |
| 메서드     | 메서드 이름, `*` 가능            | `drive`, `*`                     |
| 매개변수    | 파라미터 타입, `..` = 0개 이상     | `()`, `(..)`, `(String, ..)`     |

#### 3. exectuion 예시
| 예시                                                            | 의미                     | 적용 대상                                              |
| ------------------------------------------------------------- | ---------------------- | -------------------------------------------------- |
| `execution(* com.example.CarService.drive(..))`               | 특정 클래스의 특정 메서드         | `CarService` 클래스의 `drive()` 메서드, 매개변수 개수와 타입 무관    |
| `execution(* com.example.CarService.*(..))`                   | 특정 클래스의 모든 메서드         | `CarService` 클래스 내 모든 메서드                          |
| `execution(* com.example.service.*.*(..))`                    | 특정 패키지 모든 클래스, 모든 메서드  | `com.example.service` 패키지 내 모든 클래스, 모든 메서드         |
| `execution(String com.example.CarService.getName(..))`        | 특정 반환타입 메서드            | 반환 타입이 `String`인 `CarService` 클래스의 `getName()` 메서드 |
| `execution(public void com.example.CarService.start(String))` | 접근제어자 + 반환타입 + 매개변수 지정 | public, 반환 void, 매개변수 String 1개인 `start()` 메서드     |
| `execution(* com.example..*Service.*(..))`                    | 와일드카드 + 하위 패키지         | `com.example` 패키지와 하위 패키지의 `*Service` 클래스 모든 메서드   |

### 3. 포인트컷 표현식(pointcut expression)
#### 1. 포인트컷
 - @Pointcut을 쓰는 이유 = 여러 Advice에서 재사용 가능 + 가독성/유지보수 편리
 - 포인트컷을 안쓰고 어드바이스에 execution을 넣어도 문제없음.

#### 2. 예시s
```
// 포인트컷 사용
@Pointcut("execution(* com.example.service.*.*(..))")
public void serviceMethods() { }

@Before("serviceMethods()")
public void logBefore() { }

@After("serviceMethods()")
public void logAfter() { }
```
```
@Before("execution(* com.example.service.*.*(..))")
public void logBefore() { }
```
