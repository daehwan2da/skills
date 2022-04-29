> 이슈 #8 : Spring/async

## Executors 는 무엇이고 동작원리?
> java 에서 Thread Pool 의 역할을 수행하며, Thread 생성과 관리를 담당하는 고수준의 concurrency programming 을 지원한다.  
> ExecutorService의 구현체를 static method ( .newSingleThreadExecutor() 등) 를 통해 반환하며,  
> 이때 작업을 주어진 쓰레드풀의 쓰레드를 활용해 수행한다. 
> Thread pool의 thread 들에게 주어진 작업을 할당하는 방식이며, 만약 thread 들이 전부 작업중이라면, BlockingQueue에 요청들을  
> 쌓아둔다.  따라서 별도의 shutdown 처리를 해주지 않으면 BlockingQueue가 wait 상태를 유지하여 프로세스가 끝나지 않는다.  

### Blocking Queue?
> java util concurrent 내의 interface로, Thread safe 한 Queue이며, 내부적으로 lock 을 통해 동기적으로 수행된다.  
> 특히 queue 에 꺼내갈 데이터가 없거나, 큐가 가득찬 상태가 되면 wait 상태에 빠진다.  

## JVM 에서 Thread 들간 데이터가 공유되는 메모리 영역은?
> Heap, Method 영역 에서 데이터가 공유된다.  

### java 의 synchronized 는?
> 멀티 thread 환경에서 데이터의 동기화 처리를 위한 영역이며, 한 쓰레드가 이 영역에 접근했으면 lock 이 걸려 다른 쓰레드는 대기 상태가 된다.  

### java 에서 thread safe를 보장하기 위한 장치와 메커니즘은?
> 가장 단순한 방법은 final 을 선언하여 불변 상태로 만들어주는 것이다. 또한 synchronized block 을 통해 동기화가 필요한 영역에 대해 lock 을 활성화 시키는 방법이있다. 또한 필드 변수에 대해서는 volatile 을 적용하여  
> 데이터의 정합성을 보장한다. 최근에는 함수형 프로그래밍이 발달하면서 불변상태를 통한 thread safe 메커니즘이 많이 활용되어진다.  

### volatile 이란?
> 멀티 쓰레드 환경에서 서로 공유되는 변수의 정합성을 보장하기 위한 장치이다. 데이터가 cache 되지 않고 바로 메모리에 적재되며, 이는 cache 를 통해 
> 서로 다른 쓰레드가 같은 변수에 다른 값을 갖는 경우를 막기 위함이다.  

## java concurrent package
### ConcurrentHashMap 은 무엇이고 일반적인 HashMap과 차이는 무엇일까?
> Map 자료구조로서 thread safe 함과 원자성을 보장한다. 특히 갱신 요청에 대해서만 synchronized block 이 적용된다.  
> 만약 multi thread 환경이라면, 싱글톤 인스턴스의 필드에 대해 HashMap 을 쓰는 것은 thread safe하지 못하므로 절대적으로 ConcurrentHashMap을 사용해야한다.  
> 물론 스프링 빈 들에 대해서는 처음부터 필드에 어떤 자료구조를 선언하는 것은 옳지 않다고 본다.  

### 그럼 Entity 객체 필드에 자료구조를 사용하는 경우가 있는데 (예를 들어 @OneToMany) 이때는 어떻게 보장되나?
> 애초에 @Entity 객체들은 javax 의 영속성과 관련된 POJO 클래스이지, 빈 객체와는 관련이 없다.  

## Future 와 CompletableFuture 는 무엇일까?
> Future 는 쓰레드의 행위에 대해서 미래 지향적인 결과를 반환하는 클래스이다. 따라서 java 에서 비동기 프로그래밍을 가능하게 하는 인터페이스이다.  
> 하지만 기존 Future 만 활용하는 경우에는 예외처리 API 를 제공하지 않으며, 실제 비동기 처리에 있어 callback 을 get() 호출 전에 정의가 불가능하다.  
> 이런 문제를 해결하고자 Java 8 부터 CompletableFuture 가 도입되었으며, 이는 Future를 chaining 방식을 활용해 보다 더 편리하게 사용하도록 도와준다.  
> 또한 CompletableFuture 는 명시적으로 Executor 를 활용할 필요 없이, 비동기처리가 가능하다. (default 로 ForkJoinPool 을 활용한다.)

## Runnable 과 Callable 의 차이는?
> 둘다 함수형 인터페이스이지만, 쓰레드의 행위를 정의함에 있어 반환값의 여부 차이가 있다. 특히 Callable의 경우에는 주로 Future 를 반환하는 식으로 활용한다.  


## java 에서 thread 간 데이터를 공유하는 방법은?
> 같은 process 내의 여러 thread 간에 효과적인 데이터 공유 방법은 메모리를 공유하는 것이다.  
> 특히 JVM 에서는 Heap, Methode 영역이 쓰레드 간 공유되며, Java 에서는 static 변수 혹은 싱글톤 인스턴스를 통해 공유가 가능하며,  
> 이때 thread safe 를 보장하기 위해 synchronized block 과 volatile을 활용하거나, atomic 클래스들을 활용한다.  또한 불변상태의 데이터로 초기부터 설정하여 변경을 차단하는 방법도 있다.


