
# 🌱 스프링 MVC 2편 - 백엔드 웹 개발 핵심 기술
출저 : [`인프런 김영한 - 스프링 MVC 2편 백엔드 웹 개발 핵심 기술`](https://inf.run/AV7T)

<br>

## 목차
- [타임리프 기본 기능](#타임리프-기본-기능) 
- [타임리프 스프링 통합과 폼](#타임리프-스프링-통합과-폼) 
- [메시지, 국제화](#메시지-국제화) 
- [검증1 Validation](#검증1---validation)
- [검증2 Bean Validation](#검증2---bean-validation) 

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

Bean Validation 인터페이스 
<img width="50%" alt="image" src="https://github.com/yungenie/study-spring/assets/28051638/86563244-3b3c-4318-98c3-22b35dbb7505">

Bean Validation 인터페이스의 구현체 (hibernate) 실제 동작하는 구현체
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



### Bean Validation 에러코드

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










