# 🧹 가비지 컬렉션 (Garbage Collection)
> ### Garbage Collection이란?
**Heap 메모리에 동적 할당되었으나 참조되지 않는 대상을 탐지하여 메모리에서 해제하는 JVM의 기능.
프로그램을 개발 하다 보면 유효하지 않은 메모리인 가비지(Garbage)가 발생하게 된다. C언어를 이용하면 free()라는 함수를 통해 직접 메모리를 해제해주어야 하지만 Java나 Kotlin을 이용해 개발을 하다 보면 개발자가 메모리를 직접 해제해주는 일이 없다. 그 이유는 JVM의 가비지 컬렉터가 불필요한 메모리를 알아서 정리해주기 때문이다.**

### 👍 Garbage Collection의 목적은 메모리 관리를 간편화하고 메모리 누수를 방지하여 프로그램의 안정성과 개발 생산성을 향상시키는 것.


```
메모리 누수(Memory Leaks)란?
메모리 누수는 프로그램이 동적으로 할당한 메모리를 해제하지 않고 계속 사용하는 상황을 말한다.
시간이 지남에 따라 메모리 사용량이 계속 늘어나며, 결국 시스템의 성능 저하나 충돌로 이어질 수 있다.
```
### Garbage Collection의 단점

- 개발자가 메모리가 언제 해제되는지 정확하게 알 수 없다.
- 가비지 컬렉션이 동작하는 동안에는 다른 동작을 멈추기 때문에 오버헤드가 발생한다.

### Garbage Collection의 대상
<img src="https://velog.velcdn.com/images/nyoung215/post/625013b1-2774-4664-858b-22acb89a4743/image.png" width="50%">

- 객체가 NULL인 경우 (ex. String str = null)
- 블럭 실행 종료 후, 블럭 안에서 생성된 객체
- 부모 객체가 NULL인 경우, 포함하는 자식 객체

## Garbage Collection 동작 과정

> **JVM의 가비지 컬렉터는 'Weak Generational Hypothesis'를 전제로 설계되었다.**
```
Weak Generational Hypothesis 란?
신규로 생성한 객체의 대부분은 금방 사용하지 않는 상태가 되고, 오래된 객체에서 신규 객체로의 참조는 매우 적게 존재한다는 가설.
이 가설에 기반하여 자바는 Young 영역과 Old 영역으로 메모리를 분할하고, 신규로 생성되는 객체는 Young 영역에 보관하고, 오랫동안 살아남은 객체는 Old 영역에 보관한다.
```

```
Person person = new Person();
person.setName("nyoung");
person = null;

// 가비지 발생
person = new Person();
person.setName("real nyoung");
```
**이 코드에서 처음에 선언된 “nyoung” person 객체의 경우, “real nyoung” person 객체로 변경되면서 heap space에 남아있지만 아무도 참조할 수 없는 객체로 변해 가비지 컬렉션의 대상이 되었다.**


가비지 콜렉터는 메모리를 회수하기 위해 종종 자바 애플리케이션을 “멈추고” 메모리 회수를 진행한다.

- 회수를 위해 자바 애플리케이션을 멈추는 걸 “Stop The World”
- 회수 대상이 아닌 걸 표시해놓는 걸 “Mark” (즉, 현재 사용 중인걸 표시)
- 표시 안된 대상을 메모리 상에서 해제하는 걸 “Sweep”이라고 한다.

가비지 콜렉터의 Stop-The-World는 짧을 수도 길 수도, (살아있는 객체의 개수에 따라 달라진다.)

### 🧐 자바 애플리케이션을 멈출 수도 있기 때문에 가능한 한 빨리, heap space의 적은 범위만을 탐색할 필요가 있다.
> Yong Generation 영역과 Old Generation 영역을 나누게 된 이유.
Weak Generational Hypothesis를 지키기 위해 Young 영역과 Old 영역을 나누어놓고 힙 전체에서 Garbage Collection를 진행하지 않고 Young 영역에서만 Garbage Collection를 수행(minor GC)한다. 여기서 대부분의 garbage가 수거되기 때문에, 메모리 낭비를 막을 수 있다.

- 성능 최적화: 새로 생성된 객체와 오래된 객체를 서로 다른 방식으로 처리하여 성능을 최적화
- 메모리 관리 최적화: Young 영역은 재배열된 메모리를 사용하고, Old 영역은 오랜 시간 동안 존재하는 객체를 보관하기 위한 메모리로 구성되어 메모리 관리를 최적화
- 객체 수명에 따른 처리: 대부분의 객체는 짧은 시간 동안만 살아남기 때문에, Young 영역은 빠르게 정리하고, Old 영역은 오래된 객체를 효율적으로 처리한다.
- GC 알고리즘 선택: Young 영역과 Old 영역은 서로 다른 GC 알고리즘을 사용할 수 있어서, 객체의 수명과 메모리 상태에 따라 적절한 알고리즘을 선택할 수 있다.


## Generational Garbage Collection
<img src="https://velog.velcdn.com/images/nyoung215/post/8d45c36d-768a-4812-ab61-abeaa417c6ce/image.png" width="60%">


### Young 영역(Yong Generation 영역)
> 새롭게 생성된 객체가 할당되는 영역. 새로 생성된 객체는 Eden 영역에 할당되며, 살아남은 객체는 S0 또는 S1로 이동한다.
```
eden - 새로 생성된 객체가 할당(Allocation)되는 영역
survivor - 최소 1번의 GC 이상 살아남은 객체가 존재하는 영역
```

- 대부분의 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라진다.
- Young 영역에 대한 가비지 컬렉션(Garbage Collection)을 Minor GC라고 부른다.
  <img src="https://velog.velcdn.com/images/nyoung215/post/762f59a8-5b68-4443-87ff-4d42db82576b/image.png" width="60%">

**Young 영역의 동작 순서**

1. 새로 생성된 객체가 Eden 영역에 할당된다.
2. 객체가 계속 생성되어 Eden 영역이 꽉차게 되고 Minor GC가 실행된다.
   2-1. Eden 영역에서 사용되지 않는 객체의 메모리가 해제된다.
   2-2. Eden 영역에서 살아남은 객체는 1개의 Survivor 영역으로 이동된다.
3. 1~2번의 과정이 반복되다가 Survivor 영역이 가득 차게 되면 Survivor 영역의 살아남은 객체를 다른 Survivor 영역으로 이동시킨다.(1개의 Survivor 영역은 반드시 빈 상태가 된다.)
4. 이러한 과정을 반복하여 계속해서 살아남은 객체는 Old 영역으로 이동(Promotion)된다.

### Old 영역(Old Generation 영역)
> Young 영역에서 일정 시간 동안 살아남은 객체들이 이동하는 영역.

- Young 영역의 수명이 짧은 객체들은 큰 공간을 필요로 하지 않으며 큰 객체들은 Young 영역이 아니라 바로 Old 영역에 할당되기 때문에 Young 영역보다 크게 할당되며, 영역의 크기가 큰 만큼 가비지는 적게 발생한다.
- Old 영역에 대한 가비지 컬렉션(Garbage Collection)을 Major GC라고 부른다.
### Metaspace 영역
> Metaspace는 Java 8 이상에서 사용되는 메모리 영역으로, 클래스 메타데이터와 리플렉션을 지원하는 데 사용

- 기존 PermGen이라는 영역으로, 클래스 로딩, 메소드, 상수 풀, 정적 변수 등을 저장했다. 그러나 PermGen은 고정 크기로 설정되어 있어 오버플로우로 인한 문제가 발생할 수 있었고, Java 8 이상에서는 Metaspace로 대체되었다.
- 동적으로 크기가 조절되며, 클래스 로딩 및 언로딩에 따라 메모리가 할당되고 해제된다.

<img src="https://velog.velcdn.com/images/nyoung215/post/c7fd1c08-bfe4-4b79-bd27-f6311d5df5d9/image.png" width="50%">

___
래퍼런스
https://mangkyu.tistory.com/118
https://hoonzi-text.tistory.com/103