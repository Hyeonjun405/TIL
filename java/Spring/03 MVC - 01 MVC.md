## 1. mvc
### 1. mvc
- Model (모델)
    - 애플리케이션의 데이터와 비즈니스 로직을 담당
    - 데이터베이스에서 고객 정보를 가져오는 코드, 계산 로직 등
- View (뷰)
    - 사용자에게 보여지는 화면 담당
    - JSP, Thymeleaf, HTML 등
- Controller (컨트롤러)
    - 요청을 받아서 처리하고, 결과를 모델에 담아 뷰로 전달
    - 사용자가 /login 페이지를 요청하면, 로그인 처리 후 결과를 화면에 보여줌

### 2. 스프링 MVC 특징
 - 애노테이션 기반으로 요청 매핑 가능 (@Controller, @RequestMapping, @GetMapping 등)
 - DispatcherServlet이 중앙 컨트롤러 역할을 수행하여 모든 요청을 처리
   - MVC를 사용하지 않으면 dispatcherServlet이 없음,
   - 빈 관리용 컨테이너가 됨, 하지만 다른 기능들과 합쳐서 별도의 기능을 할 수 있음.
 - 요청 → 컨트롤러 → 모델 → 뷰 → 응답 흐름을 체계적으로 관리
 - 복잡한 웹 로직도 모듈화, 유지보수 용이하게 만들어줌

### 3. 컴포넌트 분류
``컴포넌트(component)는 스프링 MVC에서 특정 역할을 수행하는 독립적인 구성 요소``
- 핵심 컴포넌트

| 컴포넌트                     | 역할                                         |
| ------------------------ | ------------------------------------------ |
| DispatcherServlet        | 모든 요청을 받아 적절한 컨트롤러로 전달하는 중앙 컨트롤러           |
| HandlerMapping           | 요청 URL과 컨트롤러 메서드를 매핑                       |
| HandlerAdapter           | 컨트롤러 메서드를 실제 호출                            |
| HandlerExceptionResolver | 컨트롤러 처리 중 발생한 예외 처리                        |

- 요청 처리 관련 컴포넌트

  | 컴포넌트                      | 역할                         |
  | ------------------------- | -------------------------- |
  | Controller (@Controller)  | 요청 처리 및 모델 데이터 생성          |
  | Model / ModelAndView      | 컨트롤러에서 뷰로 전달할 데이터 저장       |
  | WebDataBinder / Validator | 요청 파라미터 바인딩 및 데이터 검증       |
  | Interceptor               | 요청 전/후 공통 로직 처리 (인증, 로깅 등) |

- 뷰 관련 컴포넌트

 | 컴포넌트         | 역할                                    |
  | ------------ | ------------------------------------- |
  | ViewResolver | 뷰 이름을 실제 JSP, Thymeleaf 등 파일로 변환      |
  | View         | 실제 화면 렌더링 담당 (JSP, Thymeleaf, JSON 등) |

- 기타 유틸리티 컴포넌트

| 컴포넌트              | 역할                           |
| ----------------- | ---------------------------- |
| MessageConverter  | HTTP 요청/응답 바디 변환 (JSON ↔ 객체) |
| LocaleResolver    | 다국어 처리, 요청 Locale 결정         |
| MultipartResolver | 파일 업로드 처리                    |

## 2. MVC모듈
### 1. 흐름
| 단계 | 컴포넌트                 | 역할                          |
| -- | -------------------- | --------------------------- |
| 1  | 클라이언트                | HTTP 요청 전송                  |
| 2  | DispatcherServlet    | 요청 수신, 처리 흐름 관리 (중앙 컨트롤러)   |
| 3  | HandlerMapping       | 요청 URL과 컨트롤러(Handler) 매핑    |
| 4  | HandlerAdapter       | 컨트롤러 메서드 실제 호출              |
| 5  | Controller (Handler) | 요청 처리, 모델 데이터 생성            |
| 6  | Model + View         | 컨트롤러가 뷰로 전달할 데이터와 뷰 정보      |
| 7  | ViewResolver         | 뷰 이름을 실제 JSP/HTML 등 화면으로 변환 |
| 8  | View                 | 최종 화면 렌더링, 클라이언트에 응답        |

### 2. DispatcherServlet
 - 스프링 MVC에서 모든 HTTP 요청을 중앙에서 수신하고, 적절한 컨트롤러와 뷰로 연결해주는 중앙 서블릿(Front Controller)
 - 클라이언트 요청을 받아 어떤 컨트롤러가 처리할지 결정
 - 컨트롤러 실행 후 반환된 모델과 뷰 정보를 받아 뷰로 전달
 - 예외 처리, 인터셉터, 메시지 변환 등 여러 보조 기능과 연계


### 3. HandlerMapping
#### 1. HandlerMapping
 - DispatcherServlet이 요청을 받으면 등록된 HandlerMapping 빈들을 순서대로 탐색
 - 각 HandlerMapping은 자신이 관리하는 핸들러 목록에서 해당 요청을 처리할 수 있는 핸들러를 찾음
 - 처리 가능 핸들러를 발견하면 바로 반환
 - 찾지 못하면 다음 HandlerMapping으로 이동
 - 결과: DispatcherServlet은 요청을 처리할 핸들러(Controller + 메서드)를 확보

#### 2. HandlerMapping Bean
 - 클래스 단위의 @RequestMapping + 메서드 단위의 @RequestMapping 정보를 합쳐서 HandlerMethod 객체로 생성
 - HandlerMethod = “컨트롤러 Bean + 메서드 + URL/HTTP 매핑 정보”

### 4. HandlerAdapter
#### 1. HandlerAdapter
 - DispatcherServlet은 HandlerMapping에서 반환한 핸들러를 Adapter에 전달
 - Adapter는 supports(handler)로 핸들러 객체를 실행할 수 있는 타입인지 확인
 - true → 실제 호출 → 반환값 처리

#### 2. HandlerAdapter 존재이유
 - 대부분의 경우 HandlerMapping이 반환한 HandlerMethod는 Adapter가 처리 가능
 - 그러나 스프링 MVC 설계상 → “실행 불가 타입”이 나올 가능성을 고려하여 Adapter가 supports()로 검증
 - 이 구조 덕분에 다양한 핸들러 타입 지원 + 확장성 확보 가능