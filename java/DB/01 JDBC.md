## 1. JDBC
### 1. JDBC
 - 자바에서 DB에 연결하고 SQL을 실행할 수 있도록 정의된 표준 API
 - 즉, 자바 애플리케이션 ↔ 데이터베이스 간의 통신을 담당하는 표준 인터페이스
 - DB 벤더(Oracle, MySQL, PostgreSQL 등)가 자기 DB용 JDBC 드라이버를 제공해야 사용 가능

### 2. JDBC 기능
| 기능        | 설명                                          | 관련 클래스/인터페이스                                          |
|-----------| ------------------------------------------- | ----------------------------------------------------- |
| DB 연결     | DB와 연결을 맺고 세션을 유지                           | `DriverManager`, `Connection`                         |
| SQL 실행    | SQL 쿼리를 작성하고 실행 (DML, DDL 등)                | `Statement`, `PreparedStatement`, `CallableStatement` |
| 결과 조회     | SELECT 실행 결과(테이블 형태)를 자바에서 순회하며 꺼내기         | `ResultSet`                                           |
| 파라미터 바인딩  | SQL 안의 `?` 위치에 안전하게 값 전달 (SQL Injection 방지) | `PreparedStatement`                                   |
| 트랜잭션 관리   | `commit()`, `rollback()`을 통해 DB 작업 단위 제어    | `Connection`                                          |
| 예외 처리     | SQL 실행 중 발생한 오류를 예외로 전달                     | `SQLException`                                        |
| DB 벤더 추상화 | DB 벤더가 달라도 동일한 JDBC API로 접근 가능              | JDBC Driver (ex: `mysql-connector-j`, `ojdbc`)        |

### 3. JDBC 구성요소
 - DriverManager: DB 드라이버를 관리하고 Connection 제공
 - Connection: DB와의 세션 (트랜잭션 단위 관리)
 - Statement / PreparedStatement: SQL 실행 객체
 - ResultSet: SELECT 실행 결과 집합

### 4. JDBC 흐름
```
import java.sql.*;

public class JdbcExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/testdb";
        String user = "testuser";
        String password = "1234";

        try {
            // 1. 드라이버 로드 (최근엔 생략 가능)
            Class.forName("com.mysql.cj.jdbc.Driver");

            // 2. DB 연결
            Connection conn = DriverManager.getConnection(url, user, password);

            // 3. SQL 준비 및 실행
            String sql = "SELECT id, name FROM users WHERE id = ?";
            PreparedStatement pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1, 1); // 첫 번째 ?에 1 대입

            // 4. 결과 받기
            ResultSet rs = pstmt.executeQuery();
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println(id + " : " + name);
            }

            // 5. 리소스 해제
            rs.close();
            pstmt.close();
            conn.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 5. JDBC의 한계
- 반복되는 코드가 많음: Connection 열고 닫기, try-catch, 자원 해제
- SQL 직접 관리: SQL 문자열이 코드 안에 그대로 박혀 있음
- DB 벤더별 문법 차이: SQL 호환성 문제 존재

## 2. DBMS
### 1. DBMS
 - 데이터베이스를 생성, 관리, 제어하는 소프트웨어
 - 데이터를 효율적으로 저장, 조회, 수정, 삭제할 수 있도록 지원
 - 트랜잭션 관리, 동시성 제어, 보안, 백업 기능 등 포함

### 2. DBMS
#### 1. 관계형 DBMS (RDBMS)
 - 데이터가 **테이블 형태(행과 열)**로 구성됨
 - SQL을 사용하여 데이터 처리
 - 특징
   - 데이터 무결성 보장 (제약조건, 키, 외래키)
   - 트랜잭션 지원 (ACID)
 - 종류

   | 제품 | 특징 |
   |------|------|
   | Oracle DB | 기업용, 고가, 안정성 높음 |
   | MySQL | 오픈소스, 웹 서비스 많이 사용 |
   | PostgreSQL | 오픈소스, 표준 준수, 기능 강력 |
   | SQL Server | MS 제품, Windows 친화적 |
   | MariaDB | MySQL 기반, 오픈소스 |

#### 2. NoSQL DB
 - 테이블 구조가 아닌 다양한 구조(JSON, Key-Value, Graph 등)
 - SQL 대신 자체 쿼리 언어나 API 사용
 - 특징
    - 스키마 유연, 수평 확장 용이
    - 대용량, 실시간 데이터 처리에 적합
 - 종류

   | 종류                               | 저장 방식                         | 특징                                    |
   | -------------------------------- | ----------------------------- | ------------------------------------- |
   | Key-Value (Redis, Riak)          | Key-Value 형태, 디스크 또는 인메모리+디스크 | Redis는 기본 인메모리, 옵션으로 지속화(RDB, AOF) 가능 |
   | Document (MongoDB, CouchDB)      | JSON/ BSON 문서 형태, 디스크 저장      | 각 문서가 독립적으로 저장                        |
   | Column-Family (Cassandra, HBase) | 컬럼 단위 분산 저장                   | 대용량 분산 처리에 적합                         |
   | Graph (Neo4j)                    | 노드와 관계 그래프 형태                 | 디스크에 노드와 관계 구조를 저장                    |

#### 3. NewSQL / In-Memory DB
 - 관계형 DBMS + NoSQL 확장 특성
 - 특징
   - ACID 트랜잭션 유지 + 대규모 분산 처리
 - 대표 제품: VoltDB, MemSQL, SAP HANA