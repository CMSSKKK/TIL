# ArrayList와 LinkedList

* 연속 배열과 LinkedList를 직접 구현해보면서 구조를 더 쉽게 이해하고 장단점을 파악하고자 한다.

## ArrayList

> 제가 만든 코드와 자바의 ArrayList의 메서드와 작동 방식은 차이가 있습니다. 단순히 구조를 파악하기 위한 코드입니다.

* 기본적으로 자바에서 기본 배열은 생성할 때 크기를 지정해주어야한다.
* 이와 다르게 ArrayList는 기본적으로 사이즈를 가지고 있다.
* `private static final int DEFAULT_CAPACITY = 10;` (실제 자바 API도 동일하게 10)
* 그래서 내부의 배열의 사이즈를 넘어서게 되면 내부 배열을 확장한다.
* 기본 배열을 베이스로 하기때문에 index로 값을 조회하는 것은 시간복잡도 O(1)를 가진다.
* 또한 맨 뒤에 값을 추가하게 되었을 때도 시간복잡도 O(1)을 가진다.
* 하지만 중간의 index에 새로운 값을 추가하거나, 삭제하려고 했을 때는 시간복잡도 O(N)이 된다. 왜냐하면 새로운 값을 추가한거나 삭제한 index부터 새롭게 index를 지정, 값을 옮기는 작업(shift)을 해주어야 하기 때문이다.

```java
import java.util.Arrays;
import java.util.NoSuchElementException;
import java.util.Objects;

public class MyArrayList<E> {
    private static final int DEFAULT_SIZE = 10;

    private int size;
    private Object[] array;

    public MyArrayList() {
        this.size = 0;
        array = new Object[DEFAULT_SIZE];
    }

    public void add(E e) {
        if (size >= array.length) {
            extendSize();
        }
        array[size++] = e;

    }

    // 인덱스로 값 얻어내기
    public E get(int index) {
        indexCheck(index);
        return (E) array[index];
    }

    // list 사이즈
    public int size() {
        return this.size;
    }

    public boolean isEmpty() {
        if (this.size == 0) {
            return true;
        }
        return false;
    }

    public void remove(int index) {
        indexCheck(index);
        // 길이가 하나작은 배열 생성
        Object[] arrayNew = new Object[this.size - 1];
        // 움직일 필요없는 값 담기
        for (int i = 0; i < index; i++) {
            arrayNew[i] = array[i];
        }
        // 하나의 값이 삭제됨에 따라서, 앞으로 당겨서 옮겨주기
        for (int i = array.length - 1; i > index; i--) {
            arrayNew[i - 1] = array[i];
        }
        this.array = arrayNew;
        this.size--;
    }

    //값을 모두 보여주기위해서 String으로 리턴
    public String print() {
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < size; i++) {
            stringBuilder.append(array[i]).append('\n');
        }
        return stringBuilder.toString();
    }

    // 값 변경
    public void set(int index, E e) {
        indexCheck(index);
        array[index] = e;
    }

    public int findIndex(E e) {
        for (int i = 0; i < size; i++) {
            if (e == array[i]) {
                return i;
            }
        }
        throw new NoSuchElementException();
    }

    public boolean contains(E e) {
        int index = findIndex(e);
        if (index != -1) {
            return true;
        }
        return false;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof MyArrayList)) return false;
        MyArrayList<?> that = (MyArrayList<?>) o;
        return size == that.size && Arrays.equals(array, that.array);
    }

    @Override
    public int hashCode() {
        int result = Objects.hash(size);
        result = 31 * result + Arrays.hashCode(array);
        return result;
    }

    // 범위 내의 인덱스인지 확인
    private void indexCheck(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException();
        }
    }

    // 배열 사이즈 증가 (10씩 증가시킴)
    private void extendSize() {
        this.array = Arrays.copyOf(array, array.length + DEFAULT_SIZE);
    }


}
```



## LInkedList

> 제가 만든 코드와 자바의 LinkedList의 메서드와 작동 방식은 차이가 있습니다. 단순히 구조를 파악하기 위한 코드입니다.
>
> 실제 자바의 LinkedList는 Doubly LinkedList로 구현되어 있습니다.

* 단순한 파악만을 위해서 Singly LinkedList로 구현하였다.
* 단, `last` 만을 가지고 있음으로써, 끝에 노드를 추가, 삭제를 간편하게 하고자 하는 기능만 있다.
* 실제 자바도 LinkedList에서 값들 사이에 특정한 인덱스로 값을 추가하거나 삭제하거나, 조회할때, 단순히 first노드부터 인덱스를 찾아나가는 방식을 취한다.*(인덱스와 사이즈를 비교해서 first와 last 중 더 가까운 곳에서부터 시작해서 값을 찾아낼 줄 알았는데 아니였다.)*
* LinkedList는 특정한 인덱스로 값을 조회하고자 할때, 시간복잡도가 O(N)이 된다. (first부터 차례대로 조회)
* LinkedList는 불연속적으로 위치한 각 노드들을 연결한 것이다. 그래서 기본적으로 인덱스를 가지고 있지않고, 다음 차례의 노드(혹은 앞의 차례의 노드까지)만 알고 있다.
* 배열과 다르게 shift하는 과정이 없이 새로운 노드와 앞 노드, 뒷 노드를 연결해주는 과정으로 데이터를 추가, 앞 노드와 뒷 노드를 연결해주는 과정으로의 데이터의 삭제는 O(1)의 시간 복잡도를 가진다.
* 하지만 그 노드를 찾는 과정(접근 시간)이 O(N)의 시간 복잡도를 가진다.(맨 앞의 데이터를 삭제했을 때,O(1))



```java
import java.util.NoSuchElementException;

public class MyLinkedList<E> {

    private Node first;
    private Node last; // 맨 뒤에 새로 추가하는 것을 편하게 하기 위함
    private int size;

    public MyLinkedList() {
        this.first = null;
        this.last = null;
        this.size = 0;
    }

    // 맨 앞에 넣기
    public void addFirst(E e) {
        Node node = new Node(e);
        node.next = first;
        first = node;
        if (size == 0) {
            last = first;
        }
        size++;
    }

    // 맨 뒤에 넣기
    public void add(E e) {
        if (size == 0) {
            addFirst(e);
            return;
        }
        Node node = new Node(e);
        last.next = node;
        last = node;
        size++;
    }

    // 인덱스를 찾아서 넣기
    public void add(int index, E e) {
        if (index == 0) {
            addFirst(e);
            return;
        }
        Node newNode = new Node(e);
        Node previousNode = findNode(index - 1);
        Node currentNode = previousNode.next;
        previousNode.next = newNode;
        newNode.next = currentNode;
        size++;

    }

    public int size() {
        return this.size;
    }

   //get과 같음
    public Node findNode(int index) {
        indexCheck(index);
        Node node = first;
        for (int i = 0; i < index; i++) {
            node = node.next;
        }
        return node;
    }

    public void removeLast() {
        if (size == 1) {
            first = null;
            last = null;
            size--;
            return;
        }

        if (size == 0) {
            throw new NoSuchElementException();
        }

        Node node = findNode(size - 2);
        node.next = null;
        last = node;
        size--;
    }

    public void removeFirst() {
        if (size == 0) {
            throw new NoSuchElementException();
        }
        first = first.next;
        size--;
    }

    public void remove(int index) {
        indexCheck(index);
        if (index == 0) {
            removeFirst();
            return;
        }
        Node node = findNode(index - 1);
        Node targetNode = node.next;
        node.next = targetNode.next;
        size--;
    }

    private void indexCheck(int index) {
        if (index >= size || index < 0) {
            throw new IndexOutOfBoundsException();
        }
    }


}
```



## 정리

* ArrayList는 데이터 조회가 잦을 때, LinkedList는 데이터의 변경이 잦을 때 사용하는 것이 좋다.
* ArrayList는 데이터의 갯수 변경에 취약하다.
* LinkedList는 데이터의 갯수가 증가할수록 데이터를 조회하는 성능에 취약하다.



#### etc

* 단일 연결리스트
  * 다음 노드만을 정보로 가지고 있는 연결리스트
  * 불연속적으로 위치함
* 이중 연결리스트
  * 앞의 노드와 뒤의 노드를 정보를 가지고 있는 연결리스트
  * 단일연결리스트는 현재 노드에서 앞의 노드들의 데이터를 조회하기 위해서는 다시 head에서부터 찾아야 하지만, 이중 연결리스트는 정보를 이미 가지고 있기 때문에 현재 노드에서부터 탐색할 수 있음
* 선형 연결리스트
  * 기존 알고있는 배열
  * 논리적인 순서와 실제로 메모리상에 저장되는 순서가 동일하게 저장되어 있음
* 환형 연결리스트
  * tail을 head로 연결해놓은 구조
  * 반복적인 순회에서 연결리스트의 끝을 체크해야할 필요가 없다.
  * head같은 메타데이터가 필요하지 않다.(어떠한 위치의 노드에서든 전체 노드로의 이동 및 검색이 가능하기 때문)
  * 이로인해 시간, 메모리, 코드 모두 이득을 볼 수 있다.
  * [참고](https://okky.kr/article/455301?note=1407577)



> 개인 정리이므로 틀린 부분이 있을 수 있습니다. 틀린 부분이 있다면 알려주시면 감사하겠습니다.