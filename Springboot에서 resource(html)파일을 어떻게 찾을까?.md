# Springboot에서 resource(html)파일을 어떻게 찾을까?

## 공부하게 된 계기

- 스프링부트를 통해서 게시판을 구현하면서, `Controller`에서 `viewName`을 `String`으로 리턴하는 방식으로 구현하게 되었다.
- 강의 혹은 다양한 블로그글을 보면서 원리에 대한 이해없이 resources/static에 있는 html파일을 매핑하는 것이 당연하다고 생각했었다.
- 왜 그렇게 되는 것인지 크게 고민하지 않았고, 아무런 설정이 없어도 스프링이 잘 찾는구나 하고 넘어갔었다.
- 하지만 이후에 mustache 템플릿 엔진을 사용한 동적페이지를 구성하면서도 당연하게 `templates` 디렉토리에 html 파일들을 담았고, classpath없이 정적페이지와 동일하게`viewName`을 `String` 으로 리턴했다.

### 문제 발생

- ide와 로컬에서 테스트를 하면서 아무런 에러없이 구현이 잘 되었다.
- 하지만 heroku에 배포를 하면서 문제가 발생했다. (사실 html파일을 찾는 원리와는 딱히 관계가 없을 수 도 있다.)

### 그래서 문제는?

- heroku에 배포하면서 빌드도 잘 되었고, log를 확인했을 때 서비스 로직에서는 문제가 없었다.
- 하지만, html파일을 매핑하지 못했고, `404 not found` 에러가 모든 요청에 발생했다.
- 처음에는 빌드에서 원인을 찾았으나, 문제를 찾을 수 없었다.
  https://itqna.net/questions/51018/thymeleaf-error-templates-heroku
- 이 후 많은 삽질을 하다가 이 글을 보고 문제를 해결할 수 있었다.(하지만 이 글에서도 문제에 대한 자세한 설명은 없다.)

```java
 @GetMapping("/user/sign-up")
    public String joinForm() {
        logger.info("User in joinForm");
        return "/user/form";
    }
```

- 나 또한 위처럼 viewname을 리턴했었다.

```java
return "user/form"
```

- 이렇게 수정하고 다시 배포하니 정상적으로 작동했다.
- 문제를 해결하고보니 내가 왜 `"/user/form"` 이렇게 코드를 작성했었지?? 라는 생각을 하게 되었다.
- 그리고 왜 ide에서는 정상적으로 작동하는데 heroku에서는 왜? 라는 생각도 하게 되었다.

## Spring이 작동하면서 어떻게 html파일을 찾을까??

> https://bottom-to-top.tistory.com/38 참고하였습니다.
> `mvcConfigurer`와 `addResourceHandler()`에 관해서는 위 블로그글이 잘 정리되어있습니다.

### Spring에서 default classpath는?

![img](https://media.vlpt.us/images/cmsskkk/post/29832be1-b805-4545-8646-a0a986d7661a/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%204.55.42.png)

- 참고 블로그와는 다르게 `Resources`에서 내용을 찾을 수 있다. 결론적으로 스프링에서 아무런 설정이 없어도 resources/static/에 있는 파일을 찾을 수 있다.

### 나는 templates내에 있는 파일도 설정을 안해줬는데?

- 일단 나는 template engine으로 `mustache`를 사용하였다.
- 그래서 그와 관련한 내용은 gradle에서 mustache 사용을 위해 dependency를 추가한 `mustache starter`에서 확인 할 수 있었다.
  ![img](https://media.vlpt.us/images/cmsskkk/post/5eab3881-1bc5-4495-87ef-dd678940930d/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%204.58.24.png)
- defalut prefix가 "classpath:/template/" 이다.

![img](https://media.vlpt.us/images/cmsskkk/post/9132d3be-3638-428d-a277-47c25d1a05fc/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%205.01.31.png)

- TemplateLocation이 있는지 확인하는 것을 볼 수 있다.

#### thymeleaf는?

![img](https://media.vlpt.us/images/cmsskkk/post/23a4fd18-2d4e-47d9-b8b9-d6b31e7e6e58/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%205.21.43.png)

https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html#spring-mvc-configuration

## 결론..?

https://www.inflearn.com/questions/55819

위 링크는 김영한님이 다른 질문에 대한 답변을 남겨주신 글이다.

위 설명과 동일하게 추측했을 때, 조금은 다를 수 있지만 build했을때와 ide상에서 `"user/form"`, `"/user/form"`이 접근이 다르지 않을까..?
(배포 과정에서 문제가 있을까..?)
(정확한 이유를 아시는 분이 있으시다면 댓글로 남겨주시면 정말 감사하겠습니다ㅠㅠ)

그래서 `"classpath:/template/"`로 되어있기 때문에 `"user/form"`의 방식이 더 정확하고 오류도 없을 것 같기에 이 방식으로 구현해야겠다.

### 회고

나와 같은 실수를 하는 사람이 많이 없을지도 모르겠다. 어찌되었든 문제를 해결했고 덤으로 원리도 찾아 볼 수 있는 좋은 기회가 된 것 같다.

### reference

- https://itqna.net/questions/51018/thymeleaf-error-templates-heroku
- https://github.com/AndreasKl/spring-boot-starter-mustache
- https://bottom-to-top.tistory.com/38
- https://www.inflearn.com/questions/55819
- https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html#spring-mvc-configuration

### ++참고

- jar 파일 패키지 내용 확인 하는법

```null
jar tf [jar파일이름]

jar tf cafe-0.0.1-SNAPSHOT.jar
```

![img](https://media.vlpt.us/images/cmsskkk/post/c4d9fa28-e559-499f-9ac2-9e4b53aef154/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%205.56.03.png)

- `resources` 디렉토리에 있던 파일들의 경로