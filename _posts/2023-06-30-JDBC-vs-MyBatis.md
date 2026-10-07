---
title: "JDBC vs MyBatis"
date: 2023-06-30 13:05:56 +0900
categories: ["Solution & Tools", "MyBatis"]
tags: ["jdbc", "jdbc vs mybatis 차이점", "mybatis"]
tistory_url: "https://aroma-bok.tistory.com/entry/JDBC-vs-MyBatis"
---
### **JDBC vs MyBatis**

#### **JDBC** 

**JDBC (Java Database Connectivity)는 자바에서 DB에 접속할 수 있도록 제공되는 API로서 SQL에 접근한다.**

**JDBC 한 파일에서는 SQL 및 DB 연결, Java 언어가 모두 존재하기에 재사용성이 좋지 않음.**

```sql
import java.sql.*;

public class JdbcExample {
    public static void main(String[] args) {
        // 데이터베이스 연결 정보
        String url = "jdbc:oracle:thin:@localhost:1521:xe";
        String username = "your_username";
        String password = "your_password";
        
        // JDBC 관련 객체 선언
        Connection conn = null;
        Statement stmt = null;
        
        try {
            // JDBC 드라이버 로드
            Class.forName("oracle.jdbc.driver.OracleDriver");
            
            // 데이터베이스 연결
            conn = DriverManager.getConnection(url, username, password);
            
            // 쿼리 실행을 위한 Statement 객체 생성
            stmt = conn.createStatement();
            
            // 쿼리 실행 예시: SELECT 문
            String selectQuery = "SELECT * FROM your_table";
            ResultSet rs = stmt.executeQuery(selectQuery);
            
            // 결과 처리
            while (rs.next()) {
                // 결과 값 가져오기
                int id = rs.getInt("id");
                String name = rs.getString("name");
                // 가져온 결과 처리
                System.out.println("ID: " + id + ", Name: " + name);
            }
            
            // 쿼리 실행 예시: UPDATE 문
            String updateQuery = "UPDATE your_table SET name = 'New Name' WHERE id = 1";
            int rowsAffected = stmt.executeUpdate(updateQuery);
            
            // 업데이트 결과 처리
            System.out.println("Rows affected: " + rowsAffected);
            
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 자원 해제
            try {
                if (stmt != null)
                    stmt.close();
                if (conn != null)
                    conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

* * *

#### **MyBatis**

**MyBatis는 매핑을 지원하는 퍼시스턴스 프레임워크(Persistence Framework) 중 하나이다.**

**(프레임워크가 아닌 라이브러리로 보기도 한다.)**

**MyBatis는 SQL문이 애플리케이션 소스 코드로부터 분리된다. (**XML 파일로 따로 관리)****

**또한 JDBC를 통해 수동으로 세팅한 파라미터와 결과 매핑을 대신해 주어 JDBC로 처리하는 작업보다**

**더 간편하게 작업할 수 있으며, 코드량이 줄어 생산성을 높여준다.**

**( **JDBC에서 사용해야 하는 Connection, Statement 등을 MyBatis가 직접 관리해서 코드를 줄여준다.)****

**MyBatis는 JDBC로 처리하는 상당 부분의 코드와 파라미터 설정 및 결과 매핑을 대신해 준다.**

```sql
//Maven 프로젝트의 경우 pom.xml 파일에 다음 종속성을 추가
<dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
    <dependency>
        <groupId>com.oracle.database.jdbc</groupId>
        <artifactId>ojdbc8</artifactId>
        <version>19.8.0.0</version>
    </dependency>
</dependencies>
```

```sql
// MyBatis 설정 파일(mybatis-config.xml)을 생성합니다. 아래는 기본 설정
<!-- mybatis-config.xml -->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="oracle.jdbc.driver.OracleDriver"/>
                <property name="url" value="jdbc:oracle:thin:@localhost:1521:xe"/>
                <property name="username" value="your_username"/>
                <property name="password" value="your_password"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!-- 매퍼 파일 위치 지정 -->
        <mapper resource="com/example/mapper/YourMapper.xml"/>
    </mappers>
</configuration>
```

```sql
// MyBatis 매퍼 파일(YourMapper.xml)을 생성
<!-- YourMapper.xml -->
<mapper namespace="com.example.mapper.YourMapper">

    <select id="selectAll" resultType="com.example.model.YourModel">
        SELECT * FROM your_table
    </select>
    
    <update id="updateName" parameterType="com.example.model.YourModel">
        UPDATE your_table SET name = #{name} WHERE id = #{id}
    </update>

</mapper>
```

```sql
// Java 클래스에서 MyBatis를 사용하여 데이터베이스 연동
import com.example.model.YourModel;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.util.List;

public class MyBatisExample {
    public static void main(String[] args) {
        // MyBatis 설정 파일 경로
        String configPath = "mybatis-config.xml";

        // MyBatis 설정 파일을 읽어 SqlSessionFactory 생성
        InputStream inputStream = MyBatisExample.class.getClassLoader().getResourceAsStream(configPath);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

        // SqlSessionFactory로부터 SqlSession 생성
        try (SqlSession session = sqlSessionFactory.openSession()) {
            // 매퍼 인터페이스 생성
            YourMapper mapper = session.getMapper(YourMapper.class);

            // SELECT 문 실행 예시
            List<YourModel> resultList = mapper.selectAll();
            for (YourModel model : resultList) {
                System.out.println("ID: " + model.getId() + ", Name: " + model.getName());
            }

            // UPDATE 문 실행 예시
            YourModel updateModel = new YourModel();
            updateModel.setId(1);
            updateModel.setName("New Name");
            int rowsAffected = mapper.updateName(updateModel);
            System.out.println("Rows affected: " + rowsAffected);

            // 변경사항 커밋
            session.commit();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

* * *

### **차이점**

**작업 수준**

-   **JDBC: JDBC는 데이터베이스와의 저수준 상호작용을 제공합니다. 개발자는 SQL 쿼리를 직접 작성하고, PreparedStatement 및 ResultSet과 같은 JDBC 클래스를 사용하여 쿼리 실행 및 결과 처리를 수행해야 합니다.**
-   **MyBatis: MyBatis는 개발자가 SQL 쿼리를 작성하고 실행하는 것을 도와주는 ORM 프레임워크입니다. 개발자는 XML 또는 어노테이션을 사용하여 SQL 쿼리와 자바 메서드를 매핑하고, MyBatis가 제공하는 기능을 사용하여 데이터베이스와 상호작용할 수 있습니다.**

**SQL 작성**

-   **JDBC: JDBC를 사용하는 경우 개발자는 SQL 문장을 직접 작성해야 합니다. SQL 쿼리의 구문과 파라미터 바인딩 등을 개발자가 명시적으로 처리해야 합니다.**
-   **MyBatis: MyBatis는 SQL 매핑을 통해 개발자가 SQL을 작성하는 부분을 추상화합니다. 개발자는 XML 또는 어노테이션을 사용하여 SQL 문장을 정의하고, MyBatis의 동적 쿼리 기능을 사용하여 조건부 쿼리를 처리할 수 있습니다.**

**객체 매핑**

-   **JDBC: JDBC는 개발자가 ResultSet을 사용하여 데이터베이스 결과를 처리하고, 개발자가 직접 데이터베이스 결과를 자바 객체로 매핑해야 합니다.**
-   **MyBatis: MyBatis는 개발자가 자바 객체와 데이터베이스 테이블 간의 매핑을 처리합니다. 개발자는 결과 매핑을 정의하고, MyBatis는 SQL 쿼리 결과를 자동으로 자바 객체로 매핑하여 반환합니다.**

**트랜잭션 관리**

-   **JDBC: JDBC에서는 개발자가 트랜잭션을 명시적으로 시작, 커밋 및 롤백해야 합니다. Connection 객체를 사용하여 트랜잭션을 제어합니다.**
-   **MyBatis: MyBatis는 트랜잭션 관리를 위한 편리한 기능을 제공합니다. 개발자는 설정 파일에서 트랜잭션 관리자를 설정하고, MyBatis가 트랜잭션 커밋 또는 롤백을 자동으로 처리하도록 할 수 있습니다.**

**요약하면, JDBC는 저수준의 데이터베이스 상호작용을 위한 API이며, 개발자가 SQL 쿼리와 데이터베이스 결과 처리를 직접 다루어야 합니다. 반면에 MyBatis는 SQL 쿼리와 자바 객체 간의 매핑을 추상화하고, 개발자가 보다 객체 지향적인 방식으로 데이터베이스와 상호작용할 수 있도록 도와주는 ORM 프레임워크입니다. MyBatis는 SQL 매핑, 동적 쿼리, 객체 매핑, 트랜잭션 관리 등의 기능을 제공합니다.**
