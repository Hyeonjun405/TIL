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
 
   | 컴포넌트                     | 역할                               |
   | ------------------------ | -------------------------------- |
   | DispatcherServlet        | 모든 요청을 받아 적절한 컨트롤러로 전달하는 중앙 컨트롤러 |
   | HandlerMapping           | 요청 URL과 컨트롤러 메서드를 매핑             |
   | HandlerAdapter           | 컨트롤러 메서드를 실제 호출                  |
   | HandlerExceptionResolver | 컨트롤러 처리 중 발생한 예외 처리              |

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
### 1. DispatcherServlet
#### 1. DispatcherServlet
 - 스프링 MVC에서 모든 HTTP 요청을 중앙에서 수신하고, 적절한 컨트롤러와 뷰로 연결해주는 중앙 서블릿(Front Controller)
 - 클라이언트 요청을 받아 어떤 컨트롤러가 처리할지 결정
 - 컨트롤러 실행 후 반환된 모델과 뷰 정보를 받아 뷰로 전달
 - 예외 처리, 인터셉터, 메시지 변환 등 여러 보조 기능과 연계

#### 2. 흐름
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

### 2. Controller
#### 1. Controller 설정
```
@Controller
public class HelloController {

    @GetMapping("/hello")
    public String sayHello(Model model) {
        model.addAttribute("message", "안녕하세요, 스프링 MVC!");
        return "hello"; // 뷰 이름 (hello.jsp 또는 hello.html)
    }
}
```

### 2. 애노테이션
#### 1. 클래스 레벨 
| 애노테이션             | 역할                                                                   |
| ----------------- | -------------------------------------------------------------------- |
| `@Controller`     | 일반적인 웹 컨트롤러, 뷰 이름을 반환할 때 사용                                          |
| `@RestController` | JSON, XML 등 HTTP 바디 직접 반환 시 사용 (`@Controller + @ResponseBody` 기능 포함) |

#### 2. http 메서드
| 애노테이션             | 역할                                |
| ----------------- | --------------------------------- |
| `@RequestMapping` | 요청 URL과 HTTP 메서드(GET, POST 등)를 매핑 |
| `@GetMapping`     | GET 요청을 처리 (편리한 단축 애노테이션)         |
| `@PostMapping`    | POST 요청을 처리                       |
| `@PutMapping`     | PUT 요청을 처리                        |
| `@DeleteMapping`  | DELETE 요청을 처리                     |
| `@PatchMapping`   | PATCH 요청을 처리                      |

#### 3. 요청 데이터
| 애노테이션             | 역할                         |
| ----------------- | -------------------------- |
| `@RequestParam`   | 쿼리 파라미터 값 매핑               |
| `@PathVariable`   | URL 경로 변수 매핑               |
| `@RequestBody`    | 요청 바디(JSON 등)를 객체로 변환      |
| `@ResponseBody`   | 반환값을 HTTP 응답 바디로 직접 출력     |
| `@ModelAttribute` | 폼 데이터나 모델 객체 바인딩, 뷰에 자동 추가 |

## 3. note
### 1. 서블릿과 컨트롤러
- 기존 순수 서블릿 방식에서는 URL마다 별도의 서블릿 클래스가 필요했지만
- 스프링 MVC에서는 애노테이션을 사용해 메소드 단위로 URL 매핑이 가능하므로, 비슷한 기능을 한 클래스 안에 통합해 관리할 수 있음.

| 항목         | 순수 서블릿                     | 스프링 MVC                                |
| ---------- | -------------------------- | -------------------------------------- |
| 클래스 단위     | URL마다 별도 서블릿 클래스 필요        | 한 Controller 클래스에 여러 메소드 매핑 가능         |
| 매핑 단위      | 클래스 단위(@WebServlet URL 지정) | 메소드 단위(@RequestMapping, @GetMapping 등) |
| 코드 관리      | 기능 유사해도 클래스가 많아져 관리 복잡     | 관련 기능을 한 클래스 안에 통합 가능, 유지보수 용이         |
| 비즈니스 로직 분리 | 대부분 서블릿 안에 직접 구현           | Controller → Service 계층으로 분리 가능        |
| 확장성        | 새로운 URL마다 클래스 추가 필요        | 메소드 추가만으로 기능 확장 가능                     |
| 애노테이션 활용   | 제한적, 주로 @WebServlet        | 풍부한 매핑/설정 애노테이션 활용 가능                  |
