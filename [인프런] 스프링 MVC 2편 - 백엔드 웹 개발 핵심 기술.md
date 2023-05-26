
# 🌱 스프링 MVC 2편 - 백엔드 웹 개발 핵심 기술
출저 : [`인프런 김영한 - 스프링 MVC 2편 백엔드 웹 개발 핵심 기술`](https://inf.run/AV7T)

<br>

## 목차
- [타임리프 기본 기능](#타임리프-기본-기능) 
- [타임리프 스프링 통합과 폼](#타임리프-스프링-통합과-폼) 
- [메시지, 국제화](#메시지-국제화) 
- [검증1 Validation](#검증1---validation)

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
- 실제 넘어오는 구현체는 BeanPropertyBindingResult 라는 것인데, 둘다 구현하고 있으므로 BindingResult 대신에 Errors 를 사용해도 된다. 
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

#### [application.properties](http://application.properties) 설정
```spring.messages.basename=messages,errors```
- messages.properties 와 errors.properties 둘다 찾아 사용하게 됩니다.

#### [errors.properties](http://errors.properties) 설정

```
# 제약조건.객체명.필드명
required.default= 필수로 입력해주세요.
required.item.itemName=상품 이름은 필수입니다.
range.item.price=가격은 {0} ~ {1} 까지 허용합니다.
max.item.quantity=수량은 최대 {0} 까지 허용합니다.
totalPriceMin=가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}
```

```java
public String addItemV3(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
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




### 오류 코드와 메시지 처리2

- bindingResult.FieldError()를 직접 다룰 때는 오류 코드를 range.item.price 정의한 코드를 모두 입력했다. bindingResult.rejectValue()를 사용해서 축약된 오류 코드를 사용할 수 있다. (범용성으로 간단하게 작성할 때)

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

### 오류 코드와 메시지 처리3

- 처리2는 범용적으로 간단하게 작성할 때 좋다. 세밀하게 작성해야 할 때는 메시지에 단계를 두고 사용하면 된다.
