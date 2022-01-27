# CompletableFuture 비동기 연습하기

* CompletableFuture를 사용해서 비동기 처리를 문법적으로 동작연습을 해보았다.
* 병렬 처리, 메서드의 결과를 기다리지않고 다른 작업을 수행, 즉 병렬처리를 위한 처리를 위함
* 다른 메서드와 연계하는 연습도 해보았는데, 내일 더 자세히 이해하고 정리해야겠다.

```java
import java.util.concurrent.CompletableFuture;

public class Cashier {
		
    private OrderQueue orderQueue; //큐를 가지고 있는 일급컬렉션

    public Cashier() {
        orderQueue = OrderQueue.getInstance();
    }
		
  	// 주문을 받아 주문을 큐에 넣는 메서드, 비동기처리 해보고자함
    public CompletableFuture<String> takeOrder(String input) {
				
        return CompletableFuture.supplyAsync(() -> {
            orderQueue.saveOrder(input); 
            return orderQueue.toString();
        });
    }


}
```

```java
import org.junit.jupiter.api.Test;
import java.util.concurrent.ExecutionException;

class CashierTest {

    @Test
    void test1() throws ExecutionException, InterruptedException {
        Cashier cashier = new Cashier();
        System.out.println(cashier.takeOrder("2:3").get()); // 2번메뉴를 3개 주문

    }


}
```

```
// output
[2, 2, 2]
```

