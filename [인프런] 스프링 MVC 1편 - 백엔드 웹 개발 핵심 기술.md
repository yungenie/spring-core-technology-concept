
# 🌱 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
출저 : [`인프런 김영한 - 스프링 MVC 1편 백엔드 웹 개발 핵심 기술`](https://inf.run/xRKw)

<br>

## 목차
- [웹 애플리케이션 이해](#웹-애플리케이션-이해) 
- [서블릿, JSP, MVC 패턴](#서블릿-jsp-mvc-패턴) 
- [스프링 MVC 구조 이해](#스프링-mvc-구조-이해)
- [스프링 MVC 기본기능](#스프링-mvc-기본기능)
- [웹 페이지 만들기](#웹-페이지-만들기)

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
- Jar를 사용하면 webapp 경로를 사용할 수 없슨다. /resources/static 경로는 외부에 공개가 됩니다. controller에 매핑이 되지 않으면 해당 정적 리소스로 열리게 됩니다.  
- src/main/resources 는 리소스를 보관하는 곳이고, 또 클래스패스의 시작 경로입니다. 
- 따라서 다음 디렉토리에 리소스를 넣어두면 스프링 부트가 정적 리소스로 서비스를 제공합니다.


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
    - 포맷 인수 형태로 지정하는 게 좋습니다. log.trace("trace log={}, {}, {}", name, name, name); 다중 출력도 가능합니다.  

### 요청 매핑 (요청 파라미터)

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

- 미디어 타입 조건 매핑 
```java
/**
 * Content-Type 헤더 기반 추가 매핑 Media Type (요청 Header의 Content-Type) 
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
 * Accept 헤더 기반 Media Type (요청 Header의 Accept와 produces랑 동일해야합니다.) 
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

#### HTTP 요청 파라미터 종류
- Servelt 구현시에는 HTTP method 상관없이 request.getParameter()로 값을 가져왔습니다.
- GET 쿼리 파라미터
- POST HTML Form 
- HTTP message body에 데이터를 직접 담아서 요청 (JSON/XML/TEXT)

#### @RequestParam @ModelAttribute
- 최근 HTTP 스펙에서는 Get도 HTTP Body에 넣어서 보낼 순 있지만, 선호하는 방식은 아닙니다.

```java
@Slf4j
@Controller
public class RequestParamController {

    /**
     * Servlet 방식
     * 반환 타입이 없고 응답에 값을 직접 집어넣으면, view 화면으로 매핑되지 않고 값이 반환됩니다.
     */
    @RequestMapping("/request-param-v1")
    public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        log.info("username={}, age={}", username, age);
        response.getWriter().write("ok");
    }

    /**
     * @RequestParam 사용 : 파라미터 이름으로 바인딩
     * @ResponseBody 추가 : View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력
     */
    @ResponseBody
    @RequestMapping("/request-param-v2")
    public String requestParamV2(
            @RequestParam("username") String memberName,
            @RequestParam("age") int memberAge) {

        log.info("username={}, age={}", memberName, memberAge);
        return "ok";
    }

    /**
     * @RequestParam 사용 : HTTP 파라미터 이름이 변수 이름과 같으면 @RequestParam(name="xx") 생략 가능
     */
    @ResponseBody
    @RequestMapping("/request-param-v3")
    public String requestParamV3(
            @RequestParam String username,
            @RequestParam int age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }

    /**
     * @RequestParam 사용 : String, int 등의 단순 타입이면 @RequestParam 도 생략 가능
     * ※ @RequestParam 생략하면 스프링 MVC는 내부에서 required=false 적용함.
     */
    @ResponseBody
    @RequestMapping("/request-param-v4")
    public String requestParamV4(String username, int age) {
        log.info("username={}, age={}", username, age);
        return "ok";
    }

    /**
     * @RequestParam.required 사용
     *
     * 주의!
     * /request-param-required?username= -> 빈문자로 통과
     *
     * 주의!
     * /request-param-required
     * int age -> null을 int에 입력하는 것은 불가능, 따라서 Integer 변경해야 함(또는 다음에 나오는 defaultValue 사용)
     */
    @ResponseBody
    @RequestMapping("/request-param-required")
    public String requestParamRequired(
            @RequestParam(required = true) String username,
            @RequestParam(required = false) Integer age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }

    /**
     * @RequestParam : defaultValue 사용
     *
     * 참고: defaultValue는 빈 문자의 경우에도 적용
     * /request-param-default?username=
     * 단, "" 빈문자열 값의 경우는 예외처리 해야함.
     */
    @ResponseBody
    @RequestMapping("/request-param-default")
    public String requestParamDefault(
            @RequestParam(required = true, defaultValue = "guest") String username,
            @RequestParam(required = false, defaultValue = "-1") int age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }

    /**
     * @RequestParam Map, MultiValueMap
     * Map(key=value)
     * MultiValueMap(key=[value1, value2, ...]) ex) (key=userIds, value=[id1, id2])
     * 잘 사용하지 않는 것으로 보임
     */
    @ResponseBody
    @RequestMapping("/request-param-map")
    public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
        log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
        return "ok";
    }

    /**
     * @ModelAttribute 사용
     * 참고: model.addAttribute(helloData) 코드도 함께 자동 적용됨
     * 과정 : 요청 파라미터를 받아 객체의 프로퍼티에 바인딩 합니다.
     * 1. 객체 생성 (DTO/VO/Model)
     * 2. 요청 파라미터명으로 객체의 프로퍼티를 찾습니다.
     * 3. 해당 프로퍼티의 setter() 호출 후 요청 파라미터의 값을 인자로 바인딩합니다.
     */
    @ResponseBody
    @RequestMapping("/model-attribute-v1")
    public String modelAttributeV1(@ModelAttribute HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }
    
    /**
     * @ModelAttribute 생략 가능
     * String, int 같은 단순 타입 = @RequestParam
     * argument resolver 로 지정해둔 타입 외 = @ModelAttribute
     */
    @ResponseBody
    @RequestMapping("/model-attribute-v2")
    public String modelAttributeV2(HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }
}
```

### HTTP 요청 메시지 JSON
#### @ModelAttribute , @RequestParam 애노테이션 생략
- 스프링에서 @ModelAttribute , @RequestParam 애노테이션 생략시 다음과 같은 규칙이 적용됩니다.
- String , int , Integer 같은 단순 타입의 파라미터만 있는 경우 = @RequestParam
- 객체(model) 타입 혹은 argument resolver 로 지정해둔 타입 외 나머지 경우 = @ModelAttribute


```java
    @ResponseBody
    @PostMapping("/request-body-json-v5")
    public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        return data;
    }
```
- 객체를 받는 파라미터의 경우 @RequestBody 생략하면 @ModelAttribute 가 적용됩니다.
- @ModelAttribute가 적용되면 HTTP 메시지 바디가 아니라 HTTP 요청 파라미터에서 데이터를 찾으므로, 데이터 매핑이 안됩니다. 

#### @RequestBody 요청,  @ResponseBody 응답 정리
- @RequestBody 요청 : JSON요청 -> HTTP 메시지 컨버터 > 객체 
- @ResponseBody 응답 : 객체 -> HTTP 메시지 컨버터 -> JSON응답
- ※ @ResponseBody 애노테이션을 적용하면 객체가 JSON 타입으로 반환하므로, 그 외 타입 반환 시 다른 처리가 필요하므로 유의해야합니다.

#### Thymeleaf 스프링 부트 설정
- 스프링 부트가 자동으로 ThymeleafVeiwResolver와 필요한 스프링 빈을 등록해주고 기본으로 설정 값을 셋팅해줍니다.

```
application.properties
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```

#### HTTP API
- @Controller, @ResponseBody 둘다 적용한 애노테이션이 @RestController 입니다.

```java
@Slf4j
@RestController
public class ResponseBodyController {

    @ResponseStatus(HttpStatus.OK)
    @GetMapping("/response-body-json-v2")
    public HelloData responseBodyJsonV2() {
        HelloData helloData = new HelloData();
        helloData.setUsername("userA");
        helloData.setAge(20);
        return helloData;
    }
}
```

### 정리 @RequestParam, @ModelAttribute, @RequestBody, @ResponseBody
- 쿼리 파라미터 (@RequestParam)
- HTML FORM (@ModelAttribute)
- 단순 텍스트
- API JSON (@RequestBody)

#### 요청 파라미터 vs HTTP 메시지 바디
- 요청 파라미터를 조회하는 기능: @RequestParam , @ModelAttribute
- HTTP 메시지 바디를 직접 조회하는 기능: @RequestBody

#### @RequestParam 
 - GET /test?name=yunjingwak&id=yunjin;
 - HTTP 쿼리 파라미터 이름과 바인딩(매핑)
 - @RequestParam(name="xx") name 생략 가능 (단, HTTP 쿼리 파라미터 이름과 메소드의 파라미터 동일 해야함. required=true 디폴트)
 - @RequestParam 애노테이션 생략 가능 (단, String, int등의 단순 타입일 경우. 스프링 MVC는 내부에서 required=false 적용함)
 - defaultValue 사용 가능 (단, "" 빈 문자열 값을 예외처리 해야함. 빈 문자는 defaultValue 적용함. ex /request-param-default?username=&age=29)

```java
@Controller
public class RequestParamController {

      @ResponseBody
    @RequestMapping("/request-param-v2")
    public String requestParamV2(
            @RequestParam("username") String memberName,
            @RequestParam("age") int memberAge) {

        log.info("username={}, age={}", memberName, memberAge);
        return "ok";
    }

    @ResponseBody
    @RequestMapping("/request-param-v3")
    public String requestParamV3(
            @RequestParam String username,
            @RequestParam int age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }

      @ResponseBody
    @RequestMapping("/request-param-v4")
    public String requestParamV4(String username, int age) {
        log.info("username={}, age={}", username, age);
        return "ok";
    }

      @ResponseBody
    @RequestMapping("/request-param-default")
    public String requestParamDefault(
            @RequestParam(required = true, defaultValue = "guest") String username,
            @RequestParam(required = false, defaultValue = "-1") int age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }
}
```

#### @ModelAttribute
 - GET/POST HTML Form 요청시 Form data와 VO 객체 바인딩(매핑) 
 - @ModelAttribute 애노테이션 생략 가능


```java
@Controller
public class ControllerTest {
	
    @ResponseBody
    @GetMapping("/model-attribute-get-test-v1")
    public String modelAttributeGetTestV1(@ModelAttribute HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }

    @ResponseBody
    @GetMapping("/model-attribute-get-test-v2")
    public String modelAttributeGetTestV2(HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }

    @ResponseBody
    @PostMapping("/model-attribute-post-test-v1")
    public String modelAttributePostTestV1(@ModelAttribute HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }

    @ResponseBody
    @PostMapping("/model-attribute-post-test-v2")
    public String modelAttributePostTestV2(HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }
}
```

#### @RequestBody
 - GET/POST Content-Type:application/json & VO 객체 바인딩(매핑)
 - JSON요청 -> HTTP 메시지 컨버터 > 객체
 - @RequestBody 애노테이션 생략 안됨

#### @ResponseBody
 - @ResponseBody 응답 : 객체 -> HTTP 메시지 컨버터 -> JSON응답
 - @ResponseBody 애노테이션을 적용하면 객체가 JSON 타입으로 반환하므로, 그 외 타입 반환 시 다른 처리가 필요하므로 유의해야합니다.
 - View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력

#### @Controller & return String
 - ViewResolver를 찾고 해당 문자열(논리이름)에 해당하는 물리적 경로로 변경 후 뷰로 이동합니다.

#### @Controller & @ResponseBody & return String
 - ViewResolver 찾지 않고 문자열 자체가 return 됩니다.
 - 2개 애노테이션을 사용하지 않고 @RestController로 해결 가능 (API 주로 사용)

#### @Controller & @ResponseBody & return VO
- json변환해서 나갑니다.

### HTTP 메시지 컨버터
- @ResponseBody를 사용해서 ```HTTP Body```에 문자 내용을 직접 반환할 때 viewResolver 대신에 HttpMessageConverter가 동작합니다.
- 기본 문자 처리 : StringHttpMessageConverter
- 기본 객체 처리 : MappingJackson2HttpMessageConverter
- 클라이언트의 HTTP Accecpt 해더와 서버의 컨트롤러 반환타입 정보를 조합해서 HttpMessageConverter가 선택됩니다.

#### HTTP 메시지 컨버터 인터페이스
- 스프링 부트는 다양항 메시지 컨버터를 제공합니다. 대상 클래스 타입(Class<?>)과 미디어 타입(MediaType)을 체크해서 사용여부를 결정합니다.
- canRead(), canWrite() : class와 mediaType을 지원하는 지 체크하는 메소드
- read(), write() : message 읽고 쓰는 메소드

```java
/**
 * Strategy interface for converting from and to HTTP requests and responses.
 */
public interface HttpMessageConverter<T> {

	/**
	 * Indicates whether the given class can be read by this converter.
	 * @param clazz the class to test for readability
	 * @param mediaType the media type to read (can be {@code null} if not specified);
	 * typically the value of a {@code Content-Type} header.
	 * @return {@code true} if readable; {@code false} otherwise
	 */
	boolean canRead(Class<?> clazz, @Nullable MediaType mediaType);

	/**
	 * Indicates whether the given class can be written by this converter.
	 * @param clazz the class to test for writability
	 * @param mediaType the media type to write (can be {@code null} if not specified);
	 * typically the value of an {@code Accept} header.
	 * @return {@code true} if writable; {@code false} otherwise
	 */
	boolean canWrite(Class<?> clazz, @Nullable MediaType mediaType);


	List<MediaType> getSupportedMediaTypes();

	default List<MediaType> getSupportedMediaTypes(Class<?> clazz) {
		return (canRead(clazz, null) || canWrite(clazz, null) ?
				getSupportedMediaTypes() : Collections.emptyList());
	}

	/**
	 * Read an object of the given type from the given input message, and returns it.
	 */
	T read(Class<? extends T> clazz, HttpInputMessage inputMessage)
			throws IOException, HttpMessageNotReadableException;

	/**
	 * Write a given object to the given output message.
	 */
	void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage)
			throws IOException, HttpMessageNotWritableException;

}
```

#### 스프링 부트 기본 메시지 컨버터
- 대상 클래스 타입(Class<?>)과 미디어 타입(MediaType)을 체크해서 사용여부를 결정하고 아래 순서로 만족하지 않으면 다음 메시지 컨버터로 우선순위가 넘어갑니다.
- 주요 메시지 컨버터
0 = ByteArrayHttpMessageConverter
1 = StringHttpMessageConverter
2 = MappingJackson2HttpMessageConverter

- ByteArrayHttpMessageConverter : byte[] 데이터를 처리한다.
	- 클래스 타입: byte[] , 미디어타입: &#42;/&#42; , 
	- 요청 예) @RequestBody byte[] data 
	- 응답 예) @ResponseBody return byte[] 쓰기 미디어타입 application/octet-stream 
- StringHttpMessageConverter : String 문자로 데이터를 처리한다. 
	- 클래스 타입: String , 미디어타입: &#42;/&#42;
	- 요청 예) @RequestBody String data 
	- 응답 예) @ResponseBody return "ok" 쓰기 미디어타입 text/plain 
- MappingJackson2HttpMessageConverter : application/json 
	- 클래스 타입: 객체 또는 HashMap , 미디어타입 application/json 관련 
	- 요청 예) @RequestBody HelloData data 
	- 응답 예) @ResponseBody return helloData 쓰기 미디어타입 application/json 관련 

#### HTTP 요청 데이터 읽기
- HTTP 요청이 오고, 컨트롤러에서 @RequestBody , HttpEntity 파라미터를 사용합니다.
- 메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 canRead() 를 호출합니다.
- canRead() 조건을 만족하면 read() 를 호출해서 객체 생성하고, 반환합니다.
- canRead() 조건 :
	- 대상 클래스 타입을 지원하는 지?   
	  예) @RequestBody 의 대상 클래스 ( byte[] , String , HelloData )    
	- ```HTTP 요청의 Content-Type``` 미디어 타입을 지원하는 지?   
  	  예) text/plain , application/json , &#42;/&#42;   


#### HTTP 응답 데이터 생성
- 컨트롤러에서 @ResponseBody, HttpEntity로 값이 변환됩니다.
- 메시지 컨버터가 메시지를 쓸 수 있는 지 확인하기 위해 canWrite()를 호출합니다.
- canWrite() 조건을 만족하면 write() 호출해서 HTTP 응답 메시지 바디에 데이터를 생성합니다.
- canWrite() 조건 : 
	- 대상 클래스 타입을 지원하는 지?    
	  예) return의 대상 클래스 ( byte[] , String , HelloData )
	- ```HTTP 요청의 Accept```미디어 타입을 지원하는가.(더 정확히는
	  예) text/plain , application/json , &#42;/&#42;


#### HTTP 메시지 컨버터 위치
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/232447149-126b636e-ef8b-4416-9769-5a852a105a74.png">





### 요청 매핑 핸들러 어댑터 구조
- Spring MVC 구조
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/232443524-74b40fb0-2bf2-48b5-aa3d-095902c5181a.png">

- RequestMappingHandlerAdapter 동작방식
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/232443620-0d615fec-3aaa-45cb-9b16-48a4d632e9b1.png">

#### ArgumentResolver (요청처리)
- 애노테이션 기반의 컨트롤러는 매우 다양한 파라미터를 사용할 수 있었다.
- HttpServletRequest, Model, @RequestParam , @ModelAttribute, @RequestBody, HttpEntity 같은 HTTP 메시지를 처리하는 부분까지 파라미터를 유연하게 처리할 수 있는 이유가 바로 ArgumentResolver 덕분이다.
- ```동작원리``` : 애노테이션 기반 컨트롤러를 처리하는 RequestMappingHandlerAdapter가 ArgumentResolver 호출해서 컨트롤러(핸들러)가 필요로 하는 **다양한 파라미터의 값(객체)를 생성**합니다. 그 후 컨트롤러를 호출하면서 값을 넘겨줍니다.  

#### ArgumentResolver -> HTTP 메시지 컨버터(요청)
- @RequestBody를 처리하는 ArgumentResolver가 있고, HttpEntity를 처리하는 ArgumentResolver가 있습니다.
- 이 ArgumentResolver들이 HTTP 메시지 컨버터를 사용해서 ```필요한 객체를 생성```하는 것이다.

#### ReturnValueHandler (응답처리)
- HandlerMethodReturnValueHandler를 줄여서 ReturnValueHandler라 부른다. ArgumentResolver 와 비슷한데, 이것은 응답 값을 변환하고 처리한다.
- 컨트롤러에서 String으로 뷰 이름을 반환해도, 동작하는 이유가 바로 ReturnValueHandler 덕분이다.
- 스프링은 10여개가 넘는 ReturnValueHandler 를 지원한다. 예) ModelAndView , @ResponseBody , HttpEntity , String

#### ReturnValueHandler -> HTTP 메시지 컨버터(응답)
- @ResponseBody와 HttpEntity를 처리하는 ReturnValueHandler가 있습니다. HTTP 메시지 컨버터를 호출해서 ```응답 결과```를 만듭니다.


## 웹 페이지 만들기
- IntelliJ 기본 설정 
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/231683597-601d1af2-35b9-4808-9579-b7b033c5cf91.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/231683478-20abac73-8b56-4d67-993f-102ebec320ff.png">

#### addForm.html에서 item.html post요청 시 오류 이유?
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/231994141-c9e3a9cb-86aa-4bf2-a451-c44a9164cb5a.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/231994232-75af5f45-d533-4a6d-bd7e-67882b0a08bd.png">
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/231994367-c434e0c6-a4e3-49dd-a9e7-eb953d582552.png">

- HTTP method [post]는 http message body에 데이터를 담아 서버로 요청 데이터를 전달하는데 핸들러 어댑터의 url 매핑이 안됨.  
- 즉, 처리하는 컨트롤러가 없기 때문에 에러 페이지가 나왔음.  
- Get 요청이였다면 해당 url 매핑이 안되더라도 정적 리소스 [src/main/resource/static] 경로로 처리가 됨.   

#### 정적 리소스 경로
- src/main/resource/static
- src/main/resource/static/basic/hello-form.html 다음 경로에 파일이 있으면 
- http://localhost:8080/basic/hello-form.html 웹 브라우저에서 다음과 같이 실행 합니다.
- 정적 리소스는 파일의 변경 없이 그대로 서비스 하는 것 입니다. (동적 리소스가 아님)


### thymeleaf 템플릿엔진 동작 후 경로
- src/main/resources/templates 경로에서 viewName .html 등 매핑됩니다.
<img width="100%" alt="image" src="https://user-images.githubusercontent.com/28051638/231961952-d494e1a1-84b2-4d5a-b893-71db145bb359.png">


#### 타임리프 핵심
- 서버 사이드에서 렌더링 되므로 기존 것을 대체 합니다.

```<link th:href="@{/css/bootstrap.min.css}" href="../../static/css/bootstrap.min.css" rel="stylesheet">```
	  
- 타임리프 자체가 서버 사이드에서 렌더링 되는 거다 보니 [http://localhost:8080/css/bootstrap.min.css]로 호출이 됩니다.
- 해당 [url]에 해당하는 핸들러 어댑터이 없기 때문에 main/resources/static에 있는 css로 찾아가게 됩니다.


#### 폴더구조에 대해서
- src/main/java,resources로 나뉩니다.
- java는 .java 파일, Db관련 파일
- resources static/templates .css/.html/설정파일 등이 존재합니다.

#### 경로
-  ./ : 현재경로
- ../ : 상위 디렉터리
-   / : 최상루트

#### 상대경로 vs 절대경로
- 상대경로 : 현재 디렉토리(비교 대상)을 기준으로 작성된 경로
- 절대경로 : 최상위 디렉토리가 반드시 포함된 경로


#### @RequiredArgsConstructor
- 생성자 의존관계 자동 주입을 해주는 애노테이션입니다.
- 스프링 빈이 등록된 구현체를(ItemRepository를 구현(@Repository)) 의존성 주입해줍니다.
- final 붙은 모든 변수의 생성자를 만들어 줍니다.

```java
public class BasicItemController {

    private final ItemRepository itemRepository;
	
    @Autowird // 1개만 있을 때 생략 가능.
    public BasicItemController(ItemRepository itemRepository) { 
        this.itemRepository = itemRepository;
    }
}

@RequiredArgsConstructor  
public class BasicItemController {

   private final ItemRepository itemRepository;
}
```


#### 컨트롤 URL
- 서버단에서의 controller에 매핑되는 경로(url)를 표현합니다.
- API로 설계를 한다면 HTTP Method(GET,POST,DELETE,PATCH,PUT)만 다르게 하여 동일한 /url 형식으로 나뉠 수 있습니다. ex)/members, /members/{id} 
- HTML FROM GET/POST만 지원을 하는 제약 사항을 해결하기 위해 삭제, 수정 등에 대한 **동사**된 명명을 추가하여 **리소스의 경로**로 사용하므로 구분할 수 있게 함.
- ex)/members, /members/{id}, /members/add, /members/{id}/edit, /members/{id}/delete 등등

#### 컬렉션(Collection)
- Java Collection 을 뜻하는 게 아닙니다.
- 서버가 관리하는 리소스 디텍토리로 리소스의 URL를 생성하고 관리합니다.
- 위의 컨트롤 URL를 보면 컬렉션은 /members 입니다.
- 큰 기능의 단위를 URL로 동사화하여 형식을 맞춘 디렉토리입니다.


#### @PathVariable
- HTTP API, HTML FORM 요청 시 리소스 경로에 식별자를 넣는 스타일을 선호합니다.
- ex)/order/list/1, /order/cancel/1 등
- @PathVariable 지정된 이름과 파라미터 이름이 같으면 생략할 수 있습니다.

```java
    @GetMapping("/order/list/{userId}")
    public String mappingPath(@PathVariable("userId") String data) {
         return "ok";
    }
    
    @GetMapping("/order/list/{userId}")
    public String mappingPath(String userId) { //{userId}식별자와 파라미터명 동일하면 생략가능
         return "ok";
    }    
```

#### @ModelAttribute 2가지 특징
- 요청 파라미터 처리
	- 객체를 생성하고, 요청 파라미터의 값을 프로퍼티 접근법(setter())로 자동 주입해줍니다.
	- @ModelAttribute("Name") name 이름을 생략할 수 있습니다.
		- 이름을 생략하면 모델에 저장할 때 클래스의 첫글자만 소문자로 변경되서 모델에 자동 추가됩니다.
	- @ModelAttribute 애노테이션 생략도 가능합니다. (HTML FORM 경우에만)
- Model 추가
	- Model에 @ModelAttribute로 지정한 객체를 자동으로 넣어줍니다. 
	- model.addAttribute("Name",VoName); 생략 가능합니다.

```java
    // @RequestParam으로 변수 각각 받아서 객체 셋팅
    @PostMapping("/add")
    public String addItemV1(@RequestParam String itemName,
                       @RequestParam int price,
                       @RequestParam Integer quantity,
                       Model model) {

        Item item = new Item();
        item.setItemName(itemName);
        item.setPrice(price);
        item.setQuantity(quantity);

        itemRepository.save(item);
        model.addAttribute("item", item);
        return "basic/item";
    }
    
    //@ModelAttribute 애노테이션 사용해서 한번에 처리
    @PostMapping("/add")
    public String addItemV2(@ModelAttribute("item") Item item, Model model) {
    
        itemRepository.save(item);
        model.addAttribute("item", item); //자동 추가, 생략 가능
        return "basic/item";
    }

    // @ModelAttribute, Model 생략
    @PostMapping("/add")
    public String addItemV4(Item item) {
        itemRepository.save(item);
        return "basic/item";
    }
```

### PRG POST/Redirect/GET
- 현상 : 데이터 등록 후 새로고침 했을 때 중복 등록됨.
- 원인 : 웹 브라우저의 새로고침은 마지막 서버에 전송한 데이터를 다시 전송합니다.
- 해결 : 데이터 등록 후 뷰 템플릿으로 이동하는 게 아니라, 상세 화면으로 리다이렉트 호출해주면 됩니다. 
         상세 화면인 GET /item/{id}가 되는 것으로 새로고침을 해도 상세 화면으로 이동되하게 되므로 해당 현상의 문제를 해결할 수 있습니다.

```java
    @PostMapping("/add")
    public String addItemV5(Item item) {
       itemRepository.save(item);
       return "redirect:/basic/items/" + item.getId();
    }
``` 
 
- URL에 변수를 더하는 것은 URL 인코딩이 안되기 때문에 RedirectAttributes 사용해야합니다.
- 타입의 문제도 포함. 동적 파라미터가 숫자형인데 문자형으로 리다이렉트 될 수도 있음.


### RedirectAttributes
- URL인코딩도 해주고, @PathVariable, 쿼리 파라미터까지 처리해줍니다.
- redirect:/basic/items/{itemId} (pathVariable 바인딩: {itemId} 나머지는 쿼리 파라미터로 처리: ?status=true)
- RedirectAttributes시 여러값을 추가할 수 있습니다.

```java
    @PostMapping("/add")
    public String addItemV6(Item item, RedirectAttributes redirectAttributes) {
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId()); //url 인코딩도 자동으로 해준다.
        redirectAttributes.addAttribute("status", true); // 상태코드으로 글의 등록여부 확인
        return "redirect:/basic/items/{itemId}";
    }    
``` 
# 총 요약 정리
## @RequestParam, @ModelAttribute, @RequestBody, @ResponseBody
> - 쿼리 파라미터 (@RequestParam)
> - HTML FORM (@ModelAttribute)
> - 단순 텍스트
> - API JSON (@RequestBody)
> 
## ViewResolver, HttpMessageConverter
> - ViewResolver는 단순 뷰로 반환
> - HttpMessageConverter는 HTTP Body 데이터를 담아서 반환할 때
>  
## Spring MVC 구조 (RequestMappingHandlerAdapter, HTTP Message Converter)
> 1. HTTP 메시지를 처리해서 요청 데이터 생성
> - ArgumentResolver : @RequestBody, HttpEntity
> - HTTP 메시지 컨버터 : 필요한 객체 생성
> 
> 2. controller 호출
> 
> 3. 응답 데이터를 HTTP 메시지에 입력
> - ReturnValeuHandler: @ResponseBody, HttpEntity
> - HTTP 메시지 컨버터 : 응답 데이터 생성
> 
## ArgumentResolver
> - @RequestParam, Model, @RequestBody, HttpEntity
> 
## ReturnValueHandler
> - @ModelAttribute, Model, @ResponseBody, HttpEntity, String
