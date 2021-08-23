# String/ StringBuilder/ StringBuffer

> String과 StringBuilder 그리고 StringBuffer의 차이



## String vs StringBuilder/StringBuffer

* 가장 큰 차이점으로는 **String**은 불변(immutable)하며, builder와 buffer는 가변성을 가지고 있어서 추가, 삭제, 수정 등이 빈번할 때 사용한다.
* **String**에 다른 단어를 더해서 사용하면 기존의 인스턴스를 수정하는 것이 아니라 새로운 인스턴스를 생성하는 것이다.
* 그래서 기존의 인스턴스의 메모리 영역을 가르키던 참조변수가 새로운 인스턴스의 메모리 영역을 가르키게 되어 기존의 메모리 영역는 가비지가 되어 이후에 가비지컬렉션에 의해 수거된다.



## StringBuilder vs StringBuffer

* 차이점은 동기화의 지원 유무이다.
* **StringBuffer**는 동기화 키워드를 지원하여 멀티쓰레드 환경에서 안전하다는 점(thread-safe)이다.
  * 참고로 **String**도 불변성을 가지기때문에 마찬가지로  멀티쓰레드 환경에서의 안정성(thread-safe)을 가진다. 
*  **StringBuilder**는 동기화를 지원하지 않기때문에 멀티쓰레드 환경에서 사용하는 것은 적합하지 않지만 동기화를 고려하지 않는 만큼 단일쓰레드에서의 성능은 StringBuffer 보다 뛰어나다.



## 정리

* **String** : 문자열 연산이 적고 멀티쓰레드 환경일 경우
* **StringBuffer** : 문자열 연산이 많고 멀티쓰레드 환경일 경우
* **StringBuilder** : 문자열 연산이 많고 단일쓰레드이거나 동기화를 고려하지 않아도 되는 경우 

출처: https://ifuwanna.tistory.com/221 [IfUwanna IT]

