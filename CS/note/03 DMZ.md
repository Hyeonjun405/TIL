## 1. DMZ
### 1. DMZ
 - DMZ(DeMilitarized Zone)는 네트워크 보안 설계에서 외부 네트워크(인터넷)와 내부 네트워크(사내망) 사이에 두는 중간 완충지대
 - 외부 사용자가 접근해야 하는 서버(웹 서버, 메일 서버, DNS 서버)를 배치
 - 외부 트래픽이 내부망까지 바로 들어오지 못하게 보안 완충 구간 형성
 - 외부에서 DMZ 서버가 해킹당해도 내부망 침투를 어렵게 함

### 2. DMZ의 핵심
 - 내부망을 직접 노출하지 않음
 - 보안사고 발생 시 피해를 최소화
 - 대부분 외부 서비스는 DMZ, 데이터·업무 시스템은 내부망

## 2. 외부용 서버(External-facing Server)
### 1. 외부용서버
 - DMZ(DeMilitarized Zone)나 퍼블릭 서브넷
 - 인터넷에서 직접 접근 가능
 - 웹 서버(Nginx, Apache), API Gateway, Reverse Proxy
 - WAF(Web Application Firewall)나 L4/L7 방화벽 앞단 배치
 - 내부망 DB나 중요 시스템에는 직접 접근 못함

### 2. 웹·API 서비스 계열
 - 웹 서버: Nginx, Apache, IIS (정적 페이지, 웹 애플리케이션 프록시)
 - API 서버: REST API, GraphQL API 등 외부 애플리케이션과 통신
 - Reverse Proxy: 클라이언트 대신 내부 서버와 통신 (로드밸런싱·캐싱 포함)

### 3. 애플리케이션 제공 서버
 - 애플리케이션 서버: Spring Boot, Node.js, Django, Flask 등 외부 사용자가 접속하는 비즈니스 로직 실행
 - 게임 서버: TCP/UDP 기반 실시간 게임 처리 서버
 - 채팅 서버: WebSocket, MQTT, XMPP 기반 실시간 메시징

### 4. 콘텐츠 배포·중계 서버
 - CDN 엣지 서버: 이미지·동영상 등 정적 리소스 캐싱
 - 미디어 스트리밍 서버: HLS, DASH, RTMP 기반 동영상/음성 스트리밍

### 5. 메일·도메인 관련 서버
 - 메일 서버(MTA): SMTP 기반 이메일 송·수신
 - DNS 서버: 도메인→IP 변환, 도메인 레코드 관리
 - SMTP Relay 서버: 외부 메일 전송 전용

### 6. 인증·보안 관련 서버
 - VPN 게이트웨이 서버: 외부 직원이 내부망에 안전하게 접속
 - SSO 서버: 외부 애플리케이션 통합 로그인 지원
 - WAF(Web Application Firewall): 외부 요청 필터링 및 공격 방어

### 7. 특수 목적 서버
 - FTP/SFTP 서버: 외부와 파일 교환
 - IoT 게이트웨이 서버: 외부 기기에서 들어오는 데이터 수집
 - API Gateway: 여러 내부 서비스로 요청 라우팅
 
## 3. 내부용 서버(Internal Server)
### 1. 내부용 서버
 - 프라이빗 서브넷
 - 내부 네트워크 또는 VPN/전용회선에서만 가능
 - DB 서버, 사내 ERP, 사내 API
 - 외부 트래픽 차단, 내부 방화벽(ACL)로만 통신 허용

### 2.  데이터 관리 계열
 - 데이터베이스 서버(DB Server) : MySQL, PostgreSQL, Oracle, MongoDB 등 업무·서비스 데이터 저장 및 조회 처리
 - 데이터 웨어하우스(DWH) : 분석·통계용 대규모 데이터 저장
 - 캐시 서버 : Redis, Memcached — 빠른 조회를 위해 데이터 임시 저장

### 3.백엔드 업무 서버
 - 백오피스 애플리케이션 서버 : 관리자 페이지, 사내 ERP, 인사관리(HRMS), 회계 시스템
 - 업무 API 서버 : 내부 서비스끼리 API 통신 (외부에 노출 안 됨)
 - 배치 서버 : 주기적 데이터 처리·정산·통계 작업 수행

### 4. 파일·문서 관리 계열
 - 파일 서버(NAS, SAN) : 사내 문서·자료 공유
 - 백업 서버 : 주기적 DB·파일 백업 저장
 - 버전 관리 서버 : GitLab, Bitbucket — 내부 개발 소스 관리

### 5. 개발·테스트 환경 서버
 - CI/CD 서버 : Jenkins, GitHub Actions Runner — 빌드·배포 자동화
 - 테스트 서버 : QA·Staging 환경 (외부 접속 차단)
 - 패키지 레지스트리 서버 : Maven, npm, Docker Registry — 내부 패키지 저장소

### 6. 보안·관리 계열
 - 인증 서버 : LDAP, Active Directory — 사내 계정·권한 관리
 - 모니터링 서버 : Prometheus, Zabbix, Grafana — 시스템 상태 감시
 - 로그 서버 : ELK Stack, Loki — 로그 수집·분석

### 7. 메시징·통합 계열
 - 메시지 브로커 : Kafka, RabbitMQ — 내부 서비스 간 비동기 통신
 - ESB(Enterprise Service Bus) : 다양한 내부 시스템 연동·데이터 변환