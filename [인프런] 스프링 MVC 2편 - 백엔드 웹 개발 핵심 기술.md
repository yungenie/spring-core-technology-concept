
# 🌱 스프링 MVC 2편 - 백엔드 웹 개발 핵심 기술
출저 : [`인프런 김영한 - 스프링 MVC 2편 백엔드 웹 개발 핵심 기술`](https://inf.run/AV7T)

<br>

## 목차
- [타임리프 기본 기능](#타임리프-기본-기능) 
- [타임리프 스프링 통합과 폼](#타임리프-스프링-통합과-폼) 
- [메시지, 국제화](#메시지-국제화) 
- [검증1 Validation](#검증1---validation)
- [검증2 Bean Validation](#검증2---bean-validation) 
- [로그인 처리1 - 쿠키,세션](#로그인-처리1---쿠키세션)
- [로그인 처리1 - 쿠키,세션 정리](#로그인-처리1---쿠키세션-정리)
- [로그인 처리2 - 필터, 인터셉터](#로그인-처리2---필터-인터셉터)
- [예외 처리와 오류 페이지](#예외-처리와-오류-페이지)
- [API 예외 처리](#API-예외-처리)
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

<br>

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

<br>

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
- messages.properties을 각 나라별로 별도로 관리하면 서비스를 국제화 할 수 있습니다. (Local에 맞춰서 파일명 변경)

```  
messages.properties :기본 값으로 사용(한글)
messages_en.properties : 영어 국제화 사용
```  

> 스프링은 기본적인 메시지와 국제화 기능을 모두 제공한다. 그리고 타임리프도 스프링이 제공하는 메시지와 국제화 기능을 편리하게 통합해서 제공합니다.

#### 국제화가 아닌 사용자가 언어 선택을 하게 할 때
- 한국에서 접근한 것인지 영어에서 접근한 것인지는 인식하는 방법은 HTTP accept-language 해더 값을 사용하거나 사용자가 직접 언어를 선택하도록 하고, 쿠키 등을 사용해서 처리하면 된다.
- 그러므로, 사용자가 언어를 선택하게 해서 쿠키나 세션에 언어설정 값을 넣어놓고 사용할 수 있도록 할 수도 있다.

<br>

## 검증1 - Validation

### 검증 v1 - 직접처리
#### 검증에 대해서 조건문으로 처리

```java
     /**
     * 검증V1 (검증 직접 처리)
     * - 검증시 오류가 발생하면 어떤 검증에서 오류가 발생했는 지 Map자료구조에 정보(필드,에러문구)를 담아 타임리프를 이용해 처리를 함.
     * - 문제점 : 타입오류 처리가 안되기 때문에 타입오류된 고객의 입력 데이터는 관리되지 않는다.
     */
    @PostMapping("/add")
    public String addItem(@ModelAttribute Item item, RedirectAttributes redirectAttributes, Model model) {
        
        //model.addAttribute("item", item); // 자동으로 추가해줌.
        //검증에 실패했을 때 재사용 가능하게끔 - 입력했던 값 그대로 다시 화면에 노출 (데이터형에 해당할 떄)

        //검증 오류 결과를 보관
        Map<String, String> errors = new HashMap<>();

        //검증 로직
        if (StringUtils.hasText(item.getItemName())) {
            errors.put("itemName", "상품 이름은 필수입니다.");
        }
        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
            errors.put("price", "가격은 1,000 ~ 1,000,000 까지 허용합니다.");
        }
        if (item.getQuantity() == null || item.getQuantity() >= 9999) {
            errors.put("quantity", "수량은 최대 9,999 까지 허용합니다.");
        }

        //특정 필드가 아닌 복합 룰 검증
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                errors.put("globalError", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice);
            }
        }

        //검증에 실패하면 다시 입력 폼으로
        if (hasError(errors)) {
            log.info("errors = {} ", errors);
            model.addAttribute("errors", errors); // 착각함. model.addAttribute(key, value) map 형태로 요청한 data이미 model에 담겨져 있음.
            return "validation/v1/addForm";
        }

        //성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v1/items/{itemId}";
    }
```
- 자료구조를 통한 직접 오류 처리
- 검증시 오류가 발생하면 어떤 검증에서 오류가 발생했는 지 Map자료구조에 정보(필드, 에러문구)를 담아 타임리프를 이용해 처리함.

```html
<label for="itemName" th:text="#{label.item.itemName}">상품명</label>
<input type="text" id="itemName" th:field="*{itemName}" th:class="${errors?.containsKey('itemName')} ? 'form-control field-error' : 'form-control'" class="form-control" placeholder="이름을 입력하세요">
<div class="field-error" th:if="${errors?.containsKey('itemName')}" th:text="${errors['itemName']}">상품명 오류</div>
            
-> 변경 
<label for="itemName" th:text="#{label.item.itemName}">상품명</label>
<input type="text" id="itemName" th:field="*{itemName}" th:classappend="${error?.containsKey('itemName')} ? 'field-error' : _ " class="form-control" placeholder="이름을 입력하세요">
<div class="field-error" th:if="${errors?.containsKey('itemName')}" th:text="${errors['itemName']}">상품명 오류</div>
```

- errors?은 errors NullPointException 발생하는 대신, null을 반환하는 문법이다.
- classappend 사용해서 조건문에 해당하면 기존 class 추가하고 조건에 해당하지 않으면 _  기존 class 적용
- map의 value를 가져올 때 ${errors['key']} 

#### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/98eaa3f2-353b-424c-bae7-00f2057706a6">
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/00e3d8ba-9451-4158-8bbd-fe72839ef091">

##### 검증1 문제점
- `타입 오류 처리`가 안되 컨트롤러 호출이 되지 않습니다.
- 스프링 MVC에서 컨트롤러 진입하기도 전에 404 예외 발생하면서 오류 페이지를 띄워줍니다.
- 타입의 오류로 데이터 바인딩 실패된 에러 결과를 담는 무언가가 필요합니다. 
- 타입오류 처리가 안되기 때문에 타입오류된 고객의 입력 데이터는 관리되지 않는다.

#### 정리
> 고객이 입력한 데이터에 타입 오류가 발생하면 컨트롤러 호출되지 않아 오류 페이지로 이동합니다. 고객에게 타입 에러에 대한 결과를 알려줘야 합니다.

<br>

### 검증 v2 - BindingResult
#### 검증에 대해서 스프링과 타임리프 통합으로 제공해주는 BindingResult객체로 처리

```java
    /**
     * 검증v2 (BindingResult)
     * - 객체에 타입 오류 등으로 바인딩이 실패하는 경우에도 해당 에러 결과를 보여줍니다.
     * - 문제점 : 바인딩 실패된 데이터에 대해서는 결과 및 입력 데이터가 유지되지만, 로직 검증 실패된 데이터는 사라진다.
     */
    @PostMapping("/add")
    public String addItem(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
        // BindingResult는 도메인에 바인딩된 결과가 담깁니다. @ModelAttribute 다음 순서에 위치해야 한다.

        //검증 로직 (필드 에러 처리 - FieldError(), FieldError는 ObjectError의 자손)
        if (!StringUtils.hasText(item.getItemName())) {
            bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수 입니다."));
        }
        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
            bindingResult.addError(new FieldError("item", "price", "가격은 1,000 ~ 1,000,000 까지 허용합니다."));
        }
        if (item.getQuantity() == null || item.getQuantity() >= 9999) {
            bindingResult.addError(new FieldError("item", "quantity", "수량은 최대 9,999 까지 허용합니다."));
        }

        //특정 필드가 아닌 복합 룰 검증 (글로벌 에러 처리 - ObjectError)
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
            }
        }

        //검증에 실패하면 다시 입력 폼으로
        if (bindingResult.hasErrors()) {
            log.info("errors={} ", bindingResult);
            //model.addAttribute("errors", errors); //bindingResult는 자동으로 view에 같이 넘어간다.
            return "validation/v2/addForm";
        }

        //성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v2/items/{itemId}";
    }
```
- BindingResult는 스프링이 제공하는 `검증 오류를 보관하는 객체` 입니다.
- BindingResult, FieldError, ObjectError를 사용해서 오류 메시지를 처리합니다.
- BindingResult가 있으면 @ModelAttribute에 **데이터 바인딩 오류가 발생해도 BindingResult에 FieldError/ObjectError를 담아서 컨트롤러를 정상 호출**합니다.
- 도메인에 바인딩된 결과가 담겨 BindingResult 파라미터는 **@ModelAttribute 다음 순서에 위치**해야 합니다. 순서가 중요합니다.
- BindingResult는 model에 담지 않아도 자동으로 view에 같이 넘어간다.


##### BindingResult 설명
- BindingResult 는 인터페이스이고, Errors 인터페이스를 상속받고 있다. 
- **실제 넘어오는 구현체는 BeanPropertyBindingResult 라는 것인데, 둘다 구현하고 있으므로 BindingResult 대신에 Errors 를 사용해도 된다. **
- Errors 인터페이스는 단순한 오류 저장과 조회 기능을 제공한다. 
- BindingResult 는 여기에 더해서 추가적인 기능들을 제공한다. 
- addError() 도 BindingResult 가 제공하므로 여기서는 BindingResult 를 사용하자. 주로 관례상 BindingResult 를많이 사용한다.


```html
<div class="container">

    <div class="py-5 text-center">
        <h2 th:text="#{page.addItem}">상품 등록</h2>
    </div>

    <form action="item.html" th:action th:object="${item}" method="post">

        <div th:if="${#fields.hasGlobalErrors()}">
            <p class="field-error" th:each="err : ${#fields.globalErrors()}" th:text="${err}">글로벌 오류 메시지</p>
        </div>
        <!--/* th:each="err : ${#fields.globalErrors()}" : 오류가 담긴 컬렉션이 each 돌면서 모두 출력 */-->
        <div>
            <label for="itemName" th:text="#{label.item.itemName}">상품명</label>
            <input type="text" id="itemName" th:field="*{itemName}"
                   th:errorclass="field-error" class="form-control" placeholder="이름을 입력하세요">
            <div class="field-error" th:errors="*{itemName}">
                상품명 오류22
            </div>
            <!--/* th:errors="*{itemName}" : 에러가 있으면 출력, 없으면 출력 안함 */-->
            <!--/* th:field="*{itemName}" : 해당 필드에 해당하는 오류가 있으면 뒤에 에러태그들 작동 th:errorclass="field-error" : 에러가 나면 적용, 안나면 적용 안함 */-->
        </div>
        <div>
            <label for="price" th:text="#{label.item.price}">가격</label>
            <input type="text" id="price" th:field="*{price}"
                   th:errorclass="field-error" class="form-control" placeholder="가격을 입력하세요">
            <div class="field-error" th:errors="*{price}">
                가격 오류
            </div>
        </div>

        <div>
            <label for="quantity" th:text="#{label.item.quantity}">수량</label>
            <input type="text" id="quantity" th:field="*{quantity}"
                   th:errorclass="field-error" class="form-control" placeholder="수량을 입력하세요">
            <div class="field-error" th:errors="*{quantity}">
                수량 오류
            </div>

        </div>

        <hr class="my-4">

        <div class="row">
            <div class="col">
                <button class="w-100 btn btn-primary btn-lg" type="submit" th:text="#{button.save}">상품 등록</button>
            </div>
            <div class="col">
                <button class="w-100 btn btn-secondary btn-lg"
                        onclick="location.href='items.html'"
                        th:onclick="|location.href='@{/validation/v2/items}'|"
                        type="button" th:text="#{button.cancel}">취소</button>
            </div>
        </div>

    </form>

</div> <!-- /container -->
```
- 타임리프 스프링 검증 오류 통합 기능
    - 스프링의 `BindingResult를 활용해서 편리하게 검증 오류를 표현하는 기능으로 타임리프와 통합해서 사용`할 수 있습니다.
    - #fields : BindingRessult가 제공하는 검증 오류에 접근할 수 있습니다.
    - th:errors : 해당 필드에 오류가 있는 경우 태그를 출력합니다. (객체 속성의 에러가 발생하면 오류 문구 출력)
    - th:errorclass : th:field에서 지정한 필드에 오류가 있으면 class 정보를 추가합니다. (객체 속성의 에러가 발생하면 class 속성 추가)


#### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/51b8d55c-dc25-4fd7-acc8-bdb8f9630523">

##### BindingResult 문제점
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/32e234c0-bb6a-4980-8f51-667af67cb993">
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/1dcca045-1c87-4c57-bc79-a4c3e8d0fb01">

- BindingResult를 적용해서 타입오류로 바인딩 실패에 대한 에러내용과 해당 입력문자는 유지되도록 수정을 했습니다.
- 그러나, 타입 오류가 아닌 검증 오류 발생 시 고객이 입력한 문자가 사라지고 본인이 어떤 내용을 입력해서 오류가 발생했는지 이해하기 어렵다.
- 고객이 입력한 값도 어딘가에 별도로 관리가 되어야 한다.

#### 정리
> BindingResult의 오류 처리는 2가지 존재합니다. 첫번째는 바인딩 실패(타입 미스매치), 두번째는 비지니스 로직 검증 체크로 크게 나눌 수 있습니다. 또한 바인딩 실패한 입력 데이터만 유지하는 게 아닌, 로직 검증 실패에 대해서도 사용자 입력 값을 유지해야 합니다.

<br>

### FieldError
#### FieldError 객체는 오류 발생시 사용자 입력 값을 유지하는 기능을 제공합니다.

```java
    /**
     * 검증v2 (FieldError, rejectedValue)
     * - 오류 발생시 사용자 입력 값이 유지되도록 처리 합니다. (데이터 보존)
     */
    @PostMapping("/add")
    public String addItem(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
        /* FieldError 객체는 오류가 발생한 경우 사용자 입력 값을 보관해줍니다.
        FieldError(objectName, field, rejectedValue, bindingFailure, codes, arguments, defaultMessage)
        objectName : 오류가 발생한 객체 이름
        field : 오류 필드
        rejectedValue : 사용자가 입력한 값(거절된 값) ★
        bindingFailure : 타입 오류 같은 바인딩 실패인지, 검증 실패인지 구분 값
        codes : 메시지 코드
        arguments : 메시지에서 사용하는 인자
        defaultMessage : 기본 오류 메시지
        */

        //검증 로직 - rejectedValue 파라미터에 사용자가 입력한 값(거절된 값)을 추가해줍니다.
        if (!StringUtils.hasText(item.getItemName())) {
            bindingResult.addError(new FieldError("item", "itemName", item.getItemName(), false, null, null, "상품 이름은 필수 입니다."));
        }
        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
            bindingResult.addError(new FieldError("item", "price", item.getPrice(), false, null, null, "가격은 1,000 ~ 1,000,000 까지 허용합니다."));
        }
        if (item.getQuantity() == null || item.getQuantity() >= 9999) {
            bindingResult.addError(new FieldError("item", "quantity", item.getQuantity(), false, null ,null, "수량은 최대 9,999 까지 허용합니다."));
        }

        //특정 필드가 아닌 복합 룰 검증
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.addError(new ObjectError("item",null ,null, "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
            }
        }

        //검증에 실패하면 다시 입력 폼으로
        if (bindingResult.hasErrors()) {
            log.info("errors={} ", bindingResult);
            return "validation/v2/addForm";
        }

        //성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v2/items/{itemId}";
    }
```
- 사용자의 입력 데이터가 컨트롤러의 @ModelAttribute에 바인딩 되는 시점에 오류가 발생되면 모델 객체에 사용자 입력 값을 유지하기 어렵습니다.
- 그래서 바인딩 실패가 나면 입력 값을 별도로 보관하는 FieldError()객체를 통해서 사용자 입력 값을 저장하여 처리합니다.
- FieldError객체의 rejectedValue 파라미터가 바로 오류 발생시 사용자 입력 값을 저장하는 필드입니다.


```html
        <div>
            <label for="price" th:text="#{label.item.price}">가격</label>
            <input type="text" id="price" th:field="*{price}"
                   th:errorclass="field-error" class="form-control" placeholder="가격을 입력하세요">
            <div class="field-error" th:errors="*{price}">
                가격 오류
            </div>
        </div>
```

- 타임리프의 th:field 태그는 정상 상황에서는 모델 객체 값을 사용하지만, 오류가 발생하면 FieldError에서 보관한 값을 사용해서 출력을 해줍니다.

#### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/72da5670-8c95-4eb2-9169-926d7b49d8db">
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/481d9942-9d23-40cd-9eae-60af58dc8212">

- 스프링과 타임리프를 통해 바인딩 오류나 검증 로직 오류에 대해 사용자 입력 값을 유지하고 에러 메시지를 보여주도록 개선했습니다.

##### FieldError 문제점
- 스프링이 제공하는 시스템상의 기본적인 오류 문구가 사용자에게 전달되므로, 사용자에게 보여주는 메시지가 별도 처리가 필요합니다.

#### 정리
> BindingResult, FieldError를 이용해서 바인딩 오류, 검증 로직 오류에 대해서 사용자에게 메시지를 전달하고 입력 값을 유지하는 방법에 대해서 정리했습니다.

<br>


### 오류 코드와 메시지 처리1

- errors.properties에 등록한 오류 메시지를 사용해서 처리할 수 있다.
- errors.properties 설정 파일을 사용하기 위해서 application.properties에 spring.messages.basename=에 errors 추가 합니다.

#### application.properties 설정
```spring.messages.basename=messages,errors```
- messages.properties 와 errors.properties 둘다 찾아 사용하게 됩니다.

#### errors.properties 설정

```
# 제약조건.객체명.필드명
required.default= 필수로 입력해주세요.
required.item.itemName=상품 이름은 필수입니다.
range.item.price=가격은 {0} ~ {1} 까지 허용합니다.
max.item.quantity=수량은 최대 {0} 까지 허용합니다.
totalPriceMin=가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}
```

```java
public String addItem(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
    /* FieldError 객체는 오류가 발생한 경우 사용자 입력 값을 보관해줍니다.
    FieldError(objectName, field, rejectedValue, bindingFailure, codes, arguments, defaultMessage)
    objectName : 오류가 발생한 객체 이름
    field : 오류 필드
    rejectedValue : 사용자가 입력한 값(거절된 값)
    bindingFailure : 타입 오류 같은 바인딩 실패인지, 검증 실패인지 구분 값
    codes : 메시지 코드 (errors.properties) ★
    arguments : 메시지에서 사용하는 인자
    defaultMessage : 기본 오류 메시지
    */

    //검증 로직
    if (!StringUtils.hasText(item.getItemName())) {
        bindingResult.addError(new FieldError("item", "itemName", item.getItemName(), false, new String[]{"required.item.itemName", "required.default"}, null, null));
    }
    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        bindingResult.addError(new FieldError("item", "price", item.getPrice(), false, new String[]{"range.item.price"}, new Object[]{1000, 1000000}, null));
    }
    if (item.getQuantity() == null || item.getQuantity() >= 9999) {
        bindingResult.addError(new FieldError("item", "quantity", item.getQuantity(), false, new String[]{"max.item.quantity"} ,new Object[]{9999}, null));
    }

    //특정 필드가 아닌 복합 룰 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            bindingResult.addError(new ObjectError("item",new String[]{"totalPriceMin"} ,new Object[]{10000, resultPrice}, null));
        }
    }

    //검증에 실패하면 다시 입력 폼으로
    if (bindingResult.hasErrors()) {
log.info("errors={} ", bindingResult);
        return "validation/v2/addForm";
    }

    //성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);

```
- FieldError(objectName, field, rejectedValue, bindingFailure, codes, arguments, defaultMessage)
- codes는 오류 메시지 코드로 배열(new String[]{"required.item.itemName", "required.default"})에 여러 코드를 담아 순서대로 매칭해서 보여집니다.
- errors.properties에 required.item.itemName 에러코드를 못 찾을 경우 배열의 다음 요소 코드를 찾습니다.
- 배열에 담긴 에러코드를 모두 못찾을 경우 defaultMessage를 보여주고 defaultMessage가 null일 경우 오류 페이지가 뜹니다.

#### 결과확인
<img width="459" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/3c9cbef0-db14-49fa-80fc-6615e9466d00">

<br>

### 오류 코드와 메시지 처리2

```java
    @PostMapping("/add")
    public String addItem(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

        log.info("objectName={}", bindingResult.getObjectName());
        log.info("target={}", bindingResult.getTarget());
        .
        .
        (생략)
    }
```

##### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/3a101cc1-c1bd-410f-be9e-098a93c5085b">

- BindingResult는 검증해야할 객체인 target을 알고 있습니다.

> target 도메인 객체를 알고 있기 때문에 FieldError, ObjectError를 사용하지 않고 BindingResult를 활용해서 자동화 시킬 수 있습니다. (다음 확인)

#### BindingResult rejectValue(), reject()

```java
public String addItemV4(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

log.info("objectName={}", bindingResult.getObjectName());
log.info("target={}", bindingResult.getTarget());

    if (!StringUtils.hasText(item.getItemName())) {
        bindingResult.rejectValue("itemName", "required");
    }
    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        bindingResult.rejectValue("price", "range", new Object[]{1000, 10000000}, null);
    }
    if (item.getQuantity() == null || item.getQuantity() >= 9999) {
        bindingResult.rejectValue("quantity", "max", new Object[]{9999}, null);
    }

    //특정 필드가 아닌 복합 룰 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
        }
    }

    //검증에 실패하면 다시 입력 폼으로
    if (bindingResult.hasErrors()) {
log.info("errors={} ", bindingResult);
        return "validation/v2/addForm";
    }

    //성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v2/items/{itemId}";
}
```

- bindingResult.FieldError()를 직접 다룰 때는 오류 코드를 range.item.price 정의한 코드를 모두 입력했다. 
- bindingResult.rejectValue(), reject()를 사용해서 축약된 오류 코드를 사용할 수 있다. (범용성으로 간단하게 작성할 때)
- rejectValue(), reject() 메소드 내부적으로 FieldError, ObjectError를 자동으로 생성해 `오류 코드를 조합`해서 만들어줍니다.

#### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/2ecb7d1d-464b-4a5c-88cc-fb6be69f2f40">

<br>

### 오류 코드와 메시지 처리3
#### bindingResult.rejectValue(), reject() 축약된 오류 코드 구성 설명

```java
    if (!StringUtils.hasText(item.getItemName())) {
        bindingResult.rejectValue("itemName", "required");
    }
    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        bindingResult.rejectValue("price", "range", new Object[]{1000, 10000000}, null);
    }
    if (item.getQuantity() == null || item.getQuantity() >= 9999) {
        bindingResult.rejectValue("quantity", "max", new Object[]{9999}, null);
    }

    //특정 필드가 아닌 복합 룰 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
        }
    }
```

- bindingResult.rejectValue("itemName", "required");
- 오류 코드와 메시지 처리2에서 bindingResult.rejectValue(), reject()로 축약된 오류 코드를 사용했습니다.
- 오류 코드의 조합은 제약조건.객체명.필드명로 자동으로 조합해서 오류 메시지 코드를 찾습니다. 

#### 오류 코드 범용성
```
#Level1
# 제약조건.객체명.필드명
required.default= 필수로 입력해주세요.~2
required.item.itemName=상품 이름은 필수입니다. ~2
range.item.price=가격은 {0} ~ {1} 까지 허용합니다. ~2
max.item.quantity=수량은 최대 {0} 까지 허용합니다. ~2
totalPriceMin=가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1} ~2

#Level2
required = 필수 값 입니다.
min= {0} 이상이어야 합니다.
range= {0} ~ {1} 범위를 허용합니다.
max= {0} 까지 허용합니다.
```
- Level1처럼 필드별로 세밀하게 작성할 수도 있고, Level2처럼 범용성있게 사용할 수 있습니다.
- 단순하게 만들면 범용성은 좋지만 세밀하게 작성할 수 없고, 세밀하게 만들면 범용성있게 사용을 못하는 차이가 있습니다.
- 가장 좋은 방법은 범용성으로 사용하다가 필요한 부분에서만 세밀하게 작성하는 것이 좋습니다.
- 우선순위는 Level1의 객체명과 필드명의 조합한 메시지가 있는 지 확인하고 없다면 범용성있는 코드를 찾아 적용합니다.
- 스프링은 이러한 기능을 MessageCodesResolver을 통해 지원해줍니다.

<br>

### 오류 코드와 메시지 처리4
#### MessageCodesResolver 동작 방식

```java
public class MessageCodesResolverTest {

    MessageCodesResolver codesResolver = new DefaultMessageCodesResolver();

    @Test
    void messageCodesResolverObject() {
        String[] messageCodes = codesResolver.resolveMessageCodes("required", "item");
        for (String messageCode : messageCodes) {
            System.out.println("messageCode = " + messageCode);
            /*
            * 결과는 디테일한 게 먼저 나오고 범용적인게 나온다.
            * messageCode = required.item
            * messageCode = required
            * */

        }
        
        assertThat(messageCodes).containsExactly("required.item", "required");
    }

    @Test
    void messageCodesResolverField() {
        String[] messageCodes = codesResolver.resolveMessageCodes("required", "item", "itemName", String.class);
        assertThat(messageCodes).containsExactly(
                "required.item.itemName",
                "required.itemName",
                "required.java.lang.String",
                "required"
        );
    }

}
```
- MessageCodesResolver는 검증 오류 메시지 코드를 생성하는 인터페이스 입니다.
- DefaultMessageCodesResolver가 MessageCodesResolver의 구현체입니다.

#### DefaultMessageCodesResolver의 메시지 생성 규칙 및 동작방식
- 필드 오류
    - 필드 오류의 경우 다음 순서로 4가지 메시지 코드 생성
    
```
1.: code + "." + object name + "." + field
2.: code + "." + field
3.: code + "." + field type
4.: code

예) 오류 코드: typeMismatch, object name "user", field "age", field type: int
1. "typeMismatch.user.age"
2. "typeMismatch.age"
3. "typeMismatch.int"
4. "typeMismatch"
```
- 객체 오류
    - 필드 오류의 경우 다음 순서로 2가지 메시지 코드 생성
```
객체 오류의 경우 다음 순서로 2가지 생성
1.: code + "." + object name
2.: code

예) 오류 코드: required, object name: item
1.: required.item
2.: required
```
- bindingResult.rejectValue(), reject()는 내부에서 MessageCodesResolver를 사용합니다. 
- MessageCodesResolver는 FieldError, ObejctError를 호출해서 String[] 오류 코드 배열에 생성된 순서대로 오류 코드를 반환해줍니다. 

### 오류 코드와 메시지 처리5
#### errors.properties 설정
- 오류 코드 레벨별로 설정 후 테스트 확인  
```
#==ObjectError==
#Level1
totalPriceMin.item=상품의 가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}

#Level2 - 생략
totalPriceMin=전체 가격은 {0}원 이상이어야 합니다! 현재 값 = {1}

#==FieldError==
#Level1
required.item.itemName=상품 이름은 필수입니다!
range.item.price=가격은 {0} ~ {1} 까지 허용합니다!
max.item.quantity=수량은 최대 {0} 까지 허용합니다!

#Level2
required.item=상품은 필수 입니다.

#Level3
required.java.lang.String = 필수 문자입니다.
required.java.lang.Integer = 필수 숫자입니다.
min.java.lang.String = {0} 이상의 문자를 입력해주세요.
min.java.lang.Integer = {0} 이상의 숫자를 입력해주세요.
range.java.lang.String = {0} ~ {1} 까지의 문자를 입력해주세요.
range.java.lang.Integer = {0} ~ {1} 까지의 숫자를 입력해주세요.
max.java.lang.String = {0} 까지의 숫자를 허용합니다.
max.java.lang.Integer = {0} 까지의 숫자를 허용합니다.

#Level4
required = 필수 값 입니다.
min= {0} 이상이어야 합니다.
range= {0} ~ {1} 범위를 허용합니다.
max= {0} 까지 허용합니다.
```
#### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/821b3bbd-43e3-48fc-8636-badcc8e3e6d0">

#### ValidationUtils 적용
```java
if (!StringUtils.hasText(item.getItemName())) {
    bindingResult.rejectValue("itemName", "required");
}
```
-> 변경

```java
ValidationUtils.rejectIfEmptyOrWhitespace(bindingResult, "itemName", "required");
```

- empty, 공백 같은 단순한 기능을 제공해주는 클래스로 검증 처리시 if문 없이 한줄로 처리 가능합니다.

> bindingResult 정리
> 1. rejectValue(), reject() 호출
> 2. MessageCodesResolver를 사용해서 검증 오류 코드의 Level순서로 메시지 코드를 생성
> 3. new FieldError를 생성하면서 메시지 코드들을 보관
> 4. th:errors에서 메시지 코드들로 메시지를 순서대로 메시지에서 찾고, 노출


### 오류 코드와 메시지 처리6
#### 스프링이 직접 만든 오류 메시지 처리
<img width="590" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/663c78c3-8e2f-4864-a4c1-ef40764bd946">

- 가격 필드에 문자를 입력 후 저장을 합니다. 
- 로그를 확인해보면 다음과 같은 메시지 코드들이 생성됩니다.
- **스프링은 타입오류가 발생하면 typeMismatch라는 오류코드가 MessageCodesResoulver를 통해서 생성**합니다. 

```java
Field error in object 'item' on field 'price': rejected value [qqqq]; codes [typeMismatch.item.price,typeMismatch.price,typeMismatch.java.lang.Integer,typeMismatch]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [item.price,price]; arguments []; default message [price]]; default message [Failed to convert property value of type 'java.lang.String' to required type 'java.lang.Integer' for property 'price'; nested exception is java.lang.NumberFormatException: For input string: "qqqq"]
```

- 다음과 같은 4가지 메시지 코드가 순서대로 입력되어 있습니다.
    - typeMismatch.item.price,
    - typeMismatch.price,
    - typeMismatch.java.lang.Integer,
    - typeMismatch

#### errors.properties 설정
```
#추가 (스프링이 제공해주는 오류 활용)
typeMismatch.java.lang.Integer=숫자를 입력해주세요.
typeMismatch=타입 오류입니다.
```
- MessageCodesResoulver가 생성해준 오류코드를 직접 errors.properties에 추가하여 처리합니다.

#### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/6e06ce4e-9af0-4d2f-af35-582f4fed63f2">

### Validator 분리1
#### 복잡한 검증 로직을 별도로 분리하자

```java
import hello.itemservice.domain.item.Item;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;
import org.springframework.validation.Errors;
import org.springframework.validation.Validator;

@Component // 컴포넌트 스캔으로 빈등록
public class ItemValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return Item.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        Item item = (Item) target;

        if (!StringUtils.hasText(item.getItemName())) {
            errors.rejectValue("itemName", "required");
        }
        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
            errors.rejectValue("price", "range", new Object[]{1000, 10000000}, null);
        }
        if (item.getQuantity() == null || item.getQuantity() >= 9999) {
            errors.rejectValue("quantity", "max", new Object[]{9999}, null);
        }

        //특정 필드가 아닌 복합 룰 검증
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                errors.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }
    }
}
```
- 스프링은 검증을 체계적으로 제공하기 위해 Validator라는 인터페이스를 제공합니다.
- Validator.validate(Object target, Errors errors) bindingResult의 부모 Error가 제공됩니다.

```java
@Slf4j
@Controller
@RequestMapping("/validation/v2/items")
@RequiredArgsConstructor
public class ValidationItemControllerV2 {

    private final ItemRepository itemRepository;
    private final ItemValidator itemValidator; // 등록한 빈 주입
    
    /**
     * Validator 분리1 - (검증 로직 분리)
     */
    @PostMapping("/add")
    public String addItem(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

        itemValidator.validate(item, bindingResult);

        //검증에 실패하면 다시 입력 폼으로
        if (bindingResult.hasErrors()) {
            log.info("errors={} ", bindingResult);
            return "validation/v2/addForm";
        }

        //성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v2/items/{itemId}";
    }
}

``` 
#### 결과확인 
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/b25f405f-2b30-4430-a617-cf8618f98cf4">

### Validator 분리2
- Validator인터페이스를 구현하여 검증로직에 직접 불러서 사용했습니다.
- `@Validator` 애노테이션을 사용해서 스프링의 추가적인 도움을 받을 수 있습니다.

#### WebDataBinder를 통해서 사용하기

```java
@Slf4j
@Controller
@RequestMapping("/validation/v2/items")
@RequiredArgsConstructor
public class ValidationItemControllerV2 {

    private final ItemRepository itemRepository;
    private final ItemValidator itemValidator;

    @InitBinder
    public void init(WebDataBinder dataBinder) {
        dataBinder.addValidators(itemValidator);
    }
    
    /**
     * Validator2 - (@Validate 애노테이션)
     */
    @PostMapping("/add")
    public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
        
        //itemValidator.validate(item, bindingResult);
        
        //검증에 실패하면 다시 입력 폼으로
        if (bindingResult.hasErrors()) {
            log.info("errors={} ", bindingResult);
            return "validation/v2/addForm";
        }

        //성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v2/items/{itemId}";
    }
```
- WebdataBinder는 스프링의 파라미터 바인딩 역할을 해주고 검증 기능도 내부에 포함해줍니다.
- WebdataBinder에 검증기를 추가해주면 해당 컨트롤러에서는 검증기를 자동으로 적용할 수 있습니다.
- @InitBinder는 해당 컨트롤러에만 영향을 줍니다.

#### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/34ca1bee-e2d1-432a-a219-bf8a3ff38ba3">

<br>

## 검증2 - Bean Validation
### Bean Validation
- validator2에서 검증기를 매번 도메인마다 코드로 작성하는 것이 상당히 번거롭습니다. 
- 빈 값인지, 특정 크기를 넘는 지 아닌지에 대한 검증 로직을 애노테이션으로 처리할 수 있게 공통화, 표준화한 것이 Bean Validation 입니다.
- Bean Validation는 구현체가 아니라 인터페이스로 자바에서 제공하는 표준 기술입니다. 일반적으로 사용하는 구현체는 하이버네이트 Validator 입니다.


#### build.gradle 의존관계 추가
```
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-validation'
}
```

- Bean Validation 인터페이스  
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/86563244-3b3c-4318-98c3-22b35dbb7505"> 

- Bean Validation 인터페이스의 구현체 (hibernate) 실제 동작하는 구현체 
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/37d4d4a1-a12c-4e5d-824b-8b3783925a32"> 

 
#### @NotBlank, @NotNull, @Range, @Max 기본 애노테이션
```java
import lombok.Data;
import org.hibernate.validator.constraints.Range;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

@Data
public class Item {

    private Long id;

    @NotBlank
    private String itemName;

    @NotBlank
    @Range(min = 1000, max = 1000000)
    private Integer price;

    @NotNull
    @Max(9999)
    private Integer quantity;

    public Item() {
    }

    public Item(String itemName, Integer price, Integer quantity) {
        this.itemName = itemName;
        this.price = price;
        this.quantity = quantity;
    }
}
```
- javax.validation는 특정 구현에 관계없이 제공되는 표준 인터페이스
- org.hibernate.validator는 하이버네이트 validator 구현체를 사용할때만 제공되는 검증 기능. (스프링 부트에 내장되어 있음)
- @NotBlank : null 과 "" 과 " " 모두 허용하지 않습니다.
- @NotNull : null을 허용하지 않습니다.
- @Range(min= min, mix= max) : 범위 안의 값이어야 합니다.
- @Max(max) : 최대 max까지만 허용합니다.

```java
import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import javax.validation.ValidatorFactory;
import java.util.Set;

public class BeanValidationTest {

    @Test
    void beanValidation() {
        ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
        Validator validator = factory.getValidator();

        Item item = new Item();
        //item.setItemName("상품");
        item.setItemName(" "); //공백
        item.setPrice(1111);
        item.setQuantity(1);
        //item.setPrice(0);
        //item.setQuantity(10000);

        Set<ConstraintViolation<Item>> violations = validator.validate(item);
        for (ConstraintViolation<Item> violation : violations) {
            System.out.println("violation = " + violation);
            System.out.println("violation = " + violation.getMessage());
        }

    }
}
```

#### 테스트 실행 결과
```
violation = ConstraintViolationImpl{interpolatedMessage='공백일 수 없습니다', propertyPath=itemName, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{javax.validation.constraints.NotBlank.message}'}
violation = 공백일 수 없습니다
violation = ConstraintViolationImpl{interpolatedMessage='1000에서 1000000 사이여야 합니다', propertyPath=price, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{org.hibernate.validator.constraints.Range.message}'}
violation = 1000에서 1000000 사이여야 합니다
violation = ConstraintViolationImpl{interpolatedMessage='9999 이하여야 합니다', propertyPath=quantity, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{javax.validation.constraints.Max.message}'}
violation = 9999 이하여야 합니다
```

- 검증 대상( item )을 직접 검증기에 넣고 그 결과를 받는다. Set 에는 ConstraintViolation 이라는 검증오류가 담긴다. 따라서 결과가 비어있으면 검증 오류가 없는 것이다.
- 그러므로, 직접 코드 구현을 하지 않고 애노테이션으로 검증을 할 수 있으며, 오류 메시지 또한 자동 생성해줍니다.

#### @NotBlank, @NotNull, @Range, @Max 기본 애노테이션 (dafault message)

```java
@Data
public class Item {

    private Long id;

    @NotBlank(message = "공백은 불가 합니다.")
    private String itemName;

    @NotNull(message = "입력해주세요.")
    @Range(min = 1000, max = 1000000, message = "1000~1000000 사이값 입니다.")
    private Integer price;

    @NotNull
    @Max(value = 9999, message = "최대값은 9999 입니다.")
    private Integer quantity;

    public Item() {
    }

    public Item(String itemName, Integer price, Integer quantity) {
        this.itemName = itemName;
        this.price = price;
        this.quantity = quantity;
    }
}
```
- 디폴트 오류 메시지도 추가할 수 있습니다.
- 추가적인 오류 메시지를 보여주고자 할 때 message="" 안에 내용을 넣어주면 됩니다.

### Bean Validation 스프링 적용

```java
@Slf4j
@Controller
@RequestMapping("/validation/v3/items")
@RequiredArgsConstructor
public class ValidationItemControllerV3 {

    private final ItemRepository itemRepository;
    
     /**
     * Bean Validation 적용 - @Validated
     */
    @PostMapping("/add")
    public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

        //검증에 실패하면 다시 입력 폼으로
        if (bindingResult.hasErrors()) {
            log.info("errors={} ", bindingResult);
            return "validation/v3/addForm";
        }

        //성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v3/items/{itemId}";
    }
```
- 검증할 도메인에 Bean Validation 애노테이션 적용 후, validator2 처럼 @ModelAttribute 앞에 @Validated만 써주면 자동으로 적용을 해줍니다.

#### 결과확인
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/eaa3469b-7999-4f02-8141-0fecde3edeff">


##### 스프링 MVC는 어떻게 Bean Validator를 사용?
- spring-boot-starter-validation 라이브러리를 넣어면 자동으로 Bean Validator를 인지하고 스프링이 통합합니다.
- LocalValidatorFactoryBean을 글로벌 Validator로 등록하여 Bean Validator 애노테이션을 보고 검증을 수행합니다.

##### 검증 순서
- @ModelAttribute 각각 필드의 타입 변환 시도
	1. 성공하면 다음으로
	2. 실패하면 typemismatch로 fieldError 추가
- 타입 변환 시도에 성공하면 Validator 적용



### Bean Validation 에러코드 (필드)

```
Field error in object 'item' on field 'itemName': rejected value []; codes [NotBlank.item.itemName,NotBlank.itemName,NotBlank.java.lang.String,NotBlank]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [item.itemName,itemName]; arguments []; default message [itemName]]; default message [공백은 불가 합니다.] 
```
- Bean Validation이 기본으로 제공하는 오류 메시지를 자세히 관리하고 싶은 경우 errors.properties에  메시지를 등록해주면 됩니다.

```
#Bean Validation 추가
NotBlank.item.itemName=상품 이름을 적어주세요.
NotBlank={0} 공백이 안됩니다.
Range={0}, {2} ~ {1} 허용
Max={0}, 최대 {1}
```

#### Bean Validation 에러코드 순서
1.NotBlank.item.itemName  
2.NotBlank.itemName  
3.NotBlank.java.lang.String  
4.NotBlank   

- 생성된 메시지 코드 순서대로 messageSource에서 메시지 찾기 
- 애노테이션 message 속성 사용 -> ex)NotBlank(message = "공백! {0}") 
- 라이브러리가 제공하는 기본값 사용 -> 공백일 수 없습니다. 


### Bean Validation 에러코드 (오브젝트)
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/22e1a47e-7aab-4646-a232-b3f6457d9d89">

- @Validated 애노테이션은 특정 필드에 처리가 된다.  
- ObjectError는 어떻게 처리할 수 있을까? 

```java
@ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 10000", message = "총 합이 만원 이상이여야 합니다.")
@Data
public class Item {

    private Long id;

    @NotBlank(message = "공백은 불가 합니다.")
    private String itemName;

    @NotNull(message = "입력해주세요.")
    @Range(min = 1000, max = 1000000, message = "1000~1000000 사이값 입니다.")
    private Integer price;

    @NotNull
    @Max(value = 9999, message = "최대값은 9999 입니다.")
    private Integer quantity;

    public Item() {
    }

    public Item(String itemName, Integer price, Integer quantity) {
        this.itemName = itemName;
        this.price = price;
        this.quantity = quantity;
    }
}
```
- @ScriptAssert()를 이용해서 필드의 조합으로 처리할 수 있습니다.
- 그러나 @ScriptAssert는 제약이 많고 복잡해서 Object error는 직접 자바 코드로 처리를 권장합니다.

```java
     /**
     * Bean Validation 적용 - @Validated
     */
    @PostMapping("/add")
    public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

        //특정 필드가 아닌 복합 룰 검증
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }

        //검증에 실패하면 다시 입력 폼으로
        if (bindingResult.hasErrors()) {
            log.info("errors={} ", bindingResult);
            return "validation/v3/addForm";
        }

        //성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v3/items/{itemId}";
    }
```


### Form 전송 객체 분리
- 등록과 수정은 완전히 다른 데이터가 넘어 옵니다. 예를 들어 회원가입시 로그인id, 주민번호, 약관 등등 받을 수 있지만, 수정시에는 다루는 데이터 범위에 차이가 있습니다. 그러므로 각각 별도의 객체로 데이터를 전달받는 것이 좋습니다.
- 폼 데이터 전달을 위한 등록, 수정용 폼 객체를 완전히 분리해 별도의 객체를 사용합니다.

### Form 전송 객체 개발
- saveForm, saveRepository, updateForm, updateRepository 따로 생성해서 개발합니다.

#### 하이버네이트 Validator 관련 링크
- 검증 애노테이션 모음 : https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/#validator-defineconstraints-spec
- @Email null 허용 : https://bellog.tistory.com/134

### Bean Validation HTTP 메시지 컨버터
- @Valid, @Validated는 HttpMessageConverter(@RequestBody)에도 적용할 수 있습니다.

#### 참고
- @ModelAttribute는 HTTP 요청파라미터(URL쿼리스트링, Post Form)을 다룰 때 사용합니다.
- @ReuqestBody는 HTTP Body의 데이터를 객체로 변환할 때 사용합니다. 주로 API JSON 요청을 다룰 때 사용합니다.

#### API 결과 3가지 (검증관련)

```java
import hello.itemservice.web.validation.form.ItemSaveForm;
import lombok.extern.slf4j.Slf4j;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

@Slf4j
@RestController
@RequestMapping("/validation/api/items")
public class ValidationItemApiController {

    @PostMapping("/add")
    public Object addItem(@RequestBody @Validated ItemSaveForm form, BindingResult bindingResult) {

        log.info("API 컨트롤러 호출");

        if (bindingResult.hasErrors()) {
            log.info("검증 오류 발생 errors={}", bindingResult);
            return bindingResult.getAllErrors(); // FieldError와 ObjectError 모두 반환
        }

        log.info("성공 로직 실행");
        return form;
    }
}
```
- 성공 요청 : 성공 
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/9d6421d1-bb54-4f81-be02-8518d8e0a6c3">
	
- 실패 요청 : JSON을 객체로 생성하는 것 자체가 실패함. 
	- 객체를 만들지 못하기 때문에 컨트롤러 자체가 호출되지 않고 예외가 발생하며 Validator도 실행되지 않는다. 
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/d4f010a9-d660-4939-9c0f-30e5cc20a25b">
	

- 검증 오류 요청 : JSON을 객체로 생성하는 것을 성공했고, 검증에서 실패함.  
	- HttpMessageConverter는 성공하지만 검증(Validator)에서 오류가 발생한다. 
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/d054193c-9b44-4309-9225-9d0b2228098e">

#### @ModelAttribute vs @ReuqestBody 검증 처리
- @ModelAttribute : 필드 단위로 정교하게 바인딩이 적용됩니다. 특정 필드 타입에 맞지 않는 오류가 발생해도 나머지 필드는 정상 처리할 수 있다. Validator를 사용한 검증도 적용할 수 있다. `(필드 단위로 세밀하게 적용)`
- @ReuqestBody는 : HttpMessageConverter단계에서 JSON 데이터를 객체로 변경하지 못하면 이후 단계 자체가 진행되지 않고 예외가 발생한다. 컨트롤러도 호출되지 않고, Validator도 적용할 수 없다. `(필드 단위로 적용되는 것이 아니라, 전체 객체 단위로 적용)`

<br>

## 로그인 처리1 - 쿠키,세션
### 로그인 처리하기 - 쿠키 
#### 로그인 상태를 어떻게 유지할 수 있을까?
##### 쿠키
- 서버에서 로그인에 성공하면 HTTP 응답에 쿠키를 담아서 브라우저에 전달합니다. 그러면 브라우저는 앞으로 해당 쿠키를 지속해서 보내줍니다.

##### 쿠키 생성
- 로그인
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/dc272f62-b763-4c79-97aa-803a583ea966">

##### 클라이언트 쿠키 전달1
- 로그인 이후 welcome 페이지 접근
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/7cb3716b-29c7-407d-8ffc-dc020ab1f9d3">

##### 클라이언트 쿠키 전달2
- 모든 요청에 쿠키 정보 자동 포함
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/11ec6bad-d81c-487e-8348-495242cf44db">

##### 쿠키 종류
- 쿠키에는 영속 쿠키와 세션 쿠키가 있습니다.
	- 영속 쿠키 : 만료 날짜를 입력하면 해당 날짜까지 유지
	- 세션 쿠키 : 만료 날짜를 생략하면 브라우저 종료시 까지만 유지
- 브라우저 종료시 로그아웃이 되길 기대하므로, 세션 쿠키를 사용합니다.

##### 쿠키 생성 로직

```java
@Slf4j
@Controller
@RequiredArgsConstructor
public class LoginController {

    private final LoginService loginService;

    @GetMapping("/login")
    public String loginForm(@ModelAttribute("loginForm") LoginForm form) {
        return "login/loginForm";
    }

    @PostMapping("/login")
    public String login(@Valid @ModelAttribute("loginForm") LoginForm form, BindingResult bindingResult, HttpServletResponse response) {

        if (bindingResult.hasErrors()) {
            return "login/loginForm";
        }

        Member loginMember = loginService.login(form.getLoginId(), form.getPassword());
        if (loginMember == null) {
            bindingResult.reject("loginFail"); // errorMessage -> errors.properties
            return "login/loginForm";
        }

        //로그인 성공 처리

        //쿠키 생성 로직 - 쿠키에 시간 정보를 주지 않으면 세션 쿠키(브라우저 종료시 모두 종료)
        Cookie idCookie = new Cookie("memberId", String.valueOf(loginMember.getId()));
        response.addCookie(idCookie);

        return "redirect:/";
    }
}    
```

- 로그인에 성공하면 쿠키를 생성하고 `HttpServletResponse`에 담습니다. 웹 브라우저는 브라우저 종료 전까지 회원의 쿠키 값을 서버에 계속 보내줍니다.

- 로그인 후 (Response Header)
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/4ee827ca-6cf2-47cd-b277-b5eb53031403">

- 로그인 후 새로고침 (Request Header)
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/3fa8ac66-113d-4c15-911e-1b6656331c23">

- 로그인 후 새로고침 (개발자 도구 - Application>Storage>Cookies)
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/32845c7a-a80e-4258-9786-381d1161f8cc">

##### 홈 - 로그인 처리
```java
@Slf4j
@Controller
@RequiredArgsConstructor
public class HomeController {

    private final MemberRepository memberRepository;

    @GetMapping("/")
    public String homeLogin(@CookieValue(name = "memberId", required = false) Long memberId, Model model) {

        if (memberId == null) {
            return "home"; // 로그인 쿠키가 없는 사용자
        }

        // 로그인
        Member loginMember = memberRepository.findById(memberId);
        if (loginMember == null) {
            return "home"; // 로그인 쿠키가 있어도 회원이 아닌 사용자
        }
        model.addAttribute("member", loginMember);
        return "loginHome"; // 로그인 쿠키가 있는 회원
    }
}
```

- 로그인 사용자 전용 홈
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="utf-8">
    <link th:href="@{/css/bootstrap.min.css}"
          href="../css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

<div class="container" style="max-width: 600px">
    <div class="py-5 text-center">
        <h2>홈 화면</h2>
    </div>

    <h4 class="mb-3" th:text="|로그인: ${member.name}|">로그인 사용자 이름</h4>

    <hr class="my-4">

    <div class="row">
        <div class="col">
            <button class="w-100 btn btn-secondary btn-lg" type="button"
                    th:onclick="|location.href='@{/items}'|">
                상품 관리
            </button>
        </div>
        <div class="col">
            <form th:action="@{/logout}" method="post">
                <button class="w-100 btn btn-dark btn-lg" onclick="location.href='items.html'" type="submit">
                    로그아웃
                </button>
            </form>
        </div>
    </div>

    <hr class="my-4">

</div> <!-- /container -->

</body>
</html>
```

##### 로그아웃 기능
- 세션 쿠키이므로 웹 브라우저 종료시 서버에서 해당 쿠키의 종료날짜를 0으로 지정 

<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/d51c9515-60fc-453d-b6c9-2f8f330bac5e">

### 쿠키와 보안 문제
- 쿠키를 사용해서 로그인Id를 전달해서 로그인을 유지할 수 있다. 여기에는 심각한 보안 문제가 있다. 

##### 보안문제
- 쿠키 값음 임의로 변경할 수 있다. (변조)
  - 클라이언트가 쿠키를 강제로 변경하면 다른 사용자가 된다.

- 쿠키에 보관된 정보를 훔쳐갈 수 있다. (도난)
   - 로컬PC가 털릴 수도 있고, 네트워크 전송 구간에 털릴 수 있다.

- 해커가 쿠키를 한번 훔쳐가서 쿠키로 악의적인 요청을 계속 시도할 수 있다.


##### 대안
- 쿠키에 중요한 값을 노출하지 않고, 사용자 별로 예측 불가능한 임의의 토큰(랜던값)을 노출하고 서버에서 토큰과 사용자 id를 매핑해서 인식한다. 그리고 서버에서 토큰을 관리한다.
- 토큰 만료시간을 짧게(예:30분) 유지한다. 또는 해킹이 의심되는 경우 서버에서 해당 토큰을 강제로 제거하면 된다. 

### 로그인 처리하기 - 세션
- 중요한 정보를 모두 서버에 저장해야 한다.
- 클라이언트와 서버는 추정 불가능한 임의의 식별자 값으로 연결해야 합니다.
- 서버에 중요한 정보를 보관하고 연결을 유지하는 방법을 세션이라고 합니다.

#### 세션 동작 방식
##### 로그인 
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/2328c4b0-c14e-4cb3-bc20-33df91b32b79">
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/2c931eda-39e9-44f5-be1e-0655f353c5d4">

- 사용자가 로그인아이디, 패스워드 정보를 전달하면 서버에서 해당 사용자가 맞는 지 확인한다.
- 서버에서는 생성한 세션 ID와 보관할값을 세션 저장소에 보관합니다. (UUID, 예측 불가능한 임의의 토큰, 랜덤값)
- 서버는 클라이언트에 세션 ID만 쿠키에 담아 전달합니다.
- 클라이언트는 쿠키 저장소에 세션 ID 쿠키를 보관합니다. 

> 회원관련 정보는 클라이언트에 전달하지 않고, 추정 불가능한 세션 ID만 쿠키를 통해 클라이언트에 전달

##### 로그인 이후 접근
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/e10f3c34-2edf-485f-a7b9-67f84727b894">

- 클라이언트는 요청시 항상 세션ID쿠키를 전달합니다. 
- 서버에서는 클라이언트가 전달한 세션ID쿠키 정보로 세션 저장소를 조회해서 로그인시 보관한 세션 정보를 사용합니다.

> 전체정리 : 클라이언트로 요청 받은 로그인 정보 인증 후 생성한 세션ID와 보관할 값을 쿠키저장소에 담고 세션ID만 쿠키에 담아 클라이언트에 응답합니다. 그 이후 로그인 시 클라이언트가 전달한 세션ID 쿠키를 서버의 세션 저장소에 조회해 로그인 인증을 합니다. 
> 대규모 서비스 경우 로드밸런싱을 하기 때문에 디비에 따로 세션ID를 저장하기도 합니다.

### 로그인 처리하기 - 세션 개발
- 세션관리는 크게 3가지 기능을 제공하면 됩니다.
1. 세션생성 : sessionID 생성(임의의 추정 불가능한 랜덤 값), 세션 저장소에 sessionID/보관할 값 저장, sessionID로 응답 쿠키 생성해서 클라이언트에 전달
2. 세션조회 : 클라이언트가 요청한 sessionID 쿠키의 값으로, 세션 저장소에 보관한 값 조회
4. 세션만료 : 클라이언트가 요청한 sessionID 쿠키의 값으로, 세션 저장소에 보관한 sessionID와 값 제

#### 세션관리 - SessionManager
```java
import org.springframework.stereotype.Component;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Arrays;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

/**
 * 세션관리
 */
@Component
public class SessionManager {
    public static final String SESSION_COOKIE_NAME = "mySessionId";
    private Map<String, Object> sessionStore = new ConcurrentHashMap<>();

    /**
     * 세션 생성
     * sessionId 생성 (임의의 추정 불가능한 랜덤 값)
     * 세션 저장소에 sessionId와 보관할 값 저장
     * sessionId로 응답 쿠키를 생성해서 클라이언트에 전달
     */
    public void createSession(Object value, HttpServletResponse response) {

        //세션 id를 생성하고, 값을 세션에 저장
        String sessionId = UUID.randomUUID().toString(); // UUID : Universally Unique Identifier 범용 고유 식별자
        sessionStore.put(sessionId, value);

        //쿠키 생성
        Cookie mySessionCookie = new Cookie(SESSION_COOKIE_NAME, sessionId);
        response.addCookie(mySessionCookie);
    }

    /**
     * 세션 조회
     */
    public Object getSession(HttpServletRequest request) {
        Cookie sessionCookie = findCookie(request, SESSION_COOKIE_NAME);
        if (sessionCookie == null) {
            return null;
        }
        return sessionStore.get(sessionCookie.getValue());
    }

    /**
     * 쿠키 찾기
     */
    public Cookie findCookie(HttpServletRequest request, String cookieName) {
        if (request.getCookies() == null) {
            return null;
        }
        return Arrays.stream(request.getCookies())
                .filter(cookie -> cookie.getName().equals(cookieName))
                .findAny()
                .orElse(null);
    }

    /**
     * 세션 만료
     */
    public void expire(HttpServletRequest request) {
        Cookie sessionCookie = findCookie(request, SESSION_COOKIE_NAME);
        if (sessionCookie != null) {
            sessionStore.remove(sessionCookie.getValue());
        }
    }
}
```

#### 테스트 - SessionManagerTest

```java
import hello.login.domain.member.Member;
import org.junit.jupiter.api.Test;
import org.springframework.mock.web.MockHttpServletRequest;
import org.springframework.mock.web.MockHttpServletResponse;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

class SessionManagerTest {

    SessionManager sessionManager = new SessionManager();

    @Test
    void sessionTest() {

        //세션 생성 (서버)
        MockHttpServletResponse response = new MockHttpServletResponse(); // 가짜 HttpServletResponse
        Member member = new Member();
        sessionManager.createSession(member, response);

        //요청에 응답 쿠키 저장 (클라이언트)
        MockHttpServletRequest request = new MockHttpServletRequest(); // 가짜 HttpServletRequest
        request.setCookies(response.getCookies());

        //세션 조회 (서버)
        Object result = sessionManager.getSession(request);
        assertThat(result).isEqualTo(member);

        //세션 만료 (서버)
        sessionManager.expire(request);
        Object expired = sessionManager.getSession(request);
        assertThat(expired).isNull();
    }
}
```
- junit 테스트시 HttpServletRequest, HttpServletResponse 객체를 직접 사용할 수 없기 때문에 스프링에서 제공해주는 비슷한 역할을 해주는 가짜 MockHttpServletRequest, MockHttpServletResponse을 사용합니다.

### 로그인 처리하기 - 직접 만든 세션 적용 

```java
import hello.login.domain.login.LoginService;
import hello.login.domain.member.Member;
import hello.login.web.session.SessionManager;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;

import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.validation.Valid;

@Slf4j
@Controller
@RequiredArgsConstructor
public class LoginController {

    private final LoginService loginService;
    private final SessionManager sessionManager;

    @GetMapping("/login")
    public String loginForm(@ModelAttribute("loginForm") LoginForm form) {
        return "login/loginForm";
    }

    /**
     * 세션쿠키
     */
    @PostMapping("/login")
    public String login(@Valid @ModelAttribute("loginForm") LoginForm form, BindingResult bindingResult, HttpServletResponse response) {

        if (bindingResult.hasErrors()) {
            return "login/loginForm";
        }

        Member loginMember = loginService.login(form.getLoginId(), form.getPassword());
        if (loginMember == null) {
            bindingResult.reject("loginFail"); // errorMessage -> errors.properties
            return "login/loginForm";
        }

        //로그인 성공 처리

        //세션 관리자를 통해 세션을 생성하고, 회원 데이터 보관
        sessionManager.createSession(loginMember, response);

        return "redirect:/";
    }

    @PostMapping("/logout")
    public String logout(HttpServletRequest request) {
        sessionManager.expire(request);
        return "redirect:/";
    }
}
```

```java
import hello.login.domain.member.Member;
import hello.login.web.session.SessionManager;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.CookieValue;
import org.springframework.web.bind.annotation.GetMapping;

import javax.servlet.http.HttpServletRequest;
import java.util.Arrays;

@Slf4j
@Controller
@RequiredArgsConstructor
public class HomeController {

    private final SessionManager sessionManager;

    /**
     * 세션쿠키
     */
    @GetMapping("/")
    public String homeLogin(HttpServletRequest request, Model model) {
        log.info("homeLogin request.getCookies = " + Arrays.toString(request.getCookies()));


        //세션 관리자에 저장된 회원 정보 조회
        Member member = (Member) sessionManager.getSession(request);

        //로그인
        if (member == null) {
            return "home";
        }

        model.addAttribute("member", member);
        return "loginHome";
    }

}
```
#### 결과 확인
- 첫 로그인
	- 서버에서 세션ID 생성 및 저장, Response Header에 담아 응답, 클라이언트는 브라우저 내부 쿠키 저장소에 보관.
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/b969c40f-9bba-41c3-9991-99e66637d803">
	
- 로그인 후 새로고침 
	- 쿠키 저장소에서 세션ID 조회해서 Request Header의 Cookie에 담겨 요청함.
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/f0e158e8-d366-4926-8d22-71fe641ed7c9">

- 로그아웃 
	- 서버에서는 세션ID 삭제되고, Response Header에도 삭제된다. Request Header에는 삭제되지 않는다.  

- 다시 로그인  
 	- Response Header에 새로운 세션ID 생성,  클라이언트는 브라우저 내부 쿠키 저장소에 보관. 
	- Request Header에 이전 세션ID 남아있음  
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/628e7c7f-4976-4f0b-b727-9de91bedd1f0">

- 로그인 후 새로고침
	- 쿠키 저장소에서 새로운 세션ID 조회해서 Request Header의 Cookie에 담겨 요청함.

> 브라우저가 종료되지 않은 상태에서 로그인 하면 세션ID 생성/새로고침 세션ID로 유지/로그아웃하면 세션ID 삭제, 다시 로그인하면 새로운 세션ID 생성/새로고침 새로운 세션ID로 유지/로그아웃하면 새로운 세션ID 삭제로 된다. 결국 로그아웃 하기 전까지는 생성된 세션ID로 유지합니다. 

#### 정리 
- 세션은 단지 쿠키를 사용하는데, 클라이언트와 서버에서 임의의 토큰값으로 느슨하게 유지하는 방법일 뿐
- 위의 방법은 고대의 개발자들이 사용했던 방법으로 개념을 이해하기 위해 직접 만들어보았습니다. 
- 프로젝트마다 세션을 직접 개발하는 것이 상당히 번거롭습니다. 그래서 서블릿이 공식 지원하는 세션을 사용하면 됩니다. 


### 로그인 처리하기 - 서블릿 HTTP 세션1
- 세션이라는 개념은 대부분의 웹 애플리케이션에 필요한 것이다. 어쩌면 웹이 등장하면서 부터 나온 문제이다.
- 서블릿은 세션을 위해 `HttpSession`이라는 기능을 제공합니다.
- 앞에서 직접 구현했던 세션을 구현할 필요 없이 제공하는 기능으로 사용할 수 있습니다.

#### HttpSession 소개
- 서블릿을 통해 `HttpSession`을 생성하면 쿠키를 생성합니다. 쿠키 이름은 `JSESSIONID`이고, 값은 추정 불가능한 랜덤 값입니다.

##### SessionConst
```java
public interface SessionConst {
    String LOGIN_MEMBER = "loginMember";
}
```
- HttpSession에 데이터를 보관하고 조회할 때, 같은 이름이 중복되어 사용되므로 상수를 하나 정의합니다. (Key 정의)

##### LoginController - login, logout
```java
import hello.login.domain.login.LoginService;
import hello.login.domain.member.Member;
import hello.login.web.session.SessionConst;
import hello.login.web.session.SessionManager;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import javax.validation.Valid;

@Slf4j
@Controller
@RequiredArgsConstructor
public class LoginController {

    private final LoginService loginService;
    private final SessionManager sessionManager;

    @GetMapping("/login")
    public String loginForm(@ModelAttribute("loginForm") LoginForm form) {
        return "login/loginForm";
    }
	
    /**
     * 서블릿이 제공하는 HttpSession
     * - JSESSIONID (추정 불가능한 랜덤 값)
     */
    @PostMapping("/login")
    public String login(@Valid @ModelAttribute LoginForm form, BindingResult
            bindingResult, HttpServletRequest request) {
        log.info("login");

        if (bindingResult.hasErrors()) {
            return "login/loginForm";
        }

        Member loginMember = loginService.login(form.getLoginId(), form.getPassword());
        log.info("login? {}", loginMember);

        if (loginMember == null) {
            bindingResult.reject("loginFail"); // errorMessage -> errors.properties
            return "login/loginForm";
        }

        //로그인 성공 처리
        //세션이 있으면 기존 세션 반환, 없으면 신규 세션 생성 반환
        HttpSession session = request.getSession();
        //세션에 로그인 회원 정보 보관
        session.setAttribute(SessionConst.LOGIN_MEMBER, loginMember); // 여러 값을 set() 메서드에 저장할 수 있음. 메모리에 보관됨.

        return "redirect:/";
    }

    @PostMapping("/logout")
    public String logout(HttpServletRequest request) {
        // 세션이 있으면 기존 세션 반환, 없으면 null 반환
        HttpSession session = request.getSession(false);
        if (session != null) {
            session.invalidate(); // 세션을 제거합니다.
        }
        return "redirect:/";
    }
}
```

- 로그인 : 세션 생성과 조회
	- request.getSession()을 사용하면 됩니다. 
	- public HttpSession getSession(boolean create);
		- create 옵션 (true/false)
		- `true` : 세션이 있으면 기존 세션 반환, 없으면 새로운 세션 생성 및 반환 *(default)*
		- `false` : 세션이 있으면 기존 세션 반환, 없으면 null 반환
	- 로그인 시 세션 처리 및 사용자 정보를 담아 홈으로 이동합니다. 
- 로그아웃 : 세션 삭제
	- session.invalidate()로 세션을 제거합니다. 


> 주의사항! - 에러해결<br>
> @ModelAttribute LoginForm 도메인 객체에 매핑되는 생성자가 있어야 함. <br>
> Lombok @Builder 테스트 한다고 @AllArgsConstructor 주석했더니 필드와 값 매핑이 전혀 안되서 오류 났었음. <br>

##### HomeController - home

```java
import hello.login.domain.member.Member;
import hello.login.web.session.SessionConst;
import lombok.AllArgsConstructor;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import java.util.Arrays;

@Slf4j
@Controller
@RequiredArgsConstructor
public class HomeController {

    /**
     * 서블릿이 제공하는 HttpSession
     * - JSESSIONID (추정 불가능한 랜덤 값)
     */
    @GetMapping("/")
    public String homeLogin(HttpServletRequest request, Model model) {

        HttpSession session = request.getSession(false);
        if (session == null) {
            return "home";
        }

        Member loginMember = (Member) session.getAttribute(SessionConst.LOGIN_MEMBER);

        //세션에 회원 데이터가 없으면 home
        if (loginMember == null) {
            return "home";
        }
        log.info("안녕하세요" + loginMember + "고객님");

        //세션이 유지되면 로그인으로 이동
        model.addAttribute("member", loginMember);
        return "loginHome";
    }

}
```
- `request.getSession(false)` : 비회원 및 로그인 하지 않은 사용자에게 세션을 생성하지 않아야 한다.
- `session.getAttribute(SessionConst.LOGIN_MEMBER)` : 로그인 시점에 세션에 보관한 회원 객체를 찾습니다. 

##### 결과
- 첫 로그
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/423d274c-d4b6-4316-9652-60465bc9a5c7">

- 새로고침
<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/364469fd-5c99-4868-a303-b76b8f3a3b48">

### 로그인 처리하기 - 서블릿 HTTP 세션2
- 서블릿 HTTP 세션1에서 @GetMapping("/") 홈화면으로 왔을 때 HttpServletRequest에 있는 세션을 찾아서, 세션에 들어가 있는 데이터를 찾는 로직을 @SessionAttribute 애노테이션으로 편리하게 처리할 수 있습니다. 

#### @SessionAttribute

```java
    /**
     * 스프링이 제공하는 @SessionAttribute
     */
    @GetMapping("/")
    public String homeLogin(
            @SessionAttribute(name = SessionConst.LOGIN_MEMBER, required = false)
            Member loginMember, Model model) {

        //세션에 회원 데이터가 없으면 home
        if (loginMember == null) {
            return "home";
        }

        //세션이 유지되면 로그인으로 이동
        model.addAttribute("member", loginMember);
        return "loginHome";
    }
```
- `@SessionAttribute(name = SessionConst.LOGIN_MEMBER, required = false)`
- 스프링에서 제공하는 @SessionAttribute 애노테이션을 쓰면 자동으로 세션을 찾아 모델(객체)에 매핑을 해줍니다.
- required = false는 로그인 하지 않는 사용자는 세션을 매핑하지 않도록 처리합니다.

#### TrackingModes

<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/f185d35c-3b80-49e3-8c94-da066b953b13">

- URL에 다음과 같이 `JSESSIONID`를 포함하고 있는 것을 확인할 수 있습니다. 
- NetWork에서 Request Header에 Locator에 포함되고 있습니다. 
- 이유 : 웹 브라우저가 쿠키를 지원하지 않을 때 쿠키 대신 URL을 통해 세션을 유지하는 방법입니다. 
- 이 방법은 URL이 세션값을 계속 포함해서 전달합니다. 타임리프 같은 템플릿 엔진을 통해서 링크를 걸면 URL에 자동으로 포함해줍니다. 서버 입장에서는 웹 브라우저가 쿠키를 지원하는지 하지 않는지 최초에 판단을 못하므로, 쿠키값도 URL에 포함해 함께 전달합니다.
- URL 전달방식은 끄고, 쿠키를 통해서만 세션을 유지하고 싶으면 다음과 같은 옵션을 넣습니다. (노출 되지 않음)

##### application.properties
```
server.servlet.session.tracking-modes=cookie
```
### 세션 정보와 타임아웃 설정
- 로그아웃을 직접 호출해서 session.invalidate();가 호출 되는 경우에 세션을 삭제 합니다.
- 그런데, 대부분 사용자는 그냥 웹 브라우저를 종료합니다. 
- 문제는 HTTP가 비연결성(ConnectionLess)이므로 서버 입장에서는 사용자가 웹 브라우저를 종료한건지 아닌지를 인식할 수 없습니다. 이 경우 남아있는 세션을 무한정 보관하게 됩니다.
- 그럴경우 JSESSIONID 악의적으로 탈취 및 요청, 메모리가 차서 OutOfMemory 오류 발생으로 장애가 발생할 수 있습니다.

#### 세션의 종료 시점
- 세션 생성 지점이 아니라 사용자가 서버에 최근에 요청한 시간을 기준으로 30분 정도 계속 연장
- 따라서 30분 마다 로그인 해야하는 번거로움이 사라집니다.

#### 세션 타임아웃 설정
##### application.properties
```
server.servlet.session.timeout=1800
```
- 스프링 부트로 글로벌 설정을 해줍니다. 
- 분을 초단위로 변환해서 설정을 해줘야 합니다. 1800초로 설정해줬다면 /60을 한 30분입니다. 
- 60초(1분) 미만으로 설정했다면 1분으로 설정됩니다. 
- 설정을 해주면 *서버에 요청을 할 때마다 설정한 시간동안 세션을 추가로 사용*할 수 있습니다. 

##### 특정 세션 단위로 시간 설정
```
session.setMaxInactiveInterval(1800);
```
- 특정 기능/페이지 등등 세션 단위를 설정해줘야 하는 경우, HttpSession의 `setMaxInactiveInterval` 메서드로 단위를 지정해주면 됩니다. 

> 정리 : 글로벌 설정 또는 특정 세션 단위로 설정을 통해 session.getLastAccessedTime() 최근 세션 접근 시간 이후로 timeout 시간이 지나면 WAS 내부에서 해당 세션을 제거합니다.
> 또한, 실무 주의할 점은 최소한의 데이터만 보관을 해아합니다. 로그인ID, 로그인명 등등 정말 필요한 데이터만 보관을 해서 메모리 과부하 되지 않게 데이터 사용량을 최소화 하는 것이 중요합니다. 

### 로그인 처리1 - 쿠키,세션 정리
- 쿠키만으로 처리 (로그인 정보 도난, 변조, 유실)
- 세션쿠키 (세션 저장소 직접 임의의 랜덤 아이디 UUID 생성 후 저장)
- 서블릿이 제공하는 HttpSession JSESSIONID (직접 생성하지 않고, 서블릿을 통해서 바로 생성 후 매핑 가능)
- 스프링이 제공하는 @SessionHttp (세션과 객체 매핑을 어노테이션으로 쉽게 처리)
- 세션의 timeout 글로벌 설정
- 세션 저장소를 직접 구현하든, HttpSession 기능을 이용해서 세션을 생성 및 관리 하든 Http Request/Reponse Header에 Set-Cookie/Cookie 속성에 담겨서 데이터 유지가 됩니다.

## 로그인 처리2 - 필터, 인터셉터
- 필터 - 서블릿이 제공하는 기능
- 인터셉터 - 스프링이 제공하는 기

### 공통 관심 사항
- 애플리케이션에서 여러 로직에서 공통으로 관심이 있는 것을 공통 관심사(Cross-cutting concern)이라고 합니다.
- 공통 관심사를 스프링 AOP로도 해결할 수 있지만, 웹과 관련된 공통 관심사는 서블릿 필터 또는 스프링 인터셉터를 사용하는 게 좋습니다.
- 웹과 관련된 공통 관심사란 HTTP의 헤더나 URL의 정보들이 필요할 때 서블릿 필터나 스프링 `HttpServeltRequest` 제공해줍니다.

### 서블릿 필터 소개
#### 필터 흐름
``` HTTP 요청 -> WAS -> `필터` -> 서블릿(디스패처 서블릿) -> 컨트롤러```
- 필터를 적용하면 필터가 호출된 다음에 서블릿이 호출됩니다.
- 필터는 특정 URL패턴에 적용할 수 있습니다. /*이라고 하면 모든 요청에 필터가 적용됩니다.
  
#### 필터 제한
```
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 컨트롤러 //로그인 사용자
HTTP 요청 -> WAS -> 필터(적절하지 않은 요청이라 판단, 서블릿 호출X) //비 로그인 사용자
```
- 필터에서 적절하지 않는 요청이라고 판단하면 서블릿 호출하지 않습니다.
- 비 로그인 사용자 같은 경우에 체크하기 좋습니다.
  
#### 필터 체인
- HTTP 요청 -> WAS -> `필터1` -> `필터2` -> `필터3` -> 서블릿(디스패처 서블릿) -> 컨트롤러
- 
#### 필터 인터페이스 
```java 
public interface Filter {

public default void init(FilterConfig filterConfig) throws ServletException
{}

public void doFilter(ServletRequest request, ServletResponse response,
FilterChain chain) throws IOException, ServletException;

public default void destroy() {}
}
```
- WAS가 doFilter 호출한 후에 필터가 없으면 서블릿이 호출됩니다.
- 필터 인스페이스를 구현하고 등록하면 서블릿 컨테이너가 필터를 싱글톤 객체로 생성하고 관리합니다.
- init() : 필터 초기화 메서드, 서블릿 컨테이너가 생성될 때 호출됩니다.
- doFilter() : 고객의 요청이 올 때마다 해당 메서드가 호출됩니다. 필터의 로직을 구현하면 됩니다.
- destroy() : 필터 종료 메서드, 서블릿 컨테이너가 종료될 때 호출됩니다.

### 서블릿 필터 - 요청로그
#### 로그필터 - LogFilter
```java 
import lombok.extern.slf4j.Slf4j;
import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;
import java.util.UUID;

@Slf4j
public class LogFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("log filter init");
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        log.info("log filter doFilter");
        HttpServletRequest httpRequest = (HttpServletRequest) request; // 다운캐스팅 부모의 ServeltRequest가 기능이 별로 없다.
        String requestURI = httpRequest.getRequestURI();

        String uuid = UUID.randomUUID().toString();
        try {
            log.info("REQUEST [{}][{}]", uuid, requestURI);
            chain.doFilter(request, response); // 필터 및 서블릿 호출
        } catch (Exception e) {
            throw e;
        } finally {
            log.info("RESPONSE [{}][{}]", uuid, requestURI);
        }
    }

    @Override
    public void destroy() {
        log.info("log filter destroy");
    }
}
```
#### 필터설정 - WebConfig
```java
import hello.login.web.filter.LogFilter;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.servlet.Filter;


@Configuration
public class WebConfig {

    @Bean
    public FilterRegistrationBean logFilter() {
        FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new LogFilter());
        filterRegistrationBean.setOrder(1);
        filterRegistrationBean.addUrlPatterns("/*");
        return filterRegistrationBean;
    }
}
```
#### 실행로그
```
REQUEST [a89553d4-0d13-4400-803c-d2387edb7445][/]
RESPONSE [a89553d4-0d13-4400-803c-d2387edb7445][/]
```

> 참고! 실무에서 HTTP 요청시 같은 요청의 로그에 모두 같은 식별자를 자동으로 남기는 방법은 logback mdc로 검색해보면 됩니다.


### 서블릿 필터 - 인증체크
#### 인증 체크 필터 - LogCheckFilter
```java
import hello.login.web.session.SessionConst;
import lombok.extern.slf4j.Slf4j;
import org.springframework.util.PatternMatchUtils;
import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

@Slf4j
public class LoginCheckFilter implements Filter {

    private static final String[] whitelist = {"/", "/members/add", "/login", "/logout", "/css/*"}; // 접근 허용 URL

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

        HttpServletRequest httpRequest = (HttpServletRequest) request;
        String requestURI = httpRequest.getRequestURI();

        HttpServletResponse httpResponse = (HttpServletResponse) response;

        try {
            log.info("인증 체크 필터 시작 {}", requestURI);

            if (isLoginCheckPath(requestURI)) {
                log.info("인증 체크 로직 실행 {}", requestURI);
                HttpSession session = httpRequest.getSession(false);
                if (session == null || session.getAttribute(SessionConst.LOGIN_MEMBER) == null) {
                    log.info("미인증 사용자 요청 {}", requestURI);
                    //로그인으로 redirect
                    httpResponse.sendRedirect("/login?redirectURL=" + requestURI);
                    return;
                }
            }
            chain.doFilter(request, response);
        } catch (Exception e) {
            throw e; //예외 로깅 가능 하지만, 톰캣까지 예외를 보내주어야 함
        } finally {
            log.info("인증 체크 필터 종료 {} ", requestURI);
        }
    }

    /**
     * 화이트 리스트의 경우 인증 체크X
     */
    private boolean isLoginCheckPath(String requestURI) {
        return !PatternMatchUtils.simpleMatch(whitelist, requestURI);
    }
}
```
#### WebConfig - loginCheckFilter
```java
import hello.login.web.filter.LogFilter;
import hello.login.web.filter.LoginCheckFilter;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.servlet.Filter;


@Configuration
public class WebConfig {

    @Bean
    public FilterRegistrationBean logFilter() {
        FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new LogFilter());
        filterRegistrationBean.setOrder(1);
        filterRegistrationBean.addUrlPatterns("/*");
        return filterRegistrationBean;
    }

    @Bean
    public FilterRegistrationBean loginCheckFilter() {
        FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new LoginCheckFilter());
        filterRegistrationBean.setOrder(2);
        filterRegistrationBean.addUrlPatterns("/*");
        return filterRegistrationBean;
    }

}
```
#### RedirectURL 처리 - LoginController
```java
    /**
     * 서블릿 필터 적용
     */
    @PostMapping("/login")
    public String login(@Valid @ModelAttribute LoginForm form, BindingResult bindingResult,
            @RequestParam(defaultValue = "/") String redirectURL,
            HttpServletRequest request) {
        log.info("login");

        if (bindingResult.hasErrors()) {
            return "login/loginForm";
        }

        Member loginMember = loginService.login(form.getLoginId(), form.getPassword());
        log.info("login? {}", loginMember);

        if (loginMember == null) {
            bindingResult.reject("loginFail"); // errorMessage -> errors.properties
            return "login/loginForm";
        }

        //로그인 성공 처리
        //세션이 있으면 기존 세션 반환, 없으면 신규 세션 생성 반환
        HttpSession session = request.getSession();
        //세션에 로그인 회원 정보 보관
        session.setAttribute(SessionConst.LOGIN_MEMBER, loginMember); // 여러 값을 set() 메서드에 저장할 수 있음. 메모리에 보관됨.

        return "redirect:" + redirectURL;
    }
```
- 로그인 체크 필터에서, 미인증 사용자는 요청 경로를 포함해서 /login에 redirectURL 요청 파라미터를 추가 요청했다. 이 값을 사용해서 로그인 성공시 해당 경로로 고객을 redirect 한다.


### 스프링 인터셉터
- 스프링 인터셉터도 서블릿 필터와 같이 웹 관련된 공통 관심 사항을 효과적으로 해결할 수 있는 기술 입니다. 스프링 인터셉터는 스프링 MVC가 제공하는 기술 입니다.
- 스프링 인터셉터가 서블릿 필터보다 더 많은 기능과 범위를 제공합니다.

#### 스프링 인터셉터 흐름
`HTTP 요청 -> WAS -> 필터 -> 서블릿 -> *스프링 인터셉터* -> 컨트롤러`
- 스프링 인터셉터는 디스패처 서블릿과 컨트롤러 사이에서 컨트롤러 호출 직전에 호출됩니다.

#### 스프링 인터셉터 제한
`
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터 -> 컨트롤러 //로그인 사용자
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터(적절하지 않은 요청이라 판단, 컨트롤러 호출
X) // 비 로그인 사용자
`

#### 스프링 인터셉터 체인
`HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터1 -> 인터셉터2 -> 컨트롤러`

#### 스프링 인터셉터 인터페이스
```java
public interface HandlerInterceptor {

	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {}
	
	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {}
	
	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {}

}
```
- 서블릿 필터의 경우 단순하게 doFilter() 메서드에 request,response만 제공했다.
- 인터셉터는 컨트롤러 호출 전 preHandle, 호출 후 postHandle, 요청 완료 이후 afterCompletion 와 같이 단계적으로 잘 세분화 되어 있습니다.
- 어떤 컨트롤러 (handler)가 호출되는 지 호출 정보도 받을 수 있다. 그리고 어떤 ModelAndView가 반환되는 지 응답 정보도 받을 수 있다.

#### 스프링 인터셉터 호출 흐름
<img width="100%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/d689429e-4d6e-4297-b551-d3b4b4ff5632">

##### 정상흐름
- preHandle : 컨트롤러 호출 전에 호출된다. (더 정확히는 핸들러 어댑터 호출 전에 호출된다.)
	- preHandle 의 응답값이 true 이면 다음으로 진행하고, false 이면 더는 진행하지 않는다. false 인 경우 나머지 인터셉터는 물론이고, 핸들러 어댑터도 호출되지 않는다. 그림에서 1번에서 끝이
나버린다.
- postHandle : 컨트롤러 호출 후에 호출된다. (더 정확히는 핸들러 어댑터 호출 후에 호출된다.)
- afterCompletion : 뷰가 렌더링 된 이후에 호출된다.

##### 예외상황
<img width="100%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/2621d3e2-d285-45a6-9d42-880b9a4f4b6f">

- preHandle : 컨트롤러 호출 전에 호출된다.
- postHandle : 컨트롤러에서 예외가 발생하면 postHandle 은 호출되지 않는다.
- afterCompletion : afterCompletion 은 항상 호출된다. 이 경우 예외( ex )를 파라미터로 받아서 어떤
예외가 발생했는지 로그로 출력할 수 있다.
	- 예외가 발생하면 postHandle()은 호출되지 않으므로 **예외와 무관하게 공통 처리를 하려면 afterCompletion()을 사용**해야 한다.
  	- default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, `@Nullable Exception ex`) throws Exception {}

### 스프링 인터셉터 - 요청 로그
- 모든 요청에 대해서 로그로 출력합니다.

#### 요청 로그 인터셉터 - LogInterceptor

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.UUID;

@Slf4j
public class LogInterceptor implements HandlerInterceptor {

    public static final String LOG_ID = "logId";

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        String requestURI = request.getRequestURI();
        String uuid = UUID.randomUUID().toString();

        request.setAttribute(LOG_ID, uuid);

        //@RequestMapping: HandlerMethod
        //정적 리소스: ResourceHttpRequestHandler
	// 타입에 따라 처리가 필요함!!
        if (handler instanceof HandlerMethod) {
            HandlerMethod hm = (HandlerMethod) handler;//호출할 컨트롤러 메서드의 모든 정보가 포함되어 있다.
        }

        log.info("REQUEST [{}][{}][{}]", uuid, requestURI, handler);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.info("postHandle [{}]", modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        String requestURI = request.getRequestURI();
        String logId = (String) request.getAttribute(LOG_ID);
        log.info("RESPONSE [{}][{}][{}]", logId, requestURI, handler);
        if (ex != null) {
            log.error("afterCompletion error!!", ex);
        }
    }
}
```
- `request.setAttribute(LOG_ID, uuid);`
  	- 서블릿 필터의 경우 지역변수로 해결이 가능하지만, 스프링 인터셉터는 호출 시점이 완전히 분리되어 있다. preHandle, postHandle, afterCompletion에서 함께 사용하려면 어딘가에 담아둬야 한다. LogInterceptor도 싱글톤처럼 사용되기 때문에 멤버변수를 사용하려면 위험하다. 따라서 request에 담아두었다. 


##### HandlerMethod
- 스프링을 사용하면 일반적으로 @Controller, @RequestMapping을 활용한 핸들러 매핑을 사용하는데, 이 경우 핸들러 정보로 HandlerMethod가 넘어옵니다.

##### ResourceHttpRequestHandler
- @Controller가 아니라 /resources/static 같은 정적 리소스가 호출 되는 경우
ResourceHttpRequestHandler가 핸들러 정보로 넘어오기때문에 타입에 따라 처리가 필요합니다.

#### 인터셉터 등록 - WebConfig
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

@Override
public void addInterceptors(InterceptorRegistry registry) {
registry.addInterceptor(new LogInterceptor())
.order(1)
.addPathPatterns("/**")
.excludePathPatterns("/css/**", "/*.ico", "/error");
}
//...
}
```
- WebMvcConfigurer가 제공하는 addInterceptors()를 사용해서 인터셉터를 등록할 수 있다. 
- registry.addInterceptor(new LogInterceptor()) : 인터셉터를 등록한다.
- order(1) : 인터셉터의 호출 순서를 지정한다. 낮을 수록 먼저 호출된다.
- addPathPatterns("/**") : 인터셉터를 적용할 URL 패턴을 지정한다.
- excludePathPatterns("/css/**", "/*.ico", "/error") : 인터셉터에서 제외할 패턴을 지정한다.


#### 실행 로그 
<img width="100%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/7dd4ceaa-25be-4f09-8362-aa2c1eb1c56a">


### 스프링 인터셉터 - 로그인 인증 체크 
#### 로그인 인증 체크 인터셉터 - LoginCheckInterceptor

```java
import hello.login.web.session.SessionConst;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@Slf4j
public class LoginCheckInterceptor implements HandlerInterceptor {

    /**
     * 로그인 인증 체크
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        String requestURI = request.getRequestURI();

        log.info("인증 체크 인터셉터 실행 {}", requestURI);

        HttpSession session = request.getSession();
        if (session == null || session.getAttribute(SessionConst.LOGIN_MEMBER) == null) {
            log.info("미인증 사용자 요청");
            //로그인으로 redirect
            response.sendRedirect("/login?redirectURL=" + requestURI);
            return false;
        }

        return true;
    }
}
```

#### 인터셉터 등록 - WebConfig
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LogInterceptor())
                .order(1)
                .addPathPatterns("/**")
                .excludePathPatterns("/css/**", "/*.ico", "/error");

        registry.addInterceptor(new LoginCheckInterceptor())
                .order(2)
                .addPathPatterns("/**")
                .excludePathPatterns("/", "/members/add", "/login", "/logout",
                        "/css/**", "/*.ico", "/error"); // 로그인 체크 배제
    }
}
 ```

> 정리 : 서블릿 필터와 스프링 인터셉터는 웹과 관련된 공통 관심사를 해결하기 위한 기술이다. 특별한 문제가 없다면 인터셉터를 사용하는 것이 좋습니다.


### ArgumentResolver활용
- 요청 매핑 헨들러 어뎁터 구조에서 ArgumentResolver 이용해서 로그인 회원을 조금 더 편리하게 찾을 수 있습니다. 

#### @Login 애노테이션 생성

 ```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface Login {
}
 ```

#### LoginMemberArgumentResolver 생성
- 로그인 인증체크

 ```java
import hello.login.domain.member.Member;
import hello.login.web.session.SessionConst;
import lombok.extern.slf4j.Slf4j;
import org.springframework.core.MethodParameter;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

@Slf4j
public class LoginMemberArgumentResolver implements HandlerMethodArgumentResolver {

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        log.info("supportsParameter 실행");

        boolean hasLoginAnnotation = parameter.hasParameterAnnotation(Login.class);
        boolean hasMemberType = Member.class.isAssignableFrom(parameter.getParameterType());

        return hasLoginAnnotation && hasMemberType;
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {

        log.info("resolveArgument 실행");

        HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
        HttpSession session = request.getSession(false);
        if (session == null) {
            return null;
        }

        return session.getAttribute(SessionConst.LOGIN_MEMBER);
    }
}
 ```

##### HomeController - 추가
 ```java
    /**
     * 요청 매핑 핸들러 어댑터 구조 ArgumentResolver 사용
     *
     */
    @GetMapping("/")
    public String homeLogin(@Login Member loginMember, Model model) {

        //세션에 회원 데이터가 없으면 home
        if (loginMember == null) {
            return "home";
        }

        //세션이 유지되면 로그인으로 이동
        model.addAttribute("member", loginMember);
        return "loginHome";
    }
```

##### WebConfig - 설정추가
	
 ```java
        @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new LoginMemberArgumentResolver());
    }
```

<br>

## 예외 처리와 오류 페이지
### 서블릿 예외 처리 
#### 예외 발생 흐름
- WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)

#### sendError 흐름
WAS(sendError 호출 기록 확인) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(response.sendError())

#### 예외 발생과 오류 페이지 요청 흐름
1. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
2. WAS `/error-page/500` 다시 요청 -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(/error-page/500) -> View

### 서블릿 예외 처리 - 필터
- 앞에서 컨트롤러에서 예외가 발생해서 WAS까지 전달되고, 다시 WAS가 재요청을 할 때 필터가 중복 요청되는 현상을 해결하기 위해서는 클라이언트로 부터 발생한 정상 요청인지, 아니면 오류 페이지를 출력하기 위한 내부 요청인지 구분할
수 있어야 한다. 서블릿은 이런 문제를 해결하기 위해 DispatcherType 이라는 추가 정보를 제공한다.
- DispatcherType REQUEST : 클라이언트 요청, ERROR : 오류 요청

### 스프링 부트 오류 페이지 
- 스프링 부트는 ErrorPage를 자동으로 등록해줍니다.
- 이때 /error라는 경로로 기본 오류 페이지를 설정해줍니다.
	- new ErrorPage("/error"), 상태코드와 예외를 설정하지 않으면 기본 오류 페이지로 사용됩니다.
   	- 서블릿 밖으로 예외가 발생하거나, response.sendError(...)가 호출되면 모든 오류는 /error를 호출합니다.
- BasicErrorController라는 스프링 컨트롤러를 자동으로 등록합니다.
	<img width="70%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/d557b759-f17a-41e8-8f4f-97c0d4e30588">


> 스프링 부트가 제공하는 기본 오류 로직이 BasicErrorController에 제공됩니다. 개발자는 오류 페이지 화면만 BasicErrorController가 제공하는 룰과 우선수위에 따라서 등록해줍니다.
> 우선순위 : 1. 뷰 템플릿, 2. 정적 리소스(static, public), 3. 적용 대상이 없을 대 뷰 이름

> 실무에서는 고객이 이해할 수 있는 간단한 오류 메시지나 페이지만 보여주고 오류는 서버에 로그를 남겨서 로그로 확인 하도록 합니다.

### 예외 처리와 오류 페이지 정리
- 스프링 부트를 사용하면 사용자에게 제공하는 `간단한 오류 페이지(html)만 추가`하여 처리 할 수 있습니다.
  	- 4xx.html, 404.html, 500.html 추가해주면 됩니다. 
- 그 이유는 스프링 부트에서 기본적으로 BasicErrorController에 기능이 제공되어 있기 때문입니다.


<br>

## API 예외 처리
- 스프링이 제공하는 ExceptionResolver
- 스프링이 제공하는 ExceptionResolver는 다음과 같다.
- HandlerExceptionResolverComposite에 아래와 같은 순서로 등록합니다.
  1. **ExceptionHandlerExceptionResolver**
		- @ExceptionHandler, API 예외 처리는 대부분 이 기능으로 해결
  2. **ResponseStatusExceptionResolver**
		- @ResponseStatus(value = HttpStatus.NOT_FOUND), HTTP 상태 코드를 지정해줌
  3. **DefaultHandlerExceptionResolver**
		- 우선 순위가 가장 낮다

### API 예외 처리 - @ExceptionHandler
- 단순히 오류 화면을 보여주는 것이 아니라, 스펙에 따라 다르다.
- 동일한 Exception임에도 Controller마다 다르게 보여줘야 한다면?

#### ErrorResult
- 예외가 발생했을 때 API 응답으로 사용하는 객체를 정의
```java
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.ToString;


@Getter
@AllArgsConstructor
@ToString
public class ErrorResult {
    private String code;
    private String message;
}
```

#### ApiExceptionV2Controller
- @ExceptionHandler 애노테이션을 선언하고, 해당 컨트롤러에서 처리하고 싶은 예외를 지정해주면 된다.
```java
import hello.exception.exception.UserException;
import hello.exception.exhandler.ErrorResult;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.ToString;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

/**
 * 스프링 @ExcpetionHandler // 해당 컨트롤러에서 처리하고 싶은 예외를 지정할 수 있다. (다른 컨트롤러 영향 안줌)
 */
@Slf4j
@RestController
public class ApiExceptionV2Controller {

    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    @ExceptionHandler
    public ErrorResult exHandle(Exception e) {
        log.error("[exceptionHandle] ex", e);
        return new ErrorResult("EX", "내부 오류");
    }

    @ExceptionHandler
    public ResponseEntity<ErrorResult> userExHandle(UserException e) {
        log.error("[exceptionHandle] ex", e);
        ErrorResult errorResult = new ErrorResult("USER-EX", e.getMessage());
        return new ResponseEntity<>(errorResult, HttpStatus.BAD_REQUEST);
    }

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(IllegalArgumentException.class)
    public ErrorResult illegalExHandle(IllegalArgumentException e) {
        log.error("[exceptionHandle] ex", e);
        return new ErrorResult("BAD", e.getMessage());
    }

    @GetMapping("/api2/members/{id}")
    public MemberDto getMember(@PathVariable("id") String id) {

        if (id.equals("ex")) {
            //throw new RuntimeException("잘못된 사용자");
            throw new RuntimeException();
        }
        if (id.equals("bad")) {
            throw new IllegalArgumentException("잘못된 입력 값");
        }
        if (id.equals("user-ex")) {
            throw new UserException("사용자 오류");
        }

        return new MemberDto(id, "hello " + id);
    }

    @Getter
    @AllArgsConstructor
    @ToString
    static class MemberDto {
        private String memberId;
        private String name;
    }
}
```

> 컨트롤러마다 다르게 예외를 보여주고 싶을 때 적용, 그러나 공통적으로 다른 컨트롤러도 동일하게 예외처리를 하고 싶을 때 다음에 나올 @ControllerAdvice를 사용하면 됩니다. 


### API 예외 처리 - @ControllerAdvice
```java
import hello.exception.exception.UserException;
import hello.exception.exhandler.ErrorResult;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@Slf4j
@RestControllerAdvice(basePackages = "hello.exception.api")
public class ExControllerAdvice {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(IllegalArgumentException.class)
    public ErrorResult illegalExHandler(IllegalArgumentException e) {
        log.error("[exceptionHandler] ex", e);
        return new ErrorResult("BAD", e.getMessage());
    }

    @ExceptionHandler
    public ResponseEntity<ErrorResult> userExHandler(UserException e) {
        log.error("[exceptionHandler] ex", e);
        ErrorResult errorResult = new ErrorResult("USER-EX", e.getMessage());
        return new ResponseEntity(errorResult, HttpStatus.BAD_REQUEST);
    }

    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    @ExceptionHandler
    public ErrorResult exHandler(Exception e) {
        log.error("[exceptionHandler] ex", e);
        return new ErrorResult("EX", "내부 오류");
    }

}
```
- **@RestControllerAdvice 는 @ControllerAdvice 와 같고, @ResponseBody 가 추가되어 있다. @Controller , @RestController 의 차이와 같다.**
- @RestControllerAdvice
	- 대상을 지정하지 않으면 모든 컨트롤러에 적용된다. (글로벌 적용)
- @RestControllerAdvice(annotations = RestController.class)
  	- 특정 어노테이션 대상만 적용할 수 있다.
- @ControllerAdvice(assignableTypes = {ControllerInterface.class,AbstractController.class})
  	- 특정 클래스 대상만 적용할 수 있다.
- @ControllerAdvice("org.example.controllers")
  	- 특정 패키지 대상만 적용할 수 있다.

 
> 정리, 실무에서 예외를 공통으로 처리하는 게 굉장히 중요하기 때문에 @ControllerAdvice와 @ExceptionHandler 조합하면 예외를 적절한 상황에 맞게 깔끔하게 해결할 수 있습니다.
