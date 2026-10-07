---
title: "@RestController vs @Controller  차이점 파악 (간략정리)"
date: 2022-10-05 15:37:41 +0900
categories: ["ETC", "IT Knowledge"]
tags: ["@controller", "@restcontroller", "@restcontroller vs @controller 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/RestController-vs-Controller-%EC%B0%A8%EC%9D%B4%EC%A0%90-%ED%8C%8C%EC%95%85-%EA%B0%84%EB%9E%B5"
---
**이 차이점을 궁금한 계기는 비밀번호를 변경하는 기능을 개발하는 과정에서, 현재 비밀번호를 체크하는 과정에서 인풋을 다 적어놓고 Controller를 통해서 한 번만 확인하는 방법과 Ajax를 통해서 인풋이 변하는 과정에서 계속해서 확인하는 방법을 두고 고민한 적이 있다.**  
**개인적으로 들었던 생각은 Ajax를 통한 RestApi로 검증하는 방식은 계속해서 Request를 날리니까 낭비가 아닐까?라는 생각을 했지만 하나의 request가 가는 거기 때문에, 똑같다는 대답을 들었다.**

**그렇다면 @RestController와 @Controller 차이는 무엇일까? 구글링과 작성한 본인 코드로 정리해보았다.**

* * *

**전통적인 Spring MVC의 컨트롤러인 @Controller는 주로 View를 반환하기 위해 사용한다.**

**하지만, 컨트롤러에서 Data만 반환해야 하는 경우도 생긴다.**

**그래서 @ResponseBody를 활용해서 Data를 Json형태로 반환할 수 있다.**

**그리고 @Controller, @ResponseBody를 따로 활용하는 방법도 있지만,**

**@RestController만 사용해서 2가지 기능을 한 번에 사용하는 것이 편하다.**

**즉, @RestController = @Controller + @ResponseBody이다.**

**@RestController의 주 용도는 Json으로 객체 데이터를 반환하는 것이다.**

**(컨트롤러를 통해 객체를 반환할 때는 일반적으로 ResponseEntity로 감싸서 반환한다.)**

**@Controller**

```javascript
@Controller
@RequestMapping("~~~")
public class KeepCouponController {
    
    /**
     * 사용가능한 쿠폰(전체)
     * @param model
     * @return
     */
    @RequestMapping(value= "/usable", method= RequestMethod.GET)
    public String UsableList(HttpServletRequest request, ModelMap model, HttpServletResponse response) {
    	
    	Member member = (Member) SecurityUtils.getCurrentUser();
    	if(member == null){
    		return "redirect:/";
    	}
    	Map<String, Object> q = getParameterMap(request);
    	
    	return JSP_PATH + "usableList";
    }
 }
```

**위 코드는 View를 전달해주고 있기 때문에 String으로 반환 값을 설정했다.** 

**@RestController**

```java
@RestController
@RequestMapping("~~")
public class KeepCouponResource {
    /*
     * 쿠폰 분기처리 조회
     *
     * @param request
     * @return
     * @throws URISyntaxException
     */
    @RequestMapping(value = "/coupon/list/{couponcode}", method = RequestMethod.GET)
    public ResponseEntity<Map> couponListPlustCoupon(@PathVariable(value="couponcode") String couponcode, HttpServletRequest request) throws Exception {

    	// 요청데이터 받아오기
    	Map<String, Object> q = getParameterMap(request);
    	Map<String, Object> result = new HashMap<>();
    	Pagination.resetAll();
        
    	return new ResponseEntity<Map>(result, headers, HttpStatus.OK);
    }
}
```

**위 코드에서는 Map 객체를 ResponseEntity로 감싸서 반환하고 있고, Map을 Json으로 반환하기 위해**

**@RestController 어노테이션을 사용하고 있는 것을 볼 수 있다.**
