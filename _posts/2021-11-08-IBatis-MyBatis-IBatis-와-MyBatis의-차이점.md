---
title: "[IBatis / MyBatis] IBatis 와 MyBatis의 차이점?"
date: 2021-11-08 22:16:58 +0900
categories: ["Solution & Tools", "MyBatis"]
tags: ["ibatis", "java", "jdbc", "mapper", "mybatis", "sql", "자바"]
tistory_url: "https://aroma-bok.tistory.com/entry/IBatis-MyBatis-IBatis-%EC%99%80-MyBatis%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
학원에서는 MyBatis를 사용하여 웹을 제작했고, 현재 일하고 있는 곳에서 진행하는 프로젝트는 아이바티스를 사용하고 있다. 문득 어떤 차이점이 있는거지? 라는 생각이들어서 구글링을 통해 알아보았다.

* * *

일단 결론은, **IBatis는 MyBatis의 구버전**이다 라는 것을 알게되었다.

Apache project팀에서 google code팀으로 이동하면서 명칭이 변경되었다고 한다.

그렇다면 MyBatis에 대해서 조금 더 정리를 해봐야겠다.

* * *

**MyBatis : Java의 관계형 데이터베이스(RDBMS) 프로그래밍을 좀 더 쉽게 할 수 있도록 도와주는 개발 프레임워크**

**JDBC : (Java DataBase Connectivity) Java에서 데이터베이스에 접속할 수 있도록 하는 Java API, Java언어로 데이터 베이스 프로그래밍을 하기위한 라이브러리.**

****※** MyBatis vs JDBC 차이점 : MyBatis는 SQL문이 어플리케이션 소스 코드로부터 분리된다. 또한 JDBC를 통해 수동으로 세팅한 파라미터와 결과 매핑을 대신해주어 JDBC로 처리하는 작업보다 간편하게 작업할 수 있어서 코드량이 줄어 생산성을 높여준다.**

**JDBC**

```java
package com.address.web.dao.jdbc;
 
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;
 
import com.address.web.dao.NoticeDao;
import com.address.web.entity.Notice;
import com.address.web.entity.NoticeView;
 
//@Repository
public class JdbcNoticeDao implements NoticeDao {
 
   @Override
   public List<NoticeView> getList() throws ClassNotFoundException, SQLException {
      int page = 1;
      List<NoticeView> list = new ArrayList<>();
      int index = 0;
      
      String sql = "SELECT * FROM Notice ORDER BY regdate DESC LIMIT 10 OFFSET ?";    
      String url = "jdbc:mysql://dev.notead.com:0000/address?useSSL=false&useUnicode=true&characterEncoding=utf8&serverTimezone=UTC";
 
      Class.forName("com.mysql.cj.jdbc.Driver");
      Connection con = DriverManager.getConnection(url, "address", "123");         
      PreparedStatement st = con.prepareStatement(sql);
      st.setInt(1, (page-1)*10); 
      
      ResultSet rs = st.executeQuery();
            
      while (rs.next()) {
         NoticeView noticeView = new NoticeView();
         noticeView.setId(rs.getInt("ID"));
         noticeView.setTitle(rs.getString("TITLE"));
         noticeView.setWriterId(rs.getString("writerId"));
         noticeView.setRegdate(rs.getDate("REGDATE"));
         noticeView.setHit(rs.getInt("HIT"));
         noticeView.setFiles(rs.getString("FILES"));
         noticeView.setPub(rs.getBoolean("PUB"));
         
         list.add(noticeView);      
      }
 
      rs.close();
      st.close();
      con.close();
      
      return list;
   }
 
   @Override
   public Notice get(int id) {
      // TODO Auto-generated method stub
      return null;
   }
 
   @Override
   public int insert(Notice notice) {
      // TODO Auto-generated method stub
      return 0;
   }
 
   @Override
   public int update(Notice notice) {
      // TODO Auto-generated method stub
      return 0;
   }
 
   @Override
   public int delete(int id) {
      // TODO Auto-generated method stub
      return 0;
   }
 
}

출처: https://hyoni-k.tistory.com/70 [Record *]
```

**MyBatis**

```java
package com.address.web.dao;
 
import java.sql.SQLException;
import java.util.List;
 
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
 
import com.address.web.entity.Notice;
import com.address.web.entity.NoticeView;
 
@Mapper     
public interface NoticeDao {
    
    @Select("SELECT * FROM Notice WHERE ${field} LIKE '%${query}%' ORDER BY regdate DESC LIMIT 10")
    List<NoticeView> getList(int page, String query, String field) throws ClassNotFoundException, SQLException;
    
    @Select("SELECT * FROM Notice WHERE id= #{id}")
    Notice get(int id);
    int insert(Notice notice);
    int update(Notice notice);
    int delete(int id);

출처: https://hyoni-k.tistory.com/70 [Record *]
```

**Ibatis vs MyBatis 차이점**

<table style="border-collapse: collapse; width: 100%; height: 301px;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr style="height: 20px;"><td style="width: 36.279%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';"><b>IBatis</b></span></td><td style="width: 38.721%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';"><b>MyBatis</b></span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';"><b>비고</b></span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; height: 20px; text-align: center;"><span style="font-family: 'Noto Serif KR';">JDK1.4 이상</span></td><td style="width: 38.721%; height: 20px; text-align: center;"><span style="font-family: 'Noto Serif KR';">JDK 1.5 이상</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">버전</span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; height: 20px; text-align: center;"><span style="font-family: 'Noto Serif KR';">com.ibatis.*&nbsp;</span></td><td style="width: 38.721%; height: 20px; text-align: center;"><span style="font-family: 'Noto Serif KR';">org.apache.ibatis.*&nbsp;</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">패키지구조</span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; height: 20px; text-align: center;"><span style="font-family: 'Noto Serif KR';">parameterMap, parameterClass&nbsp;</span></td><td style="width: 38.721%; height: 20px; text-align: center;"><span style="font-family: 'Noto Serif KR';">parameterType</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">SqlMap.xml에서 변경사항</span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">SqlMapConfig</span></td><td style="width: 38.721%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">Configration</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">명칭 변경</span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">sqlMap</span></td><td style="width: 38.721%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">Mapper</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">네이스페임스 변경</span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">resultClass</span></td><td style="width: 38.721%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">resultType</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">명칭 변경</span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';"><span style="color: #000000;">rowHandler</span></span></td><td style="width: 38.721%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">resultHandler</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';"><span style="color: #000000;">명칭 변경</span></span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">resultHandler</span></td><td style="width: 38.721%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">SqlSessionFactory</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">명칭 변경</span></td></tr><tr style="height: 20px;"><td style="width: 36.279%; text-align: center; height: 20px;"><span style="color: #000000; font-family: 'Noto Serif KR';">#str#</span></td><td style="width: 38.721%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">#{str}</span></td><td style="width: 25%; text-align: center; height: 20px;"><span style="font-family: 'Noto Serif KR';">구문 변경</span></td></tr><tr style="height: 21px;"><td style="width: 36.279%; text-align: center; height: 21px;"><span style="color: #000000; font-family: 'Noto Serif KR';">$str$</span></td><td style="width: 38.721%; text-align: center; height: 21px;"><span style="font-family: 'Noto Serif KR';">${str}</span></td><td style="width: 25%; text-align: center; height: 21px;"><span style="font-family: 'Noto Serif KR';">구문 변경</span></td></tr></tbody></table>

**MyBatis 구조**

![](/assets/img/posts/12/1.png)

*출처 : https://sjh836.tistory.com/127*

-   Mybatis-config.xml은 MyBatis의 메인 환경설정 파일로서, 어떤 DBMS와 접속을 할지, 어떤 맵퍼 파일들이 있는지 알 수 있음
-   MaBatis는 매퍼에 있는 각 SQL 명령어들을 Map에 담아서 저장하고 관리
