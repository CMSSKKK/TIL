> 객체 지향 4가지 핵심 요소
>
> 1. 캡슐화
> 2. 상속
> 3. 다형성
> 4. 추상화

## 객체지향 설계 5원칙

### 1. SRP (Single Responsibillity Principle) 단일 책임 원칙

* 어떠한 클래스를 변경해야 하는 이유는 한가지 뿐 이여야 한다.

### 2.  OCP (Open Closed Principle) 개방 폐쇄 원칙

* 자신의 확장에는 열려 있고, 주변으 변화에 대해서는 닫혀 있어야 한다.
* 상위 클래스 또는 인터페이스를 중간에 둠으로써, 자신의 변화에 대해서는 폐쇄적이지만, 인터페이스는 외부의 변화에 대해서 확장을 개방해 줄 수 있다.
* 이러한 부분은 JDBC 와 MyBatis, Hibernate 등 JAVA에서는 Stream(Input, Out)에서 찾아볼 수 있다.

### 3. LSP (Liskov Substitution Principle) 리스코프 치환 원칙

* 서브 타입은 언제나 자신의 기반(상위) 타입으로 교체 할 수 있어야 한다. 

### 4. ISP (Interface Segragation Principle) 인터페이스 분리 원칙

* 클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안된다.
* 프로젝트 요구 사항과 설계에 따라서 SRP / ISP 를 선택한다.

### 5. DIP (Dependency inversion principle) 의존 역전 원칙

* 자신보다 변하기 쉬운 것에 의존하지 말아야 한다.

# DesignPattern

## 1. Singleton pattern

* 싱글톤 패턴은 어떠한 클래스가 유일하게 1개만 존재 할 때 사용한다.
* 이를 주로 사용하는 곳은 서로 자원을 공유 할 때 사용하는데, 실물 세계에서는 프린터가 해당되며, 실제 프로그램밍에서는 TCP Socket 통신에서 서버와 연결된 connect 객체에 주로 사용한다.

## 2. Adapter pattern

* 어댑터는 실생활에서는 110v 를 220v로 변경해주거나, 그 반대로 해주는 흔히 돼지코라고 불리는 변환기를 예로 들 수 있다.
* 혼환성이 없는 기존 클래스의 인터페이스를 변환하여 재사용 할 수 있도록 한다.
* SOLID 중에서 개방폐쇄 원칙(OCP)를 따른다.
