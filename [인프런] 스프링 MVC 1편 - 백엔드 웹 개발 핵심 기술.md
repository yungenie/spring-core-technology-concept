
# 🌱 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
출저 : [`인프런 김영한 - 스프링 MVC 1편 백엔드 웹 개발 핵심 기술`](https://inf.run/xRKw)

<br>

## 목차
- [웹 애플리케이션 이해](#웹-애플리케이션-이해) 
<br>


## 웹 애플리케이션 이해

### 웹 서버, 웹 애플리케이션 서버

#### 웹 HTTP 기반

- 웹이라는 거는 다 http 기반으로 통신을 합니다.
- 웹 브라우저(클라이언트)에서 url 딱 치면 인터넷을 통해 서버에 접근하고 서버에서는 html만들어서 클라이언트에 내려줍니다. 그러면 웹 브라우저가 받은 html을 가지고 여러분 눈에 보여준다.
- 웹 세상에서는 HTTP 프로토콜을 기반으로 데이터를 주고 받습니다. 클라이언트에서 서버로 데이터를 전송할 때, 서버에서 클라이언트로 데이터를 응답할 때 다 HTTP라는 프로토콜 기반으로 동작을 합니다.
- 일반적으로 아는 html, 텍스트, 이미지, 음성, 영상, 파일, api json/xml, 서버간에 데이터를 주고 받을 때도 html 이런게 아니라 HTTP 프로토콜 기반으로 대부분 사용을 합니다.

#### 웹 서버 (Web Server)

- 웹 서버는 http 프로토콜을 기반으로 동작하는 서버 입니다.
- 웹 서버는 `정적`인 리소스(파일)를 제공하고 기타 부가 기능들을 제공합니다.
    - 특정 폴더에 html, css, js, 이미지, 영상과 같은 파일을 클라이언트가 웹 서버에 요청을 하면 http 프로토콜로 파일을 응답을 해줍니다.
    - 웹 서버도 사실은 프로그램을 실행하는 기능을 포함하기도 합니다. 플러그인을 설치해서 가능은 합니다.
- 웹 서버 : ex) NGINX, APACHE

#### 웹 애플리케이션 서버 (WAS, Web Application Server)

- 웹 애플리케이션 서버는 HTTP 요청이 오면 사용자에 따라서 다른 화면들도 보여줄 수 있습니다.
- 웹 애플리케이션 서버는 `동적`인 HTML 제공하며 **애플리케이션 로직**을 제공하는 서버 입니다.
    - 웹 서버의 기능을 대부분 포함하고 있습니다.
    - 웹 서버의 차이점은 프로그램 코드를 실행해서 애플리케이션 로직을 수행할 수 있습니다.
    - 특정 사용자마다  다르게 보여주는 동적인 HTML 파일 제공이 가능합니다.
    - 그래서 애플리케이션 코드를 실행하는데 조금 더 특화돼 있습니다.
- 웹 애플리케이션 서버 : ex) 톰캣(Tomcat) Jetty, Undertow

#### 웹 서버 vs 애플리케이션 서버

- 웹 서버는 정적 리소스를 제공하는 것이고, 와스는 애플리케이션 로직까지 실행할 수 있는 차이점이 있습니다.
    - 웹 서버 또한 애플리케이션 로직을 실행하는 기능을 포함하기도 하지만 와스가 애플리케이션 로직을 수행하는데 더 특화되어 있습니다.

#### 웹 시스템 구성

- Web Server - Was - DB
    - 정적 리소스만 제공하는 웹 서버는 잘 안죽지만, 와스는 잘 죽기 때문에 웹 서버를 앞에 두고 와스가 처리를 못해도 웹 서버를 앞에 두고 정적인 오류 html을 고객에게 보여줄 수 있도록 구성을 합니다.
- 자원 이용의 효율성 및 장애 극복, 배포 및 유지 보수의 편의성 때문에 웹 서버와 WAS를 분리해효율적인 분산 처리를 합니다.
   
    
#### 기타

- 웹 서버 기타 부가 기능?
    - 플러그인을 설치하면 프로그램을 실행하는 기능을 포함하기도 합니다.
- cdn 캐시 서버?
    - 콘텐츠 전송 네트워크 (content delivery network) 란 데이터 사용량이 많은 애플리케이션의 웹 페이지 로드 속도를 높이는 상호 연결된 서버 네트워크 입니다. 콘텐츠를 효율적으로 전달하기 위해 여러 노드를 가진 네트워크에 데이터를 저장하여 제공하는 시스템.

### 서블릿
- 목적
    - 웹 서버가 돌아가는 과정 이해
    - 서블릿을 사용했을 때는 어떤부분이 달라지는 지
    - 역사적 기술이 나오게 된 흐름은 자바 서블릿에 html 넣는 게 불편해서 jsp가 나타났습니다.
   
- jsp만 사용했을 때 처리 과정
    - jsp 파일을 자바코드로 변환이 되고 자바코드를 컴파일해서 컴파일된 파일이 인스턴스가 됩니다. 
    - 그 객체가 가지고 있는 서블릿의 서비스 메서드를 호출해서 처리 후 브라우저에 응답을 합니다.
    
- 웹 클라이언트의 요청을 처리할 수 있는 클래스 입니다.
- httpServelt을 상속해서 만들어집니다.
- 서블릿은 자바 코드 안에 html 넣을 수 있습니다.
- 서블릿이 처리되는 과정
    - 요청 url 들어오면 톰켓의 web.xml에서 해당 url에 맞춰 서블릿 매핑이 됩니다. 
    - 해당 서블릿 클래스에서 클라이언트의 요청에 대한 처리 후 결과를 jsp로 응답을 합니다.
- mvc 패턴
    - 결합도를 낮추고 응집도를 높힌다. 🔥
        - 하나의 클래스에 많은 기능을 담기 보다 목적을 하나만 둬서 그 목적만 처리 할 수 있고 해당 처리가 다른 곳에 영향이 안가게끔

### 멀티 쓰레드
- 서블릿은 쓰레드가 실행을 시켜줍니다.
![Untitled](https://user-images.githubusercontent.com/28051638/229498702-27604cd8-e26d-480e-be89-6b69ef845231.png)

- 요청마다 쓰레드가 생성되어 나타나는 단점
    - 고객 요청이 많아지면 쓰레드가 많이 생성되서 CPU, 메모리 임계점을 넘어서 서버가 죽는 경우가 생깁니다.
- 요청마다 쓰레드 생성의 단점 보안 (쓰레드 풀, Thread Pool)
    - 쓰레드 풀 내부에서 쓰레드를 관리할 수 있도록 쓰레드의 개수를 설정할 수 있습니다.
    - 톰캣은 최대 200개 기본, 쓰레드 풀에 생성 가능한 쓰레드의 최대치를 관리합니다.
    - 쓰레드가 필요하면 이미 생성되어 있는 쓰레드를 풀에서 꺼내서 사용합니다.
    - 사용을 종료하면 풀에 다시 반납합니다.
    - 최대 쓰레드를 모두 사용 중일 때는 요청을 거절하거나 특정 숫자 만큼 대기하도록 설정 가능합니다.
- 핵심
    - WAS가 멀티 쓰레드를 지원해줍니다.
    - 쓰레드 풀 설정 max thread로 처리를 할 수 있습니다. (개발자가 멀티 쓰레드 관련해서 코드로 신경을 쓰지 않아도 됩니다.)

### 서블릿
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/229494672-c755ef01-ad7f-4187-a7a2-cd17d3f3df19.png">
- 서블릿 클래스가 싱글톤으로 관리됩니다.

### HTML, HTML API, CSR, SSR

#### HTML

- 정적 HTML 페이지
    - 웹 브라우저 요청 → url에 해당하는 폴더 경로를 찾아서 해당 문서를 반환
- 동적 HTML 페이지
    - 웹 브라우저 요청 → was → DB 데이터 조회 → 동적으로 HTML 생성 → JSP/타임리프 (view template) → html 반환

#### HTML API

- UI 클라이언트 접점
    - 웹 브라우저 자바 스크립트를 통한 HTTP API 호출
    - React, Vue.js 같은 웹 클라이언트
    - 앱 클라이언트 (아이폰, 안드로이드, PC 앱)
- 서버 to 서버
    - 기업간 데이터 통신

#### SSR 서버 사이드 렌더링

- HTML 최종결과를 서버에서 만들어서 웹 브라우저에 전달
- 주로 정적인 화면을 사용
- 관련 기술 : JSP, 타임리프

#### CSR 클라이언트 사이드 렌더링

- HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용
- 주로 동적인 화면에 사용
- 구글 지도, Gmail, 구글 캘린더
- 관련 기술 : React, Vue.js

#### 참고
- CSR + SSR 동시에 지원하는 웹 프레임워크도 존재합니다.
- SSR을 사용하더라도, JS를 사용해서 화면 일부를 동적으로 변경 가능합니다.

### HTTP 요청 데이터 API 메시지 바디 JSON
- JSON 결과를 파싱해서 사용할 수 있는 자바 객체로 변환하려면 Jackson, Gson 같은 JSON 변환 라이브러리를 추가해서 사용해야 합니다.
- 스프링 부트로 Spring MVC를 선택하면 기본으로 Jackson 라이브러리(ObjectMapper)를 함께 제공한다.

#### 참고
- HTML form 데이터도 HTTP 메시지 파라미터로 조회를 할 수 있다. (request.getParameter(...)) 
- 조회는 가능하지만, 데이터 파싱을 불가합니다.

## 서블릿, JSP, MVC 패턴
- 서블릿 → JSP → MVC 패턴 점진적으로 개선한 순서입니다.
- JSP 같은 뷰 템플릿은 화면을 렌더링 하는 데 최적화 되어 있습니다.

#### /WEB-INF
- 이 경로안에 JSP가 있으면 외부에서 직접 JSP를 호출 할 수 없습니다.
- 항상 컨트롤러를 통해서 JSP를 호출 할 수 있습니다.

#### redirect vs forward
- 리다이렉트는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시 요청합니다.
  따라서 클라이언트가 인지할 수 있고, URL 경로도 실제로 변경된다. 
- 반면에 포워드는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못합니다.

#### 서블릿와 JSP의 한계
- 서블릿 or JSP 비지니스 로직과 뷰 렌더링까지 모두 처리하므로 많은 역할을 하게 되고 결과적으로 유지보수가 어려워집니다.

#### 서블릿 MVC 패턴
- 컨트롤러(서블릿)의 역할과 뷰를 렌더링(JSP) 하는 역할을 명확하게 구분할 수 있었습니다.
- 그런데 컨트롤러는 딱 봐도 중복이 많고, 필요하지 않는 코드들도 많이 보였습니다. (공통처리에도 어려웠음)

#### ViewPath에 중복
- String viewPath = "/WEB-INF/views/new-form.jsp";
- prefix: /WEB-INF/views/
- suffix: .jsp

## 스프링 MVC 구조 이해
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/230334437-977978fb-c4f5-493c-8873-cf154815a727.png">

#### 주요 인터페이스 목록
- 핸들러 매핑 : `org.springframework.web.servlet.HandlerMapping`
- 핸들러 어댑터 : `org.springframework.web.servlet.HandlerAdapter`
- 뷰 리졸버 : `org.springframework.web.servlet.ViewResolver`
- 뷰 : `org.springframework.web.servlet.View`

#### 주요 인터페이스 요약
- 핸들러 매핑 : 요청 URL에 매핑된 **핸들러(컨트롤러)를 조회**합니다.
- 핸들러 어댑터 : 핸들러를 **실행할 수 있는 핸들러 어댑터를 조회**하고 실행 후 핸들러가 반환하는 **ModelAndView**로 변환해서 뷰 리졸버를 찾고 실행합니다.
- 뷰 리졸버 : 뷰의 논리 이름을 **물리 이름(절대경로)** 으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환합니다.
- 뷰 : 뷰를 통해서 뷰를 렌더링 합니다.

#### 컨트롤러 어노테이션 
- RequestMappingHandlerMapping 은 스프링 빈 중에서 @RequestMapping 또는 @Controller가 클래스 레벨에 붙어 있는 경우에 매핑 정보로 인식합니다.
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/230586211-c4d15e5a-6729-46ff-9f41-914386195fcc.png">

#### 컨트롤러 통합
- @RequestMapping을 잘 보면 클래스 단위가 아니라 메서드 단위로 클래스를 유연하게 하나로 통합할 수 있습니다.

```java
/**
* 클래스 단위 -> 메서드 단위
* @RequestMapping 클래스 레벨과 메서드 레벨 조합
*/
@Controller
@RequestMapping("/springmvc/v2/members")
public class SpringMemberControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/new-form")
    public ModelAndView newForm() {
        return new ModelAndView("new-form");
    }

    @RequestMapping("/save")
    public ModelAndView save(HttpServletRequest request, HttpServletResponse response) {

        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        ModelAndView mav = new ModelAndView("save-result");
        mav.addObject("member", member);
        return mav;
    }

    @RequestMapping
    public ModelAndView members() {

        List<Member> members = memberRepository.findAll();

        ModelAndView mav = new ModelAndView("members");
        mav.addObject("members", members);
        return mav;
    }
}
```
#### 컨트롤러 실용적인 방식

#### @RequestParam 사용
- 스프링은 HTTP 요청 파라미터를 @RequestParam 받을 수 있습니다.
- GET 쿼리 파라미터, POST Form 방식을 모두 지원합니다.

#### @RequestParam HTTP Method 사용
- URL 매칭, HTTP Method도 함께 구분할 수 있습니다.
- @RequestParam 만 사용하게 되면 Get,Post Method 모두 받기때문에 side effect 우려도 있습니다.

```java
@RequestMapping(value = "/new-form", method = RequestMethod.GET)
```

#### @GetMapping, @PostMapping
- 위 애노테이션 대신 더 간편하고 편리하게 사용할 수 있습니다.
- Get,Post, Put, Delete, Patch 모두 준비되어 있습니다.

```java
@Controller
@RequestMapping("/springmvc/v3/members")
public class SpringMemberControllerV3 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    //@RequestMapping("/new-form") //RequestMapping get 쿼리 파라미터, post form 방식 모두 지원
    //@RequestMapping(value = "/new-form", method = RequestMethod.GET)
    @GetMapping("/new-form")
    public String newForm() {
        return "new-form";
    }

    @PostMapping("/save")
    public String save(
            @RequestParam("username") String username, 
            @RequestParam("age") int age, // 타입 캐스팅, 변환까지 자동으로 해줍니다.
            Model model) {

        Member member = new Member(username, age);
        memberRepository.save(member);

        model.addAttribute("member", member);
        return "save-result";
    }

    @GetMapping
    public String members(Model model) {

        List<Member> members = memberRepository.findAll();

        model.addAttribute("members", members);
        return "members";
    }
}
```

#### @GetMapping 애노테이션 내부
- @GetMapping 애노테이션 코드를 열어보면 @RequestMapping 애노테이션을 내부에 가지고 있습니다.
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/230761186-833c83fc-1933-441e-8d80-9ad54a5e1e58.png">

#### 정리
- 애노테이션도 악착같이 기능이 업그레이드 되어 있는 걸 볼 수 있습니다.
- 그래서, 애노테이션 안에 애노테이션 조합을 확인 할 수 있습니다.


## 스프링 MVC 기본기능
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/230826207-329059dd-f241-4926-96a2-2228097370a6.png">

#### spring boot packaging
- Jar는 항상 내장 서버(톰캣등)를 사용하고 webapp 경로도 사용하지 않으므로, **내장 서버 사용에 최적화**되어 최근에는 주로 이 방식을 사용합니다.
- War는 주로 **외부 서버 배포**의 목적으로 사용합니다. 내장 서버도 사용 가능합니다.

#### Welcome 페이지
- 스프링 부트에 Jar 사용하면 /resources/static/위치에 index.html 파일을 두면 Welcome 페이지로 처리해줍니다.


#### @RestController vs @Controller 차이점
- @Controller 는 반환 타입이 String 이면 뷰 이름으로 인식됩니다. 그래서 뷰를 찾고 뷰가 랜더링 됩니다.
- @RestController 는 반환 값으로 뷰를 찾는 것이 아니라, HTTP 메시지 바디에 바로 입력됩니다.


```java
@RestController
public class LogTestController {


    @RequestMapping("/log-test")
    public String logTest() {
        String name = "Spring";

        System.out.println("name = " + name);

        return "ok";
    }
}
```

```java
@Controller
public class LogTestController {


    @RequestMapping("/log-test")
    public String logTest() {
        String name = "Spring";

        System.out.println("name = " + name);

        return "log-form";
    }
}
```


### 로깅(logging)
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/230831384-b287049f-6b21-456e-9da1-a2c294b078bb.png">

- Spring Boot는 로깅 라이브러리가 포함되어 있습니다. 
- Logback, log4J, log4J2 등 수 많은 로그 라이브러리가 존재합니다.  
- 많은 로그 라이브러리 통합해서 인터페이스로 제공하는 것이 SLF4J 라이브러리입니다. 
- 구현체인 Logback이 Spring Boot가 기본으로 제공하고 성능도 좋고 기능도 많이 지원해서 실무에서 대부분 사용합니다. 


> 운영 시스템에서는 System.out.println() 시스템 콘솔로 출력하지 않고, 별도의 로깅 라이브버리를 사용해서 로그를 출력합니다.   
> 왜? 로그 레벨에 상관없이 시스템 콘솔은 다 찍힙니다. 운영 시스템에서는 중요한 로그만 찍기 위해서 입니다.


- Spring Boot 기본 Logger 사용
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@RestController
public class LogTestController {

    private final Logger log = LoggerFactory.getLogger(getClass());
    //private final Logger log = LoggerFactory.getLogger(LogTestController.class); // 클래스명 수동기입 가능
    //private static final Logger log = LoggerFactory.getLogger(LogTestController.class);

    @RequestMapping("/log-test")
    public String logTest() {
        String name = "Spring";

        System.out.println("name = " + name);

        log.trace("trace my log="+ name); 
        log.trace("trace log={}, {}, {}", name, name, name); 
        log.debug("debug log={}", name);
        log.info(" info log={}", name);
        log.warn(" warn log={}", name);
        log.error("error log={}", name);

        return "ok";
    }
}
```

- lombok 룸북 제공으로 로그 미선언 
```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class LogTestController {

    @RequestMapping("/log-test")
    public String logTest() {
        String name = "Spring";

        System.out.println("name = " + name);

        log.trace("trace my log="+ name); 
        log.trace("trace log={}, {}, {}", name, name, name); 
        log.debug("debug log={}", name);
        log.info(" info log={}", name);
        log.warn(" warn log={}", name);
        log.error("error log={}", name);

        return "ok";
    }
}
```


#### 로그 레벨 설정
- application.properties
- root level, package level 둘다 설정하면 package가 우선권을 가집니다.
- TRACE < DEBUG < INFO < WARN < ERROR
- 예를 들어 INFO 레벨로 설정을 하면 하위 레벨을 로그에 찍히지 않고, INFO 이상의 레벨만 찍힙니다.

```
#전체 로그 레벨 설정(기본 info)
logging.level.root=info

#hello.springmvc 패키지와 그 하위 로그 레벨 설정
logging.level.hello.springmvc=debug
```

#### 올바른 로그 사용법
- log.debug("data="+data)
    - 로그 레벨을 info로 설정해도 실제론 "data="+data 연산이 발생하므로, CPU/메모리를 사용해서 쓸모없는 자원을 사용하게 됩니다.
- log.debug("data={}", data)
    - 로그 레벨 info로 설정하면 앞과 같은 의미없는 연산이 발생하지 않습니다. debug 메소드 내에서 실행을 하지 않습니다.

### 요청 매핑

#### Path Variable
- HTTP API는 리소스 경로에 식별자를 넣는 스타일을 선호합니다.
```java
/**
 * PathVariable 사용
 */
@GetMapping("/mapping/{userId}")
public String mappingPath(@PathVariable("userId") String data) {
    log.info("mappingPath userId={}", data);
    return "ok";
}
```    

```java
/**
 * PathVariable 사용, 변수명이 같으면 생략 가능
 */
@GetMapping("/mapping/{userId}")
public String mappingPath(@PathVariable String userId) {
    log.info("mappingPath userId={}", userId);
    return "ok";
}
```   

#### Path Variable 다중
```java
/**
* PathVariable 사용 다중
*/
@GetMapping("/mapping/users/{userId}/orders/{orderId}")
public String mappingPath(@PathVariable String userId, @PathVariable Long orderId) {
    log.info("mappingPath userId={}, orderId={}", userId, orderId);
    return "ok";
}
```

- 미디어 타입 조건 매
```java
/**
 * Content-Type 헤더 기반 추가 매핑 Media Type
 * consumes="application/json"
 * consumes="!application/json"
 * consumes="application/*"
 * consumes="*\/*"
 * MediaType.APPLICATION_JSON_VALUE
 */
@PostMapping(value = "/mapping-consume", consumes = MediaType.APPLICATION_JSON_VALUE)
public String mappingConsumes() {
    log.info("mappingConsumes");
    return "ok";
}

/**
 * Accept 헤더 기반 Media Type
 * produces = "text/html"
 * produces = "!text/html"
 * produces = "text/*"
 * produces = "*\/*"
 */
@PostMapping(value = "/mapping-produce", produces = MediaType.TEXT_HTML_VALUE)
public String mappingProduces() {
    log.info("mappingProduces");
    return "ok";
}
```
