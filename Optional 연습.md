# Optional 연습

> [nextstep](https://edu.nextstep.camp/), 자바의 정석, [baeldung](https://www.baeldung.com/java-optional) 참고하여 정리하였습니다.

## Optinal이란?

* 일종의 래퍼클래스다.
* `Optional<T>` T타입의 객체를 감싸는 형식
* optional 클래스의 메서드를 통해서 if문없이 null 체크가 가능하다는 장점이 있다.
* `of()` 또는 `ofNullable()` 로 생성할 수 있다.
* 단순히 `of()`메서드로 생성하면 안에 참조변수가 null이라면 nullpointerexception이 터진다. 그래서 null이 입력될 가능성이 있다면 `ofNullable()`를 사용해서 생성해야한다.
* Optional 클래스는 map, filter, get, orElse, orElseGet, orElseThrow, isPresent, ifPresnet, ifPresentOrElse 등의 메서드들을 제공한다.
* 각각의 메서드들은 라이브러리 및 [optional](https://www.baeldung.com/java-optional) 참고해서 활용을 연습해보면 좋을듯하다.

> 넥스트스텝 optional 연습 예제

```java
public static boolean ageIsInRange1(User user) {
        boolean isInRange = false;

        if (user != null && user.getAge() != null
                && (user.getAge() >= 30
                && user.getAge() <= 45)) {
            isInRange = true;
        }
        return isInRange;

    }

    public static boolean ageIsInRange2(User user) {
        Optional<User> optionalUser = Optional.ofNullable(user);
        return optionalUser.map(User::getAge)
          .filter(age -> age >= 30)
          .filter(age -> age <= 45)
          .isPresent();
    }
```

```java
User getUser(String name) {
//        for (User user : users) {
//            if (user.matchName(name)) {
//                return user;
//            }
//        }
//        return DEFAULT_USER;
        return users.stream().filter(user -> user.matchName(name)).findFirst().orElse(DEFAULT_USER);
    }
```

```java
// enum

static Expression of(String expression) {
//        for (Expression v : values()) {
//            if (matchExpression(v, expression)) {
//                return v;
//            }
//        }
//
//        throw new IllegalArgumentException(String.format("%s는 사칙연산에 해당하지 않는 표현식입니다.", expression));

        return Arrays.stream(values()) 	
                .filter(v -> matchExpression(v,expression))
                .findFirst()
                .orElseThrow(()->new IllegalArgumentException(String.format("%s는 사칙연산에 해당하지 않는 표현식입니다.", expression)));
    }
```

```java
public static String getVersion(Computer computer) {
        String version = UNKNOWN_VERSION;
        if (computer != null) {
            Soundcard soundcard = computer.getSoundcard();
            if (soundcard != null) {
                USB usb = soundcard.getUsb();
                if (usb != null) {
                    version = usb.getVersion();
                }
            }
        }
        return version;
    }

    public static String getVersionOptional(Computer computer) {
        Optional<Computer> optionalComputer = Optional.ofNullable(computer);
        String version = optionalComputer.map(Computer::getSoundcard)
                .map(Soundcard::getUsb)
                .map(USB::getVersion)
                .orElse(UNKNOWN_VERSION);

        return version;
    }
```