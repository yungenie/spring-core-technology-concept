
# 🌱 스프링 MVC 2편 - 백엔드 웹 개발 핵심 기술
출저 : [`인프런 김영한 - 스프링 MVC 2편 백엔드 웹 개발 핵심 기술`](https://inf.run/AV7T)

<br>

## 목차
- [타임리프 기본 기능](#타임리프-기본-기능) 
<br>

## 타임리프 기본 기능
### 타임리프 소개
#### SSR 서버 사이드 HTML 렌더링
- 타임리를를 백엔드 서버에서 HTML을 동적으로 렌더링 하는 용도로 사용됩니다.

#### 네츄럴 템플릿
- 순수 HTML을 그대로 유지하면서 뷰 템플릿도 사용할 수 있는 타임리프의 특징을 네츄럴 템플릿(natural templates)이라고 합니다.
- JSP 경우 :  JSP 파일 자체를 그대로 웹 브라우저에 열면 Java소스코드와 HTML 섞여서 정상적인 HTML 결과를 확인하기 어렵습니다.
- Thymeleaf 경우 : HTML 파일 자체를 열면 HTML 정적 소스의 결과를 확인할 수 있습니다. 

#### 스프링 통합 지원
- 스프링과 자연스럽게 통합되고, 다양한 기능을 편리하게 사용할 수 있습니다.


### 텍스트 test,utext
- Controller
```java
@Controller
@RequestMapping("/basic")
public class BasicController {

    @GetMapping("text-basic")
    public String textBasic(Model model) {
        model.addAttribute("data", "Hello <b>Spring!</b>");
        return "basic/text-basic";
    }

    @GetMapping("text-unescaped")
    public String textUnescaped(Model model) {
        model.addAttribute("data", "Hello <b>Spring!</b>");
        return "basic/text-unescaped";
    }
 }    
 ```
 
 - HTML
 ```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<h1>text vs utext</h1>
<ul>
    <li>th:text = <span th:text="${data}"></span></li>
    <li>th:utext = <span th:utext="${data}"></span></li>
</ul>

<h1><span th:inline="none">[[...]] vs [(...)]</span></h1>
<ul>
    <li><span th:inline="none">[[...]] = </span>[[${data}]]</li>
    <li><span th:inline="none">[(...)] = </span>[(${data})]</li>
</ul>

</body>
</html>
 ```
 
#### HTML 태그 속성에 사용하는 경우
- 태그 속성 안에 ```th:text="${data} ```

#### HTML 콘텐츠 영역에 사용하는 경우
- 태그 내용 안에  ```[[${data}]]```

#### HTML 엔티티로 escape 처리 
- HTML에서 사용하는 특수 문자를 HTML 엔티티로 변경하는 것을 이스케이프(escape)라고 합니다.
- 타임리프가 제공하는 th:text, [[...]] 는 기본적으로 이스케이스(escape)를 제공합니다.
- ```예)``` 뒷단에서 태그를 생성해서 뷰로 보내면 데이터가 렌더링 되는 게 아니고 특수문자를 통해서 문자열태그가 만들어 집니다. < - &lt; > - &gt;
- 비교연산 ```> (gt), < (lt), >= (ge), <= (le), ! (not), == (eq), != (neq, ne)```

#### Unescape
- 이스케이프(escape) 기능을 사용하지 않으려면 어떻게 해야할까요?
- th:utext, [(...)]를 사용하면 됩니다.
- th:text -> th:utext, [[...]]  -> [(...)]

#### 비교 결과
<img width="279" alt="image" src="https://user-images.githubusercontent.com/28051638/232420004-101e03fa-c175-480f-b40b-30f2810bb80f.png">


### 변수 SpringEL
-  jsp에서의 jstl 문법과 비슷합니다.

```java
    @GetMapping("/variable")
    public String variable(Model model) {
        User userA = new User("yunjin", 10);
        User userB = new User("younggeun", 20);

        List<User> list = new ArrayList<>();
        list.add(userA);
        list.add(userB);

        Map<String, User> map = new HashMap<>();
        map.put("userA", userA);
        map.put("userB", userB);

        model.addAttribute("user", userA);
        model.addAttribute("users", list);
        model.addAttribute("userMap", map);

        return "basic/variable";
    }
```

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<h1>SpringEL 표현식</h1>
<ul>Object
    <li>${user.username} =    <span th:text="${user.username}"></span></li>
    <li>${user['username']} = <span th:text="${user['username']}"></span></li>
    <li>${user.getUsername()} = <span th:text="${user.getUsername()}"></span></li>
</ul>
<ul>List
    <li>${users[0].username}    = <span th:text="${users[0].username}"></span></li>
    <li>${users[0]['username']} = <span th:text="${users[0]['username']}"></span></li>
    <li>${users[0].getUsername()} = <span th:text="${users[0].getUsername()}"></span></li>
</ul>

List for each
<ul th:each="user : ${users}">
    <li>${user.username}    = <span th:text="${user.username}"></span></li>
    <li>${user.age}    = <span th:text="${user.age}"></span></li>
</ul>

반복 상태 유지 지정한 변수명 + Stat (userStat 생략도 가능)
  <tr th:each="user, userStat : ${users}">
    <td th:text="${userStat.count}">username</td>
    <td th:text="${user.username}">username</td>
    <td th:text="${user.age}">0</td>
    <td>
      index = <span th:text="${userStat.index}"></span>
      count = <span th:text="${userStat.count}"></span>
      size = <span th:text="${userStat.size}"></span>
      even? = <span th:text="${userStat.even}"></span>
      odd? = <span th:text="${userStat.odd}"></span>
      first? = <span th:text="${userStat.first}"></span>
      last? = <span th:text="${userStat.last}"></span>
      current = <span th:text="${userStat.current}"></span>
    </td>
  </tr>
    
<ul>Map
    <li>${userMap['userA'].username} =  <span th:text="${userMap['userA'].username}"></span></li>
    <li>${userMap['userA']['username']} = <span th:text="${userMap['userA']['username']}"></span></li>
    <li>${userMap['userA'].getUsername()} = <span th:text="${userMap['userA'].getUsername()}"></span></li>
</ul>

Map for each
<ul th:each="usermaplist : ${userMap}">
    <li th:each="list : ${usermaplist.value}">
        <span th:text="${list.username}"></span>
        <span th:text="${list.age}"></span>
    </li>
</ul>

<h1>지역 변수 - (th:with)</h1>
<div th:with="first=${users[0]}">
    <p>처음 사람의 이름은 <span th:text="${first.username}"></span></p>
</div>

</body>
</html>
```

- 결과
```
    SpringEL 표현식
    Object
    ${user.username} = yunjin
    ${user['username']} = yunjin
    ${user.getUsername()} = yunjin
    List
    ${users[0].username} = yunjin
    ${users[0]['username']} = yunjin
    ${users[0].getUsername()} = yunjin
    List for each
    ${user.username} = yunjin
    ${user.age} = 10
    ${user.username} = younggeun
    ${user.age} = 20
    Map
    ${userMap['userA'].username} = yunjin
    ${userMap['userA']['username']} = yunjin
    ${userMap['userA'].getUsername()} = yunjin
    Map for each
    yunjin 10
    younggeun 20
    지역 변수 - (th:with)
    처음 사람의 이름은 yunjin
```
### 리터럴 literals
- 리터럴은 소스 코드 상에 고정된 값을 말하는 용어 입니다.
- 문자 : 'value'
- 숫자 : 10
- 논리 : true, false
- null : null
- 타임리프에서 리터럴은 '(작은 따옴표)로 감싸야 합니다.
- 리터럴 대체 문법 th:text="|text ${value}|" ||를 사용해서 '(작은 따옴표) 대신 사용할 수 있습니다.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>리터럴</h1>
<ul>
    <!--주의! 다음 주석을 풀면 예외가 발생함-->
    <!--    <li>"hello world!" = <span th:text="hello world!"></span></li>-->
    <li>'hello' + ' world!' = <span th:text="'hello' + ' world!'"></span></li>
    <li>'hello world!' = <span th:text="'hello world!'"></span></li>
    <li>'hello ' + ${data} = <span th:text="'hello ' + ${data}"></span></li>
    <li>리터럴 대체 |hello ${data}| = <span th:text="|hello ${data}|"></span></li>
</ul>

</body>
</html>
```

### 타임리프 파서 주석
- 타임리프 파서 주석은 타임리프의 진짜 주석이다. 렌더링에서 주석 부분을 제거합니다.
- 페이지 소스 보기를 하면 주석 자체가 안보입니다.

```html
<h1>타임리프 파서 주석</h1>
<!--/* [[${data}]] */-->
```

## 타임리프 스프링 통합과 폼
- 스프링에서 타임리프는 기본적으로 html을 지원해줍니다.
- 변경을 원하면 application.properties 설정 파일에서 spring.thymeleaf.suffix 속성을 수정해줍니다.

### 타임리프 - 입력 폼 처리
- th:object를 적용하려면 먼저 해당 오브젝트 정보를 컨트롤러단에서 넘겨줘야 합니다. 
- 넘겨받은 오브젝트 Key 값으로 ${key.객체속성} 대신에 ```th:field="*{객체속성}"```를 사용해서 매핑할 수 있습니다.
- th:field는 name을 자동으로 추가해줍니다. (domain(vo)와 일치하는 속성을 th:field에 매)

```html
    <form action="item.html" th:action th:object="${item}" method="post">
        <div>
            <label for="itemName">상품명</label>
            <input type="text" id="itemName" th:field="*{itemName}" class="form-control" placeholder="이름을 입력하세요">
        </div>
        <div>
            <label for="price">가격</label>
            <input type="text" id="price" th:field="*{price}" class="form-control" placeholder="가격을 입력하세요">
        </div>
        <div>
            <label for="quantity">수량</label>
            <input type="text" id="quantity" th:field="*{quantity}" class="form-control" placeholder="수량을 입력하세요">
        </div>
    </form>

```

#### 타임리프 - 체크 박스
- 체크박스는 값이 선택되지 않을 때 false가 아닌 null로 넘어오는 이슈가 있어 타임리프를 사용하면 **히든 필드를 자동으로 생성**해줍니다.
- 체크박스는 단일/멀티(여러개 선택 가능) 2가지 종류로 사용할 수 있고, 단일/멀티 둘다 선택하지 않아도 됩니다.

```html
<!-- 타임리프 적용 전 -->
<input type="checkbox" id="open" name="open" class="form-check-input">
<input type="checkbox" name="_open" value="on"/>

<!-- 타임리프 적용 후 -->
<input type="checkbox" id="open" th:field="*{open}" class="form-check-input">
```
#### 타임리프 - 라디오 버튼
- 라디오 버튼은 멀티 선택이 안됩니다. 한번 선택하면 다른 라디오 버튼을 선택해서 취소 해야 합니다.
- 멀티 체크박스는 같은 이름의 여러 체크박스를 만들 수 있습니다. HTML 태그 속성에 **name은 같아도 되지만, id 값은 달라**야 합니다.
- 따라서 체크박스를 each 루프 안에서 반복해서 만들 때 임의로 1, 2, 3 숫자를 붙여 줄 수 있습니다.
- ```th:for="${#ids.prev('itemType')}"``` 를 사용하면 id="itemType1", id="itemType2", id="itemType3" 동적으로 생성되는 id 값을 사용할 수 있습니다.

```html
<div>
     <div>상품 종류</div>
     <div th:each="type : ${itemTypes}" class="form-check form-check-inline">
         <input type="radio" th:field="*{itemType}" th:value="${type.name()}" class="form-check-input">
         <label th:for="${#ids.prev('itemType')}" th:text="${type.description}" class="form-check-label">
             BOOK
         </label>
     </div>
</div>
```        
        
        

#### 타임리프 - 셀렉트 박스
- @ModelAttribute 객체가 담긴 key 값을 사용해서 옵션의 글과 값을 셋팅할 수 있습니다.


```html
<div>
    <div>배송 방식</div>
    <select th:field="*{deliveryCode}" class="form-select">
        <option value="">==배송 방식 선택==</option>
        <option th:each="deliveryCode : ${deliveryCodes}" th:value="${deliveryCode.code}"
                th:text="${deliveryCode.displayName}">FAST</option>
    </select>
</div>
```
## 메시지, 국제화
### 메시지
- 여러 HTML 파일에 하드코딩 되어 있는 label 단어를 관리할 수 있습니다.
- 다양한 메시지를 하나의 설정 파일에서 관리하도록 하는 기능을 메시지 기능이라 합니다.
- messages.properties 라는 메시지 관리용 파일을 만들고 변수에 단어를 설정합니다.

```  
- messages.properties
item=상품
item.id=상품 ID
item.itemName=상품명
item.price=가격
item.quantity=수량

- html
<label for="itemName" th:text="#{item.itemName}"></label>
```  

### 국제화
- messages.properties을 각 나라별로 별도로 관리하면 서비스를 국제화 할 수 있습니다.

```  
messages.properties :기본 값으로 사용(한글)
messages_en.properties : 영어 국제화 사용
```  

> 스프링은 기본적인 메시지와 국제화 기능을 모두 제공한다. 그리고 타임리프도 스프링이 제공하는 메시지와 국제화 기능을 편리하게 통합해서 제공합니다.

#### 국제화가 아닌 사용자가 언어 선택을 하게 할 때
- 한국에서 접근한 것인지 영어에서 접근한 것인지는 인식하는 방법은 HTTP accept-language 해더 값을 사용하거나 사용자가 직접 언어를 선택하도록 하고, 쿠키 등을 사용해서 처리하면 된다.
- 그러므로, 사용자가 언어를 선택하게 해서 쿠키나 세션에 언어설정 값을 넣어놓고 사용할 수 있도록 할 수도 있다.



