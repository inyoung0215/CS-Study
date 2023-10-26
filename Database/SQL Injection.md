# SQL Injection

## 개념
보안상의 취약점을 이용하여, 임의의 SQL 문을 주입하고 실행되게 하여 데이터베이스가 비정상적인 동작을 하도록 조작하는 행위이다.
Injection(인젝션)은 OWASP Top10(10대 웹 애플리케이션의 취약점) 중 첫 번째에 속해 있으며, 공격이 비교적 쉬운 편이고 공격에 성공할 경우 큰 피해를 입힐 수 있는 공격이다.

## 공격 종류
SQL Injection은 데이터베이스에 접근하는 방법이나 공격 기법에 따라 일반적으로 아래와 같은 유형으로 분류할 수 있다.

### 1. 오류(Error) 기반 SQL Injection
![image](https://github.com/inyoung0215/CS-Study/assets/65496092/947f6553-c4ab-4375-9c66-f7c9c97e3c5e)

잘못된 문법이나 자료형 불일치 등에 의해 웹 브라우저에 표시되는 데이터베이스 오류를 기반으로 수행되는 공격 기법이다.

공격자는 의도적인 오류를 유발시키고 해당 오류 정보를 바탕으로 데이터베이스명, 테이블, 컬럼 정보 등을 파악할 수 있게 된다.

### 2. UNION 기반 SQL Injection
![image](https://github.com/inyoung0215/CS-Study/assets/65496092/f2d4ea76-778b-4347-9e79-ba5d336a7029)

공격자가 의도한 SQL 쿼리를 UNION 연산자를 이용하여 기존 SQL 쿼리에 덧붙이고 기존 SQL을 무효화시켜 원하는 내부 데이터를 절취할 수 있는 공격 기법이다.

  ```text
  SQL 에서 Union 은 두 개의 쿼리문에 대한 결과를 통합해서 하나의 테이블로 보여주게 하는 키워드 
  Union Injection을 성공하기 위해서는 Union 하는 두 테이블의 컬럼 수가 같아야 하고, 데이터 형이 같아야 한다. 
  ```
### 3. Blind SQL Injection
웹 브라우저 화면상에 데이터베이스 오류 정보나 데이터가 직접적으로 노출되지 않을 때 이용되는 기법으로 

공격자가 육안으로 확인할 수 있는 데이터베이스 오류나 데이터가 없다는 점에 착안되어 Blind SQL Injection 혹은 추론 기반 SQL Injection이라고 불린다. 

공격자는 공격이 성공했는지 판단하기 위해 미세한 서버의 응답과 동작 방식까지 관찰하고 공격 성공의 단서로서 활용한다. 

- Boolean 기반

  ![image](https://github.com/inyoung0215/CS-Study/assets/65496092/b63a7229-27ed-4cb1-ad3d-bd292f12ee7f)

  - SQL 쿼리의 결과가 참 또는 거짓이냐에 따라 웹 애플리케이션의 응답이 다른 경우 사용된다. 
  - 대부분 참/거짓 여부에 따라 응답 내용에 차이가 있거나 응답 상태코드가 500 또는 404로 다르다던지 등 쉽게 식별할 수 있는 차이가 있기 때문에 위협받을 수 있다.
- Time 기반

  ![image](https://github.com/inyoung0215/CS-Study/assets/65496092/19870cb1-8805-4a70-b6a3-445dbe7026c6)

  - SQL 쿼리의 결과가 참 또는 거짓이냐에 따라 서버의 응답 시간을 제어할 수 있을 때 사용된다. 
  - MySQL의 Sleep(5000);와 같이 밀리초의 단위의 시간 동안 대기하는 SQL 명령을 사용해 공격자가 지정한 시간만큼 응답이 지연된다면 참으로 판단한다.
  
## 대응 방안 
### 1. 입력 값에 대한 검증
블랙리스트 & 화이트 리스트 기반 필터링을 수행한다.

블랙리스트는 SQL 쿼리의 구조를 변경하는 특수문자, SQL 예약어 등을 미리 등록해놓고 입력을 제한하는 방식이다.

해당 문자가 입력되었을 때 에러 메시지를 반환하거나, 미리 준비한 문자열로 치환하는 방법이 있다.

```
(특수문자) ' , " , = , & , | , ! , ( , ) , { , } , $ , % , @ , #, -- 등
(예 약 어) UNION, GROUP BY, IF, COLUMN, END, INSTANCE 등
(함 수 명) DATABASE(), CONCAT(), COUNT(), LOWER() 등
```

화이트 리스트 방식은 허용하는 문자열을 미리 등록해놓고 리스트 이외의 문자열이 입력되었을 때 차단하는 방식이다. 

사이트 기능에 따라 하나로 정의된 사전 목록을 사용할 수 없기 때문에 패턴화, 정규화한 문자열을 사용하는 것을 권장한다.

### 2. Prepared Statement(매개변수화된 쿼리) 구문 사용
Prepared Statement 구문을 사용하게 되면, 사용자의 입력 값을 데이터베이스의 파라미터로 넣기 전의 쿼리를 DBMS가 미리 컴파일 하여 대기하기 때문에 

그 후 사용자의 입력 값에 대해서는 쿼리가 아닌 단순 문자열로 인식하여 전체 쿼리문이 공격자의 의도대로 작동하지 않는다.

```java
// SQL Injection에 취약한 코드
String query = "SELECT * FROM USERS WHERE name = '" + loginName + "' AND password = '"+loginPassword="'";
// SQL Injection 방어 코드
String prepareStatement = "SELECT * FROM USERS WHERE name = ? AND password =?;
```

SQL 쿼리를 보면 데이터가 입력될 곳은 물음표(?)로 표기되어있다. 이를 Placeholder라고 한다. 

이 SQL 쿼리는 데이터베이스에 전송되고, 추후 사용자로부터 전달받은 데이터를 매개변수를 통해 전달(바인딩)하여 SQL 쿼리를 실행한다.

### 3. Error Message 노출 금지
공격자가 SQL Injection을 수행하기 위해서는 데이터베이스의 정보(테이블명, 컬럼명 등)가 필요하다. 

보통 데이터베이스 에러 발생 시 에러가 발생한 쿼리문과 함께 에러에 관한 내용을 반환한다. 

따라서, DB 오류 메시지는 그대로 클라이언트에게 노출시켜서는 안된다. 

### 4. 최소 권한 & 최소 기능 사용 
간혹 웹 애플리케이션이 사용하는 데이터베이스 계정이 데이터베이스 관리자 계정이거나 관리자급의 권한을 보유하고 있는 경우가 있다. 

웹 애플리케이션 서비스에 필요한 최소한의 권한만을 보유한 별도의 계정을 사용하여 관리자 계정과 분리하여 운영해야한다.

이외에도 웹 방화벽 사용, 주기적 로그 점검, 취약점 점검 등을 적용할 수 있다.

# ✅ JPA는 SQL Injection에 대응하고 있을까?
Spring Data JPA에서 제공하는 API(save(), saveAll() 등의)를 활용하게 된다면 SQL Injection에 대한 고민은 거의 하지 않아도 된다.

JPA 의 ORM 인 Hibernate는 Default로 Prepared Statment 를 사용한다.

> Reference <br></br>
> [SQL 인젝션 기초](https://www.bugbountyclub.com/pentestgym/view/52) <br></br>
> [SQL Injection 이란? (SQL 삽입 공격)](https://noirstar.tistory.com/264) <br></br>
> [[웹해킹] SQL Injection 총정리](https://gomguk.tistory.com/118) <br></br>