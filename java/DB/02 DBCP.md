## 1. DBCP
### 1. DBCP (Database Connection Pool)
 - Apache Commons 프로젝트에서 제공하는 커넥션 풀 라이브러리
 - 톰캣은 내부적으로 commons-dbcp와 commons-pool을 기반으로 JNDI DataSource를 구현함
 - 그래서 server.xml이나 context.xml에 <Resource> 태그로 설정하면 DBCP 풀을 자동으로 사용하게 됨.

### 2. DBCP가 필요한 이유
 - JDBC에서 매번 DriverManager.getConnection() 호출 → TCP 연결 + 인증 = 매우 무거움
 - 요청 많아지면 DB, 애플리케이션 둘 다 병목 발생
 - 커넥션 풀을 쓰면
   - 미리 생성된 커넥션을 재사용
   - 성능 향상 (Latency ↓)
   - 리소스 효율적 사용 (DB 커넥션 개수 제한 관리 가능)

### 3. 동작구조
 - 애플리케이션 시작 시 DBCP가 DB 커넥션 N개 생성
 - 웹 요청이 들어옴 → 풀에서 커넥션 빌려옴
 - 쿼리 실행
 - close() 호출 → 실제 닫는 게 아니라 풀에 반환
 - 풀은 Idle(유휴) / Active(사용 중) 상태의 커넥션을 관리

### 4. 핵심 특징
 - 성능 최적화: Connection 객체 생성/종료 비용 절약
 - 리소스 관리: DB 최대 동시 접속 수를 제어 가능
 - 안정성: 풀에서 커넥션이 터지면 자동으로 복구, 재연결 가능

## 2. 톰캣 대신 DBCP
### 1. 톰캣이 없는 경우 (순수 Java 애플리케이션)
 - 톰캣은 내장적으로 Apache Commons DBCP를 포함하고 있어, 그래서 JNDI로 쉽게 풀을 쓸 수 있음.
 - 그런데 톰캣이 없으면 직접 커넥션 풀 라이브러리를 추가해야 함.
 - 다른 DBCP
    - HikariCP → Spring Boot 기본 풀 (빠르고 가볍다)
    - Apache Commons DBCP → 톰캣 내장 풀
    - C3P0 → 예전부터 쓰이던 풀
    - BoneCP → 지금은 거의 안 씀
 - 즉, 톰캣 대신 애플리케이션 코드에서 DataSource를 직접 만들어 관리해야 함.
 
### 2. 다른 서버를 쓰는 경우
 - Jetty, Undertow 같은 다른 서블릿 컨테이너 → 이 경우도 자체적인 DBCP 기능 없으면 HikariCP 같은 외부 풀 붙여야 함.
 - 애플리케이션 서버(JBoss/WildFly, WebLogic, WebSphere) → 얘네들은 자체 Connection Pool 모듈을 제공함 (톰캣처럼 따로 추가 안 해도 됨).

### 3. 클라우드 환경
 - AWS RDS, GCP Cloud SQL 같은 매니지드 DB → 애플리케이션 쪽에서는 보통 HikariCP 사용
 - 서버리스(FaaS) 환경 (예: AWS Lambda) → 풀을 두기 애매해서, 보통은 RDS Proxy 같은 중간 계층을 둬서 Connection Pool 기능을 대신함

## 3. 메모
### 1. tomcat과 DBCP
#### 1. 초기 톰캣과 DBCP 등장 배경
 - 서블릿/JSP 초창기
   - 2000년대 초반 서블릿/JSP가 막 쓰이기 시작했을 때, 대부분의 웹앱 은 JDBC 직접 연결 (DriverManager.getConnection)만 사용했음.
   - 문제: 매 요청마다 DB 연결 → TCP 연결/인증 비용 → 성능 최악, DB 서버 부담 큼
 - 커넥션 풀 필요
   - 그래서 Apache Commons에서 **DBCP (Database Connection Pool)**를 만들어 톰캣과 연동
   - 톰캣은 오픈소스 WAS이니까, 웹 애플리케이션에서 바로 쓸 수 있도록 DBCP를 내장시킴
 - 목적
   - 웹 개발자 편의: JSP/Servlet만으로도 쉽게 DB 커넥션 풀 사용 가능
   - 성능 개선: Connection 재사용으로 DB 부하 감소
   - 표준화된 설정: context.xml/server.xml에서 <Resource> 태그로 선언만 하면 사용 가능

#### 2. 톰캣이 DBCP를 제공한 이유
 - 당시 HikariCP 같은 경량 고성능 풀은 없었음
 - Commons DBCP는 오픈소스, 톰캣과 호환성 좋음, 설정 단순
 - 톰캣 사용자 대부분이 JSP/Servlet 중심 웹앱 → 빠르게 적용 가능해야 했음

### 3. DBCP 변화 흐름
#### 1. 스프링 없는 시기 (JSP/Servlet + WAS 초기)
 - 환경: 톰캣, JBoss 같은 WAS + JSP/Servlet
 - DB 커넥션 풀 방법: WAS 내장 DBCP 사용 (context.xml, server.xml <Resource> 설정)
 - 특징
    - WAS가 DB 커넥션 풀 제공 → JSP/Servlet에서 JNDI lookup으로 사용
    - 설정은 톰캣/서버 수준에서 관리
    - 애플리케이션 단독 관리 어려움
    - 성능/기능 제한 (DBCP 1.x → lock 문제, 느림)

#### 2. 스프링 레거시 시기 (Spring 2~4)
 - 환경: Spring Framework + JSP/Servlet + WAS
 - DB 커넥션 풀 방법: 스프링 내부에서 DBCP 라이브러리 직접 사용 (BasicDataSource 빈)
 - 특징
    - WAS 내장 풀 대신 스프링에서 DataSource 빈 생성
    - DI(의존성 주입)로 DAO/Service에서 주입받아 사용
    - WAS 변경에도 설정 그대로 사용 가능
    - 트랜잭션 관리 연동 쉬움
    - 성능은 WAS 내장 DBCP와 비슷 (HikariCP 등장 전까지 표준)

#### 3. 스프링 부트 시기 (Spring Boot 1.x~현재)
 - 환경: Spring Boot (내장 톰캣) + Maven/Gradle 프로젝트
 - DB 커넥션 풀 방법: HikariCP 사용 (스프링부트 기본)
 - 특징
   - 톰캣은 HTTP 서버 역할만 담당
   - 커넥션 풀은 애플리케이션 단에서 HikariCP가 관리
   - 고성능, 낮은 레이턴시, 안정적 커넥션 관리
   - application.properties/yaml로 쉽게 설정 가능
   - DBCP는 옵션으로 사용 가능하지만 기본은 HikariCP

### 3. 스프링부트에서 HikariCP를 사용하는 이유
#### 1. 톰캣 내장과 HikariCP의 역할 구분
- 내장 톰캣(Tomcat embedded)
    - Spring Boot에서 제공
    - 웹 서버 역할만 함: HTTP 요청을 받아서 서블릿/컨트롤러로 전달
    - DB 커넥션 풀 기능은 기본적으로 없음
    - 톰캣이 제공하는 DBCP 풀을 붙일 수는 있지만, Spring Boot는 기본적으로 HikariCP를 선호

- HikariCP
    - DB 커넥션 풀만 담당
    - JDBC Connection 객체를 미리 만들어 두고, 필요할 때 빌려주고, 다 쓰면 풀에 반환
    - 성능과 안정성이 뛰어나고, Spring Boot 기본 풀

#### 2. 톰캣 내장 DBCP를 안 쓰고 HikariCP를 쓰는 이유
- 관심사의 차이
    - 톰캣 = HTTP 서버
    - HikariCP = DB 연결 풀
- 성능
    - HikariCP는 초당 처리량과 응답속도에서 DBCP보다 훨씬 빠름
    - 최소한의 동기화(lock)만 사용 → 레이스 컨디션 최소화
- 신뢰성
    - 풀 관리(Idle, Active, timeout, leak detection 등) 기능이 강력
    - 대규모 서비스에서 안정적으로 커넥션 관리 가능