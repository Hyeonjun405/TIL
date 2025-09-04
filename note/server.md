## 1. Server
### 1. 서버 소프트웨어(Server Software)
  - 가장 넓은 범주
  - 네트워크에서 요청(Request)을 받고 응답(Response)을 내보내는 역할을 하는 프로그램 전체.
  - 웹 서버·WAS 모두 여기에 들어감.

### 2. 네트워크 계층 관점
 - 애플리케이션 서버(Application Server)
   - 좁은 의미에서는 Tomcat 같은 WAS
   - 넓은 의미에서는 클라이언트 요청을 받아 처리하는 모든 서버 소프트웨어를 포함.
 - 미들웨어(Middleware)
   - OS(커널/네트워크 스택)와 애플리케이션(비즈니스 로직) 사이에서 동작하는 계층을 통칭.
   -웹 서버, WAS, 메시지 큐, ESB(Enterprise Service Bus) 등이 다 미들웨어에 속함.

### 3. 웹 인프라 컴포넌트(Web Infrastructure Components)
 - 서비스 제공 관점
 - 인터넷 서비스 아키텍처를 구성하는 단위로 웹 서버, WAS, DBMS 등을 묶어서 부르는 용어.
 - 시스템 설계할 때는 보통 이렇게 묶음.
 
## 2. Web Server / WAS
### 1. 요청 흐름
```
[Client] → [Nginx(Web Server)] → [WAS(Tomcat 등)] → [DB]
```
 - 클라이언트
   - 브라우저, 앱 등 사용자가 사용하는 장치
   - HTTP 요청(웹 페이지, API 등)을 보냄
 - 웹 서버(Web Server)
   - 클라이언트 요청이 하드웨어 서버로 들어오면 가장 먼저 받는 소프트웨어 계층
   - 역할
     - 요청 파싱(HTTP, HTTPS)
     - 정적 파일 반환(HTML, CSS, JS, 이미지)
     - 동적 요청은 WAS로 전달
 - WAS(Web Application Server)
   - 웹 서버가 전달한 요청 처리
   - 동적 컨텐츠 생성, DB 연동, 비즈니스 로직 수행
   - DB 서버 등 뒤쪽 시스템
   - WAS가 필요하면 DB에서 데이터를 조회 후 결과 반환

### 2. 웹 서버 (Web Server)
- 주요 역할
  - 정적 리소스(HTML, CSS, JS, 이미지, 영상 등) 반환
  - 간단한 요청/응답 처리 (예: index.html 보여주기)
- 동작 방식
  - HTTP 프로토콜 기반 요청을 파싱 → 파일 시스템에서 리소스 찾아서 응답
  - 동적 요청은 직접 처리 못 하고, 뒤에 있는 WAS나 다른 애플리케이션 서버로 전달
- 예시

  | 웹 서버                   | 특징                             | 주요 역할 / 사용 사례              |
  | ---------------------- | ------------------------------ | -------------------------- |
  | Nginx              | 이벤트 기반, 고성능, 가벼움               | 정적 파일 제공, 리버스 프록시, 로드 밸런싱  |
  | Apache HTTP Server | 모듈 기반, 오래된 전통, 프로세스/스레드 기반     | PHP 연동, 전통적인 웹 서비스         |
  | IIS (Windows 전용)   | MS 생태계와 통합, GUI 관리 가능          | ASP.NET 호스팅, Windows 서버 환경 |
  | LiteSpeed          | Apache 호환, 상용/오픈소스, 성능 최적화     | 대규모 트래픽 사이트, 호스팅 서비스       |
  | Caddy              | 자동 HTTPS(Let’s Encrypt), 설정 단순 | 소규모 서비스, 빠른 HTTPS 적용 필요    |


### 3. WAS (Web Application Server)
- 주요 역할
  - 동적 컨텐츠 처리 (비즈니스 로직 수행)
  - DB와 연동해서 데이터를 가져오고, 비즈니스 규칙 적용해서 결과 생성
- 동작 방식
  - 클라이언트 요청 → 애플리케이션 코드 실행(JSP, Servlet, Spring, Django, Rails 등)
  - DB와 연동 후 결과를 HTML/JSON/XML 형태로 만들어 반환
- 예시: Tomcat, JBoss/WildFly, WebLogic, WebSphere

  | WAS                 | 특징                               | 주요 역할 / 사용 사례                        |
  | ------------------- | -------------------------------- | ------------------------------------ |
  | Tomcat          | 경량, 서블릿/JSP 실행 가능, Java EE 일부 지원 | 스프링 등 Java 웹 애플리케이션 실행, 소규모\~중규모 서비스 |
  | JBoss / WildFly | 풀 Java EE 지원, 엔터프라이즈급 기능         | 복잡한 비즈니스 로직, 트랜잭션, EJB 등 필요할 때       |
  | WebLogic        | Oracle 상용, 엔터프라이즈용 안정성 높음        | 대기업/금융권, 대규모 시스템 배포                  |
  | WebSphere       | IBM 상용, 안정성과 확장성 강조              | 금융/공공기관, Java EE 기반 대규모 서비스          |
  | GlassFish       | Java EE 공식 참조 구현, 오픈소스           | 실험/개발 환경, Java EE 기능 테스트             |

## 3. note
### 1. 로드밸런싱(Load Balancing)
#### 1. 로드밸런싱
 - 서버에 들어오는 요청이나 트래픽을 여러 장치/서버에 분산하는 것
 - 목적: 과부하 방지, 성능 안정화, 장애 대응, 확장성 확보
#### 2. 사용하는곳
| 환경            | 로드밸런싱 방식                                           |
| ------------- | -------------------------------------------------- |
| 웹 서버          | Nginx, Apache, HAProxy 등을 이용해 WAS 여러 대로 요청 분산      |
| WAS/애플리케이션 서버 | 여러 WAS 인스턴스 간 세션 기반 분산 처리                          |
| DB 서버         | 읽기 전용 Replica 서버로 쿼리 분산 (Read Load Balancing)      |
| 클라우드/가상화      | AWS ELB, GCP Load Balancer, Azure LB 등에서 트래픽 자동 분산 |
| 네트워크 장비       | L4/L7 스위치에서 트래픽 분산, 라우팅                            |

#### 3. 형태(Nginx 예시, 다른 서버에서도 비슷하게 사용함.)
```
[Client1] \
[Client2]  → [Nginx(Web Server/Reverse Proxy + Load Balancer)] → [Tomcat1(WAS)]
[Client3]                                                      → [Tomcat2(WAS)]
                                                               → [Tomcat3(WAS)]
```
### 2. JBOSS
#### 1. JBOSS
 - JBoss = Red Hat에서 만든 Java EE 기반 오픈소스 WAS(Web Application Server)
 - 현재는 WildFly라는 이름으로 개발/배포되고 있음.
 - Java EE(이제는 Jakarta EE) 표준을 거의 대부분 지원해서, 엔터프라이즈급 애플리케이션을 운영할 수 있음.

#### 2. 특징
| 특징         | 설명                                             |
| ---------- | ---------------------------------------------- |
| Java EE 지원 | Servlet, JSP, EJB, JPA, JMS 등 엔터프라이즈 표준 대부분 지원 |
| 오픈소스       | 무료 사용 가능, 소스 수정/확장 가능                          |
| 엔터프라이즈급    | 트랜잭션, 보안, 클러스터링, 메시징 등 대규모 서비스에 적합             |
| WildFly    | JBoss의 최신 버전, 경량화 및 성능 개선                      |
| 관리 콘솔      | 웹 기반 관리 UI 제공, 서버 상태·배포 관리 가능                  |

#### 3. 용도
 - 대규모 Java 웹 애플리케이션 배포
 - 복잡한 비즈니스 로직 처리
 - 클러스터링, 트랜잭션, 메시징 등 엔터프라이즈 기능 필요할 때

#### 4. 톰캣과 JBOSS
| 구분         | Tomcat            | JBoss/WildFly            |
| ---------- | ----------------- | ------------------------ |
| Java EE 지원 | 서블릿/JSP 중심        | 풀 Java EE 지원(EJB, JPA 등) |
| 무게         | 가벼움, 소규모\~중규모 서비스 | 무겁고 기능 많음, 대규모 서비스 적합    |
| 클러스터링      | 제한적               | 내장 지원                    |
| 사용 용도      | 스프링 앱 등           | 복잡한 엔터프라이즈 애플리케이션        |
 ※ 클러스터링 = 여러 서버를 하나의 그룹처럼 묶어서 동작하게 만드는 것

#### 5. 컨테이너 + JBOSS
```
[Client] → [Ingress / Load Balancer] → [POD1(JBoss)]
                                     → [POD2(JBoss)]
                                     → [POD3(JBoss)]
```
- 컨테이너(POD): Docker나 Kubernetes 환경에서 애플리케이션과 필요한 라이브러리, WAS를 한 단위로 패키징
- JBoss 실행: POD 안에서 JBoss/WildFly 서버를 띄워서 스프링, EJB 등 Java 애플리케이션을 실행
- 외부에서는 POD 단위로 접근, 컨테이너 오케스트레이션(Kubernetes 등)이 트래픽 분산/스케일링 담당
- POD 안에 JBoss가 WAS 역할 수행
