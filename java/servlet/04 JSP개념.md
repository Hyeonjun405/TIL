## 1. JSP
### 1. JSP
- JSP(JavaServer Pages)
- HTML 코드 안에 Java 코드를 삽입해 동적인 웹 페이지를 생성할 수 있게 해주는 자바 기반 기술
- JSP는 실행되기 전에 서블릿 코드로 변환되고, 이후 컴파일되어 동작함.
- JSP 파일(.jsp)은 기본적으로 HTML 구조를 가지면서, 필요한 부분에만 Java 코드나 JSP 태그를 삽입해서 동적인 데이터를 표현함.

### 2. jsp 기본 흐름
 - 클라이언트 요청
   - 사용자가 브라우저에서 http://localhost:8080/hello.jsp 같은 URL을 요청
   - 이 요청은 웹 서버(Apache, Nginx 등) → WAS(Tomcat 등) 으로 전달됨
 - WAS가 JSP 요청 처리 준비
   - WAS 내부의 서블릿 컨테이너가 JSP 요청을 감지
   - JSP 파일이 이전에 변환된 적이 없다면, 변환 작업을 수행
 - JSP → 서블릿 소스 변환
   - JSP 파일(hello.jsp)이 **서블릿 자바 소스 코드(hello_jsp.java)**로 변환됨
   - 이 소스에는 JSP의 HTML은 out.write("...") 형태로, <%= ... %> 같은 자바 표현식은 자바 코드로 변환됨
 - 서블릿 클래스 생성 (컴파일)
   - 변환된 hello_jsp.java 파일을 자바 컴파일러가 컴파일
   - 결과물로 .class 파일 (hello_jsp.class) 생성됨
   - 이 클래스는 HttpServlet을 상속하고, _jspService() 메서드 안에 JSP 코드가 들어감
 - 서블릿 실행
   - 서블릿 컨테이너가 hello_jsp.class를 로딩해서 실행
   - request와 response 객체를 전달받아 동작
   - 결과로 HTML 문자열이 생성됨
 - 응답 반환
   - 실행 결과(HTML)가 WAS를 거쳐 웹 서버 → 클라이언트 브라우저로 전송됨
   - 브라우저는 HTML을 렌더링해서 화면에 보여줌

### 3. JSP → 서블릿 소스 변환
- hello.jsp
```
<html>
  <body>
    <h1>Hello, <%= request.getParameter("name") %></h1>
  </body>
</html>
```
- hello_jsp.java
```
 public final class index_jsp extends HttpServlet {
    public void _jspService(HttpServletRequest request, HttpServletResponse response)
            throws IOException, ServletException {
        response.setContentType("text/html;charset=UTF-8");
        JspWriter out = response.getWriter();

        out.write("<html><body>");
        out.write("<h1>Hello, ");
        out.write(request.getParameter("name"));
        out.write("</h1>");
        out.write("</body></html>");
    }
}
```
### 4. 두번째 수행
- JSP 파일이 한 번 서블릿/클래스로 변환되면, 다음 요청부터는 기존 서블릿 클래스를 재사용
- JSP 파일이 수정되면 WAS가 다시 변환/컴파일 과정을 거쳐 새로운 클래스 로딩

### 5. JSP - DB
```
<%
// JSP 안에서 DB 연결 가능하기는 함. 
Connection conn = DriverManager.getConnection(url, user, password);
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT name FROM users");

    while(rs.next()){
        out.println("<p>" + rs.getString("name") + "</p>");
    }

    rs.close();
    stmt.close();
    conn.close();
%>
```
- 유지보수 문제: 화면 출력과 DB 로직이 섞이면 스파게티 코드가 됨
- 재사용성 문제: DB 처리 로직을 다른 화면에서 재사용하기 어려움
- 보안/트랜잭션 관리 문제: Connection, commit/rollback 관리가 어려움

### 6. JSP 한계
| 한계        | 설명                                |
| --------- | --------------------------------- |
| 역할 혼동     | 화면 + 로직 + DB 처리 혼합 → 스파게티 코드 발생   |
| HTML 친화성  | 스크립틀릿 때문에 HTML 구조 깨짐, 디자이너 수정 어려움 |
| 동적 UI 한계  | Ajax, SPA, WebSocket 대응 어려움       |
| 유지보수성     | 로직 혼합 → 테스트·재사용 어려움, 대규모 서비스에 비효율 |
| 최신 기술 호환성 | REST API, 프론트엔드 프레임워크와 연동 어려움     |
| 성능        | 최초 요청 시 컴파일 필요, 반복문 과다 시 서버 부하    |


## 2. jsp와 서블릿
### 1. 서블릿과 jsp
| 구분        | 서블릿(Servlet)                               | JSP(JavaServer Pages)        |
|-----------| ------------------------------------------ | ---------------------------- |
| 정의        | 자바 클래스 기반 서버 컴포넌트                          | HTML 기반 템플릿 안에 자바 코드 삽입 가능   |
|  코드 작성 방식 | 순수 자바 코드로 작성, `doGet()`, `doPost()` 메서드 사용 | HTML 중심, 필요한 부분만 자바 코드/EL 사용 |
| 역할        | 로직 처리, 요청/응답 제어 (Controller/Service 역할)    | 화면 출력, 데이터 표시 (View 역할)      |
| 실행 구조     | 개발자가 직접 작성 → 컴파일 후 실행                      | WAS가 자동으로 서블릿 변환 후 실행        |
| 장점        | 로직 제어에 유리, 구조적                             | UI 작성이 쉬움, HTML 친화적          |
| 단점        | 화면 출력 불편 (`out.println()` 남발)              | 로직 섞이면 스파게티 코드 위험            |

### 2. 생명주기
| 구분    | 서블릿(Servlet)                            | JSP                                        |
| ----- |-----------------------------------------| ------------------------------------------ |
| 로딩    | 개발자가 작성한 `.java` 컴파일 후 로딩               | JSP 파일이 요청되면 WAS가 `.java` 변환 후 `.class` 로딩 |
| 초기화   | `init()`                                | `jspInit()`                                |
| 요청 처리 | `service()` → `doGet()`, `doPost()`<br> ※ service() = 모든 요청이 들어오는 입구 | `_jspService()` (HTML+Java 코드 실행)          |
| 종료    | `destroy()`                             | `jspDestroy()`                             |
| 개발 관점 | 비즈니스 로직 중심                              | 화면(View) 중심                                |

## 3. jsp와 MVC
### 1. MVC 패턴
 - M(Model): 비즈니스 로직, 데이터 처리, DB 접근
 - V(View): 사용자에게 보여줄 화면
 - C(Controller): 요청을 받아서 처리 → 모델 호출 → 뷰에 전달

### 2. 흐름
 - 클라이언트가 GET /users 요청
 - Controller(서블릿)가 요청 받음 → DB에서 사용자 목록 조회
 - Model(Service/DAO)가 데이터 처리
 - JSP(View)에 데이터를 전달 → 화면 렌더링(화면 출력에 최적화되어 있고, HTML 구조와 동적 데이터 출력만 처리)
 - 클라이언트에게 HTML 응답

### 3. 변화
| 과거 JSP                  | MVC + JSP                             |
| ----------------------- | ------------------------------------- |
| HTML + 자바 코드 자유롭게 혼합 가능 | HTML + EL/JSTL 정도만 사용                 |
| 화면 + 로직 + DB 모두 처리 가능   | 화면 출력만 담당, 로직은 Controller/Service/DAO |
| 빠른 화면 구현 가능             | MVC 원칙 준수로 유지보수성 ↑, 단 JSP 강점은 거의 사용 X |


## 4. MVC - Spring
### 1. Spring - MVC
- MVC 패턴
   - 소프트웨어 설계 원칙 중 하나
   - 1979년 Smalltalk에서 처음 등장 → 화면(View), 로직(Controller), 데이터(Model) 분리
   - 자바 이전에도 존재했음 → 설계 패턴이지 특정 프레임워크가 아님
- 스프링(Spring)
   - 2002년경 Rod Johnson이 만든 자바 기반 애플리케이션 프레임워크
   - Spring Framework 안에 Spring MVC 모듈이 있음 → 자바에서 MVC 패턴을 쉽게 구현하도록 지원

### 2. MVC 강점
| 강점       | 설명                                 | 예시/효과                          |
| -------- | ---------------------------------- | ------------------------------ |
| 역할 분리    | Model, View, Controller 각각 역할 명확   | 화면 수정 시 로직 건드리지 않고 가능          |
| 유지보수 용이  | 코드가 섞이지 않아 수정/확장 쉬움                | DB 로직 변경 → View 코드 영향 없음       |
| 재사용성     | Model과 Controller 로직 재사용 가능        | 같은 Model 데이터를 여러 화면에서 사용 가능    |
| 테스트 용이   | Model과 Controller를 독립적으로 단위 테스트 가능 | 화면 출력(View) 테스트 부담 최소화         |
| 규모 확장 용이 | 기능 증가 시 역할별 책임 분리되어 관리 편리          | 팀 단위 개발 시 작업 분리 가능, 스파게티 코드 방지 |

### 3. 변화
 - 과거 JSP 강점
   - JSP는 HTML 안에 자바 코드를 삽입해서 동적 페이지를 만들 수 있었음
   - 내부적으로 서블릿으로 자동 변환되므로, 요청 처리와 화면 렌더링을 동시에 처리 가능
   - 작은 프로젝트에서는 빠르게 화면+로직 구현 가능

 - MVC 구조와 스프링 등장
   - Spring MVC가 등장하면서, 역할 분리가 철저하게 지켜지는 개발 패턴이 가능해짐
   - Controller → 요청 처리, Model → 데이터/비즈니스 로직, View → 화면 출력
   - JSP는 원래 서블릿처럼 동적으로 로직 처리 가능하지만, MVC에서는 View 역할만 담당

 - 결과
   - JSP의 “서블릿처럼 Java 코드를 쓰면서 동적 페이지 처리”라는 강점은 사실상 필요 없어짐
   - EL, JSTL, Thymeleaf 같은 템플릿 기반 View 기술이 등장하면서 JSP 스크립틀릿을 거의 안 쓰게됨.
   - 그래서 JSP를 굳이 유지할 이유가 점점 사라진 것

## 5. JSP - MVC - Spring
### 1. JSP + WAS (MVC 미적용, 레거시)
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

### 2. JSP + WAS (MVC 적용, 스프링 미사용)
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

### 3. JSP + WAS (MVC 적용, 스프링 사용)
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

### 4. Controller - JSP - WAS
| 케이스           | Controller            | JSP   | WAS                 |
| ------------- | --------------------- | ----- | ------------------- |
| 레거시          | 없음                    | 화면 + 로직 | 요청 수신, JSP 실행       |
| MVC 적용, 스프링X | 직접 만든 Servlet         | 화면 출력 | 요청 수신, JSP 클래스 실행   |
| MVC 적용, 스프링O | DispatcherServlet이 호출 | 화면 출력 | 요청 수신, 서블릿 관리, JSP 실행 |

