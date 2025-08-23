## 1. JSP 태그
### 1. JSP태그
 - JSP는 HTML에 Java 코드를 직접 넣을 수 있는데, 이렇게 하면 유지보수가 어렵고 코드가 지저분해짐
 - 그래서 JSP는 태그(tag) 형태로 기능을 캡슐화해서 제공
 - HTML처럼 <태그> 형식으로 작성
 - 내부적으로 Java 코드로 변환하여 실행됨.

### 2. JSP 태그 종류
| 구분                        | 목적 / 역할                    | 특징                                    |
| ------------------------------------- | --- |---------------------------------------|
| 스크립트 요소 (Script Elements) | JSP 안에서 Java 코드 직접 삽입   | 선언, 로직 처리, 값 출력 가능. 유지보수 어려움          |
| 디렉티브 (Directive)          | JSP 페이지 전체 설정, 컴파일 지시   | 페이지 속성 지정, 정적 포함, 커스텀 태그 선언           |
| 액션 태그 (Action Tag)        | JSP에서 객체 생성, 포함, 서블릿 호출 등 | 서블릿과 연동, Bean 생성/속성 제어, 동적 include 가능 |

#### 1. 스크립트 요소 (Script Elements)
| 태그               | 예시                      | 설명                                             |
| ---------------- | ----------------------- | ---------------------------------------------- |
| 선언(Declaration)  | `<%! int count = 0; %>` | JSP 페이지 내에서 전역 변수, 메서드 선언                  |
| 스크립틀릿(Scriptlet) | `<% count++; %>`        | JSP 페이지에서 로직 코드 작성. `service()` 메서드 안에 들어감 |
| 표현식(Expression)  | `<%= count %>`          | 값 출력. JSP 실행 결과가 HTML로 변환되어 출력             |

#### 2. 디렉티브 (Directive)
| 태그      | 예시                                                                  | 설명                                      |
| ------- | ------------------------------------------------------------------- | --------------------------------------- |
| page    | `<%@ page language="java" contentType="text/html; charset=UTF-8"%>` | JSP 페이지 속성 설정 (언어, 인코딩, 에러 페이지 등)       |
| include | `<%@ include file="header.jsp"%>`                                   | JSP 파일을 컴파일 시점에 포함 (static include) |
| taglib  | `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`   | 커스텀 태그 라이브러리 사용 선언                      |

#### 3. 액션 태그 (Action Tag)
| 태그               | 예시                                                                  | 설명                                |
| ---------------- | ------------------------------------------------------------------- | --------------------------------- |
| jsp\:include     | `<jsp:include page="header.jsp" />`                                 | JSP 실행 시점에 다른 JSP 포함 (동적 include) |
| jsp\:useBean     | `<jsp:useBean id="user" class="com.example.User" scope="session"/>` | 자바 객체(bean) 생성 또는 참조              |
| jsp\:setProperty | `<jsp:setProperty name="user" property="name" value="John"/>`       | Bean 속성 설정                        |
| jsp\:getProperty | `<jsp:getProperty name="user" property="name"/>`                    | Bean 속성 출력                        |

## 2. JSP 내장 객체
### 1. 내장객체 
 - JSP는 내부적으로 서블릿으로 변환돼 실행됨.
 - 서블릿에서라면 HttpServletRequest, HttpServletResponse 같은 객체를 직접 받아야 하지만, 
 - JSP에서는 WAS가 미리 생성해서 JSP 코드 안에서 바로 쓸 수 있게 제공함.
 - 즉, JSP 편의를 위해 미리 준비된 Servlet API 기반 객체

### 2. 특징
 - WAS가 요청 시점에 자동 생성
 - JSP 코드에서는 별도 선언 없이 바로 사용 가능
 - Servlet에서 request/response/session/context 등을 직접 쓰는 것과 동일한 객체지만, JSP용 편의 래퍼가 적용돼 있음

### 3. 주요 내장객체
| 내장 객체         | 타입 / 역할               | 설명                               |
| ------------- | --------------------- | -------------------------------- |
| `request`     | `HttpServletRequest`  | 클라이언트 요청 정보(파라미터, 헤더, 쿠키 등)      |
| `response`    | `HttpServletResponse` | 서버가 클라이언트에 보낼 응답 제어(헤더, 리다이렉트 등) |
| `out`         | `JspWriter`           | HTML 등의 출력 스트림                   |
| `session`     | `HttpSession`         | 클라이언트별 세션 상태 유지                  |
| `application` | `ServletContext`      | WAS 전역에서 공유되는 애플리케이션 범위 객체       |
| `page`        | `Object`              | 현재 JSP 페이지 자기 자신(this)           |
| `config`      | `ServletConfig`       | JSP 서블릿 초기화 정보                   |
| `pageContext` | `PageContext`         | JSP 페이지 전체 관리, 내장객체 접근 및 범위 관리   |
| `exception`   | `Throwable`           | JSP 에러 페이지에서 예외 정보 제공            |

## 3. JSTL
### 1. JSTL (JSP Standard Tag Library)
 - JSP에서 자바 코드를 직접 쓰지 않고도 반복, 조건, 포맷, XML 처리 등을 할 수 있도록 제공되는 표준 태그 라이브러리
 - JSP 태그로 Java 로직을 대신하는 도구
 - JSP 내 스크립틀릿(<% ... %>) 최소화 가능 → 유지보수성 ↑
 - HTML과 Java 코드 분리 가능 → View 역할 명확
 - 표준화되어 있어서 어떤 JSP 환경에서도 호환

### 2. 주요 태그 라이브러리
| 라이브러리      | 설명                 | 예시                                                                                   |
| ---------- | ------------------ | ------------------------------------------------------------------------------------ |
| core (`c`) | 조건, 반복, URL 처리     | `<c:if test="${user != null}">Hello</c:if>` `<c:forEach var="item" items="${list}">` |
| fmt        | 날짜/숫자 포맷, 국제화      | `<fmt:formatDate value="${date}" pattern="yyyy-MM-dd"/>`                             |
| sql        | DB 접근 (실무에서는 잘 안씀) | `<sql:query var="result" dataSource="${ds}">SELECT * FROM users</sql:query>`         |
| xml        | XML 처리             | `<x:out select="/book/title"/>`                                                      |
| fn         | 문자열/컬렉션 함수         | `${fn:length(list)}`|
