# JVM의 구조

> 미완성 글

## Class Loader

* 런타임에 바이트 코드로 된 .class 파일을 읽어서 class 객체를 메모리에 생성한다. (compile time이 아니다.)

### Loading 

* BootStrap ClassLoader
  * 모든 JVM의 구현은 신뢰할 수 있는 클래스를 로드하기 위해서 bootstrap classLoader를 반드시 필요로 한다.
  * “JAVA_HOME/jre/lib” 디렉토리 안에 있는 자바 API를 로드한다.
  * c와 c++같은 네이티브 언어로 구현된다.
* Extension ClassLoader
  * bootstreap classLoader의 child이다.
  * “*JAVA_HOME/jre/lib/ext”*(Extension path)와 같은 확장 경로 내의 클래스를 로드한다.
  * 자바 내의 *sun.misc.Launcher$ExtClassLoader* class로 구현된다.
* Application ClassLoader
  * Extension classLoader의 child이다.
  * 애플리케이션 클래스패스에서 클래스를 로드한다.
  * java.class.path에 매핑된 환경 변수를 내부적으로 사용한다.
  * 자바 내의 *sun.misc.Launcher$ExtClassLoader* class로 구현된다.

### Linking

* Verify : 바이트코드가 올바른지 검증
* Prepare : 스태틱 변수를 메모리에 디폴트값으로 할당
* Resolve : symbolic references는 메소드 영역의 원래 참조로 대체
* symbolic reference는 실제 개체를 검색하는 데 사용하는 기호(문자열)

### Initailization

* 모든 static 변수를 원래 값으로 할당하고 static block이 있다면 할당한다.

## Runtime Data Area

### Method Area

### Heap Area

### Stack Area

### PC Registers

### Native Method stacks

## Excution Engine

### Interpreter

### JIT Compiler

### Garbage collector

### etc

* Java Native Interface(JNI)
* Native Method Libraries





## Reference

* https://dzone.com/articles/jvm-architecture-explained
* https://www.geeksforgeeks.org/jvm-works-jvm-architecture/







