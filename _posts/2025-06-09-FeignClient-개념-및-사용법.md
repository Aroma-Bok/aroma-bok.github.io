---
title: "FeignClient 개념 및 사용법"
date: 2025-06-09 14:11:12 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["feignclient", "feignclient 사용법", "feignclient 설정 및 사용법", "feignclient란?"]
tistory_url: "https://aroma-bok.tistory.com/entry/FeignClient-%EA%B0%9C%EB%85%90-%EB%B0%8F-%EC%82%AC%EC%9A%A9%EB%B2%95"
---
![](/assets/img/posts/311/1.jpg)

**FeignClient**

-   **Netflix에서 개발된 선언적(Declarative) HTTP 클라이언트 라이브러리로, 주로 마이크로서비스 아키텍처(MSA) 환경에서**  
    **서비스 간 통신을 간편하게 구현하기 위해 사용**

  
**주요 특징 및 개념**

1.  **선언적 방식**  
    **FeignClient는 Java 인터페이스와 애노테이션(@FeignClient 등)을 활용해 HTTP 요청을 정의**  
    **복잡한 HTTP 요청 코드를 직접 작성할 필요 없이, 메서드 시그니처와 애노테이션만으로 외부 API 호출이 가능**
2.  **코드 간결성 및 가독성**  
    **인터페이스 기반 접근 방식 덕분에 코드가 매우 간단하고 직관적이며, 유지보수와 재사용성이 용이**  
    **Spring Data JPA에서 쿼리 메서드만 선언하면 구현체가 자동 생성되는 것과 유사한 방식**
3.  **다양한 HTTP 메서드 지원**  
    **GET, POST, PUT, DELETE 등 다양한 HTTP 요청을 지원하며, 요청/응답 처리, 에러 핸들링, 헤더/파라미터 설정가능**
4.  **Spring Cloud와의 통합**  
    **현재는 Spring Cloud OpenFeign 프로젝트로 관리되며, Spring Boot 환경에서  
    @EnableFeignClients 애노테이션만 추가하면 쉽게 사용가능**
5.  **로드 밸런싱 및 확장성**  
    **자체적으로 로드 밸런싱 기능을 제공하며, 대규모 서비스 환경에서도 효율적으로 사용가능**

**정리**

-   **Feign Client는 선언적으로 HTTP 클라이언트를 구현할 수 있게 해주는 라이브러리**
-   **복잡한 HTTP 통신 코드를 줄이고, 인터페이스와 애노테이션만으로 외부 API와 손쉽게 통신 가능**
-   **Spring Cloud 환경에서 마이크로서비스 간 통신을 표준화하고 단순화하는 데 매우 유용하게 사용가능**

* * *

**사용법**

**1\. Pom.xml 추가**

```java
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-openfeign -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
    <version>3.1.7</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-loadbalancer -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
    <version>3.1.7</version>
</dependency>
```

**2.FeignClient 활성화**

```java
@SpringBootApplication
@EnableFeignClients
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

3\. **FeignClient 인터페이스 생성**

-   **외부 API를 호출할 인터페이스를 만들고, @FeignClient 어노테이션을 붙입니다.**
-   **name: 클라이언트 이름, url: 호출할 API의 도메인 또는 IP, configuration: (필요시) 커스텀 설정 클래스  
    **
-   ****FeignClient가 ApiFeignClient라는 이름으로 서비스 호출을 시도할 때, 정의한 커스텀 로드밸런서 설정이 적용****

```java
@FeignClient(name = "userClient", url = "https://api.example.com")
public interface ApiFeignClient {
    @PostMapping("${feign.api.sendEmailUrl}")
    CommonResult sendEmail(ApiEmailReq apiEmailReq);

    @PostMapping("${feign.api.sendSmsUrl}")
    CommonResult sendSms(ApiMobileReq apiMobileReq);
}
```

**4\. (선택) FeignClient 공통 설정 클래스 작성**

```java
@Configuration
public class ApiFeignClientLoadBalancerConfig {

    @Bean
    public ServiceInstanceListSupplier apiFeignClientInstanceSupplier(@Value("#{'${apiFeignClient.instances}'.split(',')}") List<String> instanceUrls) {
        return new ServiceInstanceListSupplier() {
            @Override
            public String getServiceId() {
                return "apiFeignClient";
            }
            @Override
            public Flux<List<ServiceInstance>> get() {
                List<ServiceInstance> instances = instanceUrls.stream().map(url -> {
                    URI uri = URI.create(url);
                    return new DefaultServiceInstance(getServiceId() + "-" + uri.getHost(),
                            getServiceId(),
                            uri.getHost(),
                            uri.getPort(),
                            uri.getScheme().equals("https")
                    );
                }).collect(Collectors.toList());
                return Flux.just(instances);
            }
        };
    }
}
```

-   **실제로 커스텀 인스턴스 리스트(여러 서버 주소)를 제공하는 설정 클래스**
-   **여기서 ServiceInstanceListSupplier 빈을 등록하****면, 해당 서비스 이름으로 요청이 들어올 때 이 빈이 반환하는  
    인스턴스 목록을 사용**

```java
@LoadBalancerClient(name = "apiFeignClient", configuration = apiFeignClientLoadBalancerConfig.class)
public class ApiFeignClientLoadBalancerClientConfig {}
```

-   **"apiFeignClient"라는 서비스 이름에 대해 커스텀 로드밸런서 설정(ApiFeignClientLoadBalancerClientConfig)을 적용하라는 선언**
-   **이 어노테이션이 붙은 클래스는 빈(bean)으로 등록될 필요는 없으며, 단순히 설정 연결 역할**

**5\. FeignClient 사용**

```java
@Autowired
private ApiFeignClient apiFeignClient;

..생략

try {
        result = apiFeignClient.sendEmail(apiEmailReq);
    }
```

```java
// 프로퍼티에 설정된 인스턴스 목록
// 이 프로퍼티에 등록된 값이 바로 요청을 보낼 수 있는 서버(인스턴스)들의 주소 리스트
messageApiFeignClient.instances=
http://xx.xxx.xx.x:8080,
http://xx.xxx.xx.x:8080,
http://xx.xxx.xx.x:8080,
http://xx.xxx.xx.x:8080
```

**즉, 직접 URL을 지정하지 않고, 로드밸런서가 제공하는 인스턴스 중 하나로 요청이 분산**

**프로세스 순서**

1.  **Feign Client가 messageApiFeignClient 이름으로 서비스 호출 시도**
2.  **Spring Cloud LoadBalancer가 @LoadBalancerClient 어노테이션을 확인**
3.  **해당 서비스 이름에 연결된 커스텀 설정(ApiFeignClientLoadBalancerConfig)을 적용**
4.  **설정 클래스에서 제공하는 ServiceInstanceListSupplier 빈을 통해 여러 인스턴스 목록을 획득**
5.  **로드밸런서가 이 목록 중 하나를 선택하여 요청을 전달**
