# JVM의 구조

## 1. Class Loader

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

## 2. Runtime Data Area

### Method Area

* static 변수를 포함해서 모든 클래스 수준 데이터를 저장
* JVM당  하나의 메서드 영역만 있으며, 공유자원이다.

### Heap Area

* 모든 객체와 해당 인스턴스 변수 및 배열이 저장
* JVM당 하나의 힙 영역이 있다. 
* 메서드 영역과 힙 영역은 여러 스레드에 대해 메모리를 공유하기 때문에 스레드세이프 하지 않다.

### Stack Area

* 모든 스레드에 대해서, 각각 런타임 스택이 생성된다.
* 모든 메서드 호출에서 스택 프레임을 스택 메모리에 생성한다.
* 모든 지역변수는 스택 메모리에 저장된다.
* 스택 영역은 공유 자원이 아니여서 스레드세이프하다.

#### Stack Frame

* Local Variable Array : 메서드의 지역 변수를 갖는다.
* Operand stack : 메서드 내  중간작업이 필요한 경우 작업을 위한 런타임 공간
* Frame data : 메서드와 관련한 symbol들이 저장된다. 만약 익셉션이 발생하면 catch블록 정보를 유지한다.

### PC Registers

* 모든 스레드는 각각 별도의 PC Reigsters를 가지고 있다.
* 명령이 실행되면 현재 실행 중인 명령의 주소를 유지하기 위해서 PC register가 다음 명령으로 업데이트된다.

### Native Method stacks

* Native method 정보를 가지고 있다.
* 모든 스레드는 별도의 native method stack을 생성한다.

## 3. Excution Engine

* 런타임 데이터 영역에 할당된 바이트코드를 excution engine이 실행한다.
* excution engine이 바이트코드를 읽고 하나하나 실행한다.

### Interpreter

* 인터프리터는 바이트코드를 해석하는 것은 빠르지만 실행하는 것은 느리다.
* 인터프리터는 하나의 메서드가 여러번 호출될때 매번 새롭게 해석하기 때문에 비효율적이다.

### JIT Compiler

* JIT compiler는 인터프리터의 단점을 상쇄한다.
* 실행엔진은 바이트코드를 변환할 때는 인터프리터를 활용하지만, 반복되는 코드를 찾게되면 JIT compiler를 사용해서 전체바이트코드를 컴파일하고, 네이티브코드로 변경하도록 한다.
* 변경된 네이티브 코드는 반복되는 메서드 호출에 사용되어 시스템 성능을 향상 시킨다.

1. Intermediate Code Generater : 중간 코드 생성
2. Code Optimizer : 생성된 중간코드를 최적화
3. Target Code Generator : 기계어 코드 혹은 네이티브 코드를 생성
4. Profiler : 핫스팟(메서드가 여러번 호출되었는지의 여부) 찾기

### Garbage collector

* 참조되지 않는 객체를 수집하고 제거한다.
* 가비지 컬렉션은 `System.gc()`를 호출해서 작동하게 할 수 있지만, 실행이 보장되지는 않는다.
* JVM의 가비지 컬렉션은 생성된 객체를 수집한다.

### etc

* Java Native Interface(JNI) : 네이티브 메서드 라이브러리와 상호작용하고 실행 엔진에 필요한 네이티브 라이브러리를 제공
* Native Method Libraries : 실행엔진에 필요한 네이티브 라이브러리의 모음





## Reference

* https://dzone.com/articles/jvm-architecture-explained
* https://www.geeksforgeeks.org/jvm-works-jvm-architecture/







