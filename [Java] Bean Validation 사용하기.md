# [Java] Bean Validation 사용하기



* 학습계기

  * 회원가입과 관련한 로직을 학습하면서  null과 정규식과 관련한 검증을 처리하고 싶었다.

  * 하지만, 각각의 변수마다 검증메서드를 통해서 검증하는것이 꽤 번거로운 작업이라고 생각되서, 방법을 찾아보게 되었다.

    

## Bean Validation

* 어노테이션 형태로 제약 조건을 달아줘서 쉽게 검증할 수 있도록 돕는 API이다.
* Bean Validation은 인터페이스로 된 명세일 뿐이고 실제 동작할 수 있도록 구현한 것이 `Hibernate Validator`이다.

## SpringBoot에서 사용하기

* gradle dependency에 추가하기

```java
implementation 'org.springframework.boot:spring-boot-starter-validation'
```

* 적용하기 

```java
import javax.validation.constraints.Email;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotEmpty;

public class UserDto {

    @NotBlank
    private final String userId;
    @NotBlank
    private final String password;
    @NotBlank
    private final String name;
    @NotEmpty
    @Email
    private final String email;

    public UserDto(String userId, String password, String name, String email) {
        this.userId = userId;
        this.password = password;
        this.name = name;
        this.email = email;
    }
  
  // ...
  
} 
```



> 이 외에도 훨씬 많은 제약조건을 달 수 있는 어노테이션들이 많다.



## @NotNull @NotEmpty @NotBlank

* 단순하게 null이 아니고, 비어있지않고, 공백이 아니다라고는 알고있다.
* 그렇다면 String의 경우 null도 안되고, 비어있지도 않고, 공백도 아니여야 한다면 세가지 어노테이션을 다 달아줘야할까?



### @NotNull

* 제한된 CharSequence, Collection, Map, Array는 null만 아니라면 유효하지만, 비어있을 수는 없다.

### @NotEmpty

* 제한된 CharSequence, Collection, Map, Array는 null이 아니고 size또는 length가 0보다 커야한다.

### @NotBlank

* String은 null이 아니고 trimmed length가 0보다 커야한다.



## 정리

* `@NotEmpty`와 `@NotBlank`의 경우는 검증 메서드 내에서 `@NotNull`의 `isValid()`메서드를 먼저 사용하기 때문에 null값도 통과할 수 없다.
* `@NotBlank`의 경우는 위의 내 코드처럼 `String` 의 경우에만 해당한다.



## 테스트하기

* @NotBlank(messages = "공백안돼요~") 와 같이 message를 직접 정해줄 수 있다.
* ~~물론 기본 메시지가 있는데, 한글메시지가 나와서 신기했다...~~

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import java.util.Set;

import static org.assertj.core.api.Assertions.assertThat;


class UserDtoTest {

    private Validator validator;

    @BeforeEach
    void setUp() {
        validator = Validation.buildDefaultValidatorFactory().getValidator();
    }

    @Test
    @DisplayName("userDto userId,password,name,email에 null이 담기면 안된다.")
    void userDto_nullTest() {
        UserDto userDtoNull = new UserDto(null, null, null, null);
        Set<ConstraintViolation<UserDto>> violations = validator.validate(userDtoNull);

        violations.forEach(i -> System.out.println(i.getMessage()));
        assertThat(violations.size()).isEqualTo(4);
    }

    @Test
    @DisplayName("userDto userId,password,name,email가 비어있으면 안된다.")
    void userDto_emptyTest() {
        UserDto userDtoEmpty = new UserDto("", "", "", "");
        Set<ConstraintViolation<UserDto>> violations = validator.validate(userDtoEmpty);

        violations.forEach(i -> System.out.println(i.getMessage()));
        assertThat(violations.size()).isEqualTo(4);
    }

    @Test
    @DisplayName("userDto userId,password,name,email에 공백이 담기면 안된다.")
    void userDto_blankTest() {
        UserDto userDtoEmpty = new UserDto(" ", " ", " ", " ");
        Set<ConstraintViolation<UserDto>> violations = validator.validate(userDtoEmpty);

        violations.forEach(i -> System.out.println(i.getMessage()));
        assertThat(violations.size()).isEqualTo(4);
    }

    @Test
    @DisplayName("userDto email은 email 정규식을 지켜야한다.")
    void userDto_emailRegexTest() {
        UserDto userDtoWrongEmail = new UserDto("ron2", "1234", "ron2", "failEmail");
        Set<ConstraintViolation<UserDto>> violations = validator.validate(userDtoWrongEmail);

        violations.forEach(i -> System.out.println(i.getMessage()));
        assertThat(violations.size()).isEqualTo(1);
    }

    @Test
    @DisplayName("userDto 검증 통과 테스트")
    void userDto_successTest() {
        UserDto userDtoSuccess = new UserDto("ron2", "1234", "ron2", "ron2@gmail.com");
        Set<ConstraintViolation<UserDto>> violations = validator.validate(userDtoSuccess);

        assertThat(violations.size()).isEqualTo(0);
    }

}

```



## @Valid 사용하기

* Post 전송시 UserDto를 검증한다.

```java
@PostMapping("/user/create")
    public String joinUser(@Valid UserDto userDto) {
        logger.info("{} joined", userDto);
        userService.join(userDto.toEntity());

        return "redirect:/users";
    }
```

* 제약 조건에 일치하지 않는 경우, `BindException`이 발생한다.





> 정규식과 관련하거나 다른 검증이 필요하고 사용하게될때마다 사용법에 대해서 이 글에 더 정리해야겠습니다. 
>
> 개인의 정리이기 때문에 틀린 점이 있을 수 있습니다. 

## 참고

* https://www.baeldung.com/java-bean-validation-not-null-empty-blank
* https://www.baeldung.com/javax-validation
* https://stackoverflow.com/questions/48986091/hibernate-notempty-is-deprecated