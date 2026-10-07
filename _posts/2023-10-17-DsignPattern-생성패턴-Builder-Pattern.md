---
title: "[DsignPattern] 생성패턴 - Builder Pattern"
date: 2023-10-17 14:52:16 +0900
categories: ["Language", "Design Pattern"]
tags: ["@builder 빌더", "@builder 원리", "builder pattern", "디자인 패턴 builder", "디자인패턴 빌더", "빌더 패턴"]
tistory_url: "https://aroma-bok.tistory.com/entry/DsignPattern-%EC%83%9D%EC%84%B1%ED%8C%A8%ED%84%B4-Builder-Pattern"
---
### **빌더 (Builder) 패턴**

-   **빌더 패턴 구현의 요점은 메소드 체이닝**
-   **메소드 체이닝으로 엔티티의 데이터를 세팅해 주는 빌더라는 인터페이스를 만들어 준 후 계속 스스로 반환하고,**  
    **마지막에 해당 객체를 반환하는 것이다**
-   **생성자와 깊은 연관이 있다**
-   **동일한 프로세스를 거쳐 다양한 구성의 인스턴스를 만드는 방법**

* * *

#### **빌더 (Builder)  패턴이 필요한 경우**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class TourPlan {

    private String title;
    private int nights;
    private int days;
    private LocalDate startDate;
    private String whereToStay;
    private List<DetailPlan> plans = new ArrayList<>();

    public void addPlan(int day, String plan) {
        this.plans.add(new DetailPlan(day, plan));
    }
    @Override
    public String toString() {
        return "TourPlan{" +
                "title='" + title + '\'' +
                ", nights=" + nights +
                ", days=" + days +
                ", startDate=" + startDate +
                ", whereToStay='" + whereToStay + '\'' +
                ", plans=" + plans +
                '}';
    }
}
--------------------------------------------------------
@Data
@NoArgsConstructor
@AllArgsConstructor
public class DetailPlan {
    int day;
    String plan;
}
```

-   **해당 객체의 데이터를 세팅하고 생성하려면 아래처럼 복잡한 세팅이 필요하다.**

```java
public class BuilderPatternTest {
    @Test
    void BuilderTest() {
        TourPlan tourPlan = new TourPlan();
        tourPlan.setTitle("제주 여행");
        tourPlan.setNights(2); // 2박
        tourPlan.setDays(3); // 3일
        tourPlan.setStartDate(LocalDate.of(2020, 12, 9));
        tourPlan.setWhereToStay("리조트");
        tourPlan.addPlan(0, "체크인 이후 짐풀기");
        tourPlan.addPlan(0, "저녁 식사");
        tourPlan.addPlan(1, "조식 부페에서 식사");
        tourPlan.addPlan(1, "해변가 산책");
        tourPlan.addPlan(1, "점심은 수영장 근처 음식점에서 먹기");
        tourPlan.addPlan(1, "리조트 수영장에서 놀기");
        tourPlan.addPlan(1, "저녁은 BBQ 식당에서 스테이크");
        tourPlan.addPlan(2, "조식 부페에서 식사");
        tourPlan.addPlan(2, "체크아웃");

        System.out.println(tourPlan.toString());
    }
}
```

* * *

### **무엇이 문제인가?**

**1\. Setter로 속성값을 설정하는 방식은 일관된 프로세스를 제공하지 못한다.**  
**→ 함수 호출이 인자만큼 늘어나고, 객체 호출 한 번에 생성할 수 없다.**

**또한, 객체가 immutable 한 객체를 생성할 수 없다. 불완전한 상태로 만들어질 수 있다.**

**(스레드 간 공유 가능한 객체 일관성이 일시적으로 깨질 수 있다.)**

```java
    // Java Bean 패턴 사용
    @Test
    void BuilderTest2() {
        TourPlan dayTrip = new TourPlan();
        dayTrip.setTitle("[당일치기] 부산 여행");
        dayTrip.setStartDate(LocalDate.of(2022, 5, 15));
        dayTrip.addPlan(0, "광안대교");
        dayTrip.addPlan(0, "감천 문화마을");
        dayTrip.addPlan(0, "부산 아쿠아리움");
        dayTrip.addPlan(0, "귀가");

        System.out.println(dayTrip.toString());
    }
```

  
  

**2\. 인스턴스가 가지는 목적에 따라 생성자의 수가 선형적으로 늘어날 수 있다.**  
**→ 예를 들어, 1박 2일 여행과 당일치기 여행은 필요로 하는 속성이 다르므로 새로운 생성자가 정의되어야 한다.**

**아래처럼 인스턴스가 가지는 목적에 따라 필요로 하는 속성이 다르고 이를 대응하기 위해서 생성자를 하나씩 추가하다 보면 코드의 유지보수나 가독성을 고려했을 때 좋은 방법은 아니다.**

```java
// 당일치기 여행의 생성자
public TourPlan(String title, LocalDate startDate, List<DetailPlan> plans){
    this.title = title;
    this.startDate = startDate;
    this.plans = plans;
}

---------------------------------------------------------------------------
// 자유여행 생성자
public TourPlan(String title, LocalDate startDate){
    this.title = title;
    this.startDate = startDate;
    this.plans = plans;
}
```

* * *

#### **빌더 (Builder)  구현**

-   **생성자 패턴과 자바 빈 패턴의 장점을 결함 하여 문제를 해결**
-   **필요한 객체를 직접 생성하는 대신, 먼저 필수 인자들을 생성자에 전부 전달하여 빌더 객체를 만든다.**
-   **선택 인자는 가독성이 좋은 코드로 인자를 넘길 수 있다.**
-   **Setter가 없으므로 객체 일관성을 유지하여 불변 객체로 생성할 수 있다.**

-   **Interface인 Builder 생성**

```java
public interface TourPlanBuilder {
    // 메소드 체이닝 사용 -> 인터페이스에서 정의한 또 다른 기능을 사용하기 위해서
    TourPlanBuilder title(String title);
    TourPlanBuilder nightsAndDays(int nights, int days);
    TourPlanBuilder startDate(LocalDate localDate);
    TourPlanBuilder whereToStay(String whereToStay);
    TourPlanBuilder addPlan(int day, String plan);

    // getPlan을 호출하기 전 까지는 계속 체이닝 사용 (마지막에 검증하기 좋은 위치)
    TourPlan getPlan();
}
```

-   **이를 구현하는 클래스 생성**

```java
public class DefaultTourBuilder implements TourPlanBuilder{
    private String title;
    private int nights;
    private int days;
    private LocalDate startDate;
    private String whereToStay;
    private List<DetailPlan> plans;

    @Override
    public TourPlanBuilder title(String title) {
        this.title = title;
        return this;
    }

    @Override
    public TourPlanBuilder nightsAndDays(int nights, int days) {
        this.nights = nights;
        this.days = days;
        return this;
    }

    @Override
    public TourPlanBuilder startDate(LocalDate startDate) {
        this.startDate = startDate;
        return this;
    }

    @Override
    public TourPlanBuilder whereToStay(String whereToStay) {
        this.whereToStay = whereToStay;
        return this;
    }

    @Override
    public TourPlanBuilder addPlan(int day, String plan) {
        if(this.plans == null) {
            this.plans = new ArrayList<>();
        }
        this.plans.add(new DetailPlan(day, plan));
        return this;
    }

    @Override
    public TourPlan getPlan() {
        return new TourPlan(title, nights, days, startDate, whereToStay, plans);
    }
}
```

-   **Builder를 이용한 객체 생성**

```java
    @Test
    void BuilderTest3() {
        TourPlanBuilder builder1 = new DefaultTourBuilder();
        TourPlanBuilder builder2 = new DefaultTourBuilder();

        TourPlan plan = builder1.title("화성여행")
                .nightsAndDays(2,3)
                .whereToStay("Ground")
                .addPlan(0,"점심식사")
                .addPlan(1, "수영")
                .addPlan(1, "체크아웃")
                .getPlan();

        TourPlan plan2 = builder2.title("제주도")
                .startDate(LocalDate.now())
                .getPlan();

        System.out.println(plan);
        // TourPlan{title='화성여행', nights=2, days=3, startDate=null, whereToStay='Ground', plans=[DetailPlan(day=0, plan=점심식사), DetailPlan(day=1, plan=수영), DetailPlan(day=1, plan=체크아웃)]}
        System.out.println(plan2);
        // TourPlan{title='제주도', nights=0, days=0, startDate=2023-10-17, whereToStay='null', plans=null}
    }
```

* * *

#### **@Builder를 적용한다면?**

**→ @Builder 어노테이션을 사용하면 어떻게 변화되는지 알아보자.**

-   **해당 클래스에 @Builder 어노테이션을 클래스 레벨에 사용하면 모든 요소를 받는 생성자가 자동으로 생성되며  
    이 생성자에 @Builder 어노테이션을 붙인 것과 동일하게 동작한다.**  
    **즉, 클래스 레벨도 결국은 중간 단계를 거쳐 생성자 레벨로 변환되어 동작한다.**

```java
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class TourPlan {

    private String title;
    private int nights;
    private int days;
    private LocalDate startDate;
    private String whereToStay;
    private List<DetailPlan> plans = new ArrayList<>();

    public void addPlan(int day, String plan) {
        this.plans.add(new DetailPlan(day, plan));
    }
    @Override
    public String toString() {
        return "TourPlan{" +
                "title='" + title + '\'' +
                ", nights=" + nights +
                ", days=" + days +
                ", startDate=" + startDate +
                ", whereToStay='" + whereToStay + '\'' +
                ", plans=" + plans +
                '}';
    }
}
```

-   **@Builder를 붙이고 Build를 한다면 아래와 같이 Class 파일이 생성된다.**

```java
public class TourPlan {
    private String title;
    private int nights;
    private int days;
    private LocalDate startDate;
    private String whereToStay;
    private List<DetailPlan> plans = new ArrayList();

	// 생략

    public static class TourPlanBuilder {
        private String title;
        private int nights;
        private int days;
        private LocalDate startDate;
        private String whereToStay;
        private List<DetailPlan> plans;

        TourPlanBuilder() {
        }

        public TourPlanBuilder title(String title) {
            this.title = title;
            return this;
        }

        public TourPlanBuilder nights(int nights) {
            this.nights = nights;
            return this;
        }

        public TourPlanBuilder days(int days) {
            this.days = days;
            return this;
        }

        public TourPlanBuilder startDate(LocalDate startDate) {
            this.startDate = startDate;
            return this;
        }

        public TourPlanBuilder whereToStay(String whereToStay) {
            this.whereToStay = whereToStay;
            return this;
        }

        public TourPlanBuilder plans(List<DetailPlan> plans) {
            this.plans = plans;
            return this;
        }

        public TourPlan build() {
            return new TourPlan(this.title, this.nights, this.days, this.startDate, this.whereToStay, this.plans);
        }

        public String toString() {
            return "TourPlan.TourPlanBuilder(title=" + this.title + ", nights=" + this.nights + ", days=" + this.days + ", startDate=" + this.startDate + ", whereToStay=" + this.whereToStay + ", plans=" + this.plans + ")";
        }
    }
}
```

* * *

#### **빌더 (Builder) 패턴 장점**

-   **필요한 데이터만 설정할 수 있음**
-   **유연성**
-   **가독성**
-   **불변성 확보**
