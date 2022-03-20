## deadlock avoidance

* 데드락이 생길 가능성이 있다면, (cycle이 생길 가능성,불완전한 상황) 자원을 주지 않는다. (시스템이 그러한 가능성을 차단하는 것을 보장)
* 시스템이 safe state에 있으면, 데드락이 생기지 않는다.
* 시스템이 unsafe state에 있다면, 데드락이 생길 가능성이 있다.
* Unsafe state가 되지않도록 차단한다.

### Banker's Algorithm

* Available 자원으로 process가 MAX로 사용할 자원을 감당할 수 있는 상황에서만, 해당 process에게 자원을 할당한다.
* Available 자원으로 처리를 할 수 있는 프로세스가 있기 때문에, 그러한 프로세스에게 자원을 몰아주고, 반납함으로써 sequence가 존재한다. -> safe state
* Safe sequence를 따지는 것은 banker's algorithm이 하는 것이 아니다.

## Deadlock Detection and recovery

* Deadlock Detection
  * Resource type 당 single instance인 경우
    * 자원할당 그래프에서의 cycle이 곧 deadlock을 의미
  * Resource type 당 multiple instance인 경우
    * 뱅커스 알고리즘과 같이 테이블을 그리는 방법과 유사하게 활용
  * 현재 자원을 요청하지 않은 프로세스들이 자원을 반납할 것이라고 가정하여 deadlock을 체크한다. (낙관적)ㄲ
* Wait-for graph 알고리즘
  * Resource type 당 single instance인 경우
  * Wait-for graph(자원할당 그래프의 변형)
  * 프로세스만으로 node를 구성(자원을 체크하지않음)
  * Pi가 가지고 있는 자원을 Pj가 기다리는 경우 Pj -> Pi
  * Algorithm
    * Wait-for graph에 사이클이 존재하는지를 주기적으로 조사
    * O(n^2)

* Recovery
  1. Process termination
     * 데드락에 연루된 모든 프로세스들을 다 종료시키는 방법
     * 데드락에 연루된 프로세스들을 하나씩 종료시키는 방법
  2. Resource Preemption
     * 비용을 최소화할 victim의 선정
     * safe state로 rollback하여 process를 restart
     * Starvation 문제
       * 동일한 프로세스가 계속해서 victim으로 선정되는 경우
       * cost factor에 rollback 횟수도 같이 고려

## Deadlock Ignorance

* Deadlock이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음
  * Deadlock이 매우 드물게 발생하므로 deadlock에 대한 조치 자체가 더 큰 overhead일 수 있다.
  * 만약, 시스템에 데드락이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 process를 죽이는 등의 방법으로 대처한다
  * UNIX, Windows 등 대부분의 범용 OS가 채택

# 메모리 관리 1

## Logical VS. Physical Address

### Logical address (=가상 주소)

* 프로세스마다 독립적으로 가지는 주소 공간
* 각 프로세스마다 0번지부터 시작
* **CPU가 보는 주소는 logical address이다.**

### Physical address

* 메모리에 실제 올라가는 위치



* 주소 바인딩 : 주소를 결정하는 것
* Symbolic Address(프로그래머가 보는 주소)-> Logical Address(컴파일될 때) -> Physical Address(실행될 때)



## 주소 바인딩

* Compile time binding
  * 물리적 메모리 주소가 컴파일 시 알려짐
  * 시작 위치 변경시 다시 컴파일
  * 컴파일러는 절대 코드(absolute code) 생성한다.
* Load time binding
  * Loader의 책임하에 물리적 메모리 주소 부여
  * 컴파일러가 재배치가능코드(relocatable code = 메모리 주소가 재배치 가능한 코드)를 생성한 경우 가능
* Execution time binding (=Run time binding)
  * 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음
  * CPU가 주소를 참조할 때마다 binding을 점검
  * **하드웨어적인 지원이 필요하다**
    * base and limit registers, MMU

CPU는 기계어를 실행하려면, 그때그때, 논리적인 주소를 물리적인 주소로 변환해서  메모리에 접근한다.

메모리접근은 (주소를 바인딩하는 것은 빠르게 이뤄져야하기 때문에) 운영체제에 CPU로 넘어가서 처리하는 것이 아니라 하드웨어가 처리한다.



## Memory- Management Unit (MMU)

### Dynamic Relocation

* 실제로는 프로세스가 쪼개져서 필요한 것만 물리적 메모리에만 담기고, 필요없는 것은 스왑영역으로 보내진다.
* 하지만 이 경우 단순하게 프로세스가 연속적으로 메모리에 담겨있는 경우로 가정한다.
* base register : 시작점, 접근할 수 있는 물리적 메모리 주소의 최솟값
* Limit register : 논리적 주소의 범위, 범위를 초과하면 trab 발생

#### MMU

* logical address를 physical address로 매핑해 주는 Hardware device

#### MMU scheme

* 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register(=relocation register)의 값을 더한다.

#### user program

* logical address만을 다룬다
* 실제 physical address를 볼 수 없으며 알 필요가 없다.



## 용어 정리

#### Dynamic Loading

* 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load하는 것
* meory utiliztion의 향상한다.
* 가끔식 사용되는 많은 양의 코드의 경우 유용하다.
  * 예외 상황을 처리하는 코드
* 운영체제의 특별한 지원없이 프로그램 자체에서 구현 가능하다. (OS는 라이브러리를 통해 지원 가능)

#### Overlays

* 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
* 프로세스의 크기가 메모리보다 클 때 유용하다.
* 운영체제의 지원없이 사용자에 의해 구현된다.
* 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현
  * Manual Overlay : 프로그래밍이 매우 복잡하다.

#### Swapping

* 프로세스를 일시적으로 메모리에서 backing store로 쫓아내는 것
  * 프로세스를 통째로!
* Backing store (= swap area)
  * 디스크 : 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간
* Swap in / Swap out
  * 일반적으로 중기 스케줄러(swapper)에 의해 swap out 시킬 프로세스 선정
  * Priority-based CPU scheduling algorithm
  * Compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in해야 함
  * **Exection time binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음**
  * swap time은 대부분 transfer time(swap되는 양에 비례하는 시간)이다.

> original swapping은 프로세스 전체를 swap in, swap out 하지만, 페이징에 의해서 하나의 페이지만 swap in, swap out하는 상황도 있기때문에 유의해서 의미를 파악해야한다.

#### Dynamic Linking

* Linking 라이브러리 코드를 프로그램에 연결하는 것

* Linking을 실행 시간까지는 미루는 기법
* Static Linking (static library)
  * 라이브러리가 프로그램의 실행 파일 코드에 포함되는 것
  * 실행 파일의 크기가 커짐
  * 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리를 낭비한다. (Printf 함수의 라이브러리 코드)
* Dynamic Linking (shared library) (linux: `.so`)(windows: `.dll`)
  * 라이브러리가 실행시 연결됨
  * 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub이라는 작은 코드를 둔다.
  * 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어온다.
  * 운영체제의 도움이 필요하다.

## Allocation of Physical Memory

* 메모리는 일반적으로 두 영역으로 나눠서 사용
  * OS 상주 영역
    * interrupt vector와 함께 낮은 주소 영역 사용
  * 사용자 프로세스 
    * 높은 주소 영역 사용
* 사용자 프로세스 영역의 할당 방법
  * Contiguos allocation
    * 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
      * 고정 분할 방식
      * 가변 분할 방식
  * Noncontiguos allocation
    * 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음



### Contiguos Allocation

* Hole
  * 가용 메모리 공간
  * 다양한 크기의 hole들이 메모리 여로 곳에 흩어져 있다.
  * 프로세스가 도착하면 수용가능한 hole을 할당
  * 운영체제는 할당 공간과 가용 공간의 정보를 유지한다.

#### Dynamic Storage-Allocation Problem

* 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제
* First-fit
  * 사이즈가 n 이상인 것 중 최초로 찾아지는 hole에 할당한다.
* Best-fit
  * 사이즈가 n 이상인 hole 중에 가장 작은 hole을 찾아서 할당한다.
  * hole들의 리스트가 크기순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색해야한다. (탐색의 오버헤드가 발생)
  * 많은 수의 아주 작은 hole들이 생성된다.
* Worst-fit
  * 가장 큰 hole에 할당한다.
  * 모든 hole의 리스트를 탐색해야한다. (탐색의 오버헤드가 발생)
  * 상대적으로 아주 큰 hole들이 생성된다.
* First-fit과 best-fit이 worst-fit보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려져있다. (실험적인 결과)

### compaction

* External fragmentation 문제를 해결하는 방법 중 하나이다.
* 사용 중인 메모리 영역을 한군데로 몰고 hole들을 다른 한 곳으로 몰아 큰 hole block을 만드는 것이다.
* 매우 비용이 많이 드는 방법이다.
* 최소한의 메모리 이동으로 compaction하는 방법도 있으나 매우 복잡하다.
* compaction은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있다. (Run time binding에서만 가능)