## 1. MVC1&MVC2
### 1. MVC1 
#### 1. MVC 1 패턴 (정의)
 - 초기 JSP/서블릿 웹 구조에서 나온 패턴
 - JSP가 Controller와 View 역할을 동시에 수행하는 구조
 - 요청 처리, 비즈니스 로직 호출, 화면 출력까지 JSP 안에서 처리

#### 2. 역할
| 구분      | Controller                      | Model                         | View              |
| ------- | ------------------------------- | ----------------------------- | ----------------- |
| 목적      | 요청 처리, 사용자 입력을 받고 비즈니스 로직 호출    | 데이터와 비즈니스 로직 제공               | 사용자에게 화면 출력       |
| 포함 내용   | JSP 안 스크립틀릿(`<% %>`)으로 요청 처리    | DTO, VO, Entity, Service 등    | HTML, JSP 화면 템플릿  |
| 역할 범위   | 사용자 요청 파라미터 읽기, Model 호출, 결과 전달 | 데이터 검증, 계산, 저장/조회, 비즈니스 로직 수행 | 화면 구성, 데이터 표현     |
| MVC1 특징 | JSP 안에서 View와 섞여 있음             | JSP가 직접 호출하거나 포함              | Controller와 섞여 있음 |
| 수정/연산   | 입력 처리 및 로직 호출                   | 데이터 변경, 계산, 검증                | 거의 없음, 표현만        |

#### 3. note
 - MVC1 패턴 자체는 JSP 환경에서 자연스럽게 발생한 용어
 - 이유: JSP가 Controller와 View 역할을 동시에 수행했기 때문 다른 기술에서도 비슷한 구조는 존재
 - 예
   - PHP, ASP 초기 버전에서는 한 파일에서 요청 처리, DB 호출, 화면 출력까지 한꺼번에 처리 → MVC1과 유사
   - 단, 역사적 배경이나 공식 명칭에서 “MVC1”이라는 표현은 JSP/서블릿 진영에서만 사용

### 2. MVC2
### 1. MVC 패턴 (정의)
- ** 애플리케이션을 Model, View, Controller 세 부분으로 분리하여 각 컴포넌트의 역할을 명확히 하고, 관심사를 분리(Separation of Concerns)하는 소프트웨어 아키텍처 패턴 **
- M(Model): 비즈니스 로직, 데이터 처리, DB 접근
- V(View): 사용자에게 보여줄 화면
- C(Controller): 요청을 받아서 처리 → 모델 호출 → 뷰에 전달

#### 2. 역할
| 구분      | Controller                       | Model                    | View                   |
| ------- | -------------------------------- | ------------------------ | ---------------------- |
| 목적      | 요청 처리, DTO 생성, Service 호출, 결과 전달 | 데이터와 비즈니스 로직 수행          | 사용자에게 화면 출력            |
| 포함 내용   | Servlet, DTO 전달, Service 호출      | DTO, VO, Entity, Service | JSP, HTML, CSS 등 화면 구성 |
| 역할 범위   | HTTP 요청 처리, 파라미터 수집, Service 호출  | 검증, 계산, 저장/조회, 도메인 상태 관리 | 화면 출력, 데이터 표시          |
| MVC2 특징 | View와 분리, 단일 책임                  | 비즈니스 로직 집중               | 화면 표현만 집중              |
| 수정/연산   | 입력 검증은 최소, Service 호출            | 데이터/상태 수정 가능             | 거의 없음, 표현만             |

#### 3. 강점
| 강점       | 설명                                 | 예시/효과                          |
| -------- | ---------------------------------- | ------------------------------ |
| 역할 분리    | Model, View, Controller 각각 역할 명확   | 화면 수정 시 로직 건드리지 않고 가능          |
| 유지보수 용이  | 코드가 섞이지 않아 수정/확장 쉬움                | DB 로직 변경 → View 코드 영향 없음       |
| 재사용성     | Model과 Controller 로직 재사용 가능        | 같은 Model 데이터를 여러 화면에서 사용 가능    |
| 테스트 용이   | Model과 Controller를 독립적으로 단위 테스트 가능 | 화면 출력(View) 테스트 부담 최소화         |
| 규모 확장 용이 | 기능 증가 시 역할별 책임 분리되어 관리 편리          | 팀 단위 개발 시 작업 분리 가능, 스파게티 코드 방지 |

## 2. MVC1 -> MVC2
### 1. Controller 분리
- MVC1: JSP가 Controller 역할까지 겸함 (요청 처리 + 화면 출력)
- MVC2: Servlet 등 별도 Controller가 만들어져서 요청 처리 전담
- JSP는 화면 출력(View)만 담당

### 2. Model 사용 위치 변경
- MVC1: JSP에서 직접 Model(DTO/VO/Entity/Service)을 호출
- MVC2: Controller 안에서 Model을 호출
- Controller가 DTO/VO/Entity를 이용해 Service 호출 → 처리 결과를 JSP(View)에 전달

### 3. Note
 - Controller(Servlet)에서 DTO를 생성하고 Service 호출 → 결과 DTO를 JSP에 전달
 - JSP는 받은 DTO를 화면에 출력함.
 - 따라서 JSP 안에서는 비즈니스 로직, 검증, DB 접근 등은 거의 사라짐
 - 사용되는 내장 객체(request, session)도 데이터를 화면에 표시하거나 간단한 제어용으로만 제한

## 3. Servlet / JSP / MVC
### 1. Servlet / JSP / MVC
| 구분        | Servlet만 사용              | JSP 사용                 | MVC + JSP                             |
| --------- | ------------------------ | ---------------------- | ------------------------------------- |
| 화면과 로직 혼합 | HTML + PrintWriter 코드 혼합 | HTML + Java 코드 혼합 가능   | HTML + EL/JSTL 정도만 사용                 |
| 역할        | 로직 + 화면 모두 서블릿에서 처리      | 화면 + 로직 + DB 모두 처리 가능  | 화면 출력만 담당, 로직은 Controller/Service/DAO |
| 장점/특징     | 빠른 개발 가능, 단 유지보수 어려움     | 빠른 화면 구현 가능, JSP 문법 편리 | MVC 원칙 준수로 유지보수성 ↑, JSP 강점은 거의 사용 X   |

### 2. 변화
 - 서블릿 초기
   - Servlet 클래스 안에서 HTTP 요청 처리 + View(HTML) 생성을 모두 시도
   - 화면 출력이 복잡해지고, 코드가 길어짐 → 유지보수 어려움
   - 
 - JSP
   - HTML 화면을 쉽게 만들기 위해 JSP 도입
   - JSP 안에서 Java 코드(스크립틀릿) 작성 → 화면(View) + Controller 역할 혼합
   - DTO/VO 등 모델 객체를 JSP 안에서 직접 사용 → MVC1 구조 등장
   - 
 - MVC1
   - Controller와 View가 JSP 안에서 계속 겹침
   - 비즈니스 로직이 화면 코드에 섞여 유지보수, 재사용성 떨어짐
   - 
 - MVC2 등장
   - Controller(Servlet)를 별도 클래스 단위로 분리
   - Controller에서 DTO/VO를 만들어 Service 호출 → 처리 결과를 JSP(View)에 전달
   - JSP는 화면 출력만 담당
   - 최종 구조: Controller → Model → View, 역할이 명확하게 분리

### 3. 소스 코드 흐름
#### 1. JSP + WAS (MVC 미적용, 레거시)
```
클라이언트 요청 → WAS 수신
    ↓
JSP 파일 직접 요청
    ↓
WAS가 JSP → 서블릿(.java)로 변환 (최초 요청 시)
    ↓
컴파일 → 서블릿 클래스(.class) 생성
    ↓
서블릿 실행 → HTML 생성
    ↓
WAS → 클라이언트 응답
```

#### 2. JSP + WAS (MVC2 적용, 스프링 미사용)
```
클라이언트 요청 → WAS 수신
    ↓
URL 매핑 확인 → Controller(Servlet) 호출
    ↓
Controller → Model(Service/DAO) 호출 (데이터 처리)
    ↓
Controller → JSP(View) 호출 (forward)
    ↓
WAS가 JSP 존재 여부 확인
        - 없으면 JSP → 서블릿 변환 + 컴파일
        - 있으면 기존 서블릿 클래스 실행
    ↓
JSP 실행 → HTML 생성
    ↓
WAS → 클라이언트 응답
```

#### 3. JSP + WAS (MVC2 적용, 스프링 사용)
```
클라이언트 요청 → WAS 수신
    ↓
DispatcherServlet (Spring Front Controller) 최초 요청 수신
    ↓
DispatcherServlet → 알맞은 Controller 호출
    ↓
Controller → Model(Service/DAO) 호출 (데이터 처리)
    ↓
Controller → View 이름 반환
    ↓
DispatcherServlet → ViewResolver 통해 JSP(View) 호출
    ↓
JSP 존재 여부 확인 및 실행 (서블릿 클래스 재사용)
    ↓
HTML 생성 → DispatcherServlet → WAS → 클라이언트 응답
```

