# 1. SOLID 원칙
## 1. SOLID 원칙
- 유지보수가 쉽고, 확장 가능하며, 오류가 적은 소프트웨어를 만들기 위한 설계 지침
- SOLID 원칙은 ‘좋은 객체지향 설계의 뼈대’이며, 소프트웨어 품질과 생산성을 높이기 위한 기본 철칙

## 2. SOLID의 시작
| 배경 요소            | 내용                                    |
| ---------------- | ------------------------------------- |
| 복잡한 대규모 소프트웨어 개발 | 유지보수와 확장성의 어려움 증가                     |
| 객체지향 설계의 표준 부재   | 다양한 설계 방식으로 인한 혼란과 비효율성               |
| 변화에 유연한 설계 요구    | 변경이 잦은 현실적 요구사항 대응 필요                 |
| 경험 기반 연구 및 실용화   | Robert C. Martin이 여러 설계 규칙을 체계적으로 정리함 |

## 3. SOLID 원칙의 종류
| 원칙                        | 해결하려는 문제                       | 공학적 설명 (CS 관점)                            |
| ------------------------- | ------------------------------ | ----------------------------------------- |
| **S - 단일 책임 원칙 (SRP)**    | 하나의 클래스가 여러 이유로 바뀌는 것 → 변경에 취약 | 책임 분리 → 응집도↑, 변경 영향도↓                     |
| **O - 개방/폐쇄 원칙 (OCP)**    | 기존 코드를 직접 수정해야만 기능 추가 가능       | 기존 코드는 닫고, 확장만 열어둬서 안정성 확보                |
| **L - 리스코프 치환 원칙 (LSP)**  | 하위 클래스가 부모 역할을 제대로 못함 → 버그     | 다형성이 깨짐 → 계약 기반 설계(contract-based design) |
| **I - 인터페이스 분리 원칙 (ISP)** | 인터페이스가 지나치게 비대함 → 불필요한 구현 강제   | 역할 최소화된 인터페이스로 분리                         |
| **D - 의존 역전 원칙 (DIP)**    | 하위 모듈에 상위 모듈이 직접 의존 → 결합도 ↑    | 추상화 계층에 의존 → DI 컨테이너와 잘 결합됨               |


# 2. 역할과 책임
## 1. 역할(Role)
- 객체가 전체 시스템 안에서 “어떤 일을 맡고 있는가”.
- 다른 객체들과의 관계 속에서 맡은 위치.
- 역할은 객체가 무엇을 할 수 있는지를 정의하는 **계약(Interface)**이나 사용 패턴과 연결됨.
- 역할 중심으로 설계하면 객체 간 협력과 재사용이 쉬워짐.

## 2. 계약
- 객체가 특정 역할(Role)을 수행할 때, 다른 객체와 어떤 방식으로 상호작용할지에 대한 약속
- "이 메서드는 어떤 입력을 받으면 어떤 행동을 해야 하며, 어떤 결과를 반환해야 한다."
- 인터페이스에서 메소드를 정의한 것과 같음

## 3. 책임(Responsibility)
- 객체(또는 클래스)가 “무엇을 해야 하는가”.
- “어떤 기능을 담당하고 어떤 의무를 가지고 있는가”를 의미.
- 클래스 하나가 너무 많은 일을 하지 않도록 역할을 작게 나누는 것.

## 4. 역할(Role)과 책임(Responsibility) 예시
```
// 메일을 보내는 역할 < 시스템상 포지션 >
public interface Notifier { 
    void send(String message); // send() 메서드를 제공해야 한다(계약)
}
```
```
// 각기 구현체는 책임을 짐. < 해당 포지션이 수행하는 실질 업무 > 
public class EmailNotifier implements Notifier {
    @Override
    public void send(String message) {
        // 책임: 이메일 전송
        System.out.println("Send email: " + message);
    }
}

public class SMSNotifier implements Notifier {
    @Override
    public void send(String message) {
        // 책임: 문자 전송
        System.out.println("Send SMS: " + message);
    }
}
```

# 3. SOLID 원칙 종류
## 1. SRP ( 단일 책임 원칙 )
### 1. 기능
- 상황 : 리포트 데이터를 관리하는 기능
- 개선전 : 리포트의 특정 기능을 조정하면 전체 영향을 줄 수 있음.
 ```
  public class Report {
    private String content;

    public Report(String content) { this.content = content; }

    public String getContent() { return content; }

    public void printReport() { System.out.println(content);  // 출력 책임 }

    public void saveToFile(String filename) { // 파일 저장 책임 }

    public void sendEmail(String address) { // 이메일 전송 책임 }
  }
 ```
- 개선 후
 ```
  // 보고서 데이터만 책임
  public class Report {
    private String content;
    public Report(String content) { this.content = content; }
    public String getContent() { return content; }
  }

  // 출력 담당
  public class ReportPrinter {
    public void print(Report report) {
        System.out.println(report.getContent());
    }
  }

  // 저장 담당
  public class ReportSaver {
    public void saveToFile(Report report, String filename) {
        // 파일로 저장하는 코드
    }
  }
 ```
### 2. check 🔍
- 하나의 클래스가 데이터 처리, 출력, 저장, 로깅 등 서로 다른 기능을 동시에 담당하고 있다면, 한 기능이 바뀌면 다른 기능도 영향을 받을 수가 있어서 조정이 어려움.
- 따라서 각 클래스마다 단일 기능으로 만들어, 해당 역할의 책임이 있는 클래스만 건드릴 수 있도록 하는게 유리함.



## 2. OCP (개방-폐쇄 원칙)
### 1. 기능
- 상황 : 알림 기능에 이메일, 문자뿐 아니라 푸시 알림도 추가 필요.
- 개선전 : 새로운 알림 수단 추가 때마다 if-else문을 수정해야 함
  ```
   class Notification {
     void notify(String type, String msg) {
       if (type.equals("email")) { // 이메일 발송
       } else if (type.equals("sms")) { // 문자 발송
       }
     }
    }

  ```
- 개선후
  ```
    interface Notifier { void send(String msg); }

    class EmailNotifier implements Notifier {
       public void send(String msg) { /* 이메일 발송 */ }
    }

    class SMSNotifier implements Notifier {
       public void send(String msg) { /* 문자 발송 */ }
    }

    class PushNotifier implements Notifier {
       public void send(String msg) { /* 푸시 알림 발송 */ }
    }

    class Notification {
        private Notifier notifier;
        Notification(Notifier notifier) { this.notifier = notifier; }
        void notify(String msg) { notifier.send(msg); }
     }
  ```

### 2. check 🔍
- 기능이 추가 또는 변경 될 경우에, 다른 클래스에 영향을 줄이기 위함.
- 추상화를 통해서 공통적인 역할을 정하고, 새로운 기능들을 상속이나 구현을 통해서 내부 구현만 확장하여 사용할 수 있도록 함.
- 기능이 추가되거나 변경될 경우 기존 클래스의 수정을 최소화하고, 다른 클래스나 모듈에 영향을 주지 않도록 설계하는 원칙

## 3. LSP (리스코프 치환 원칙)
### 1. 기능
- 상황 : 조류(Bird) 클래스를 상속받아 타조(Ostrich) 클래스를 만들었는데, 날지 못하는 타조 때문에 문제가 발생
- 개선전 : 부모 클래스가 제공하는 행위를 자식이 수행하지 못함
  ```
  class Bird {
     void fly() { System.out.println("날아간다"); }
  }

  class Ostrich extends Bird {
     void fly() { throw new UnsupportedOperationException("타조는 못 날아"); }
  }

  ```
- 개선후
  ```
  interface Bird {
    void move(); // 날다 -> 이동 으로 변경함(더 추상적인 부분으로)
  }

  class FlyingBird implements Bird {
    public void move() { System.out.println("날아서 이동"); //나는 새는 날아서 }
  }

  class Ostrich implements Bird {
    public void move() { System.out.println("걸어서 이동"); //걷는 새는 걸어서 }
  }
  ```
### 2. check 🔍
- 객체가 부모 클래스를 상속받았을때, 부모의 역할을 온전히 대체하지 못한다면, 신뢰할 수 없는 구조가됨. 그러면 OOP가 가진 유지보수성과 확장성의 장점이 무너지기 때문에, 자식 클래스가 부모 클래스를 대체해도 프로그램의 의미와 기능이 유지되어야함.


## 4. ISP (인터페이스 분리 원칙)
### 1. 기능
- 상황 : 복합기 장치(MultiFunctionDevice)를 구현하는데, 단순 프린터(SimplePrinter)는 스캔과 팩스 기능이 없어서 문제
- 개선전 : 필요 없는 메서드까지 강제 구현해야함.
  ```
  interface MultiFunctionDevice {
    void print();
    void scan();
    void fax();
  }

  class SimplePrinter implements MultiFunctionDevice {
    public void print() { /* 프린터 기능 */ }
    public void scan() { throw new UnsupportedOperationException(); }
    public void fax() { throw new UnsupportedOperationException(); }
  }
  ```
- 개선후 : 필요한 기능만 구현
  ```
  interface Printer { 
    void print(); // 기능 분리
  }  

  interface Scanner {
    void scan(); // 기능 분리
  }

  interface Fax {
    void fax(); // 기능 분리
  }

  class SimplePrinter implements Printer { 
    public void print() { /* 프린터 기능 */ }
  }
  ```
### 2. check 🔍
- 비대하고 다기능적인 인터페이스는 자식 클래스가 구현했을때 불필요한 기능의 구현을 요구하게 될 수 있고, 인터페이스의 역할이 늘어날 경우 기존에 구현했던 클래스도 사용하지 않아도 추가 구현해야하는 상황이 생김.
- 따라서 많은 역할을 주어진 인터페이스 구조보다는 역할을 나누어서 단계를 나누는 방법등을 활용하여 필요한 것만 구현할 수 있도록 함.

## 5. DIP (의존 역전 원칙)
### 1. 기능
- 상황 : UserService가 직접 MySQLDatabase 클래스를 생성해 DB에 저장하는 구조
- 개선전 : 구체 클래스에 직접 의존해 유연성 떨어짐.
  ```
  class MySQLDatabase {
       void save(String data) { /* 저장 */ }
   }

   class UserService {
      private MySQLDatabase db = new MySQLDatabase();
      void saveUser(String user) {
           db.save(user);
       }
    }
  ```
- 개선후 : 인터페이스에 의존해 유연성 상승.
  ```
   interface Database {
      void save(String data);
   }

   class MySQLDatabase implements Database {
       public void save(String data) { /* 저장 구현 */ }
   }

   class UserService {
       private Database db;

       UserService(Database db) {
           this.db = db;
   }

      void saveUser(String user) {
           db.save(user);
      }
  }
  ```
### 2. check 🔍
- 클래스가 직접 다른 클래스의 구체적인 구현을 생성하거나 사용하면,
  그 구현한 클래스가 수정될 경우 함께 변경해야 하는 상황에 놓이게됨.
- 따라서 인터페이스, 추상클래스등에 의존하여 역할에 따른 개발을 하여, 결합도를 낮추는 방향으로 조정해야함.

# 5. SOLID 🔍
- 솔리드원칙은
    - SRP는 클래스를 작게 만들고
    - OCP는 확장을 가능하게 하며
    - LSP는 상속의 안정성을 보장하고
    - ISP는 인터페이스도 SRP처럼 분리하며,
    - DIP는 이 모든 구조를 느슨하게 연결함.
- SOLID 원칙은 변화에 유연하고, 유지보수가 쉬운 소프트웨어 설계 구조를 만들기 위해서 하는 원칙으로 지속적인 변화의 대응을 중심에 두고, 객체지향의 유연성과 재사용성을 끌어올려서 유지보수와 확장에 능동적으로 대처하기 위한 방법
- SOLID는 소프트웨어의 지속 가능한 성장과 변화 대응을 위한 구조를 설계하는 방법
