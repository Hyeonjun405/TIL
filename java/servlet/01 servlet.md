## 1. Servlet
### 1. Servlet
 - Java 기반 웹 애플리케이션에서 HTTP 요청을 받아 처리하고, 그 결과를 HTTP 응답으로 돌려주는 서버 측 컴포넌트
 - 브라우저 → 서버로 들어오는 요청을 자바 코드로 처리하게 해주는 역할

### 2. Servlet 동작 흐름
 - 클라이언트 요청 (HTTP Request) : 브라우저가 URL 호출 → Tomcat 같은 WAS(Web Application Server)로 요청 도착
 - WAS가 요청을 해당 Servlet에게 전달 : URL 패턴에 따라 어떤 서블릿이 요청을 처리할지 매핑 (web.xml 또는 @WebServlet)
 - Servlet 객체의 doGet()/doPost() 실행 : 요청 방식에 따라 메소드가 실행되고 HttpServletRequest, HttpServletResponse 객체가 전달됨
 - 비즈니스 로직 처리 :  DB 조회, 계산 등 필요한 작업 진행
 - 응답 생성 후 클라이언트로 응답 전송 : HttpServletResponse를 이용해 HTML, JSON 등 클라이언트로 반환

### 3. 주요개념
| 개념                    | 설명                                         |
| --------------------- |--------------------------------------------|
| HttpServletRequest    | 클라이언트 요청 정보 (파라미터, 헤더 등) 담고 있음             |
| HttpServletResponse   | 서버 → 클라이언트로 보낼 응답 (컨텐츠타입, 상태코드, body 등) 다룸 |
| web.xml / @WebServlet | 서블릿 URL 매핑 정보<br>실제 서블릿 클래스를 공개하지 않기 위해서 URL로 접근함.                       |
| Lifecycle             | init() → service() → destroy() 순으로 동작      |

### 4. 라이프사이클
```
 클래스 로딩 → 인스턴스 생성 → init() → service() → destroy()
```
 - 클래스 로딩 & 인스턴스 생성
   - 서버가 처음 요청을 받거나, 서블릿이 초기화될 때
   - .class 를 메모리에 로딩하고 Servlet 객체를 1개만 생성 (Singleton)
 - init(ServletConfig config)
   - 생성 직후 단 한번만 호출
   - 초기화 파라미터 세팅 등 서블릿 시작 준비 수행
   - 이 시점 이후에 요청 받기 시작함
 - service(HttpServletRequest req, HttpServletResponse resp)
   - 클라이언트 요청이 들어올 때마다 호출되는 메소드
   - HTTP 방식에 따라 내부에서 doGet(), doPost() 등 각 메소드를 호출
   - 핵심 비즈니스 로직이 실행되는 영역
 - destroy()
   - WAS가 종료되거나 서블릿이 언로드 될 때 단 한번 호출
   - 리소스 반납, 연결 종료 같은 정리(clean-up) 작업 수행

## 2. Request / Response
### 1. HttpServletRequest
 - HttpServletRequest 는 서블릿 간, JSP와 같은 서버 사이에서 데이터를 전달하는 용도
 - request 속성은 같은 요청 내에서만 유효
 - 메서드 종류
 
| 메서드                               | 반환 / 설명                 | 용도                       |
| --------------------------------- | ----------------------- | ------------------------ |
| `getParameter(String name)`       | String                  | GET/POST 파라미터 조회         |
| `getParameterValues(String name)` | String\[]               | 체크박스 등 다중값 조회            |
| `getParameterMap()`               | Map\<String, String\[]> | 전체 파라미터 Map 조회           |
| `getHeader(String name)`          | String                  | 요청 헤더 조회                 |
| `getContentType()`                | String                  | 요청 Content-Type 조회       |
| `getMethod()`                     | String                  | HTTP 메서드(GET, POST 등) 확인 |
| `getRequestURI()`                 | String                  | 요청 URI 조회                |
| `getContextPath()`                | String                  | 서블릿 컨텍스트 경로 조회           |
| `getSession()`                    | HttpSession             | 세션 객체 얻기                 |
| `getCookies()`                    | Cookie\[]               | 요청 쿠키 조회                 |
| `getReader()`                     | BufferedReader          | 요청 Body 문자 읽기 (JSON 등)   |
| `getInputStream()`                | ServletInputStream      | 요청 Body 바이트 읽기           |

### 2. HttpServletRequest
| 메서드                                    | 설명                  | 용도                        |
| -------------------------------------- | ------------------- | ------------------------- |
| `setContentType(String type)`          | Content-Type 지정     | 클라이언트가 데이터를 올바르게 해석하도록    |
| `setCharacterEncoding(String enc)`     | 문자 인코딩 지정           | UTF-8 등                   |
| `setHeader(String name, String value)` | 응답 헤더 설정            | Cache-Control, Location 등 |
| `setStatus(int sc)`                    | 상태 코드 설정            | 200, 404, 500 등           |
| `sendError(int sc, String msg)`        | 에러 응답 전송            | 404 Not Found 등           |
| `sendRedirect(String location)`        | 클라이언트 리다이렉트         | 다른 URL로 이동                |
| `getWriter()`                          | PrintWriter         | 문자 기반 응답 출력               |
| `getOutputStream()`                    | ServletOutputStream | 바이너리 응답 출력 (파일, 이미지 등)    |
| `addCookie(Cookie cookie)`             | 쿠키 추가               | Set-Cookie 헤더 전송          |

## 3. Request / Response 처리
### 1. Servelt으로 http 응답
```
@WebServlet("/hello") // URL 패턴 설정
public class FirstServlet extends HttpServlet { // HttpServlet 확장

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException { // http method

        resp.setContentType("text/html"); // 응답 타입 설정
        resp.setContentType("text/html; charset=UTF-8"); // 응답 타입 설정 + UTF8로 설정

        PrintWriter out = resp.getWriter(); // 리턴 객체 설정 
        out.print("<html><body><h1>"); // HTTP 형식으로 작성
        out.print("Hello Servlet");  // HTTP 형식으로 작성
        out.print("</h1></body></html>");  // HTTP 형식으로 작성
    }
```

### 2. http method
 ```
 protected void doGet(HttpServletRequest request, HttpServletResponse response) { ... }
 protected void doPost(HttpServletRequest request, HttpServletResponse response) { ... }
 protected void doPut(HttpServletRequest request, HttpServletResponse response) { ... }
 protected void doDelete(HttpServletRequest request, HttpServletResponse response) { ... }
 ```
 - WebServlet으로 요청온 http method에 따라서 get/post/put/delete 등으로 각기 분류됨
 
### 3. Content-Type
#### 1. 요청구분
 - 요청 방향에 따라서 Content-Type의 역할과 Servlet에서 가지는 의미가 달라짐. 
  
   | 요청 방향           | 사용 목적                        | Servlet에서 의미                                             |
   | ------------ | ---------------------------- | -------------------------------------------------------- |
   | 요청(Request)  | 서버가 어떤 형식으로 데이터를 받았는지 알려줌    | `request.getContentType()` 으로 확인 → 어떤 방식으로 읽을지 결정        |
   | 응답(Response) | 클라이언트에게 어떤 형식으로 데이터를 보낼지 알려줌 | `response.setContentType()` 으로 지정 → 브라우저나 클라이언트가 올바르게 해석 |

#### 2. Content-Type 종류
  
  | Content-Type                      | 의미           | Servlet에서 처리 방법                                                        |
  | --------------------------------- | ------------ | ---------------------------------------------------------------------- |
  | application/x-www-form-urlencoded | 일반 HTML 폼 전송 | `request.getParameter()` 로 자동 파라미터 추출 가능                               |
  | multipart/form-data               | 파일 업로드 폼     | `Servlet 3.0` 이상: `@MultipartConfig` + `request.getPart()` 사용          |
  | application/json                  | JSON 데이터     | `request.getReader()` / `request.getInputStream()` 로 직접 읽어서 JSON 파싱 필요 |
  | text/plain                        | 일반 텍스트       | `request.getReader()` 로 읽음                                             |

#### 3. Request에서 파라미터 확인
  - QueryString과 Pom
  ```
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // 쿼리스트링 파라미터 꺼내기
        String key1 = request.getParameter("key1"); // key1 = value1 
        String key2 = request.getParameter("key2"); // key2 = valeu2
    }
 ```

 - Json Type
 ```
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

      // 1. Request Body 획득
      StringBuilder sb = new StringBuilder();
      BufferedReader reader = request.getReader(); //버퍼로 한줄씩 읽음.
      String line;
      while((line = reader.readLine()) != null) {
          sb.append(line); 
      }

      String jsonString = sb.toString(); // JSON 문자열 획득 

      // 2. JSON 파싱 (예: org.json 사용)
      JSONObject json = new JSONObject(jsonString);
      String name = json.getString("name");
      int age = json.getInt("age");

    }
 ```

## 4. JSP
### 1. JSP
 - JSP(Java Server Pages)는 HTML 안에 Java 코드를 삽입해 동적인 웹 페이지를 만드는 기술
 - 서블릿을 대신하거나 보완하기 위해 만들어짐
 - 서블릿은 순수 Java 코드로 화면 생성 → 유지보수 어려움
 - JSP는 HTML 중심 + 필요한 Java 코드 삽입 → 화면 작성 간편

### 2. 흐름
 - 클라이언트 요청 → WAS가 JSP 호출
 - JSP → 서블릿으로 변환 () JSP 파일이 최초 요청 시 서블릿 클래스로 변환되고 컴파일 )
 - 서블릿 실행 ( HTML + JSP 코드가 포함된 response 생성 )
 - 결과를 HttpServletResponse에 기록 → 클라이언트 전송

### 3. 패턴
| 패턴        | 요청 흐름                                                                                                 | 역할                                     | 장점                                              | 단점                          |
| --------- | ----------------------------------------------------------------------------------------------------- | -------------------------------------- | ----------------------------------------------- | --------------------------- |
| 직접 접근     | 클라이언트 → JSP → WAS → JSP 실행 → 클라이언트                                                                    | JSP가 화면 생성 + 일부 로직 처리                  | 간단, 바로 화면 확인 가능                                 | 로직과 화면이 뒤섞임 → 유지보수 어려움      |
| 서블릿 + JSP | 클라이언트 → WAS → 서블릿 → request.setAttribute() → RequestDispatcher → JSP → 클라이언트                          | 서블릿: 로직 처리 / JSP: 화면 출력                | 로직과 화면 분리 → 유지보수 용이                             | 서블릿마다 반복 코드 존재, 공통 처리 어려움   |
| 스프링 MVC   | 클라이언트 → WAS → DispatcherServlet → Controller/Service Bean → Model 데이터 준비 → ViewResolver → JSP → 클라이언트 | Controller/Service: 로직 처리 / JSP: 화면 출력 | 로직/화면 완전 분리, 중앙 집중 처리, DI/Interceptor/AOP 활용 가능 | 초기 구조 이해 필요, 설정/DI 개념 학습 필요 |


## 5. WAS - Serlvet - Spring
### 1. 스프링 이전 구조 (기본 WAS + 서블릿)
   - 흐름
     1. 클라이언트 요청 → WAS(톰캣 등) 수신
     2. WAS가 HttpServletRequest / HttpServletResponse 객체 생성
     3. 요청 URL 매핑 → 웹 서블릿 선택
     4. 웹 서블릿 실행
       - 로직 처리 (DB 조회, 계산 등)
       - JSP로 데이터 전달 필요 시 request.setAttribute()
     5. RequestDispatcher 사용
       - 서블릿 → JSP forward/include
     6. JSP 실행 → 화면 생성 → response로 클라이언트 반환
   - 포인트
     - WAS는 입출구 + 객체 생성 + 서블릿 연결 정도만 수행
     - 실제 로직/화면 처리는 서블릿/JSP가 담당
     - Dispatcher 역할은 있었지만 단순 forward/include 수준
### 2. 스프링 이후 구조 (WAS + DispatcherServlet + Bean)
  - 흐름
    1. 클라이언트 요청 → WAS 수신
    2. WAS가 HttpServletRequest / HttpServletResponse 생성
    3. WAS가 DispatcherServlet 호출 (모든 요청 집중)
    4. DispatcherServlet 내부 처리
      - HandlerMapping → Controller/Bean 호출
      - 비즈니스 로직 수행 (Service/Repository)
      - Model 데이터 준비
      - ViewResolver → JSP/템플릿 선택
    5. DispatcherServlet이 response 작성 → WAS → 클라이언트 반환
  - 포인트
    - WAS는 request/response 생성 + DispatcherServlet 호출 정도로 역할 최소화
    - DispatcherServlet이 모든 요청/응답, Bean 호출, View 연결 중앙 관리
    - 로직/뷰 분리, 공통 처리 통합, DI 지원 등 장점 확보
### 3. 비교
| 항목                  | 스프링 이전               | 스프링 이후                              |
| ------------------- | -------------------- | ----------------------------------- |
| request/response 생성 | WAS가 생성 → 개별 서블릿     | WAS가 생성 → DispatcherServlet에 통으로 전달 |
| 요청 처리               | 서블릿이 직접              | DispatcherServlet → Controller/Bean |
| JSP 연결              | RequestDispatcher 사용 | DispatcherServlet + ViewResolver    |
| 공통 처리               | 서블릿마다 반복 구현          | Interceptor/AOP로 중앙 관리              |
| WAS 역할              | 입출구 + 객체 생성 + 서블릿 호출 | 입출구 + DispatcherServlet 호출 (단순화)    |