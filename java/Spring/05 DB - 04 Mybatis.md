## 1. mybatis
### 1. mybatis
 - SQL Mapper 프레임워크
 - 직접 작성한 SQL을 매핑해서 Java 객체와 연동
 - JDBC 기반 → 커넥션 풀(HikariCP 등)과 함께 사용
 - 특징: SQL 제어권을 개발자가 직접 갖는다

### 2. 특징
| 항목     | 내용                                  |
| ------ | ----------------------------------- |
| SQL 작성 | 직접 SQL 작성 → 복잡한 쿼리도 자유롭게 사용 가능      |
| 객체 매핑  | ResultMap/자동 매핑으로 SQL 결과 → 객체 변환    |
| 유연성    | ORM보다 자유로운 SQL 제어 가능                |
| 커넥션    | JDBC 기반 → DataSource(HikariCP 등) 사용 |
| 트랜잭션   | 스프링 `@Transactional`과 연동 가능         |

### 3. mybatis 흐름
```
Java 코드
   │
   ▼
Mapper Interface(Java에서 호출할 메서드 정의) / XML(SQL 문과 ResultMap 정의) 
   │
   ▼
SQL 실행
   │
   ▼
ResultMap → Java Object (SQL 결과를 Java 객체로 매핑)
```

## 2. mybatis 사용법
### 1. 유저객체
```
@Data
public class User {
    private Long id;
    private String name;
    private String email;
```
 - 리턴타입은 자유지만, 필요할 경우 객체로도 사용가능함. 

### 2.애너테이션으로 사용
```
@Mapper
public interface UserMapper {

    @Select("SELECT id, name, email FROM users WHERE id = #{id}")
    User findById(@Param("id") Long id);

    @Insert("INSERT INTO users(name, email) VALUES(#{name}, #{email})")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    void insert(User user);

    @Update("UPDATE users SET email = #{email} WHERE id = #{id}")
    void updateEmail(@Param("id") Long id, @Param("email") String email);

    @Delete("DELETE FROM users WHERE id = #{id}")
    void delete(@Param("id") Long id);
}
```
- XML 없이 SQL 작성 가능
- Mapper 인터페이스 중심
- 간단 CRUD나 작은 프로젝트에 적합
- 스프링 트랜잭션, 커넥션 풀과 완전히 통합 가능

### 3. Mapper.xml
#### 1. XML파일 생성
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.mapper.UserMapper">

    <resultMap id="userResultMap" type="com.example.model.User">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="email" column="email"/>
    </resultMap>
    
    <select id="findById" parameterType="long" resultMap="userResultMap">
        SELECT id, name, email
        FROM users
        WHERE id = #{id}
    </select>
~~~~
```
 - resources/mapper/ 폴더에 위치
#### 2. Mapper 인터페이스
```
public interface UserMapper {
    User findById(Long id);
    void insert(User user);
    void updateEmail(Long id, String email);
    void delete(Long id);
}
```
 - 메서드 이름 = XML의 <select>/<insert>/<update>/<delete> id와 동일
 - 파라미터 타입, 반환 타입도 XML과 매핑

#### 3. 매퍼 sacn
```
@SpringBootApplication
@MapperScan("com.example.mapper") // Mapper 인터페이스 스캔
public class Application { }
```

#### 4. JAVA
```
@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    @Transactional
    public void register(User user) {
        userMapper.insert(user);
    }
}
```

## 3. note