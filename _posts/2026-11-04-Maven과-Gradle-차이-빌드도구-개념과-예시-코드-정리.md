---
title: "Maven과 Gradle 차이 - 빌드도구 개념과 예시 코드 정리"
date: 2026-11-04 10:30:00 +0900
categories: ["Framework & Library", "Spring Boot"]
tags: ["maven", "gradle", "build-tool", "pom-xml", "build-gradle", "dependency-management", "spring-boot", "egovframe", "mvnw", "gradlew"]
tistory_url: "https://aroma-bok.tistory.com/entry/Maven%EA%B3%BC-Gradle-%EC%B0%A8%EC%9D%B4-%EB%B9%8C%EB%93%9C%EB%8F%84%EA%B5%AC-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%98%88%EC%8B%9C-%EC%BD%94%EB%93%9C-%EC%A0%95%EB%A6%AC"
---
### 들어가며

전자정부 표준프레임워크 템플릿 두 개를 받아서 돌려 보다가 이상한 점이 있었다. 모놀리식 템플릿(`egovframe-template-simple-backend`)은 `pom.xml`이 있는 **Maven** 프로젝트였고, MSA 템플릿(`egovframe-msa-edu`)은 `gradlew`로 실행하는 **Gradle** 프로젝트였다. "모놀리식은 Maven, MSA는 Gradle인가?"라는 의문이 들어서 두 빌드도구를 한 번에 정리했다.

### 1. 먼저 결론

- **아키텍처와 빌드도구는 무관하다.** 모놀리식을 Gradle로 빌드해도 되고, MSA를 Maven으로 빌드해도 된다. 템플릿을 만든 쪽의 선택일 뿐이다.
- 두 도구가 하는 일은 같다. **라이브러리를 내려받고, 컴파일·테스트·패키징을 자동으로 해 준다.**
- **Maven**은 규칙이 고정된 선언형(XML)이고, **Gradle**은 설정을 코드로 쓰는 유연한 방식이다.
- 전자정부프레임워크는 **Maven 기준**으로 만들어져 있어서, 표준을 따르려면 Maven이 자연스럽다.

### 2. 빌드도구란

소스코드를 실행 가능한 결과물(jar, war)로 만드는 과정을 자동화하는 도구다. 크게 세 가지를 한다.

1. **의존성 관리**: 필요한 라이브러리를 저장소에서 내려받는다. (예: `spring-boot-starter-web`)
2. **빌드 수행**: 컴파일 → 테스트 → 패키징 순서로 진행한다.
3. **일관된 실행**: 누가 어디서 빌드해도 같은 결과가 나오게 한다.

없으면 jar 수십 개를 직접 내려받고 `javac` 명령을 일일이 입력해야 한다. IntelliJ에서 누르는 **"Maven 동기화"** 버튼이나 라이브러리가 빨갛게 표시되는 현상이 바로 이 도구가 하는 일과 관련된 것이다.

### 3. Maven

- 2004년경 등장한, Java 진영의 사실상 표준 빌드도구다.
- 설정 파일은 **`pom.xml`** (XML)이다.
- **선언형**이다. "무엇을 쓸지"만 적고, 어떻게 빌드할지는 Maven이 정한 규칙을 따른다.
- 빌드 순서(라이프사이클)가 고정되어 있다: `compile → test → package → install → deploy`
- 디렉토리 구조도 규약이다: `src/main/java`, `src/main/resources`, `src/test/java`

#### Maven 예시: pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- 1. 공통 설정과 버전을 물려받는다 -->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.5.6</version>
  </parent>

  <!-- 2. 내 프로젝트의 이름표 -->
  <groupId>com.example</groupId>
  <artifactId>demo</artifactId>
  <version>0.0.1-SNAPSHOT</version>

  <properties>
    <java.version>17</java.version>
  </properties>

  <!-- 3. 라이브러리를 내려받을 저장소 (기본은 Maven Central) -->
  <repositories>
    <repository>
      <id>egovframe</id>
      <url>https://maven.egovframe.go.kr/maven/</url>
    </repository>
  </repositories>

  <!-- 4. 실제로 쓸 라이브러리 (버전은 parent가 관리하므로 생략) -->
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <!-- 5. 빌드 플러그인: 실행 가능한 jar로 묶어 준다 -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

`<parent>`, `<repositories>`, `<dependencies>` 세 가지가 서로 다른 역할이라는 점이 핵심이다. parent는 **버전을 대신 정해 주고**, repositories는 **어디서 받을지**를 정하고, dependencies는 **실제로 쓸 것**을 고른다.

### 4. Gradle

- 2007년경 등장했고, Android의 공식 빌드도구로 채택되면서 널리 퍼졌다.
- 설정 파일은 **`build.gradle`** (Groovy) 또는 **`build.gradle.kts`** (Kotlin)다.
- 설정이 **코드**다. 필요하면 조건문이나 함수로 빌드 로직을 직접 작성할 수 있다.
- 변경된 부분만 다시 빌드하고(증분 빌드), 결과를 캐시해서 대체로 **빠르다.**

#### Gradle 예시: build.gradle (Groovy)

앞의 pom.xml과 같은 내용을 Gradle로 쓰면 이렇다.

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.5.6'            // 실행 가능한 jar로 묶어 주는 플러그인
    id 'io.spring.dependency-management' version '1.1.7'     // 버전을 대신 관리해 주는 플러그인
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = JavaVersion.VERSION_17
}

// 라이브러리를 내려받을 저장소
repositories {
    mavenCentral()
    maven { url 'https://maven.egovframe.go.kr/maven/' }
}

// 실제로 쓸 라이브러리 (버전은 플러그인이 관리하므로 생략)
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}
```

#### 같은 내용을 Kotlin DSL(build.gradle.kts)로

```kotlin
plugins {
    java
    id("org.springframework.boot") version "3.5.6"
    id("io.spring.dependency-management") version "1.1.7"
}

group = "com.example"
version = "0.0.1-SNAPSHOT"

java {
    sourceCompatibility = JavaVersion.VERSION_17
}

repositories {
    mavenCentral()
    maven { url = uri("https://maven.egovframe.go.kr/maven/") }
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
}

tasks.test {
    useJUnitPlatform()
}
```

Maven의 구조가 Gradle에서도 같은 개념으로 존재한다.

| Maven | Gradle |
|---|---|
| `<parent>` (버전 상속) | `io.spring.dependency-management` 플러그인 또는 `platform(...)` (BOM) |
| `<repositories>` | `repositories { }` |
| `<dependencies>` | `dependencies { }` |
| `<build><plugins>` | `plugins { }` |
| `<scope>test</scope>` | `testImplementation` |
| `<scope>provided</scope>` | `compileOnly` |

### 5. Maven과 Gradle의 차이

| | Maven | Gradle |
|---|---|---|
| 설정 형식 | XML (`pom.xml`) | Groovy/Kotlin 코드 |
| 가독성 | 길지만 구조가 단순함 | 짧지만 문법을 익혀야 함 |
| 유연성 | 규약 중심, 커스터마이징이 제한적 | 매우 유연 |
| 속도 | 상대적으로 느림 | 증분 빌드와 캐시로 빠른 편 |
| 학습 곡선 | 낮음 | 중간에서 높음 |
| 실행기(래퍼) | `mvnw` | `gradlew` |
| 주 사용처 | 엔터프라이즈, 공공, SI | Android, 최근의 Spring/MSA 프로젝트 |

#### 같은 일을 하는 명령어

| 작업 | Maven | Gradle |
|---|---|---|
| 실행 | `mvnw spring-boot:run` | `gradlew bootRun` |
| 빌드 | `mvnw package` | `gradlew build` |
| 정리 | `mvnw clean` | `gradlew clean` |
| 테스트 | `mvnw test` | `gradlew test` |
| 테스트 생략 | `mvnw package -DskipTests` | `gradlew build -x test` |

```bash
# Maven
./mvnw clean package -DskipTests
java -jar target/demo-0.0.1-SNAPSHOT.jar

# Gradle
./gradlew clean build -x test
java -jar build/libs/demo-0.0.1-SNAPSHOT.jar
```

결과물이 나오는 위치도 다르다. Maven은 `target/`, Gradle은 `build/libs/` 아래에 jar가 생긴다.

### 6. 래퍼(mvnw, gradlew)는 무엇인가

`mvnw`와 `gradlew`는 **빌드도구를 컴퓨터에 따로 설치하지 않아도 되도록 프로젝트에 들어 있는 실행기**다. 처음 실행하면 프로젝트가 지정한 버전의 Maven/Gradle을 내려받아 쓴다. 그래서 팀원 모두가 같은 빌드도구 버전으로 빌드하게 된다. MSA 템플릿을 띄울 때 `gradlew bootRun`을 쓴 것이 이 방식이다.

### 7. 그래서 왜 이 둘이 갈렸나

- **simple-backend (Maven)**: 전자정부프레임워크 본가의 표준이 Maven이다. parent POM(`egovframe-boot-starter-parent`), 전용 저장소(`maven.egovframe.go.kr`), 공통컴포넌트가 모두 Maven 기준이다.
- **msa-edu (Gradle)**: 서비스마다 `gradlew`로 실행하는 구조였다. 왜 Gradle을 골랐는지는 문서로 확인하지 못했고, Spring Cloud 계열 예제에서 Gradle을 쓰는 경우가 많다는 정도만 짐작할 수 있다. 직접 확인한 사실은 `gradlew`가 있다는 것까지다.

### 8. 무엇을 써야 할까

- **전자정부프레임워크 준수가 중요하면 Maven**이 안전하다. parent POM과 저장소 구조를 그대로 쓸 수 있다.
- **빌드가 크고 모듈이 많거나 빌드 로직을 자유롭게 짜야 하면 Gradle**이 유리하다.
- **소스코드는 어느 쪽으로 빌드하든 동일하다.** 달라지는 것은 설정 파일뿐이다.
- 나중에 MSA로 나누더라도 서비스를 Maven으로 만들면 된다.
- 납품이나 감리 기준에 빌드도구가 지정되어 있는지는 미리 확인해 두는 편이 좋다.

### 마치며

한 줄로 줄이면 이렇다.

**Maven은 규칙이 정해진 선언형, Gradle은 코드로 쓰는 유연한 방식이고, 둘 다 같은 일을 한다. 모놀리식과 MSA 중 무엇을 쓰느냐는 템플릿 제작자의 선택이지 아키텍처가 정하는 것이 아니다.**

### 참고

- Apache Maven 공식 문서
- Gradle 공식 문서
- 표준프레임워크 템플릿 (egovframe-template-simple-backend, egovframe-msa-edu)
