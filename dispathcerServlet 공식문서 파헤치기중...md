[hierarchy](https://docs.spring.io/spring-framework/docs/5.1.9.RELEASE/spring-framework-reference/images/mvc-context-hierarchy.png)

## Special Bean Types

DispatcherServlet은 요청을 처리하고 적절한 응답을 렌더링하기 위해서 special bean에 위임한다.

Special bean은  Spring 관리 객체 인스턴스(프레임워크 규약 구현)를 의미한다.

일반적으로 기본 규약으로 되어있지만 커스텀하게 속성을 확장, 교체할 수 있다.



### HandlerMapping

- 인터셉터와 함께 핸들러들을 요청(request)에 매핑한다.
- 매핑은 `HandlerMapping` 구현에 따른다.
- 대표적인 `HandlerMapping` 구현은 `RequestMappingHandlerMapping` (@RequestMapping 어노테이션 사용한 매서드)` SimpleUrlHandlerMapping`(URI 경로패턴 매핑)

### HandlerAdapter

- DispatcherServlet가 핸들러가 실제로 호출되는 것과는 관계없이 요청에 매핑된 핸들러를 호출되게 도와주는 역할
- DispatcherServlet이 세부사항으로부터 보호하는 역할

### HandlerExceptionResolver

- 예외를 처리하기 위한 전략(가능하면 익셉션핸들러, HTML 에러 뷰, 또는 다른 타겟)

### ViewResolver

- String `View`이름을 기준으로 렌더링할 뷰 객체를 정해주는 역할





### LocaleResolver, LocaleContextResolver

- 국제적인 view를 제공하기위해서, 사용자가 사용하는 타임존(로케일)을 맞춰준다. 

### ThemeResolver

- 웹 어플리케이션에서 사용하는 테마를 맞춰준다.

### MultipartResolver

- multipart parsing library의 도움을 받아서 브라우저에서 파일을 업로드하는 경우처럼 multi-part 요청을 파싱하기위한 추상화

### FlashMapManager

- 리다이렉션을 통해서 다른 요청으로 속성을 전달하는데 사용할 수 있는 input, output FlashMap을 저장하고 검색

## Web MVC Config

-`DispatcherServlet` 은 위의 special bean들 각각에 대하여 `WebApplicationContext` 를 체크한다. 만약 매칭되는 빈이 없으면 DispatcherServlet.properties 파일에 나열된 default로 정의된다.



## DispatcherServlet Request Processing

- 기본적으로 httpRequest를 처리(그 요청을 위임해서 HandlerAdapter를 통해서 처리) 

1. WebApplicationContext는 컨트롤러와 프로세스의 다른 요소가 사용할 수 있는 속성으로 요청에서 검색되고 바인딩된다. 기본적으로 DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE 키에 바인딩된다.

2. dispatcherServlet은 getHandler()메서드를 사용하여 디스패처에 구성된 HandlerAdapter 인터페이스의 모든 구현체를 찾는다. 발견된 구현체들은 나머지 프로세스를 통해서 handle() 메서드로 request를 처리한다.

3. LocaleResolver는 선택적으로 요청에 바인딩되어 프로세스의 요소가 로케일을 확인하도록 한다.

4. ThemeResolver는 View와 같은 요소가 사용할 테마를 결정하도록 하는 요청에 선택적으로 바인딩된다.

5. request가 MultipartFiles인지 확인해서 MultipartResolver가 지정된다면, 추가 처리를 위해 MultipartHttpServletRequest에 래핑된다.

6. `WebApplicationContext`에 선언된 `HandlerExceptionResolver` 빈은 request 처리 중엔 발생한 예외를 해결하는데 사용된다.. 커스텀할 수 있다.

   

### 